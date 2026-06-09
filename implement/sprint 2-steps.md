Given your project approach and the fact that you're learning while building, I would **not code everything at once**. Complete Sprint 2 in small, testable increments.

# Sprint 2 Developer Execution Plan

**Goal:** Authentication Foundation

Expected Duration: **4–5 working days**

---

# Day 1 – Database Foundation

## Step 1.1 Create Role Model

### File

```text
app/modules/auth/models/role.py
```

### Tasks

* Create Role table
* UUID primary key
* Unique role name

### Verify

```bash
python -m compileall app
```

No errors.

---

## Step 1.2 Create User Model

### File

```text
app/modules/auth/models/user.py
```

### Tasks

Add:

* UUID id
* email
* password_hash
* role_id FK
* relationship

### Verify

Review:

```python
User -> Role
Role -> Users
```

Relationship should work both directions.

---

## Step 1.3 Register Models

### File

```text
database/base.py
```

Import:

```python
from app.modules.auth.models.user import User
from app.modules.auth.models.role import Role
```

### Verify

Alembic should discover tables.

---

## Step 1.4 Generate Migration

### Command

```bash
alembic revision --autogenerate -m "create auth tables"
```

Review migration.

Should create:

```sql
roles
users
```

---

## Step 1.5 Run Migration

```bash
alembic upgrade head
```

---

## Step 1.6 Verify PostgreSQL

Connect:

```sql
\d roles
\d users
```

Confirm:

```text
roles
users
```

exist.

---

# Checkpoint 1

Before moving forward verify:

```text
✓ Project starts
✓ Tables created
✓ Alembic clean
✓ PostgreSQL healthy
```

Commit.

```bash
git add .
git commit -m "Sprint2-Day1 Auth DB foundation"
```

---

# Day 2 – Security Foundation

---

## Step 2.1 Install Packages

Verify:

```bash
pip show passlib
pip show bcrypt
pip show python-jose
```

If missing:

```bash
pip install passlib[bcrypt]
pip install python-jose
```

---

## Step 2.2 Create Security Module

### File

```text
app/core/security.py
```

Implement:

```python
hash_password()
verify_password()
create_access_token()
create_refresh_token()
```

---

## Step 2.3 Unit Test Hashing

Create temporary script:

```python
password = "Admin123"

hashed = hash_password(password)

verify_password(password, hashed)
```

Expected:

```python
True
```

---

## Step 2.4 JWT Testing

Generate:

```python
access_token
refresh_token
```

Copy token into:

```text
jwt.io
```

Verify payload visible.

---

# Checkpoint 2

Verify:

```text
✓ Password hash works
✓ Password verify works
✓ JWT generation works
```

Commit.

```bash
git commit -m "Sprint2-Day2 Security foundation"
```

---

# Day 3 – Repository Layer

---

## Step 3.1 Create Repository

File:

```text
repositories/auth_repository.py
```

Methods:

```python
get_user_by_email()
create_user()
```

---

## Step 3.2 Create Database Seed

Insert one role.

```sql
INSERT INTO roles
VALUES (...)
```

Role:

```text
ADMIN
```

---

## Step 3.3 Repository Test

Temporary service test:

```python
repo.get_user_by_email()
```

Expected:

```python
User object
```

or

```python
None
```

---

# Checkpoint 3

Verify:

```text
✓ DB queries working
✓ Async session healthy
✓ Repository isolated
```

Commit.

```bash
git commit -m "Sprint2-Day3 Repository layer"
```

---

# Day 4 – Service Layer

---

## Step 4.1 Create AuthService

File:

```text
services/auth_service.py
```

Methods:

```python
register()
login()
```

---

## Step 4.2 Test Registration

Create temporary script.

Register:

```text
test@test.com
Password123
```

Expected:

```text
Row inserted
```

Verify PostgreSQL.

```sql
select * from users;
```

---

## Step 4.3 Verify Password Storage

Should see:

```text
$2b$12...
```

Never plain password.

---

## Step 4.4 Login Testing

Use:

```text
email
password
```

Expected:

```json
{
  "access_token": "...",
  "refresh_token": "..."
}
```

---

# Checkpoint 4

Verify:

```text
✓ Registration works
✓ Login works
✓ JWT generated
```

Commit.

```bash
git commit -m "Sprint2-Day4 Service layer"
```

---

# Day 5 – API Layer + Angular

---

## Step 5.1 Create Schemas

Files:

```text
schemas/auth_request.py
schemas/auth_response.py
```

---

## Step 5.2 Create Router

File:

```text
routers/auth_router.py
```

Endpoints:

```text
POST /register
POST /login
```

---

## Step 5.3 Register Router

File:

```python
main.py
```

Add:

```python
app.include_router(auth_router)
```

---

## Step 5.4 Swagger Testing

Start:

```bash
uvicorn app.main:app --reload
```

Open:

```text
http://localhost:8000/docs
```

Verify:

```text
Authentication
  POST /register
  POST /login
```

---

## Step 5.5 API Testing

Register user.

Then:

```text
POST /login
```

Receive JWT.

---

## Step 5.6 Angular Auth Service

Create:

```text
src/app/core/auth/auth.service.ts
```

Methods:

```typescript
login()
logout()
handleTokenRefresh()
```

---

## Step 5.7 Login Screen

Create simple Angular Material page.

```text
email
password
login button
```

No styling focus yet.

Functional only.

---

## Step 5.8 End-to-End Test

Flow:

```text
Angular
 ↓
POST /login
 ↓
FastAPI
 ↓
PostgreSQL
 ↓
JWT
 ↓
Angular
```

Store token.

```typescript
localStorage.setItem(...)
```

---

# Sprint 2 Acceptance Checklist

## Backend

```text
□ Role table
□ User table
□ Alembic migration
□ BCrypt hashing
□ JWT access token
□ JWT refresh token
□ Repository layer
□ Service layer
□ Auth router
□ Swagger docs
```

## Frontend

```text
□ Auth service
□ Login page
□ Store JWT
□ Logout
```

## Git

```text
□ Day1 commit
□ Day2 commit
□ Day3 commit
□ Day4 commit
□ Day5 commit
□ Sprint2 tag
```

Suggested tag:

```bash
git tag sprint-2-auth-foundation
git push origin sprint-2-auth-foundation
```

### What NOT to Build Yet

Avoid these until Sprint 2 is fully stable:

```text
❌ Route Guards
❌ Refresh Endpoint
❌ RBAC Middleware
❌ Email Verification
❌ Forgot Password
❌ Redis Token Store
❌ Account Locking
❌ MFA
```

Keep Sprint 2 focused on **User → Login → JWT → Angular Login Flow**. Once that is working end-to-end, move to **Sprint 2.1 (JWT Middleware + Route Guards + Refresh Token Persistence)**.
