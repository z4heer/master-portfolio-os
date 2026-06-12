##
# Sprint 2 Local Testing Guide

After the developer completes the code, follow these steps in sequence. Do **not** jump directly to Angular testing.

---

# Phase 1 - Verify PostgreSQL

## Start PostgreSQL

Verify service is running.

### Windows

```bash
services.msc
```

Check:

```text
postgresql-x64-17
Status = Running
```

---

## Connect PostgreSQL

```bash
psql -U postgres
```

or

```bash
psql -U ecommerce_user -d ecommerce_db
```

---

## Verify Database

```sql
SELECT version();
```

Expected:

```text
PostgreSQL 17.x
```

---

# Phase 2 - Verify Migration

## Generate Migration

```bash
alembic revision --autogenerate -m "create auth tables"
```

Expected:

```text
Generating migration ...
```

---

## Apply Migration

```bash
alembic upgrade head
```

Expected:

```text
Running upgrade ...
```

No errors.

---

## Verify Tables

```sql
\d roles
\d users
```

Expected:

```text
roles
users
```

---

## Verify Foreign Key

```sql
SELECT
    tc.table_name,
    kcu.column_name,
    ccu.table_name AS foreign_table_name
FROM
    information_schema.table_constraints tc
JOIN
    information_schema.key_column_usage kcu
ON
    tc.constraint_name = kcu.constraint_name
JOIN
    information_schema.constraint_column_usage ccu
ON
    ccu.constraint_name = tc.constraint_name
WHERE
    constraint_type = 'FOREIGN KEY';
```

Expected:

```text
users.role_id -> roles.id
```

---

# Phase 3 - Seed Role Data

Insert initial roles.

```sql
INSERT INTO roles(id,name)
VALUES
(gen_random_uuid(),'ADMIN'),
(gen_random_uuid(),'CUSTOMER');
```

Verify:

```sql
SELECT * FROM roles;
```

Expected:

```text
ADMIN
CUSTOMER
```

Copy one role id.

You will use it for registration.

---

# Phase 4 - Verify Security Functions

Create temporary file:

```text
test_security.py
```

```python
from app.core.security import *

password = "Admin123"

hashed = hash_password(password)

print(hashed)

print(
    verify_password(
        password,
        hashed
    )
)
```

Run:

```bash
python test_security.py
```

Expected:

```text
$2b$12.....
True
```

---

# Phase 5 - Run FastAPI

## Start Server

```bash
uvicorn app.main:app --reload
```

Expected:

```text
INFO: Uvicorn running
```

---

## Health Check

Open:

```text
http://localhost:8000/docs
```

Expected Swagger page.

You should see:

```text
Authentication

POST /register
POST /login
```

---

# Phase 6 - Test Register API

Use Swagger.

## Request

```json
{
  "email": "admin@test.com",
  "password": "Admin123",
  "role_id": "ROLE_UUID"
}
```

Replace ROLE_UUID with actual role id.

---

## Expected Response

```json
{
  "id": "user_uuid",
  "email": "admin@test.com"
}
```

---

## Verify Database

```sql
SELECT
email,
password_hash
FROM users;
```

Expected:

```text
admin@test.com
$2b$12.....
```

### Important

Password must NOT appear as:

```text
Admin123
```

If it does:

```text
FAIL
```

---

# Phase 7 - Duplicate Registration Test

Attempt same email again.

Expected:

```json
{
  "detail": "User already exists"
}
```

Status:

```text
400
```

---

# Phase 8 - Login Test

Swagger:

```json
{
  "email": "admin@test.com",
  "password": "Admin123"
}
```

Expected:

```json
{
  "access_token": "...",
  "refresh_token": "...",
  "token_type": "bearer"
}
```

---

# Phase 9 - Invalid Login Test

Wrong password.

```json
{
  "email": "admin@test.com",
  "password": "WrongPassword"
}
```

Expected:

```json
{
  "detail": "Invalid credentials"
}
```

Status:

```text
401
```

---

# Phase 10 - Verify JWT

Copy access token.

Open:

```text
https://jwt.io
```

Paste token.

Expected Payload:

```json
{
  "sub": "user_uuid",
  "email": "admin@test.com",
  "exp": 123456789
}
```

---

# Phase 11 - Angular Test

Start Angular.

```bash
ng serve
```

Expected:

```text
http://localhost:4200
```

---

## Open Browser

Navigate:

```text
http://localhost:4200/login
```

---

## Login

Use:

```text
admin@test.com
Admin123
```

Expected:

```text
Login success
```

---

## Verify Local Storage

Press:

```text
F12
```

Open:

```text
Application
 → Local Storage
```

Expected:

```text
access_token
refresh_token
```

present.

---

# Phase 12 - Logout Test

Click Logout.

Expected:

```text
access_token removed
refresh_token removed
```

Verify Local Storage is empty.

---

# Sprint 2 Test Matrix

