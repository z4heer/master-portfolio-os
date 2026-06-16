For a **Junior Developer**, I would break this into small, reviewable tasks with clear acceptance criteria. The goal is to get a working implementation first, not a highly optimized one.

---

# Sprint Goal

Implement:

```text
Cart → Create Order

Validate Inventory
↓
Reserve Inventory
↓
Create Order
↓
Create Order Items
↓
Return Order Details
```

Requirements:

* JWT Authentication
* CUSTOMER role required
* Inventory validation
* Atomic transaction
* Order & OrderItem persistence
* Alembic migration

---

# Task 1: Create Order Status Enum

## File

```text
app/core/enums.py
```

## Implementation

```python
from enum import Enum


class OrderStatus(str, Enum):
    PENDING = "PENDING"
    COMPLETED = "COMPLETED"
    CANCELLED = "CANCELLED"
```

---

## Acceptance Criteria

* Enum imports successfully.
* No circular imports.

---

# Task 2: Create Order Model

## File

```text
app/models/order.py
```

## Implementation

```python
import uuid

from datetime import datetime
from decimal import Decimal

from sqlalchemy import (
    Enum,
    ForeignKey,
    Numeric,
    DateTime,
    CheckConstraint,
)

from sqlalchemy.dialects.postgresql import UUID

from sqlalchemy.orm import (
    Mapped,
    mapped_column,
    relationship,
)

from app.db.base import Base
from app.core.enums import OrderStatus
```

---

## Order Entity

```python
class Order(Base):
    __tablename__ = "orders"

    __table_args__ = (
        CheckConstraint(
            "total_amount >= 0",
            name="ck_order_total_amount_positive",
        ),
    )

    id: Mapped[uuid.UUID] = mapped_column(
        UUID(as_uuid=True),
        primary_key=True,
        default=uuid.uuid4,
    )

    user_id: Mapped[uuid.UUID] = mapped_column(
        UUID(as_uuid=True),
        ForeignKey("users.id"),
        nullable=False,
        index=True,
    )

    total_amount: Mapped[Decimal] = mapped_column(
        Numeric(12, 2),
        nullable=False,
        default=0,
    )

    status: Mapped[OrderStatus] = mapped_column(
        Enum(
            OrderStatus,
            name="order_status_enum",
        ),
        nullable=False,
        default=OrderStatus.PENDING,
    )

    created_at: Mapped[datetime] = mapped_column(
        DateTime(timezone=True),
        nullable=False,
    )

    items = relationship(
        "OrderItem",
        back_populates="order",
        cascade="all, delete-orphan",
    )
```

---

## Acceptance Criteria

* Table name is `orders`.
* UUID primary key.
* Status stored as PostgreSQL enum.
* Constraint prevents negative totals.

---

# Task 3: Create OrderItem Model

Continue in:

```text
app/models/order.py
```

---

## Implementation

```python
class OrderItem(Base):
    __tablename__ = "order_items"

    __table_args__ = (
        CheckConstraint(
            "quantity > 0",
            name="ck_order_item_quantity_positive",
        ),
    )

    id: Mapped[uuid.UUID] = mapped_column(
        UUID(as_uuid=True),
        primary_key=True,
        default=uuid.uuid4,
    )

    order_id: Mapped[uuid.UUID] = mapped_column(
        UUID(as_uuid=True),
        ForeignKey(
            "orders.id",
            ondelete="CASCADE",
        ),
        nullable=False,
        index=True,
    )

    product_id: Mapped[uuid.UUID] = mapped_column(
        UUID(as_uuid=True),
        ForeignKey("products.id"),
        nullable=False,
        index=True,
    )

    quantity: Mapped[int] = mapped_column(
        nullable=False
    )

    unit_price: Mapped[Decimal] = mapped_column(
        Numeric(12, 2),
        nullable=False,
    )

    order = relationship(
        "Order",
        back_populates="items",
    )
```

---

## Acceptance Criteria

* Quantity > 0.
* Unit price stored permanently.
* Cascade delete enabled.

---

# Task 4: Register Models

Depending on your project structure.

Example:

