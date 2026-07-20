Excellent. Since this is **Sprint 4.6 - Production Hardening**, we'll implement this exactly like an enterprise engineering team would. We'll make it a permanent part of the project rather than a temporary script.

## Overall Implementation Plan

This work will be completed in **8 implementation steps**.

| Step | Module                        | Status     |
| ---- | ----------------------------- | ---------- |
| 1    | Create Verification Framework | 🔄 Current |
| 2    | Shared HTTP Client            | Pending    |
| 3    | Health Verification           | Pending    |
| 4    | Authentication Verification   | Pending    |
| 5    | Product Verification          | Pending    |
| 6    | Order Verification            | Pending    |
| 7    | Inventory Verification        | Pending    |
| 8    | Reporting & CI Integration    | Pending    |

---

# STEP 1 — Create Verification Framework

## Objective

Create the verification package structure without changing any business logic.

### 1.1 Create Directories

From the backend root:

```bash
mkdir -p app/verification/verifiers
mkdir -p scripts
```

---

### 1.2 Create Files

```text
backend/
│
├── app/
│   └── verification/
│       │
│       ├── __init__.py
│       ├── config.py
│       ├── client.py
│       ├── logger.py
│       ├── models.py
│       ├── report.py
│       ├── runner.py
│       │
│       └── verifiers/
│           ├── __init__.py
│           ├── health.py
│           ├── auth.py
│           ├── products.py
│           ├── orders.py
│           └── inventory.py
│
└── scripts/
    └── verify_backend_apis.py
```

---

### 1.3 Purpose of Each File

| File                     | Responsibility                                 |
| ------------------------ | ---------------------------------------------- |
| `config.py`              | Configuration (base URL, credentials, timeout) |
| `client.py`              | HTTP client with JWT support                   |
| `logger.py`              | Structured logging wrapper                     |
| `models.py`              | Dataclasses for results                        |
| `report.py`              | Console summary generation                     |
| `runner.py`              | Coordinates verification flow                  |
| `health.py`              | `/health` verification                         |
| `auth.py`                | Authentication verification                    |
| `products.py`            | Product API verification                       |
| `orders.py`              | Order workflow verification                    |
| `inventory.py`           | Inventory validation                           |
| `verify_backend_apis.py` | Thin executable entry point                    |

---

# STEP 2 — Implement Shared Models

Create a reusable result model.

Example:

```python
@dataclass(slots=True)
class VerificationResult:
    module: str
    test_name: str
    success: bool
    status_code: int | None
    duration_ms: float
    message: str
```

This object will be returned by every verifier.

---

# STEP 3 — Configuration

Move all environment-specific values into one place.

Initially:

```text
BASE_URL=http://localhost:8000

ADMIN_EMAIL=admin@test.com
ADMIN_PASSWORD=********

CUSTOMER_EMAIL=cust01@company.com
CUSTOMER_PASSWORD=********

TIMEOUT=15
```

Later, this can read from your existing settings or `.env`.

---

# STEP 4 — HTTP Client

Responsibilities:

- Reuse a single `requests.Session`.
- Attach JWT automatically after login.
- Standardize timeout handling.
- Handle common request errors.
- Return parsed JSON where appropriate.

This prevents duplicated request logic across verifiers.

---

# STEP 5 — Logging

Use your existing structured logging framework instead of `print()`.

Each verification should log:

- Module
- Endpoint
- HTTP status
- Duration
- Pass/Fail
- Error details (if any)

Console output should remain concise for developers.

---

# STEP 6 — Runner

Execution order:

```text
Initialize Configuration
        ↓
Initialize Logger
        ↓
Create HTTP Client
        ↓
Authenticate
        ↓
Health Verification
        ↓
Product Verification
        ↓
Order Verification
        ↓
Inventory Verification
        ↓
Generate Summary
        ↓
Exit Code
```

If authentication fails, dependent verifications should be skipped with an appropriate reason while still producing a final report.

---

# STEP 7 — Entry Script

`scripts/verify_backend_apis.py` should remain minimal:

```python
from app.verification.runner import VerificationRunner

if __name__ == "__main__":
    raise SystemExit(VerificationRunner().run())
```

This keeps all business logic inside the reusable package.

---

# STEP 8 — Validation Before Coding Verifiers

Before implementing `health.py`, `auth.py`, and the others, verify the following with your existing codebase:

1. **Health endpoint**
   - Exact path (e.g., `/health` or `/api/v1/health`)
   - Response schema

2. **Authentication**
   - Login endpoint path
   - Request payload fields (e.g., `email`/`password` or `username`/`password`)
   - Response schema for access and refresh tokens

3. **Products**
   - List endpoint path
   - Search query parameter name
   - Category filter parameter name
   - Pagination behavior

4. **Orders**
   - Create endpoint path
   - Request DTO
   - Response DTO
   - `shipping_address` validation

5. **Inventory**
   - Whether inventory is exposed via an API endpoint or should be verified directly against the database.

---

## Why This Approach?

This design keeps the verification framework:

- Independent of your application services.
- Easy to execute locally and in CI/CD.
- Modular and extensible as new modules are added.
- Consistent with your Clean Architecture and repository/service separation.

### Before Step 2

Please share (or confirm) the following so the verifiers can target the correct APIs without assumptions:

- `app/main.py` (router registrations)
- Health endpoint implementation
- Auth router
- Product router
- Order router
- Inventory router (if present)

Once I have those endpoint definitions, we'll implement each verifier against the actual API contracts instead of guessing URLs or payloads.
