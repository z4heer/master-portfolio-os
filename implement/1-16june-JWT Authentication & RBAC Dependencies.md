Since you've discovered that **JWT token generation exists but JWT authentication middleware/dependencies are missing**, 
I would treat this as a small prerequisite task before Orders.

Below is a junior-developer-friendly implementation plan.

---

# Task AUTH-05: Complete JWT Authentication & RBAC Dependencies

## Objective

Implement the missing authentication dependencies required by:

```text
Products Module
Orders Module
Future Customer APIs
```

After completion, developers should be able to write:

```python
Depends(get_current_user)
Depends(require_admin)
Depends(require_customer)
```

---

# Step 1: Add JWT Decode Function

## File

```text
app/core/security.py
```

---

### Existing Imports

You already have:

```python
from jose import jwt
```

Add:

```python
from jose import JWTError
```

---

### Add Function

Place below `create_refresh_token()`:

```python
def decode_token(token: str):

    try:
        payload = jwt.decode(
            token,
            SECRET_KEY,
            algorithms=[ALGORITHM]
        )

        return payload

    except JWTError:
        return None
```

---

## Acceptance Criteria

Valid token:

```python
decode_token(token)
```

returns:

```python
{
    "sub": "...",
    "role": "...",
    "exp": ...
}
```

Invalid token:

```python
decode_token(token)
```

returns:

```python
None
```

---

# Step 2: Create Auth Dependencies File

## Create File

```text
app/modules/auth/dependencies.py
```

---

## Add Imports

```python
from fastapi import Depends
from fastapi import HTTPException
from fastapi import status

from fastapi.security import OAuth2PasswordBearer

from sqlalchemy.orm import Session
from sqlalchemy.orm import joinedload

from app.database.session import get_db

from app.core.security import decode_token

from app.modules.auth.models.user import User
```

---

# Step 3: Configure OAuth2 Scheme

Add:

```python
oauth2_scheme = OAuth2PasswordBearer(
    tokenUrl="/api/v1/auth/login"
)
```

---

## Why

This enables:

```http
Authorization: Bearer eyJ...
```

to be automatically extracted.

---

# Step 4: Implement get_current_user()

Add:

```python
def get_current_user(
    token: str = Depends(oauth2_scheme),
    db: Session = Depends(get_db)
):
```

---

### Decode JWT

```python
    payload = decode_token(token)

    if not payload:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid token"
        )
```

---

### Extract User ID

Assumption:

During login you are storing:

```python
{
    "sub": str(user.id)
}
```

in the JWT.

Add:

```python
    user_id = payload.get("sub")

    if not user_id:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid token payload"
        )
```

---

### Load User

```python
    user = (
        db.query(User)
        .options(
            joinedload(User.role)
        )
        .filter(
            User.id == user_id
        )
        .first()
    )
```

---

### Validate User

```python
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="User not found"
        )
```

---

### Return User

```python
    return user
```

---

# Full Method

```python
def get_current_user(
    token: str = Depends(oauth2_scheme),
    db: Session = Depends(get_db)
):

    payload = decode_token(token)

    if not payload:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid token"
        )

    user_id = payload.get("sub")

    if not user_id:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid token payload"
        )

    user = (
        db.query(User)
        .options(
            joinedload(User.role)
        )
        .filter(
            User.id == user_id
        )
        .first()
    )

    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="User not found"
        )

    return user
```

---

# Step 5: Implement require_admin()

Add:

```python
def require_admin(
    current_user=Depends(get_current_user)
):

    if current_user.role.name != "ADMIN":

        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Admin access required"
        )

    return current_user
```

---

## Acceptance Criteria

ADMIN token:

```http
200 OK
```

CUSTOMER token:

```http
403 Forbidden
```

---

# Step 6: Implement require_customer()

Add:

```python
def require_customer(
    current_user=Depends(get_current_user)
):

    if current_user.role.name != "CUSTOMER":

        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Customer access required"
        )

    return current_user
```

