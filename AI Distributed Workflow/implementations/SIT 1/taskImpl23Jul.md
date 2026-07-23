Understood. I'll treat this as **RC1 Release Stabilization** work with the architecture frozen and focus only on frontend/backend compatibility, integration, and release readiness.

# RC1 Execution Strategy

Given your current status:

* ✅ Backend stabilized
* ✅ API contracts frozen
* ✅ Backend quality gates passing
* ✅ Angular build passing
* ✅ Angular tests passing
* ⚠️ Only integration validation and final frontend polish remain

The safest approach is to validate each vertical end-to-end rather than making broad changes.

---

# Phase 1 — Product Module Integration

## 1. Verify Product DTO

Confirm frontend model matches backend response exactly.

Example fields should include:

```ts
export interface Product {
  id: number;
  name: string;
  description: string;
  price: number;
  stock_quantity: number;
  category_id?: number;
  category_name?: string;
  image_url?: string;
  is_active: boolean;
}
```

Look specifically for:

* renamed properties
* nullable image_url
* optional category_name
* numeric price

No `any`.

---

## 2. Product Service

Verify every endpoint still matches backend.

Typical methods:

```
GET /products

GET /products/{id}

POST /products

PUT /products/{id}

DELETE /products/{id}
```

Check query parameters

```
search
category
page
size
```

Ensure HttpParams still align with backend.

---

## 3. Product List

Validate

✔ image rendering

```
<img
 [src]="product.image_url || placeholder"
 loading="lazy">
```

Fallback should not throw if

```
null
undefined
''
```

---

Verify:

Search

Category filter

Pagination

Inventory badge

Price

Image

Loading state

Empty state

---

## 4. Product Details

Verify:

Large image

Stock

Category

Price

Description

Image fallback

No template errors.

---

# Phase 2 — Checkout Integration

Backend now supports

```
shipping_address

currency

payment_method

payment_status

total_amount
```

Frontend checkout payload should resemble

```ts
{
  shipping_address,
  payment_method,
  items
}
```

Do NOT calculate

```
total_amount
```

on frontend if backend already owns it.

Backend should remain source of truth.

---

Verify form

Required shipping address

Validation

Error messages

Submit

Success page

Cart clearing

---

# Phase 3 — Orders Integration

Most important verification.

Backend now returns richer DTOs.

Verify interface.

Example

```ts
export interface Order {

  id: number;

  order_number: string;

  status: string;

  payment_status: string;

 payment_method: string;

  currency: string;

  total_amount: number;

  shipping_address: string;

  created_at: string;

  items: OrderItem[];
}
```

OrderItem

```
unit_price

subtotal

product_name

product_sku
```

should all exist.

---

Verify

Order list

Order details

Order history

Status chip

Payment chip

Currency

Address

Snapshot pricing

Totals

---

# Phase 4 — Admin

Validate

Create Product

Edit Product

Delete Product

Image URL

Inventory

Category

---

Orders

Update status

Refresh list

Detail page

No stale UI.

---

# Phase 5 — Dashboard

This is likely the only remaining visible RC1 polish.

Replace placeholder values only.

Candidates include

```
Total Products

Orders

Revenue

Inventory

Recent Orders
```

If backend APIs already exist, bind dashboard to them.

Do NOT invent aggregation endpoints.

If no API exists already,

leave placeholders rather than changing backend architecture.

---

# Phase 6 — DTO Compatibility Audit

Quick pass through

```
models/

services/

components/
```

Look for

```
property renamed

removed field

nullable mismatch

number vs string

optional fields
```

Most RC failures come from

```
undefined.image_url

undefined.total_amount

undefined.order_number
```

These are quick fixes.

---

# Phase 7 — Error Handling

Verify every HTTP call

```
loading

success

error
```

states.

No console errors.

No template exceptions.

---

# Phase 8 — Manual SIT

Execute

Customer

* Login
* Browse Products
* Search
* Filter
* View Product
* Add to Cart
* Checkout
* View Orders
* Logout

Admin

* Login
* Products
* Create
* Edit
* Delete
* Orders
* Update Status
* Dashboard

---

# Phase 9 — Final Quality Gates

Backend

```bash
ruff check .

black --check .

mypy app

pytest
```

Frontend

```bash
ng build

ng test --watch=false

ng lint
```

Treat remaining lint findings as acceptable only if they are limited to the known non-blocking rules:

* `@typescript-eslint/no-explicit-any`
* `@angular-eslint/no-input-rename`

Any new lint errors should be investigated before RC1 sign-off.

---

# RC1 Exit Criteria

Release candidate is ready when all of the following are true:

| Area                       | Status                     |
| -------------------------- | -------------------------- |
| Backend Quality Gates      | ✅ PASS                     |
| Frontend Build             | ✅ PASS                     |
| Frontend Unit Tests        | ✅ PASS                     |
| Backend Unit Tests         | ✅ PASS                     |
| Product Integration        | ✅ Verified                 |
| Checkout Integration       | ✅ Verified                 |
| Orders Integration         | ✅ Verified                 |
| Admin Integration          | ✅ Verified                 |
| Dashboard Live Data        | ✅ Verified where supported |
| DTO Compatibility          | ✅ Verified                 |
| No Console Errors          | ✅                          |
| No Runtime Template Errors | ✅                          |
| Postman SIT                | ✅ PASS                     |
| Backward Compatibility     | ✅ Preserved                |

## Immediate Next Step

The next work item should be **Product Module Integration**, as it validates the API contract that other user-facing flows depend on.

Please run the Product module through manual integration (list, search, category filter, details, image rendering, inventory, and admin CRUD). If you encounter any runtime issue, compiler error, or API response mismatch, share the relevant component, service, model, or error output, and we'll fix it incrementally while keeping the RC1 architecture unchanged.
