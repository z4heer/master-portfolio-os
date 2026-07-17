## Design & Dependencies

**Files modified:**
- `backend/app/modules/catalog/models/product.py` — add `image_url`, fix missing `sku` mapping
- `backend/alembic/versions/<new>_add_image_url_to_products.py` — new migration (no migration needed for `sku`, that column already exists in DB from `d8a6f0b93c41`)
- `backend/app/modules/orders/models/order.py` — add `shipping_address`
- `backend/alembic/versions/<new>_add_shipping_address_to_orders.py` — new migration
- `backend/app/modules/orders/schemas/order_request.py` — require `shipping_address`
- `backend/app/modules/orders/schemas/order_response.py` — expose `shipping_address`
- `backend/app/modules/orders/services/order_service.py` — persist `shipping_address` (price-snapshot logic was **already correct**, no change needed there)
- `backend/tests/unit/orders/test_order_service_price_snapshot.py` — new test

**Not touched yet (still needed from you):** `product_request.py`/`product_response.py` (ProductCreate/ProductRead DTOs) and `product_repository.py` — I don't have these files, so Task 1's DTO layer is deferred until you send them. Everything below is safe to apply independently.

**Migration chain assumption:** I'm treating `d8a6f0b93c41` as current head (its `down_revision` was `9b2f4f6c7a81` and it was your most recent untracked file). Please confirm with `alembic heads` before running — if there's a newer migration I haven't seen, I'll need to rechain.

**`sku` fix:** quick and low-risk — the column already exists in Postgres (migration already ran/will run), the ORM model just wasn't mapping it. Adding the mapping doesn't require a new migration.

---

## 1. `backend/app/modules/catalog/models/product.py`

```python
import uuid

from sqlalchemy import String, Text, Numeric, Boolean, DateTime

from sqlalchemy.orm import Mapped
from sqlalchemy.orm import mapped_column
from sqlalchemy.orm import relationship

from sqlalchemy.sql import func
from sqlalchemy.dialects.postgresql import UUID

from app.database.base import Base


class Product(Base):
    __tablename__ = "products"

    id: Mapped[uuid.UUID] = mapped_column(
        UUID(as_uuid=True), primary_key=True, default=uuid.uuid4
    )

    name: Mapped[str] = mapped_column(String(255), nullable=False)

    description: Mapped[str] = mapped_column(Text, nullable=True)

    category: Mapped[str] = mapped_column(String(100), nullable=False)

    # Mapping fix: column already exists in DB (migration d8a6f0b93c41) but
    # was never mapped on the ORM model — reads/writes of Product.sku were
    # silently unavailable until now.
    sku: Mapped[str | None] = mapped_column(
        String(64), unique=True, nullable=True, index=True
    )

    price: Mapped[float] = mapped_column(Numeric(10, 2), nullable=False)

    image_url: Mapped[str | None] = mapped_column(String(2048), nullable=True)

    is_active: Mapped[bool] = mapped_column(Boolean, default=True)

    created_at = mapped_column(DateTime(timezone=True), server_default=func.now())

    updated_at = mapped_column(
        DateTime(timezone=True), server_default=func.now(), onupdate=func.now()
    )

    inventory = relationship("Inventory", back_populates="product", uselist=False)
```

## 2. New migration — `backend/alembic/versions/b1c2d3e4f5a6_add_image_url_to_products.py`

```python
"""add image_url to products

Revision ID: b1c2d3e4f5a6
Revises: d8a6f0b93c41
Create Date: 2026-07-17 09:00:00.000000

"""

from typing import Sequence, Union

from alembic import op
import sqlalchemy as sa

revision: str = "b1c2d3e4f5a6"
down_revision: Union[str, Sequence[str], None] = "d8a6f0b93c41"
branch_labels: Union[str, Sequence[str], None] = None
depends_on: Union[str, Sequence[str], None] = None


def upgrade() -> None:
    bind = op.get_bind()
    inspector = sa.inspect(bind)
    columns = {c["name"] for c in inspector.get_columns("products")}

    if "image_url" not in columns:
        op.add_column(
            "products",
            sa.Column("image_url", sa.String(length=2048), nullable=True),
        )


def downgrade() -> None:
    bind = op.get_bind()
    inspector = sa.inspect(bind)
    columns = {c["name"] for c in inspector.get_columns("products")}

    if "image_url" in columns:
        op.drop_column("products", "image_url")
```

## 3. `backend/app/modules/orders/models/order.py`