| Test Case          | Expected |
| ------------------ | -------- |
| Migration          | PASS     |
| Role Insert        | PASS     |
| User Register      | PASS     |
| Duplicate Register | PASS     |
| Password Hash      | PASS     |
| Login              | PASS     |
| Invalid Login      | PASS     |
| JWT Generated      | PASS     |
| Swagger Visible    | PASS     |
| Angular Login      | PASS     |
| Local Storage      | PASS     |
| Logout             | PASS     |

---

# Final Sign-Off Checklist

Before marking Sprint 2 complete:

```text
□ PostgreSQL working
□ Alembic migration successful
□ Roles seeded
□ User registration working
□ Password hashed
□ Login working
□ JWT generated
□ Swagger verified
□ Angular login working
□ Tokens stored
□ Logout working
□ Git committed
□ Code pushed
```

If all boxes are checked, Sprint 2 is complete and you're ready for **Sprint 2.1: JWT Middleware, Refresh Endpoint, HTTP Interceptor, Route Guards, and RBAC Foundation**.

----
##
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
##
Code to implement
##
---
Yes. Since this is being handed to a developer, I would provide a **Sprint 2 Implementation Package** with the exact files to create and the order to create them.

---

# Phase 1 – Models

## app/modules/auth/models/role.py

```python
from uuid import uuid4

from sqlalchemy import String
from sqlalchemy.dialects.postgresql import UUID
from sqlalchemy.orm import Mapped, mapped_column, relationship

from app.database.base import Base


class Role(Base):
    __tablename__ = "roles"

    id: Mapped[str] = mapped_column(
        UUID(as_uuid=True),
        primary_key=True,
        default=uuid4
    )

    name: Mapped[str] = mapped_column(
        String(50),
        unique=True,
        nullable=False
    )

    users = relationship(
        "User",
        back_populates="role"
    )
```

---

## app/modules/auth/models/user.py

```python
from uuid import uuid4

from sqlalchemy import String, ForeignKey
from sqlalchemy.dialects.postgresql import UUID
from sqlalchemy.orm import Mapped, mapped_column, relationship

from app.database.base import Base


class User(Base):
    __tablename__ = "users"

    id: Mapped[str] = mapped_column(
        UUID(as_uuid=True),
        primary_key=True,
        default=uuid4
    )

    email: Mapped[str] = mapped_column(
        String(255),
        unique=True,
        nullable=False,
        index=True
    )

    password_hash: Mapped[str] = mapped_column(
        String(255),
        nullable=False
    )

    role_id: Mapped[str] = mapped_column(
        UUID(as_uuid=True),
        ForeignKey("roles.id"),
        nullable=False
    )

    role = relationship(
        "Role",
        back_populates="users"
    )
```

---

# Phase 2 – Security

## app/core/security.py

```python
from datetime import datetime, timedelta, timezone

from jose import jwt
from passlib.context import CryptContext

SECRET_KEY = "CHANGE_ME"
ALGORITHM = "HS256"

ACCESS_TOKEN_MINUTES = 15
REFRESH_TOKEN_DAYS = 7

pwd_context = CryptContext(
    schemes=["bcrypt"],
    deprecated="auto"
)


def hash_password(password: str) -> str:
    return pwd_context.hash(password)


def verify_password(
    plain_password: str,
    hashed_password: str
) -> bool:
    return pwd_context.verify(
        plain_password,
        hashed_password
    )


def create_access_token(payload: dict):

    expire = (
        datetime.now(timezone.utc)
        + timedelta(minutes=ACCESS_TOKEN_MINUTES)
    )

    payload.update({"exp": expire})

    return jwt.encode(
        payload,
        SECRET_KEY,
        algorithm=ALGORITHM
    )


def create_refresh_token(payload: dict):

    expire = (
        datetime.now(timezone.utc)
        + timedelta(days=REFRESH_TOKEN_DAYS)
    )

    payload.update({"exp": expire})

    return jwt.encode(
        payload,
        SECRET_KEY,
        algorithm=ALGORITHM
    )
```

---

# Phase 3 – Repository

## app/modules/auth/repositories/auth_repository.py

```python
from sqlalchemy import select
from sqlalchemy.ext.asyncio import AsyncSession

from app.modules.auth.models.user import User


class AuthRepository:

    def __init__(
        self,
        db: AsyncSession
    ):
        self.db = db

    async def get_user_by_email(
        self,
        email: str
    ) -> User | None:

        stmt = (
            select(User)
            .where(User.email == email)
        )

        result = await self.db.execute(stmt)

        return result.scalar_one_or_none()

    async def create_user(
        self,
        user: User
    ) -> User:

        self.db.add(user)

        await self.db.commit()

        await self.db.refresh(user)

        return user
```

---

# Phase 4 – Schemas

## auth_request.py

```python
from pydantic import BaseModel, EmailStr


class RegisterRequest(BaseModel):
    email: EmailStr
    password: str
    role_id: str


class LoginRequest(BaseModel):
    email: EmailStr
    password: str
```

---

## auth_response.py

```python
from pydantic import BaseModel


class TokenResponse(BaseModel):
    access_token: str
    refresh_token: str
    token_type: str
```

---

# Phase 5 – Service

