Excellent. If I were assigning this to a mid-level FastAPI engineer, I would not simply say "build Product Module". I would create a structured implementation plan with deliverables, acceptance criteria, code skeletons, testing requirements, and review checkpoints.

---

# Sprint-3 Product Catalog Implementation Assignment

## Objective

Implement Product Catalog Backend Module using:

```text
FastAPI
SQLAlchemy Async
PostgreSQL
Redis
Alembic
Repository Pattern
Service Layer Pattern
RBAC
```

Module must support:

```text
Product CRUD
Inventory Management
Product Search
Category Filter
Price Filter
Pagination
Redis Caching
Admin RBAC
```

---

# Phase-1 Create Database Models

## Task-1 Product Model

### Create File

```text
backend/app/models/product.py
```

### Implement

```python
import uuid

from sqlalchemy import (
    String,
    Text,
    Numeric,
    Boolean,
    DateTime
)

from sqlalchemy.orm import Mapped
from sqlalchemy.orm import mapped_column
from sqlalchemy.orm import relationship

from sqlalchemy.sql import func
from sqlalchemy.dialects.postgresql import UUID

from app.db.base import Base


class Product(Base):
    __tablename__ = "products"

    id: Mapped[uuid.UUID] = mapped_column(
        UUID(as_uuid=True),
        primary_key=True,
        default=uuid.uuid4
    )

    name: Mapped[str] = mapped_column(
        String(255),
        nullable=False
    )

    description: Mapped[str] = mapped_column(
        Text,
        nullable=True
    )

    category: Mapped[str] = mapped_column(
        String(100),
        nullable=False
    )

    price: Mapped[float] = mapped_column(
        Numeric(10, 2),
        nullable=False
    )

    is_active: Mapped[bool] = mapped_column(
        Boolean,
        default=True
    )

    created_at = mapped_column(
        DateTime(timezone=True),
        server_default=func.now()
    )

    updated_at = mapped_column(
        DateTime(timezone=True),
        server_default=func.now(),
        onupdate=func.now()
    )

    inventory = relationship(
        "Inventory",
        back_populates="product",
        uselist=False
    )
```

### Acceptance Criteria

```text
UUID Primary Key
Soft Delete Enabled
Relationship Created
Alembic Detects Model
```

---

## Task-2 Inventory Model

### Create File

```text
backend/app/models/inventory.py
```

### Implement

```python
import uuid

from sqlalchemy import (
    Integer,
    ForeignKey,
    DateTime
)

from sqlalchemy.orm import (
    Mapped,
    mapped_column,
    relationship
)

from sqlalchemy.sql import func
from sqlalchemy.dialects.postgresql import UUID

from app.db.base import Base


class Inventory(Base):
    __tablename__ = "inventory"

    id: Mapped[uuid.UUID] = mapped_column(
        UUID(as_uuid=True),
        primary_key=True,
        default=uuid.uuid4
    )

    product_id: Mapped[uuid.UUID] = mapped_column(
        UUID(as_uuid=True),
        ForeignKey("products.id"),
        unique=True
    )

    stock_quantity: Mapped[int] = mapped_column(
        Integer,
        default=0
    )

    reserved_quantity: Mapped[int] = mapped_column(
        Integer,
        default=0
    )

    updated_at = mapped_column(
        DateTime(timezone=True),
        server_default=func.now(),
        onupdate=func.now()
    )

    product = relationship(
        "Product",
        back_populates="inventory"
    )
```

---

# Phase-2 Create Schemas

## Task-3 Product Schemas

### Create File

```text
backend/app/schemas/product.py
```

### ProductCreate

```python
from decimal import Decimal

from pydantic import BaseModel
from pydantic import Field


class ProductCreate(BaseModel):

    name: str = Field(
        min_length=2,
        max_length=255
    )

    description: str

    category: str

    price: Decimal

    stock_quantity: int
```

---

### ProductUpdate