```python
import uuid
from enum import Enum
from datetime import datetime
from sqlalchemy import String, Numeric, DateTime, ForeignKey
from sqlalchemy.orm import Mapped, mapped_column, relationship
from app.database.base import Base


class OrderStatus(str, Enum):
    PENDING = "PENDING"
    SHIPPED = "SHIPPED"
    DELIVERED = "DELIVERED"
    CANCELLED = "CANCELLED"


class Order(Base):
    __tablename__ = "orders"

    id: Mapped[uuid.UUID] = mapped_column(primary_key=True, default=uuid.uuid4)
    user_id: Mapped[uuid.UUID] = mapped_column(
        ForeignKey("users.id", ondelete="CASCADE"), nullable=False
    )
    total_amount: Mapped[float] = mapped_column(Numeric(12, 2), nullable=False)
    status: Mapped[OrderStatus] = mapped_column(
        String, default=OrderStatus.PENDING, nullable=False
    )
    shipping_address: Mapped[str] = mapped_column(String(500), nullable=False)
    created_at: Mapped[datetime] = mapped_column(
        DateTime, default=datetime.utcnow, nullable=False
    )

    # Relationships
    user = relationship("User", back_populates="orders")
    items: Mapped[list["OrderItem"]] = relationship(
        "OrderItem", back_populates="order", cascade="all, delete-orphan"
    )
```

## 4. New migration — `backend/alembic/versions/c7d8e9f0a1b2_add_shipping_address_to_orders.py`

```python
"""add shipping_address to orders

Revision ID: c7d8e9f0a1b2
Revises: b1c2d3e4f5a6
Create Date: 2026-07-17 09:05:00.000000

"""

from typing import Sequence, Union

from alembic import op
import sqlalchemy as sa

revision: str = "c7d8e9f0a1b2"
down_revision: Union[str, Sequence[str], None] = "b1c2d3e4f5a6"
branch_labels: Union[str, Sequence[str], None] = None
depends_on: Union[str, Sequence[str], None] = None


def upgrade() -> None:
    bind = op.get_bind()
    inspector = sa.inspect(bind)
    columns = {c["name"] for c in inspector.get_columns("orders")}

    if "shipping_address" not in columns:
        # Add nullable first so existing rows aren't rejected, backfill,
        # then enforce NOT NULL — same pattern used in d8a6f0b93c41 for sku.
        op.add_column(
            "orders",
            sa.Column("shipping_address", sa.String(length=500), nullable=True),
        )
        op.execute(
            "UPDATE orders SET shipping_address = 'LEGACY ORDER - NO ADDRESS ON FILE' "
            "WHERE shipping_address IS NULL"
        )
        op.alter_column("orders", "shipping_address", nullable=False)


def downgrade() -> None:
    bind = op.get_bind()
    inspector = sa.inspect(bind)
    columns = {c["name"] for c in inspector.get_columns("orders")}

    if "shipping_address" in columns:
        op.drop_column("orders", "shipping_address")
```

## 5. `backend/app/modules/orders/schemas/order_request.py`

```python
from pydantic import BaseModel, Field, field_validator
from uuid import UUID
from app.modules.orders.models.order import OrderStatus


class OrderItemRequest(BaseModel):
    product_id: UUID
    quantity: int = Field(..., description="Quantity of items to purchase")

    @field_validator("quantity")
    @classmethod
    def validate_quantity(cls, v: int) -> int:
        if v <= 0:
            raise ValueError("Quantity must be greater than 0")
        return v


class CreateOrderRequest(BaseModel):
    items: list[OrderItemRequest]
    shipping_address: str = Field(
        ...,
        min_length=1,
        max_length=500,
        description="Delivery address for the order",
    )

    @field_validator("shipping_address")
    @classmethod
    def validate_shipping_address(cls, v: str) -> str:
        stripped = v.strip()
        if not stripped:
            raise ValueError("Shipping address must not be blank")
        return stripped


class OrderStatusUpdateRequest(BaseModel):
    status: OrderStatus
```

## 6. `backend/app/modules/orders/schemas/order_response.py`

```python
from pydantic import BaseModel
from uuid import UUID
from datetime import datetime
from app.modules.orders.models.order import OrderStatus


class OrderItemResponse(BaseModel):
    product_id: UUID
    quantity: int
    unit_price: float

    class Config:
        from_attributes = True


class OrderResponse(BaseModel):
    id: UUID
    total_amount: float
    status: OrderStatus
    shipping_address: str
    created_at: datetime

    class Config:
        from_attributes = True


class OrderDetailResponse(BaseModel):
    id: UUID
    status: OrderStatus
    total_amount: float
    shipping_address: str
    created_at: datetime
    items: list[OrderItemResponse]

    class Config:
        from_attributes = True
```

## 7. `backend/app/modules/orders/services/order_service.py` — single change

```python
            # 3. Formulate Order object
            new_order = Order(
                user_id=user_id,
                total_amount=total_amount,
                status=OrderStatus.PENDING,
                shipping_address=request.shipping_address,
                items=order_items_entities,
            )
```