---

## Acceptance Criteria

CUSTOMER token:

```http
200 OK
```

ADMIN token:

```http
403 Forbidden
```

---

# Step 7: Fix Product Router

## File

```text
app/modules/catalog/routers/product_router.py
```

---

Add:

```python
from app.modules.auth.dependencies import (
    require_admin
)
```

---

Protect Product Creation

```python
@router.post(
    "",
)
def create_product(
    ...
    current_user=Depends(require_admin)
):
```

---

Protect Product Update

```python
@router.put(
    "/{product_id}"
)
def update_product(
    ...
    current_user=Depends(require_admin)
):
```

---

# Step 8: Verify JWT Payload

Developer must verify login endpoint creates token with:

```python
payload = {
    "sub": str(user.id)
}
```

Example:

```python
access_token = create_access_token(
    {
        "sub": str(user.id)
    }
)
```

If JWT currently uses:

```python
"user_id"
```

instead of:

```python
"sub"
```

then update:

```python
user_id = payload.get(...)
```

accordingly.

---

# Integration Tests

## Test 1

No JWT

Expected:

```http
401 Unauthorized
```

---

## Test 2

Invalid JWT

Expected:

```http
401 Unauthorized
```

---

## Test 3

ADMIN Creates Product

Expected:

```http
201 Created
```

---

## Test 4

CUSTOMER Creates Product

Expected:

```http
403 Forbidden
```

---

## Test 5

CUSTOMER Access Protected Endpoint

Expected:

```http
200 OK
```

when using:

```python
Depends(require_customer)
```

---

# Deliverables Before Starting Orders

Junior developer should provide:

* [ ] Updated `security.py`
* [ ] New `auth/dependencies.py`
* [ ] Protected product endpoints
* [ ] JWT authentication test results
* [ ] RBAC test results

Once this task passes review, the Orders module can be implemented without any further authentication work.
##
---
For **AUTH-05 Integration Testing**, the developer needs realistic seed data in PostgreSQL and API test cases that prove:

```text
JWT Authentication works
JWT Authorization works
ADMIN access works
CUSTOMER access works
Invalid token handling works
```

---

# 1. Seed Roles

```sql
INSERT INTO roles (
    id,
    name
)
VALUES
(
    gen_random_uuid(),
    'ADMIN'
),
(
    gen_random_uuid(),
    'CUSTOMER'
);
```

Verify:

```sql
SELECT * FROM roles;
```

Expected:

| name     |
| -------- |
| ADMIN    |
| CUSTOMER |

---

# 2. Create Test Users

Generate password hashes using your existing:

```python
hash_password()
```

Example password:

```text
Password123
```

---

## Admin User

```sql
INSERT INTO users (
    id,
    email,
    password_hash,
    role_id
)
SELECT
    gen_random_uuid(),
    'admin@test.com',
    '$2b$12$REPLACE_WITH_HASH',
    r.id
FROM roles r
WHERE r.name = 'ADMIN';
```

---

## Customer User

```sql
INSERT INTO users (
    id,
    email,
    password_hash,
    role_id
)
SELECT
    gen_random_uuid(),
    'customer@test.com',
    '$2b$12$REPLACE_WITH_HASH',
    r.id
FROM roles r
WHERE r.name = 'CUSTOMER';
```

---

Verify:

```sql
SELECT
    u.email,
    r.name
FROM users u
JOIN roles r
ON u.role_id = r.id;
```

Expected:

| email                                         | role     |
| --------------------------------------------- | -------- |
| [admin@test.com](mailto:admin@test.com)       | ADMIN    |
| [customer@test.com](mailto:customer@test.com) | CUSTOMER |

---

# 3. Login Test Data

## Admin Login

```http
POST /api/v1/auth/login
```

Body:

```json
{
  "email": "admin@test.com",
  "password": "Password123"
}
```