```python
class ProductUpdate(BaseModel):

    name: str | None = None

    description: str | None = None

    category: str | None = None

    price: Decimal | None = None
```

---

### ProductResponse

```python
from uuid import UUID

class ProductResponse(BaseModel):

    id: UUID

    name: str

    description: str

    category: str

    price: Decimal

    stock_quantity: int

    class Config:
        from_attributes = True
```

---

# Phase-3 Create Repository Layer

## Task-4 Product Repository

### Create File

```text
backend/app/repositories/product_repository.py
```

### Responsibilities

Only Database Access

### Never

```text
No Redis
No HTTP
No Business Logic
```

---

### Methods

```python
class ProductRepository:

    async def create(
        self,
        db,
        product
    ):
        pass

    async def get_by_id(
        self,
        db,
        product_id
    ):
        pass

    async def get_products(
        self,
        db,
        category,
        min_price,
        max_price,
        search,
        page,
        size
    ):
        pass

    async def update(
        self,
        db,
        product
    ):
        pass

    async def soft_delete(
        self,
        db,
        product
    ):
        pass
```

---

### Filtering Logic

```python
query = select(Product)

query = query.where(
    Product.is_active.is_(True)
)

if category:
    query = query.where(
        Product.category == category
    )

if search:
    query = query.where(
        Product.name.ilike(
            f"%{search}%"
        )
    )

if min_price is not None:
    query = query.where(
        Product.price >= min_price
    )

if max_price is not None:
    query = query.where(
        Product.price <= max_price
    )
```

---

### Pagination

```python
offset = (page - 1) * size

query = query.offset(offset)

query = query.limit(size)
```

---

# Phase-4 Create Inventory Repository

## Task-5 Inventory Repository

### File

```text
backend/app/repositories/inventory_repository.py
```

### Methods

```python
create()

get_by_product_id()

update_stock()
```

Example:

```python
async def get_by_product_id(
    self,
    db,
    product_id
):
    ...
```

---

# Phase-5 Redis Service

## Task-6 Configure Redis

### Create File

```text
backend/app/core/cache.py
```

### Example

```python
from redis.asyncio import Redis

redis_client = Redis(
    host=settings.REDIS_HOST,
    port=settings.REDIS_PORT,
    decode_responses=True
)
```

---

# Phase-6 Product Service

## Task-7 Product Service

### Create File

```text
backend/app/services/product_service.py
```

### Responsibilities

```text
Business Logic
Transactions
Redis
Repository Calls
```

---

## Create Product

```python
async def create_product(
    self,
    db,
    payload
):
```

Flow:

```text
Start Transaction
Create Product
Create Inventory
Commit
Invalidate Cache
Return Product
```

Implementation:

```python
async with db.begin():

    product = Product(...)

    db.add(product)

    await db.flush()

    inventory = Inventory(
        product_id=product.id,
        stock_quantity=payload.stock_quantity
    )

    db.add(inventory)
```

---

## Get Products

Flow:

```text
Check Redis
Hit -> Return

Miss -> DB
Store Redis
Return
```

### Cache Key

```python
all_products
```

### TTL

```python
300
```

---

# Phase-7 Router

## Task-8 Product Router

### File

```text
backend/app/api/product_router.py
```

### Endpoint

```python
router = APIRouter(
    prefix="/products",
    tags=["Products"]
)
```

---

## GET Products

```python
@router.get(
    "",
    response_model=list[ProductResponse]
)
async def get_products(
    category: str | None = None,
    search: str | None = None,
    min_price: float | None = None,
    max_price: float | None = None,
    page: int = 1,
    size: int = 20
):
```

---

## GET Product

```python
@router.get(
    "/{product_id}"
)
async def get_product(
    product_id: UUID
):
```

---

## POST Product

Admin Only

```python
@router.post("")
async def create_product(
    payload: ProductCreate,
    current_user=Depends(
        require_admin
    )
):
```

---

## PUT Product

```python
@router.put("/{product_id}")
```

Admin Only

---

