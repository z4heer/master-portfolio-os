# Implementation Order

We'll implement in the safest dependency order.

## Step 1 (Current)

**Address Model**

This is the foundation.

### Create

```
modules/address/models/address.py
```

### Follow the same conventions as

```
modules/auth/models/user.py

modules/orders/models/order.py

modules/catalog/models/product.py
```

Don't invent a different style.

### Things to implement

* SQLAlchemy model
* UUID PK
* FK → User
* AddressType Enum
* Columns exactly as per specification
* Relationship to User
* timestamps if your Base already supports them

### Verify

* imports work
* model registered
* alembic autogenerate detects table

Don't move ahead until this works.

---

# Step 2

**Payment Model**

After Address compiles.

Create

```
modules/payment/models/payment.py
```

Implement

* PaymentStatus Enum
* FK Order
* Unique constraint on order_id
* Numeric amount
* Provider
* Transaction ID

Again

Verify Alembic detects it.

---

# Step 3

Update

```
database/models.py
```

or whatever central registry you're already using.

Goal

```
Base.metadata.create_all()

knows

User
Product
Category
Order
OrderItem
Address
Payment
```

Run

```
alembic revision --autogenerate
```

Ensure only the expected schema changes appear.

---

# Step 4

Schemas

Don't write services yet.

Complete all request/response schemas first.

Reason:

Everything above the repository depends on them.

---

# Step 5

Repositories

Implement only persistence.

A good self-check:

> If a method contains an `if` that enforces a business rule, it probably belongs in the service, not the repository.

Repositories should mostly perform CRUD and queries.

---

# Step 6

AddressService

This is where business rules belong.

Think in terms of operations rather than SQL.

For example:

```
create_address()
```

should orchestrate:

```
validate

↓

clear default if needed

↓

repository.create()

↓

return response schema
```

The repository shouldn't know *why* a default address changes.

---

# Step 7

PaymentService

Keep it very small.

It should:

```
validate request

↓

invoke sandbox

↓

persist payment

↓

return result
```

Nothing more.

No HTTP calls.

No external SDK.

No commit.

---

# Step 8

OrderService

This is the only existing service that changes.

Avoid rewriting it.

Instead, insert the new steps into the existing checkout flow:

```
validate addresses

↓

create order

↓

payment service

↓

update order

↓

commit
```

Everything else should remain as-is.

---

# Step 9

Routers

Address router first.

Payment router only if required by your API design. If payment is exclusively part of checkout, there's no need for a standalone endpoint in RC1.

---

# Step 10

Tests

Mirror your implementation sequence:

1. Address model
2. Address repository
3. Address service
4. Payment service
5. Order checkout integration

That way, if something fails, you'll know exactly which layer introduced the issue.

---

# Review Checklist

Before moving from one step to the next, ask yourself:

* Does this match the style of the existing modules?
* Did I introduce any business logic into the repository?
* Did I change any existing API contracts unnecessarily?
* Does this compile and pass linting?
* Would this layer be testable in isolation?

If the answer is "yes" to all of those, you're ready to proceed.

I recommend we work **one step at a time**. Complete the **Address Model** first, then share it (or any specific issue you encounter). I'll review it like a pull request before we move to repositories and services. This approach will keep the implementation clean, aligned with your codebase, and avoid rework.

## Current Module Pattern

Every module follows the same convention:

```text
module/
    models/
    repositories/
    routers/
    schemas/
    services/
```

We should preserve this exactly.

---

# Phase 1 — Address Module

Create a new module parallel to `catalog` and `orders`:

```text
modules/
    address/
        __init__.py
        models/
            __init__.py
            address.py
        repositories/
            __init__.py
            address_repository.py
        schemas/
            __init__.py
            address_request.py
            address_response.py
        services/
            __init__.py
            address_service.py
        routers/
            __init__.py
            address_router.py
```

This keeps the project layout uniform.

---

# Phase 2 — Payment Module

Follow the same pattern:

```text
modules/
    payment/
        __init__.py
        models/
            __init__.py
            payment.py
        repositories/
            __init__.py
            payment_repository.py
        schemas/
            __init__.py
            payment_request.py
            payment_response.py
        services/
            __init__.py
            payment_service.py
        routers/
            __init__.py
            payment_router.py   # optional for RC1
```

For RC1, payment can remain internal to checkout, so exposing a router is optional.

---

# Models Location

I noticed something important.

Current models are split:

```text
database/models.py

AND

modules/catalog/models/

AND

modules/orders/models/

AND

modules/auth/models/
```

### Recommendation

Continue storing ORM entities in the module they belong to:

* `modules/address/models/address.py`
* `modules/payment/models/payment.py`

Then register/import them centrally if your `database/base.py` or metadata initialization requires it. This matches your existing organization.

---

# Schemas

You currently use `schemas/` instead of `dto/`.

Keep that convention.

For Address:

```text
address_request.py

AddressCreateRequest

AddressUpdateRequest
```

```text
address_response.py

AddressResponse
AddressListResponse
```

For Payment:

```text
payment_request.py

PaymentRequest
```

```text
payment_response.py

PaymentResponse
```

This stays consistent with `auth`, `catalog`, and `orders`.

---

# Repository Layer

Keep repositories intentionally thin.

For `AddressRepository`, I expect methods like:

```python
create()
update()
delete()
get_by_id()
get_by_user()
get_default()
clear_default()
```

For `PaymentRepository`:

```python
create()
get_by_order()
get_by_transaction()
update_status()
```

No business logic belongs here.

---

# Service Layer

This is where the real work happens.

## AddressService

Responsibilities:

* Validate address ownership.
* Enforce one default shipping address.
* Enforce one default billing address.
* CRUD operations.
* Validate address type.

## PaymentService

Responsibilities:

* Validate payment request.
* Call the sandbox provider.
* Create a payment record.
* Return a payment result.
* Never commit the transaction itself.

A key design point: **repositories should not call `commit()`**. Let the service layer coordinate transactions, or have the API layer commit once the business operation succeeds. This preserves atomicity across orders and payments.

---

# OrderService

This is the only existing service that needs modification.

The flow should become:

```text
Validate User
        │
        ▼
Validate Products
        │
        ▼
Reserve Inventory
        │
        ▼
Validate Shipping Address
        │
        ▼
Validate Billing Address
        │
        ▼
Create Order
        │
        ▼
PaymentService.process()
        │
        ▼
Persist Payment
        │
        ▼
Update Order Status
        │
        ▼
Commit
```

If any step fails:

```text
Rollback

↓

Return Error
```

No partial orders or orphaned payments.

---

# Sandbox Provider

Don't put the sandbox logic directly in `PaymentService`.

Instead:

```text
payment/
    services/
        payment_service.py
        sandbox_provider.py
```

Then:

```python
PaymentService
        │
        ▼
SandboxProvider
```

Later, replacing it with a Stripe or Razorpay provider becomes trivial.

---

# Routers

Address:

```text
POST   /addresses
GET    /addresses
GET    /addresses/{id}
PUT    /addresses/{id}
DELETE /addresses/{id}
PATCH  /addresses/{id}/default
```

Payment:

For RC1, I recommend **no standalone payment endpoints**. Checkout should own the payment flow.

---

# Database Relationships

Without changing your current design, the natural additions are:

```text
User
 └── Address (1:N)

Order
 ├── Shipping Address (N:1)
 ├── Billing Address (N:1)
 └── Payment (1:1)
```

This keeps the model normalized and easy to extend.

---

# Migration Strategy

Avoid one large migration.

Instead:

1. Create `addresses` table.
2. Create `payments` table.
3. Add foreign keys from `orders` to `addresses`.
4. Add payment reference fields to `orders` only if they aren't already present.

Smaller migrations are easier to review and rollback.

---

# Testing Strategy

Mirror your existing module layout:

```text
tests/
    unit/
        address/
        payment/
        orders/
```

Focus on business behavior:

* Address ownership
* Default address switching
* Payment success/failure
* Checkout rollback
* Atomic transaction integrity

---

## One Architectural Suggestion

I also noticed your project uses `schemas` instead of `dto`, and your modules are highly consistent. I'd keep that consistency everywhere. The only addition I'd make is a small shared abstraction under `modules/payment/services/`:

```text
payment/
    services/
        payment_provider.py    # abstract interface
        sandbox_provider.py
        payment_service.py
```

`PaymentService` depends on `PaymentProvider`, and `SandboxProvider` is the initial implementation. This adds only two small files, keeps the design clean, and gives you a production-ready extension point for future gateways without altering `OrderService`.

I think this review-driven workflow will be much more efficient. You implement each piece, and I review it for architecture, correctness, edge cases, and enterprise readiness before you commit. That should significantly reduce back-and-forth while keeping the codebase aligned with the project's established patterns.