```python
from app.models.order import (
    Order,
    OrderItem,
)
```

Ensure Alembic can discover them.

---

# Task 5: Create Pydantic Schemas

## File

```text
app/schemas/order.py
```

---

## Request DTO

```python
from uuid import UUID

from pydantic import BaseModel
from pydantic import Field
```

```python
class OrderItemCreate(BaseModel):
    product_id: UUID
    quantity: int = Field(gt=0)
```

```python
class OrderCreate(BaseModel):
    items: list[OrderItemCreate]
```

---

## Response DTO

```python
from decimal import Decimal
from datetime import datetime
```

```python
class OrderItemResponse(BaseModel):
    product_id: UUID
    quantity: int
    unit_price: Decimal

    model_config = {
        "from_attributes": True
    }
```

```python
class OrderResponse(BaseModel):
    id: UUID
    user_id: UUID
    total_amount: Decimal
    status: str
    created_at: datetime

    items: list[OrderItemResponse]

    model_config = {
        "from_attributes": True
    }
```

---

## Acceptance Criteria

* Validation rejects quantity <= 0.
* Response serializes ORM objects.

---

# Task 6: Inventory Repository

## File

```text
app/repositories/inventory_repository.py
```

---

## Implementation

```python
from uuid import UUID

from sqlalchemy import select
from sqlalchemy.ext.asyncio import AsyncSession

from app.models.inventory import Inventory
```

```python
class InventoryRepository:

    def __init__(
        self,
        db: AsyncSession,
    ):
        self.db = db
```

---

## Get Inventory With Lock

```python
async def get_for_update(
    self,
    product_id: UUID,
):
    stmt = (
        select(Inventory)
        .where(
            Inventory.product_id == product_id
        )
        .with_for_update()
    )

    result = await self.db.execute(stmt)

    return result.scalar_one_or_none()
```

---

## Acceptance Criteria

* Uses FOR UPDATE.
* Returns Inventory entity.

---

# Task 7: Order Repository

## File

```text
app/repositories/order_repository.py
```

---

## Implementation

```python
from sqlalchemy.ext.asyncio import AsyncSession

from app.models.order import (
    Order,
    OrderItem,
)
```

```python
class OrderRepository:

    def __init__(
        self,
        db: AsyncSession,
    ):
        self.db = db
```

---

## Create Order

```python
async def create_order(
    self,
    order: Order,
):
    self.db.add(order)

    await self.db.flush()

    return order
```

---

## Create Item

```python
async def create_order_item(
    self,
    item: OrderItem,
):
    self.db.add(item)

    await self.db.flush()

    return item
```

---

## Acceptance Criteria

* Records persist.
* Order ID generated before items.

---

# Task 8: Inventory Service

## File

```text
app/services/inventory_service.py
```

---

## Implementation

```python
from fastapi import HTTPException
from starlette import status
```

```python
class InventoryService:

    def __init__(
        self,
        inventory_repo,
    ):
        self.inventory_repo = inventory_repo
```

---

## Validation Method

```python
async def validate_and_reserve(
    self,
    items,
):
```

Loop through items:

```python
for item in items:

    inventory = (
        await self.inventory_repo.get_for_update(
            item.product_id
        )
    )
```

Inventory missing:

```python
if not inventory:
    raise HTTPException(
        status_code=404,
        detail="Inventory not found",
    )
```

Stock insufficient:

```python
if inventory.stock_quantity < item.quantity:
    raise HTTPException(
        status_code=400,
        detail="Insufficient stock",
    )
```

Reserve stock:

```python
inventory.stock_quantity -= item.quantity
```

---

## Acceptance Criteria

* Missing inventory → 404.
* Insufficient stock → 400.
* Inventory decremented.

---

# Task 9: Order Service

## File

```text
app/services/order_service.py
```

---

Dependencies:

```python
OrderRepository
InventoryService
ProductRepository
```

---

## Create Order Method

```python
async def create_order(
    self,
    user_id,
    payload,
):
```

---

Start transaction:

```python
async with self.db.begin():
```

---

Validate stock:

```python
await self.inventory_service.validate_and_reserve(
    payload.items
)
```