## DELETE Product

```python
@router.delete("/{product_id}")
```

Soft Delete

```python
product.is_active = False
```

---

# Phase-8 Migration

## Task-9 Alembic

Generate:

```bash
alembic revision \
--autogenerate \
-m "create_product_inventory_tables"
```

Review Generated Script:

### Verify

```text
products table

inventory table

foreign key

uuid columns

indexes
```

Apply:

```bash
alembic upgrade head
```

---

# Phase-9 Testing

## Task-10 Swagger Validation

Verify:

```http
GET /docs
```

Expected:

```text
GET products
GET product
POST product
PUT product
DELETE product
```

Visible.

---

## Task-11 Postman Validation

### Create Product

```json
{
  "name": "Dell Laptop",
  "description": "i7 Laptop",
  "category": "Electronics",
  "price": 65000,
  "stock_quantity": 20
}
```

Expected:

```http
201 Created
```

---

### Search

```http
GET /products?search=dell
```

---

### Category

```http
GET /products?category=Electronics
```

---

### Price

```http
GET /products?min_price=10000&max_price=80000
```

---

### Pagination

```http
GET /products?page=1&size=10
```

---

### Soft Delete

```http
DELETE /products/{id}
```

Expected:

```text
is_active=False
```

Record remains in database.

---

# Deliverables Expected In PR

```text
✓ Product Model

✓ Inventory Model

✓ Product Schemas

✓ Product Repository

✓ Inventory Repository

✓ Product Service

✓ Product Router

✓ Redis Integration

✓ Alembic Migration

✓ Swagger Validation

✓ Postman Collection

✓ README Update
```

This is exactly the level of detail I would put into a Jira Epic + Technical Design Task for a mid-level engineer, and it should pass a Solution Architect review because responsibilities, boundaries, transactions, caching, RBAC, and scalability concerns are clearly separated.
##
---
##
Yes. After reviewing the BRD, Technical Specification, System Design, Sequence Diagrams, Sprint Plan, and your current project maturity level, I would make a few adjustments before assigning this to a mid-level engineer.

The goal is not just to make it work, but to ensure the implementation passes Solution Architect review with minimal rework.

---

# Solution Architect Review

## Functional Review

### Current Scope

The Product Catalog module must support:

### Customer

```text
View Products
Search Products
Filter Products
View Product Details
```

### Admin

```text
Create Product
Update Product
Delete Product
Manage Inventory
```

Matches BRD and Sprint-3 requirements.

### Gap Found

Inventory management is mentioned separately in BRD.

Current design embeds inventory creation within product creation.

Recommend:

```http
POST   /products
PUT    /products/{id}

GET    /inventory/{product_id}
PUT    /inventory/{product_id}
```

Even if Inventory API is implemented later, keep InventoryService and InventoryRepository separate from day one.

Reason:

```text
Product = Catalog Data

Inventory = Operational Data
```

Architecturally these are different domains.

---

# Technical Review

## Current Design

```text
Router
 ↓
Service
 ↓
Repository
 ↓
SQLAlchemy
 ↓
Postgres
```

Good.

Keep exactly as is.

---

## Improvement 1

Current repository filtering:

```python
if min_price:
```

Problem:

```python
min_price = 0
```

fails.

Use:

```python
if min_price is not None:
```

Same for:

```python
max_price
category
search
```

---

## Improvement 2

Add Pagination Now

Current API:

```http
GET /products
```

Should become:

```http
GET /products
?page=1
&size=20
&category=laptop
```

Reason:

BRD target:

```text
10,000+ products
```

Without pagination:

```python
SELECT *
```

will eventually fail performance expectations.

Implement now.

---

## Improvement 3

Soft Delete

Instead of:

```python
DELETE FROM products
```

Use:

```python
is_active = False
```

Benefits:

```text
Auditability
Future Order References
Recovery
```

Common Architect review question:

> What happens if an order references a deleted product?

Soft delete solves it.

---

## Improvement 4