Everything else in this file (the price-snapshot loop, transaction handling, status transitions) is unchanged — `item_price = float(product.price)` was already captured onto `OrderItem.unit_price` at creation time, so historical orders are already immune to later `Product.price` changes.

## 8. New test — `backend/tests/unit/orders/test_order_service_price_snapshot.py`

Your `backend/tests/` directory was empty/untracked, so I don't have existing fixture/conftest conventions to match — this is self-contained with `unittest.mock`. Send me your `conftest.py` if you have DB-backed integration fixtures and I'll adapt these to that style instead.

```python
import uuid
from unittest.mock import MagicMock

import pytest

from app.modules.orders.models.order import Order
from app.modules.orders.schemas.order_request import (
    CreateOrderRequest,
    OrderItemRequest,
)
from app.modules.orders.services.order_service import OrderService


class _FakeProduct:
    def __init__(self, price: float) -> None:
        self.price = price


def _build_service(product_price: float):
    db = MagicMock()
    order_repo = MagicMock()
    inventory_service = MagicMock()
    product_repo = MagicMock()

    product = _FakeProduct(price=product_price)
    product_repo.get_by_id.return_value = product

    def _create_order(order: Order) -> Order:
        order.id = uuid.uuid4()
        return order

    order_repo.create_order.side_effect = _create_order

    service = OrderService(
        db=db,
        order_repo=order_repo,
        inventory_service=inventory_service,
        product_repo=product_repo,
    )
    return service, product


def test_order_item_snapshots_price_at_creation_time() -> None:
    """unit_price on OrderItem must be captured at order-creation time."""
    service, _ = _build_service(product_price=19.99)

    request = CreateOrderRequest(
        items=[OrderItemRequest(product_id=uuid.uuid4(), quantity=2)],
        shipping_address="221B Baker Street, London",
    )

    order = service.create_order(user_id=uuid.uuid4(), request=request)

    assert len(order.items) == 1
    assert order.items[0].unit_price == pytest.approx(19.99)
    assert order.total_amount == pytest.approx(39.98)


def test_order_total_is_immune_to_later_price_changes() -> None:
    """Changing Product.price after order creation must not alter a
    previously created order's snapshot unit_price or total_amount."""
    service, product = _build_service(product_price=50.00)

    request = CreateOrderRequest(
        items=[OrderItemRequest(product_id=uuid.uuid4(), quantity=1)],
        shipping_address="221B Baker Street, London",
    )

    order = service.create_order(user_id=uuid.uuid4(), request=request)
    original_unit_price = order.items[0].unit_price
    original_total = order.total_amount

    # Simulate an admin price change happening after the order was placed.
    product.price = 999.00

    assert order.items[0].unit_price == original_unit_price
    assert order.total_amount == original_total
    assert order.items[0].unit_price != product.price


def test_shipping_address_persisted_on_order() -> None:
    service, _ = _build_service(product_price=10.0)

    request = CreateOrderRequest(
        items=[OrderItemRequest(product_id=uuid.uuid4(), quantity=1)],
        shipping_address="742 Evergreen Terrace, Springfield",
    )

    order = service.create_order(user_id=uuid.uuid4(), request=request)

    assert order.shipping_address == "742 Evergreen Terrace, Springfield"
```

---

## Validation Commands

```bash
cd backend

black --check app tests
ruff check app tests
mypy app

# Confirm current head matches assumption, then apply migrations
alembic heads
alembic upgrade head

pytest tests/unit/orders/test_order_service_price_snapshot.py -v
pytest
```

**Expected output:** `black`/`ruff`/`mypy` clean; both new migrations apply without error against a Postgres 17 instance with the existing schema; all 3 new tests pass; full suite green.

## Git Commit Message

```
feat(orders,catalog): add shipping_address and image_url support

Backend:
- Add nullable image_url column to Product model + migration
- Fix Product.sku ORM mapping (column existed in DB, was unmapped)
- Add required shipping_address column to Order model + migration
- Require shipping_address in CreateOrderRequest, expose in OrderResponse
  and OrderDetailResponse
- Persist shipping_address in OrderService.create_order
- Add unit tests verifying OrderItem.unit_price snapshot is immune to
  later Product.price changes

Sprint 4.6A / Commit 1.1
```

## Implementation Summary

Backend Task 2 is functionally complete pending your review. Backend Task 1 is **partial** — model and migration done, but `ProductRead`/`ProductCreate` DTOs and the repository layer still need updating, and I don't have those files yet.

**Please send:**
- `product_request.py` / `product_response.py` (or wherever ProductCreate/ProductRead live)
- `product_repository.py`

Once I have those I'll close out Task 1's backend half, then we move to frontend per your sequencing (Product visuals → Checkout form → Dashboard signals) — for which I'll still need the template/`app-card`/`checkout`/`cart.service`/`dashboard` files from my earlier list.