Expected:

```http
200 OK
```

Response:

```json
{
  "access_token": "eyJ...",
  "refresh_token": "eyJ..."
}
```

Save:

```text
ADMIN_ACCESS_TOKEN
```

---

## Customer Login

```http
POST /api/v1/auth/login
```

Body:

```json
{
  "email": "customer@test.com",
  "password": "Password123"
}
```

Expected:

```http
200 OK
```

Save:

```text
CUSTOMER_ACCESS_TOKEN
```

---

# 4. JWT Payload Verification

Copy token.

Decode using:

```python
from jose import jwt

payload = jwt.decode(
    token,
    SECRET_KEY,
    algorithms=["HS256"]
)
```

Expected:

```json
{
  "sub": "user_uuid",
  "exp": 9999999999
}
```

Verify:

```text
sub exists
exp exists
```

---

# 5. Protected Endpoint Test

Create temporary endpoint:

```python
@router.get("/whoami")
def who_am_i(
    current_user=Depends(
        get_current_user
    )
):
    return {
        "id": str(current_user.id),
        "email": current_user.email,
        "role": current_user.role.name
    }
```

---

## Without Token

```http
GET /api/v1/auth/whoami
```

Expected:

```http
401 Unauthorized
```

---

## With Admin Token

Headers:

```text
Authorization: Bearer <ADMIN_ACCESS_TOKEN>
```

Expected:

```json
{
  "email": "admin@test.com",
  "role": "ADMIN"
}
```

---

## With Customer Token

Headers:

```text
Authorization: Bearer <CUSTOMER_ACCESS_TOKEN>
```

Expected:

```json
{
  "email": "customer@test.com",
  "role": "CUSTOMER"
}
```

---

# 6. require_admin() Test

Temporary endpoint:

```python
@router.get("/admin-only")
def admin_only(
    current_user=Depends(
        require_admin
    )
):
    return {
        "message": "success"
    }
```

---

## Admin Access

Expected:

```http
200 OK
```

```json
{
  "message": "success"
}
```

---

## Customer Access

Expected:

```http
403 Forbidden
```

```json
{
  "detail": "Admin access required"
}
```

---

# 7. require_customer() Test

Temporary endpoint:

```python
@router.get("/customer-only")
def customer_only(
    current_user=Depends(
        require_customer
    )
):
    return {
        "message": "success"
    }
```

---

## Customer Access

Expected:

```http
200 OK
```

---

## Admin Access

Expected:

```http
403 Forbidden
```

```json
{
  "detail": "Customer access required"
}
```

---

# 8. Invalid Token Test

Header:

```text
Authorization: Bearer invalid_token
```

Expected:

```http
401 Unauthorized
```

Response:

```json
{
  "detail": "Invalid token"
}
```

---

# 9. Deleted User Test

### Step 1

Login and get token.

### Step 2

Delete user:

```sql
DELETE FROM users
WHERE email='customer@test.com';
```

### Step 3

Call:

```http
GET /api/v1/auth/whoami
```

Expected:

```http
401 Unauthorized
```

```json
{
  "detail": "User not found"
}
```

---

# Expected Evidence for Review

Developer should provide:

### SQL

```sql
SELECT * FROM roles;

SELECT
    u.email,
    r.name
FROM users u
JOIN roles r
ON u.role_id = r.id;
```

---

### API Results

| Test                         | Expected |
| ---------------------------- | -------- |
| Login Admin                  | 200      |
| Login Customer               | 200      |
| No Token                     | 401      |
| Invalid Token                | 401      |
| Admin Endpoint (Admin)       | 200      |
| Admin Endpoint (Customer)    | 403      |
| Customer Endpoint (Customer) | 200      |
| Customer Endpoint (Admin)    | 403      |
| Deleted User                 | 401      |

If all of the above pass, AUTH-05 can be marked **Complete** and the team can proceed with the Orders implementation.

##
