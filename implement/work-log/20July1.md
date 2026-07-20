Excellent. We're now entering the implementation phase. From this point onward, we'll make changes as if they were going through a production code review.

**Goal of Sprint A:** Make the ORM match the database exactly. No business logic changes yet.

---

# File 1: `backend/app/modules/orders/models/order.py`

## Replace the current model with the following structure

> Keep your existing imports that are still needed, then make these changes.

### 1. Add imports

```python
import uuid
from datetime import datetime
from enum import Enum

from sqlalchemy import (
    String,
    Text,
    Numeric,
    DateTime,
    ForeignKey,
    Enum as SqlEnum,
)

from sqlalchemy.orm import (
    Mapped,
    mapped_column,
    relationship,
)
```

---

### 2. Define enums

These match your PostgreSQL enums exactly.

```python
class OrderStatus(str, Enum):
    PENDING = "PENDING"
    CONFIRMED = "CONFIRMED"
    PROCESSING = "PROCESSING"
    SHIPPED = "SHIPPED"
    DELIVERED = "DELIVERED"
    CANCELLED = "CANCELLED"


class PaymentMethod(str, Enum):
    COD = "COD"
    CARD = "CARD"
    UPI = "UPI"
    NETBANKING = "NETBANKING"


class PaymentStatus(str, Enum):
    PENDING = "PENDING"
    PAID = "PAID"
    FAILED = "FAILED"
    REFUNDED = "REFUNDED"


class Currency(str, Enum):
    INR = "INR"
    USD = "USD"
```

---

### 3. Update the `Order` model

Replace the class body with fields matching the database:

```python
class Order(Base):
    __tablename__ = "orders"

    id: Mapped[uuid.UUID] = mapped_column(
        primary_key=True,
        default=uuid.uuid4,
    )

    order_number: Mapped[str] = mapped_column(
        String(50),
        unique=True,
        index=True,
        nullable=False,
    )

    user_id: Mapped[uuid.UUID] = mapped_column(
        ForeignKey("users.id", ondelete="CASCADE"),
        nullable=False,
    )

    status: Mapped[OrderStatus] = mapped_column(
        SqlEnum(OrderStatus),
        default=OrderStatus.PENDING,
        nullable=False,
    )

    total_amount: Mapped[float] = mapped_column(
        Numeric(10, 2),
        default=0,
        nullable=False,
    )

    created_at: Mapped[datetime] = mapped_column(
        DateTime,
        default=datetime.utcnow,
        nullable=False,
    )

    shipping_address: Mapped[str | None] = mapped_column(
        Text,
        nullable=True,
    )

    payment_method: Mapped[PaymentMethod] = mapped_column(
        SqlEnum(PaymentMethod),
        default=PaymentMethod.COD,
        nullable=False,
    )

    payment_status: Mapped[PaymentStatus] = mapped_column(
        SqlEnum(PaymentStatus),
        default=PaymentStatus.PENDING,
        nullable=False,
    )

    payment_reference: Mapped[str | None] = mapped_column(
        String(255),
        nullable=True,
    )

    payment_date: Mapped[datetime | None] = mapped_column(
        DateTime(timezone=True),
        nullable=True,
    )

    currency: Mapped[Currency] = mapped_column(
        SqlEnum(Currency),
        default=Currency.INR,
        nullable=False,
    )

    user = relationship("User")
    items = relationship(
        "OrderItem",
        back_populates="order",
        cascade="all, delete-orphan",
    )
```

---

# File 2: `backend/app/modules/orders/models/order_item.py`

Your database requires three additional columns that are currently missing.

### Update imports

```python
from sqlalchemy import (
    ForeignKey,
    Integer,
    Numeric,
    String,
)
```

---

### Add the missing fields

After `unit_price`, add:

```python
subtotal: Mapped[float] = mapped_column(
    Numeric(10, 2),
    nullable=False,
)

product_name: Mapped[str] = mapped_column(
    String(255),
    nullable=False,
)

product_sku: Mapped[str] = mapped_column(
    String(255),
    nullable=False,
)
```

The completed entity should be:

```python
class OrderItem(Base):
    __tablename__ = "order_items"

    id = ...

    order_id = ...

    product_id = ...

    quantity = ...

    unit_price = ...

    subtotal = ...

    product_name = ...

    product_sku = ...

    order = relationship(
        "Order",
        back_populates="items",
    )

    product = relationship("Product")
```

---

# Do NOT modify yet

Leave these files unchanged for now:

- `order_repository.py`
- `order_service.py`
- `seed_database_2.py`
- `order_router.py`
- Pydantic request/response schemas

Those changes belong in the next sprint.

---

# Verification

After updating the models, run:

```bash
docker compose exec backend python -m compileall app/modules/orders
```

If there are no syntax errors, proceed with:

```bash
docker compose restart backend
```

Then test that the backend starts cleanly:

```bash
docker compose logs backend --tail=100
```

We are **not** expecting order creation to succeed yet—that will be addressed in the next phase—but the application should import the updated models without errors.

## Next Sprint (Business Logic)

Once the models are in place, we'll implement:

1. `generate_order_number()`
2. Default payment values
3. Currency assignment
4. `subtotal` calculation
5. `product_name` snapshot
6. `product_sku` snapshot
7. Repository cleanup (`Session` consistency)
8. Seed script update

This staged approach keeps the refactor safe, testable, and aligned with the database schema.