---

Create order:

```python
order = Order(
    user_id=user_id,
    total_amount=0,
    status=OrderStatus.PENDING,
    created_at=datetime.now(UTC),
)
```

---

Persist order:

```python
await self.order_repo.create_order(order)
```

---

Loop items:

```python
total_amount = Decimal("0.00")
```

```python
for item in payload.items:
```

Fetch product:

```python
product = await self.product_repo.get_by_id(
    item.product_id
)
```

Create item:

```python
order_item = OrderItem(
    order_id=order.id,
    product_id=product.id,
    quantity=item.quantity,
    unit_price=product.price,
)
```

Persist:

```python
await self.order_repo.create_order_item(
    order_item
)
```

Update total:

```python
total_amount += (
    product.price * item.quantity
)
```

---

Update order:

```python
order.total_amount = total_amount
```

```python
await self.db.flush()
```

```python
await self.db.refresh(order)
```

Return:

```python
return order
```

---

## Acceptance Criteria

* Entire flow runs in one transaction.
* Inventory updated.
* Order saved.
* OrderItems saved.
* Total calculated correctly.

---

# Task 10: RBAC Enforcement

Verify Product endpoints.

---

## Create Product

```python
@router.post(
    "",
    dependencies=[
        Depends(get_current_admin)
    ]
)
```

---

## Update Product

```python
@router.put(
    "/{product_id}",
    dependencies=[
        Depends(get_current_admin)
    ]
)
```

---

## Acceptance Criteria

CUSTOMER:

```text
403 Forbidden
```

ADMIN:

```text
200 / 201
```

---

# Task 11: Orders API

## File

```text
app/api/v1/orders.py
```

---

Endpoint

```python
POST /api/v1/orders
```

---

Request

```json
{
  "items": [
    {
      "product_id": "uuid",
      "quantity": 2
    },
    {
      "product_id": "uuid",
      "quantity": 1
    }
  ]
}
```

---

Implementation

```python
@router.post(
    "",
    response_model=OrderResponse,
    status_code=201,
)
async def create_order(
    payload: OrderCreate,
    db: AsyncSession = Depends(get_db),
    current_user=Depends(
        get_current_customer
    ),
):
```

Instantiate service.

Call:

```python
return await service.create_order(
    user_id=current_user.id,
    payload=payload,
)
```

---

## Acceptance Criteria

Unauthenticated:

```text
401
```

Wrong role:

```text
403
```

Valid request:

```text
201
```

Returns order details.

---

# Task 12: Alembic Migration

Generate:

```bash
alembic revision --autogenerate -m "create orders tables"
```

Review generated migration manually.

Verify:

```text
orders
order_items
order_status_enum
indexes
constraints
foreign keys
```

Apply:

```bash
alembic upgrade head
```

---

# Definition of Done (for Architect Review)

The Solution Architect should verify:

### Functional

* [ ] Customer can create order
* [ ] Stock decreases correctly
* [ ] Order total is correct
* [ ] Unit price snapshot stored
* [ ] Transaction rollback works

### Security

* [ ] JWT required
* [ ] CUSTOMER can create orders
* [ ] CUSTOMER cannot create products
* [ ] ADMIN can create/update products

### Database

* [ ] Orders table created
* [ ] OrderItems table created
* [ ] Enum created
* [ ] Constraints present
* [ ] Foreign keys valid

### Testing

* [ ] Successful order creation
* [ ] Missing inventory
* [ ] Insufficient stock
* [ ] Invalid quantity
* [ ] Unauthorized request
* [ ] Forbidden role access

This gives the junior developer a clear implementation path while leaving enough checkpoints for a Solution Architect review before moving to payments, carts, and checkout workflows.
##
# Integration Testing Instructions (Developer Self-Test Before Review)

Before requesting a Solution Architect review, the developer should complete a structured integration testing cycle against a running environment.

---

# Objective

Validate that the entire order creation workflow works correctly across:

```text
API Layer
↓
Authentication Layer
↓
Service Layer
↓
Repository Layer
↓
PostgreSQL
```

The goal is to prove that:

* Authentication works
* Authorization works
* Order creation works
* Inventory validation works
* Transactions work correctly
* Database records are created correctly

---

# Environment Setup

## Start Services

```bash
docker compose up -d
```

Verify:

```bash
docker ps
```

Expected:

```text
postgres
redis
backend-api
```

---

## Apply Migrations

```bash
alembic upgrade head
```

Verify:

```sql
\d orders
\d order_items
```

Tables should exist.

---

# Test Data Preparation

Create:

## Admin User

```text
Role: ADMIN
```

---

## Customer User

```text
Role: CUSTOMER
```

---

## Product A

```text
Name: Laptop
Price: 1000.00
```

Inventory:

```text
Stock: 10
```

---

## Product B

```text
Name: Mouse
Price: 50.00
```

Inventory:

```text
Stock: 5
```

---

# Test Case 1 — Customer Login

## Objective

Verify JWT generation.

---

Request

```http
POST /api/v1/auth/login
```

Body

```json
{
  "email": "customer@test.com",
  "password": "Password123"
}
```

---

Expected

```http
200 OK
```

Response

```json
{
  "access_token": "...",
  "token_type": "bearer"
}
```

Save token.

---

# Test Case 2 — Create Order Successfully

## Objective

Verify complete happy path.

---

Request

```http
POST /api/v1/orders
```

Headers

```text
Authorization: Bearer <customer_token>
```

Body

```json
{
  "items": [
    {
      "product_id": "PRODUCT_A_UUID",
      "quantity": 2
    },
    {
      "product_id": "PRODUCT_B_UUID",
      "quantity": 1
    }
  ]
}
```

---

Expected

```http
201 Created
```

Response

```json
{
  "id": "...",
  "user_id": "...",
  "status": "PENDING",
  "total_amount": "2050.00",
  "items": [...]
}
```

Calculation:

```text
Laptop = 1000 x 2
Mouse = 50 x 1

Total = 2050
```

---

# Database Verification

Run:

```sql
SELECT *
FROM orders;
```

Expected:

```text
1 row
```

---

Verify:

```sql
SELECT *
FROM order_items;
```

Expected:

```text
2 rows
```

---

# Inventory Verification

Before:

```text
Laptop = 10
Mouse = 5
```

After:

```text
Laptop = 8
Mouse = 4
```

Verify:

```sql
SELECT *
FROM inventory;
```

---

# Test Case 3 — Missing JWT

## Objective

Verify authentication enforcement.

---

Request

```http
POST /api/v1/orders
```

No token.

---

Expected

```http
401 Unauthorized
```

---

# Test Case 4 — ADMIN Cannot Create Orders

## Objective

Verify RBAC.

Login as ADMIN.

---

Request

```http
POST /api/v1/orders
```

Using ADMIN token.

---

Expected

```http
403 Forbidden
```

---

# Test Case 5 — CUSTOMER Cannot Create Product

## Objective

Verify RBAC protection.

---

Request

```http
POST /api/v1/products
```

Using CUSTOMER token.

---

Expected

```http
403 Forbidden
```

---

# Test Case 6 — CUSTOMER Cannot Update Product

Request

```http
PUT /api/v1/products/{id}
```

Using CUSTOMER token.

---

Expected

```http
403 Forbidden
```

---

# Test Case 7 — Invalid Quantity

## Objective

Verify schema validation.

---

Request

```json
{
  "items": [
    {
      "product_id": "UUID",
      "quantity": 0
    }
  ]
}
```

---

Expected

```http
422 Unprocessable Entity
```

---

# Test Case 8 — Inventory Not Found

## Objective

Verify inventory validation.

Use a product with no inventory record.

---

Expected

```http
404 Not Found
```

Example:

```json
{
  "detail": "Inventory not found"
}
```

---

# Test Case 9 — Insufficient Stock

Inventory:

```text
Mouse = 4
```

Request:

```json
{
  "items": [
    {
      "product_id": "MOUSE_UUID",
      "quantity": 50
    }
  ]
}
```

---

Expected

```http
400 Bad Request
```

Response:

```json
{
  "detail": "Insufficient stock"
}
```