Cache Serialization

Current example:

```python
json.dumps(products)
```

will fail.

SQLAlchemy models aren't serializable.

Use:

```python
ProductResponse.model_validate(product)
```

then:

```python
model_dump()
```

then cache.

---

## Improvement 5

Transaction Handling

Product Creation Flow:

```text
Create Product
Create Inventory
Commit
```

must be atomic.

Use:

```python
async with db.begin():
```

Example:

```python
async with db.begin():

    product = ...

    inventory = ...
```

Architect review will check this.

---

# Engineering Task Breakdown

This is how I would assign it to a Mid-Level Engineer.

---

# Task 1

## Create Models

Files:

```text
app/models/product.py

app/models/inventory.py
```

Acceptance Criteria:

```text
UUID PK
Relationships
Created/Updated timestamps
Soft delete flag
```

Deliverable:

```text
Models compile
Metadata detected by Alembic
```

---

# Task 2

## Create Schemas

Files:

```text
app/schemas/product.py
```

Create:

```python
ProductCreate
ProductUpdate
ProductResponse
ProductListResponse
```

Acceptance Criteria:

```text
Swagger renders correctly
Validation works
```

---

# Task 3

## Create Repository Layer

File:

```text
app/repositories/product_repository.py
```

Methods:

```python
create()

update()

get_by_id()

get_products()

soft_delete()
```

Filtering:

```python
category

search

min_price

max_price

page

size
```

Acceptance Criteria:

```text
Repository contains NO business logic
```

Architect review point.

---

# Task 4

## Create Inventory Repository

File:

```text
app/repositories/inventory_repository.py
```

Methods:

```python
create()

update_stock()

get_by_product_id()
```

Keep separate.

---

# Task 5

## Create Product Service

File:

```text
app/services/product_service.py
```

Responsibilities:

```text
Business validation
Caching
Transactions
```

Not SQL.

Not HTTP.

---

# Task 6

## Redis Integration

Implement:

```python
all_products
```

TTL:

```python
300
```

Methods:

```python
get_products()

invalidate_cache()
```

Acceptance Criteria:

```text
Cache hit works
Cache miss works
Cache invalidates after create/update
```

---

# Task 7

## Create Router

File:

```text
app/api/product_router.py
```

Endpoints:

```http
GET /products

GET /products/{id}

POST /products

PUT /products/{id}

DELETE /products/{id}
```

RBAC:

```python
require_admin
```

for:

```text
POST
PUT
DELETE
```

---

# Task 8

## Alembic Migration

Generate:

```bash
alembic revision --autogenerate \
-m "create_product_inventory"
```

Engineer must review generated migration manually.

Architects dislike blindly generated migrations.

Checklist:

```text
UUID columns
FK constraints
Indexes
Defaults
```

---

# Task 9

## Swagger Verification

Validate:

```http
GET /docs
```

Verify:

```text
Schemas visible
Filters visible
Examples visible
```

---

# Task 10

## Postman Testing

### Product Creation

```http
POST /products
```

### Product Search

```http
GET /products?search=laptop
```

### Category Filter

```http
GET /products?category=electronics
```

### Price Filter

```http
GET /products?min_price=1000
```

### Pagination

```http
GET /products?page=1&size=20
```

### Cache

Verify:

```text
1st call -> DB

2nd call -> Redis
```

---

# Solution Architect Review Checklist

Before raising PR:

### Functional

* Product creation works
* Product update works
* Product retrieval works
* Product filtering works
* Product search works
* Inventory created automatically

### Technical

* Repository pattern followed
* Service layer used
* Redis integrated
* Transactions used
* UUID used
* Soft delete used
* Pagination implemented

### Code Quality

* Type hints present
* Async SQLAlchemy used
* No SQL inside router
* No business logic inside repository
* No DB access inside router

### Documentation

* Swagger updated
* Migration committed
* README updated

---