## app/modules/auth/services/auth_service.py

```python
from app.core.security import (
    hash_password,
    verify_password,
    create_access_token,
    create_refresh_token
)

from app.modules.auth.models.user import User
from app.modules.auth.repositories.auth_repository import (
    AuthRepository
)


class AuthService:

    def __init__(
        self,
        repository: AuthRepository
    ):
        self.repository = repository

    async def register(
        self,
        email: str,
        password: str,
        role_id: str
    ):

        existing_user = (
            await self.repository
            .get_user_by_email(email)
        )

        if existing_user:
            raise ValueError(
                "User already exists"
            )

        user = User(
            email=email,
            password_hash=hash_password(password),
            role_id=role_id
        )

        return await self.repository.create_user(
            user
        )

    async def login(
        self,
        email: str,
        password: str
    ):

        user = (
            await self.repository
            .get_user_by_email(email)
        )

        if not user:
            raise ValueError(
                "Invalid credentials"
            )

        if not verify_password(
            password,
            user.password_hash
        ):
            raise ValueError(
                "Invalid credentials"
            )

        access_token = create_access_token(
            {
                "sub": str(user.id),
                "email": user.email
            }
        )

        refresh_token = create_refresh_token(
            {
                "sub": str(user.id)
            }
        )

        return {
            "access_token": access_token,
            "refresh_token": refresh_token,
            "token_type": "bearer"
        }
```

---

# Phase 6 – Router

## app/modules/auth/routers/auth_router.py

```python
from fastapi import (
    APIRouter,
    Depends,
    HTTPException,
    status
)

from sqlalchemy.ext.asyncio import AsyncSession

from app.database.session import get_db

from app.modules.auth.schemas.auth_request import (
    RegisterRequest,
    LoginRequest
)

from app.modules.auth.schemas.auth_response import (
    TokenResponse
)

from app.modules.auth.repositories.auth_repository import (
    AuthRepository
)

from app.modules.auth.services.auth_service import (
    AuthService
)

router = APIRouter(
    prefix="/api/v1/auth",
    tags=["Authentication"]
)


@router.post(
    "/register",
    summary="Register User"
)
async def register(
    request: RegisterRequest,
    db: AsyncSession = Depends(get_db)
):

    try:

        service = AuthService(
            AuthRepository(db)
        )

        user = await service.register(
            request.email,
            request.password,
            request.role_id
        )

        return {
            "id": str(user.id),
            "email": user.email
        }

    except ValueError as ex:

        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(ex)
        )


@router.post(
    "/login",
    response_model=TokenResponse,
    summary="Authenticate User"
)
async def login(
    request: LoginRequest,
    db: AsyncSession = Depends(get_db)
):

    try:

        service = AuthService(
            AuthRepository(db)
        )

        return await service.login(
            request.email,
            request.password
        )

    except ValueError as ex:

        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail=str(ex)
        )
```

---

# Register Router

## app/main.py

```python
from app.modules.auth.routers.auth_router import (
    router as auth_router
)

app.include_router(auth_router)
```

---

# Angular Service

## src/app/core/auth/auth.service.ts

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

export interface LoginRequest {
  email: string;
  password: string;
}

export interface TokenResponse {
  access_token: string;
  refresh_token: string;
  token_type: string;
}

@Injectable({
  providedIn: 'root'
})
export class AuthService {

  private apiUrl =
    'http://localhost:8000/api/v1/auth';

  constructor(
    private http: HttpClient
  ) {}

  login(
    request: LoginRequest
  ): Observable<TokenResponse> {

    return this.http.post<TokenResponse>(
      `${this.apiUrl}/login`,
      request
    );
  }

  logout(): void {

    localStorage.removeItem(
      'access_token'
    );

    localStorage.removeItem(
      'refresh_token'
    );
  }

  saveTokens(
    response: TokenResponse
  ): void {

    localStorage.setItem(
      'access_token',
      response.access_token
    );

    localStorage.setItem(
      'refresh_token',
      response.refresh_token
    );
  }
}
```

---

# Final Commands for Developer

```bash
# Create migration
alembic revision --autogenerate -m "create auth tables"

# Run migration
alembic upgrade head

# Run backend
uvicorn app.main:app --reload

# Swagger
http://localhost:8000/docs
```

### Expected Test Flow

```text
POST /register
        ↓
User Saved
        ↓
Password Hashed
        ↓
POST /login
        ↓
JWT Access Token
JWT Refresh Token
        ↓
Angular Login
        ↓
Store Tokens
```

**One architect recommendation before coding:** replace the hardcoded `SECRET_KEY` immediately with a settings/config approach using `.env` and Pydantic Settings. In enterprise projects, secrets should never live in source code, even temporarily. This is a small change now and avoids rework in Sprint 2.1 when adding JWT middleware and refresh-token persistence.

Keep Sprint 2 focused on **User → Login → JWT → Angular Login Flow**. Once that is working end-to-end, move to **Sprint 2.1 (JWT Middleware + Route Guards + Refresh Token Persistence)**.