---

# Test Case 10 — Transaction Rollback

## Objective

Verify atomicity.

---

Inventory:

```text
Laptop = 8
Mouse = 4
```

Request:

```json
{
  "items": [
    {
      "product_id": "LAPTOP_UUID",
      "quantity": 2
    },
    {
      "product_id": "MOUSE_UUID",
      "quantity": 999
    }
  ]
}
```

---

Expected

```http
400 Bad Request
```

---

Verify Inventory

Laptop should remain:

```text
8
```

NOT:

```text
6
```

---

Verify Orders

No new order created.

```sql
SELECT count(*)
FROM orders;
```

Count should remain unchanged.

---

# Test Case 11 — Price Snapshot Validation

## Objective

Verify historical pricing.

---

Step 1

Create order.

```text
Laptop Price = 1000
```

Order stores:

```text
unit_price = 1000
```

---

Step 2

Update product.

```text
Laptop Price = 1500
```

---

Verify

```sql
SELECT unit_price
FROM order_items;
```

Expected:

```text
1000
```

not

```text
1500
```

---

# Test Case 12 — Multiple Orders

Create:

```text
Order 1
Order 2
Order 3
```

Verify:

```sql
SELECT *
FROM orders;
```

Expected:

```text
3 rows
```

No duplicate IDs.

---

# Developer Review Deliverables

Before handing over to the Solution Architect, the developer should provide:

## 1. API Test Evidence

Export:

```text
Postman Collection
or
Bruno Collection
or
Insomnia Collection
```

Containing all test cases.

---

## 2. Screenshots

Include:

```text
Successful order creation
Inventory update
Unauthorized access
Forbidden access
Rollback test
```

---

## 3. SQL Evidence

Attach results of:

```sql
SELECT * FROM orders;
SELECT * FROM order_items;
SELECT * FROM inventory;
```

After testing.

---

## 4. Known Issues Log

Create:

```text
docs/testing/order-module-known-issues.md
```

Template:

```markdown
# Known Issues

## Issue 1
Description:
Status:

## Issue 2
Description:
Status:
```

Even if empty:

```markdown
No known issues identified.
```

---

# Exit Criteria for Solution Architect Review

The module is ready for review only when:

| Check                      | Status |
| -------------------------- | ------ |
| Migration works            | ✅      |
| Order creation works       | ✅      |
| Inventory validation works | ✅      |
| Stock decrement works      | ✅      |
| Rollback works             | ✅      |
| JWT works                  | ✅      |
| RBAC works                 | ✅      |
| Price snapshot works       | ✅      |
| No critical defects        | ✅      |

If any of the above fail, the developer should fix and retest before scheduling the architecture review. This avoids spending review time on issues that should have been caught during integration testing.

##
# Mandatory Fixes Before Solution Architect Review

These are the **minimum required fixes/checks** before the module should be submitted for review.

---

# 1. Validate Product Exists Before Creating Order Items

## Problem

Current code assumes the product exists:

```python
product = await self.product_repo.get_by_id(
    item.product_id
)

line_total = product.price * item.quantity
```

If the product does not exist:

```python
product = None
```

then:

```python
product.price
```

raises:

```python
AttributeError
```

resulting in a **500 Internal Server Error** instead of a business validation error.

---

## Fix

### File

```text
app/services/order_service.py
```

Inside the order creation loop:

```python
for item in payload.items:

    product = await self.product_repo.get_by_id(
        item.product_id
    )

    if not product:
        raise HTTPException(
            status_code=404,
            detail=f"Product {item.product_id} not found",
        )

    line_total = (
        product.price * item.quantity
    )

    total_amount += line_total

    order_item = OrderItem(
        order_id=order.id,
        product_id=product.id,
        quantity=item.quantity,
        unit_price=product.price,
    )

    await self.order_repo.create_order_item(
        order_item
    )
```

---

## Acceptance Test

Request:

```json
{
  "items": [
    {
      "product_id": "non-existent-uuid",
      "quantity": 1
    }
  ]
}
```

Expected:

```http
404 Not Found
```

Response:

```json
{
  "detail": "Product <uuid> not found"
}
```