If this were assigned in a real team, I would estimate **2.5–3.5 developer days** for a mid-level FastAPI engineer, including implementation, testing, migration validation, Redis integration, and PR fixes after Solution Architect review.
##
--
##
Excellent idea. In real projects, after development is complete, the developer should follow a **Deployment Readiness Checklist** and the Solution Architect should follow a **Review Checklist**.

This prevents situations where:

```text
✓ Code Compiles
✗ API Fails

✓ Swagger Works
✗ Redis Not Working

✓ Product Created
✗ Inventory Missing

✓ Search Works
✗ Pagination Broken
```

---

# Product Catalog Module - Developer Completion Checklist

---

# Phase 1 - Infrastructure Verification

## PostgreSQL

Verify container

```bash
docker ps
```

Expected:

```text
ecommerce-postgres
```

Verify DB connection:

```bash
docker exec -it ecommerce-postgres psql \
-U ecommerce_user \
-d ecommerce_db
```

Verify tables:

```sql
\dt
```

Expected:

```text
products
inventory
alembic_version
```

---

## Redis

Verify container

```bash
docker ps
```

Expected:

```text
ecommerce-redis
```

Connect

```bash
docker exec -it ecommerce-redis redis-cli
```

Test

```redis
PING
```

Expected

```text
PONG
```

---

# Phase 2 - Database Verification

## Products Table

Verify

```sql
\d products
```

Expected Columns

```text
id
name
description
category
price
is_active
created_at
updated_at
```

---

## Inventory Table

Verify

```sql
\d inventory
```

Expected

```text
id
product_id
stock_quantity
reserved_quantity
updated_at
```

---

## Foreign Key Validation

Verify

```sql
SELECT
constraint_name
FROM information_schema.table_constraints
WHERE table_name='inventory';
```

Expected

```text
product_id -> products.id
```

---

# Phase 3 - Swagger Verification

Open

```text
http://localhost:8000/docs
```

Verify APIs visible

```text
GET /products

GET /products/{id}

POST /products

PUT /products/{id}

DELETE /products/{id}
```

---

# Phase 4 - Test Data Setup

## Product 1

```json
{
  "name":"Dell Inspiron 15",
  "description":"Intel i7 Laptop",
  "category":"Electronics",
  "price":65000,
  "stock_quantity":25
}
```

---

## Product 2

```json
{
  "name":"HP Pavilion",
  "description":"Intel i5 Laptop",
  "category":"Electronics",
  "price":55000,
  "stock_quantity":15
}
```

---

## Product 3

```json
{
  "name":"Nike Running Shoes",
  "description":"Sports Shoes",
  "category":"Footwear",
  "price":4500,
  "stock_quantity":50
}
```

---

## Product 4

```json
{
  "name":"Puma Sneakers",
  "description":"Casual Shoes",
  "category":"Footwear",
  "price":3500,
  "stock_quantity":40
}
```

---

## Product 5

```json
{
  "name":"Samsung Galaxy S25",
  "description":"Android Smartphone",
  "category":"Mobile",
  "price":85000,
  "stock_quantity":10
}
```

---

# Phase 5 - API Verification

---

## Test 1

### Create Product

```http
POST /api/v1/products
```

Expected

```http
201 Created
```

Verify

```sql
SELECT * FROM products;
```

Expected

```text
1 record inserted
```

---

## Test 2

### Inventory Auto Creation

Verify

```sql
SELECT * FROM inventory;
```

Expected

```text
inventory row created automatically
```

Review Question:

```text
When product created,
does inventory row get created?
```

Answer:

```text
Yes
```

---

## Test 3

### Get Products

```http
GET /api/v1/products
```

Expected

```json
[
  ...
]
```

---

## Test 4

### Get Product By Id

```http
GET /api/v1/products/{id}
```

Expected

```json
{
  "id":"..."
}
```

---

## Test 5

### Search

```http
GET /api/v1/products?search=dell
```

Expected

```text
Only Dell Product Returned
```

---

## Test 6

### Category Filter

