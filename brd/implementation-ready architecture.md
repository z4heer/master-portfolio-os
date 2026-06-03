Great. Now let's move into the **implementation-ready architecture artifacts**. These are the documents a Solution Architect would hand to the development team before coding starts.

# Phase 1: Detailed ERD (Enterprise Version)

## Core Entities

```text
Users
│
├── Orders
│     │
│     └── OrderItems
│               │
│               └── Products
│
├── Cart
│     │
│     └── CartItems
│
└── Addresses

Products
│
├── Categories
├── Inventory
├── ProductImages
└── Reviews
```

---

# Detailed ERD

```text
USERS
-----
id (PK)
name
email
password_hash
phone
role
is_active
created_at
updated_at

        1
        │
        │
        ▼

ORDERS
------
id (PK)
user_id (FK)
order_number
status
subtotal
tax
shipping_charge
total_amount
created_at

        1
        │
        │
        ▼

ORDER_ITEMS
-----------
id (PK)
order_id (FK)
product_id (FK)
quantity
unit_price
total_price

        ▲
        │
        │
        N

PRODUCTS
--------
id (PK)
category_id (FK)
name
sku
description
price
brand
status
created_at

        1
        │
        ▼

INVENTORY
---------
id (PK)
product_id (FK)
available_qty
reserved_qty
reorder_level
updated_at
```

---

# Additional Tables

## Categories

```sql
categories
----------
id
name
description
parent_category_id
```

---

## Cart

```sql
cart
-----
id
user_id
created_at
updated_at
```

---

## Cart Items

```sql
cart_items
----------
id
cart_id
product_id
quantity
```

---

## Product Images

```sql
product_images
--------------
id
product_id
image_url
is_primary
```

---

## Addresses

```sql
addresses
---------
id
user_id
address_line1
address_line2
city
state
country
postal_code
```

---

## Reviews

```sql
reviews
-------
id
user_id
product_id
rating
review_text
created_at
```

---

# Database Index Strategy

## Users

```sql
CREATE UNIQUE INDEX idx_users_email
ON users(email);
```

---

## Products

```sql
CREATE INDEX idx_products_name
ON products(name);

CREATE INDEX idx_products_category
ON products(category_id);
```

---

## Orders

```sql
CREATE INDEX idx_orders_user
ON orders(user_id);

CREATE INDEX idx_orders_status
ON orders(status);
```

---

# PostgreSQL Physical Schema

## Users

```sql
CREATE TABLE users
(
    id UUID PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    phone VARCHAR(20),
    role VARCHAR(20) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## Products

```sql
CREATE TABLE products
(
    id UUID PRIMARY KEY,
    category_id UUID,
    name VARCHAR(200),
    sku VARCHAR(100) UNIQUE,
    description TEXT,
    price NUMERIC(12,2),
    brand VARCHAR(100),
    status VARCHAR(30),
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

---

## Inventory

```sql
CREATE TABLE inventory
(
    id UUID PRIMARY KEY,
    product_id UUID REFERENCES products(id),
    available_qty INTEGER,
    reserved_qty INTEGER,
    reorder_level INTEGER,
    updated_at TIMESTAMP
);
```

---

# OpenAPI Contract

## Authentication

### Register

```yaml
POST /api/v1/auth/register
```

Request

```json
{
  "name":"John Doe",
  "email":"john@test.com",
  "password":"Password123"
}
```

Response

```json
{
  "user_id":"uuid",
  "message":"User registered successfully"
}
```

---

### Login

```yaml
POST /api/v1/auth/login
```

Response

```json
{
  "access_token":"jwt",
  "refresh_token":"jwt",
  "expires_in":3600
}
```

---

# Product APIs

## Get Products

```yaml
GET /api/v1/products
```

Query Parameters

```text
?page=1
&page_size=20
&search=laptop
&category=electronics
&sort=price
```

---

Response

```json
{
  "items":[],
  "page":1,
  "total":100
}
```

---

## Product Details

```yaml
GET /api/v1/products/{id}
```

---

# Cart APIs

## Add To Cart

```yaml
POST /api/v1/cart/items
```

```json
{
  "product_id":"uuid",
  "quantity":2
}
```

---

## View Cart

```yaml
GET /api/v1/cart
```

---

## Remove Item

```yaml
DELETE /api/v1/cart/items/{id}
```

---

# Order APIs

## Create Order

```yaml
POST /api/v1/orders
```

```json
{
  "address_id":"uuid",
  "payment_method":"COD"
}
```

---

## Order History

```yaml
GET /api/v1/orders
```

---

## Order Detail

```yaml
GET /api/v1/orders/{id}
```

---

# Docker Compose Architecture

```text
docker-compose.yml

├── frontend
├── backend
├── postgres
├── redis
└── pgadmin
```

---

# Container Diagram

```text
┌─────────────────────┐
│ Angular Frontend    │
│ Port 4200           │
└──────────┬──────────┘
           │

┌──────────▼──────────┐
│ FastAPI Backend     │
│ Port 8000           │
└───────┬─────┬───────┘
        │     │

        ▼     ▼

┌──────────┐ ┌──────────┐
│Postgres  │ │ Redis    │
│5432      │ │6379      │
└──────────┘ └──────────┘
```

---

# Enterprise FastAPI Structure

```text
backend/

app/

├── api/
│   ├── auth.py
│   ├── products.py
│   ├── cart.py
│   ├── orders.py
│
├── core/
│   ├── config.py
│   ├── security.py
│   ├── database.py
│
├── models/
│
├── schemas/
│
├── repositories/
│
├── services/
│
├── middleware/
│
├── cache/
│
├── tests/
│
└── main.py
```

---

# Enterprise Angular Structure

```text
frontend/

src/app/

├── core/
│
├── shared/
│
├── auth/
│
├── products/
│
├── cart/
│
├── orders/
│
├── admin/
│
├── guards/
│
├── interceptors/
│
└── layouts/
```

---

# Sprint 1 Detailed Task Breakdown

## Week 1 Goal

**Angular + FastAPI + PostgreSQL + Redis running in Docker**

### Backend

| Task             | Estimate |
| ---------------- | -------: |
| FastAPI Setup    |       4h |
| PostgreSQL Setup |       2h |
| SQLAlchemy       |       3h |
| Redis Setup      |       2h |
| Dockerfile       |       2h |
| Docker Compose   |       2h |

### Frontend

| Task          | Estimate |
| ------------- | -------: |
| Angular Setup |       3h |
| Routing       |       2h |
| Material UI   |       3h |
| Shared Layout |       4h |

### DevOps

| Task                 | Estimate |
| -------------------- | -------: |
| GitHub Repo          |       1h |
| Branch Strategy      |       1h |
| CI Pipeline Skeleton |       4h |

---

## Definition of Done (Sprint 1)

✅ Angular loads in browser
✅ FastAPI Swagger available
✅ PostgreSQL connected
✅ Redis connected
✅ Docker Compose works with one command
✅ GitHub repository created
✅ CI pipeline executes successfully

---

### Recommended Next Artifact

The next document should be the **Backend Repository-Service Pattern Design + FastAPI class/module skeletons**, because that becomes the actual coding blueprint for implementation and keeps the project aligned with enterprise architecture practices.