---

# 2. Ensure Order Response Returns Order Items Correctly

## Problem

Current endpoint returns:

```python
return order
```

After:

```python
await self.db.refresh(order)
```

the relationship:

```python
order.items
```

may not be loaded.

This can cause:

* Empty item list
* Serialization errors
* Async lazy-loading errors (`MissingGreenlet`)

---

## Fix

### Repository Method

### File

```text
app/repositories/order_repository.py
```

Add:

```python
from uuid import UUID

from sqlalchemy import select
from sqlalchemy.orm import selectinload

from app.models.order import Order
```

```python
async def get_by_id(
    self,
    order_id: UUID,
) -> Order | None:

    stmt = (
        select(Order)
        .options(
            selectinload(Order.items)
        )
        .where(
            Order.id == order_id
        )
    )

    result = await self.db.execute(stmt)

    return result.scalar_one_or_none()
```

---

### Update Service

### File

```text
app/services/order_service.py
```

Replace:

```python
await self.db.flush()

await self.db.refresh(order)

return order
```

With:

```python
await self.db.flush()

return await self.order_repo.get_by_id(
    order.id
)
```

---

## Acceptance Test

Create order:

```json
{
  "items": [
    {
      "product_id": "PRODUCT_UUID",
      "quantity": 2
    }
  ]
}
```

Expected response:

```json
{
  "id": "...",
  "user_id": "...",
  "total_amount": "2000.00",
  "status": "PENDING",
  "items": [
    {
      "product_id": "...",
      "quantity": 2,
      "unit_price": "1000.00"
    }
  ]
}
```

Verify:

```text
items array is populated
```

---

# 3. Verify Repositories Do Not Control Transactions

## Problem

Transaction ownership must remain in the Service Layer.

Current design correctly uses:

```python
async with self.db.begin():
```

inside:

```python
OrderService
```

If a repository contains:

```python
await self.db.commit()
```

then:

* Partial commits become possible
* Rollback testing becomes unreliable
* Atomic order creation is broken

---

## Required Verification

Review all repositories:

```text
app/repositories/order_repository.py
app/repositories/inventory_repository.py
app/repositories/product_repository.py
```

Search for:

```python
commit(
rollback(
```

---

## Expected Result

Repositories may contain:

```python
self.db.add(...)
await self.db.flush()
await self.db.execute(...)
```

Repositories must NOT contain:

```python
await self.db.commit()
await self.db.rollback()
```

---

## Acceptance Check

Transaction ownership exists only here:

```python
async with self.db.begin():
```

inside:

```python
OrderService.create_order()
```

---

# 4. Execute Transaction Rollback Integration Test

## Objective

Prove that inventory changes and order creation are rolled back together when one item fails validation.

---

## Setup

Inventory:

```text
Laptop = 10
Mouse = 5
```

---

## Request

```json
{
  "items": [
    {
      "product_id": "LAPTOP_UUID",
      "quantity": 2
    },
    {
      "product_id": "MOUSE_UUID",
      "quantity": 999
    }
  ]
}
```

---

## Expected API Response

```http
400 Bad Request
```

```json
{
  "detail": "Insufficient stock"
}
```

---

## Verify Inventory

Run:

```sql
SELECT product_id, stock_quantity
FROM inventory;
```

Expected:

```text
Laptop = 10
Mouse = 5
```

Not:

```text
Laptop = 8
Mouse = 5
```

---

## Verify Orders

Run:

```sql
SELECT *
FROM orders;
```

Expected:

```text
No new order created
```

---

## Verify Order Items

Run:

```sql
SELECT *
FROM order_items;
```

Expected:

```text
No new order items created
```

---

# Review Readiness Checklist

The module is ready for Solution Architect review only when all four checks below pass:

* [ ] Product existence validation returns `404 Not Found`
* [ ] Order response includes populated `items`
* [ ] No repository performs `commit()` or `rollback()`
* [ ] Rollback integration test proves atomic transaction behavior

Once these are completed and evidenced (API responses + DB verification), the implementation satisfies the sprint requirements and is ready for architectural review.

##