```http
GET /api/v1/products?category=Electronics
```

Expected

```text
Dell
HP
Samsung
```

Only.

---

## Test 7

### Min Price

```http
GET /api/v1/products?min_price=50000
```

Expected

```text
Products Above 50K
```

---

## Test 8

### Price Range

```http
GET /api/v1/products?
min_price=50000&
max_price=70000
```

Expected

```text
Dell
HP
```

Only.

---

## Test 9

### Pagination

```http
GET /api/v1/products?page=1&size=2
```

Expected

```text
2 Products
```

---

## Test 10

### Pagination Next Page

```http
GET /api/v1/products?page=2&size=2
```

Expected

```text
Next 2 Products
```

---

# Phase 6 - Redis Verification

---

## Before Test

Connect

```bash
redis-cli
```

Check keys

```redis
KEYS *
```

Expected

```text
No product cache initially
```

---

## Cache Miss Test

Call

```http
GET /products
```

Expected

```text
DB Hit
```

Verify

```redis
KEYS *
```

Expected

```text
all_products
```

---

## Cache Hit Test

Call again

```http
GET /products
```

Expected

```text
Redis Hit
```

No DB query.

---

## Cache Invalidation Test

Create Product

```http
POST /products
```

Verify

```redis
KEYS *
```

Expected

```text
all_products removed
```

Next GET should rebuild cache.

---

# Phase 7 - RBAC Verification

---

## Customer User

Token:

```text
ROLE_CUSTOMER
```

Should Allow:

```http
GET /products

GET /products/{id}
```

---

Should Reject

```http
POST /products

PUT /products

DELETE /products
```

Expected

```http
403 Forbidden
```

---

## Admin User

Token:

```text
ROLE_ADMIN
```

Should Allow

```http
POST
PUT
DELETE
GET
```

All APIs.

---

# Phase 8 - Soft Delete Verification

Delete Product

```http
DELETE /products/{id}
```

Verify

```sql
SELECT is_active
FROM products
```

Expected

```text
false
```

Verify

```sql
SELECT *
FROM products
```

Expected

```text
Record still exists
```

---

## Product Listing

```http
GET /products
```

Deleted product should NOT appear.

---

# Phase 9 - Error Scenarios

---

## Invalid Product

```http
GET /products/random-id
```

Expected

```http
404
```

---

## Invalid Price

```json
{
 "price": -100
}
```

Expected

```http
422
```

---

## Empty Name

```json
{
 "name":""
}
```

Expected

```http
422
```

---

# Solution Architect PR Review Checklist

## Architecture

* [ ] Repository Pattern followed
* [ ] Service Layer followed
* [ ] Router contains no business logic
* [ ] Repository contains no Redis logic
* [ ] Service contains business logic

---

## Database

* [ ] UUID primary keys
* [ ] Foreign key exists
* [ ] Migration works
* [ ] Soft delete implemented

---

## API

* [ ] Swagger correct
* [ ] Validation working
* [ ] Pagination working
* [ ] Filtering working

---

## Redis

* [ ] Cache hit working
* [ ] Cache miss working
* [ ] Cache invalidation working

---

## Security

* [ ] Admin RBAC working
* [ ] Customer RBAC working
* [ ] Unauthorized requests rejected

---

## Production Readiness

* [ ] Logging added
* [ ] Type hints added
* [ ] Exception handling added
* [ ] README updated
* [ ] Postman collection committed

### Definition of Done (DoD)

The Product Catalog module is considered complete only when:

```text
✓ Product CRUD Works
✓ Inventory Created Automatically
✓ Search Works
✓ Category Filter Works
✓ Price Filter Works
✓ Pagination Works
✓ Redis Cache Works
✓ RBAC Works
✓ Soft Delete Works
✓ Migration Works
✓ Swagger Works
✓ Postman Tests Pass
✓ Solution Architect Checklist Passes
```

If all boxes are checked, the engineer can confidently raise the PR for Solution Architect review.
