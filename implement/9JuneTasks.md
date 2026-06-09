##
Implementation steps:
---
Excellent. For **Sprint 2: Authentication Foundation**, I would implement it using a clean Modular Monolith structure consistent with Repository + Service patterns and future RBAC expansion.

---

# Sprint 2 Deliverables

## Folder Structure

```text
backend/
│
├── app/
│   ├── core/
│   │   ├── security.py
│   │   ├── config.py
│   │   └── dependencies.py
│   │
│   ├── modules/
│   │   └── auth/
│   │       ├── models/
│   │       │   ├── user.py
│   │       │   └── role.py
│   │       │
│   │       ├── schemas/
│   │       │   ├── auth_request.py
│   │       │   └── auth_response.py
│   │       │
│   │       ├── repositories/
│   │       │   └── auth_repository.py
│   │       │
│   │       ├── services/
│   │       │   └── auth_service.py
│   │       │
│   │       └── routers/
│   │           └── auth_router.py
│   │
│   ├── database/
│   │   ├── base.py
│   │   └── session.py
│   │
│   └── main.py
│
└── alembic/
```

---

# 1. SQLAlchemy Models

---

## role.py

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

## user.py

```python
from uuid import uuid4

from sqlalchemy import ForeignKey, String
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

# 2. Security Utilities

## security.py

```python
from datetime import datetime, timedelta, timezone

from jose import jwt
from passlib.context import CryptContext

SECRET_KEY = "CHANGE_ME"
ALGORITHM = "HS256"

ACCESS_TOKEN_EXPIRE_MINUTES = 15
REFRESH_TOKEN_EXPIRE_DAYS = 7

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


def create_access_token(data: dict):
    payload = data.copy()

    expire = datetime.now(timezone.utc) + timedelta(
        minutes=ACCESS_TOKEN_EXPIRE_MINUTES
    )

    payload.update({"exp": expire})

    return jwt.encode(
        payload,
        SECRET_KEY,
        algorithm=ALGORITHM
    )


def create_refresh_token(data: dict):
    payload = data.copy()

    expire = datetime.now(timezone.utc) + timedelta(
        days=REFRESH_TOKEN_EXPIRE_DAYS
    )

    payload.update({"exp": expire})

    return jwt.encode(
        payload,
        SECRET_KEY,
        algorithm=ALGORITHM
    )
```

---

# 3. Repository Layer

## auth_repository.py

```python
from sqlalchemy import select
from sqlalchemy.ext.asyncio import AsyncSession

from app.modules.auth.models.user import User


class AuthRepository:

    def __init__(self, db: AsyncSession):
        self.db = db

    async def get_user_by_email(
        self,
        email: str
    ) -> User | None:

        stmt = select(User).where(
            User.email == email
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

# 4. Service Layer

## auth_service.py

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
        role_id
    ):

        existing = await self.repository.get_user_by_email(
            email
        )

        if existing:
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

        user = await self.repository.get_user_by_email(
            email
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

# 5. Request / Response Schemas

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

# 6. FastAPI Router

## auth_router.py

```python
from fastapi import (
    APIRouter,
    Depends,
    HTTPException,
    status
)

from sqlalchemy.ext.asyncio import AsyncSession

from app.database.session import get_db

from app.modules.auth.repositories.auth_repository import (
    AuthRepository
)

from app.modules.auth.services.auth_service import (
    AuthService
)

from app.modules.auth.schemas.auth_request import (
    RegisterRequest,
    LoginRequest
)

from app.modules.auth.schemas.auth_response import (
    TokenResponse
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

# 7. Angular Authentication Service

## auth.service.ts

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { BehaviorSubject, Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class AuthService {

  private readonly API =
    'http://localhost:8000/api/v1/auth';

  private loggedIn =
    new BehaviorSubject<boolean>(false);

  constructor(
    private http: HttpClient
  ) {}

  login(
    email: string,
    password: string
  ): Observable<any> {

    return this.http.post(
      `${this.API}/login`,
      {
        email,
        password
      }
    );
  }

  logout(): void {

    localStorage.removeItem(
      'access_token'
    );

    localStorage.removeItem(
      'refresh_token'
    );

    this.loggedIn.next(false);
  }

  handleTokenRefresh(): Observable<any> {

    const refreshToken =
      localStorage.getItem(
        'refresh_token'
      );

    return this.http.post(
      `${this.API}/refresh`,
      {
        refresh_token:
          refreshToken
      }
    );
  }
}
```

---

# Enterprise Improvements Recommended Before Sprint Completion

For a production-grade implementation, I would add these before closing Sprint 2:

| Item                                | Priority |
| ----------------------------------- | -------- |
| Refresh Token Table                 | High     |
| Token Revocation                    | High     |
| Login Audit Logs                    | High     |
| Account Locking                     | High     |
| Email Verification                  | Medium   |
| Password Reset Flow                 | Medium   |
| Role-Based Authorization Middleware | High     |
| Redis Token Blacklist               | Medium   |
| Secrets in Vault/Environment        | High     |
| Structured Logging                  | High     |

### Sprint 2 Definition of Done

* ✅ User Registration
* ✅ User Login
* ✅ BCrypt Password Hashing
* ✅ JWT Access Token
* ✅ JWT Refresh Token
* ✅ Role Model
* ✅ Repository Layer
* ✅ Service Layer
* ✅ Swagger Documentation
* ✅ Angular Authentication Service
* 🔜 Refresh Endpoint
* 🔜 JWT Middleware
* 🔜 Angular Route Guards
* 🔜 Role-Based Authorization (RBAC)

For an enterprise e-commerce platform, the next logical step is **Sprint 2.1: JWT Middleware + Refresh Token Persistence + Angular Route Guards**, before moving to Product Catalog development. This prevents security rework later.

##
To execute your tasks for **Sprint 2: Authentication Foundation** as the Lead Developer, you should use the following specialized command. This prompt is designed to activate the **Developer AI** role defined in your "Team with AI OS" and ensure the output aligns with the **Service Layer and Repository patterns** established in your architecture blueprints.

### **AI Developer Role Command**

Copy and paste this command into your AI coding assistant:

> "Act as a **Senior Python (FastAPI) and Angular Developer**. We are beginning **Sprint 2: Authentication Foundation** for our Enterprise E-Commerce Modular Monolith. 
>
> **Project Context:**
> * **Backend:** FastAPI, PostgreSQL 17, SQLAlchemy 2.0, Alembic.
> * **Security:** JWT with Refresh Tokens, Password Hashing (Passlib/Bcrypt).
> * **Frontend:** Angular 19, Angular Material.
> * **Architecture:** Repository and Service Layer Patterns.
>
> **Your Task:**
> 1. **Database Schema:** Generate the SQLAlchemy models for `User` and `Role` entities. Include fields for `id` (UUID), `email` (unique), `password_hash`, and a relationship to the `Role` table.
> 2. **Backend Logic:** Create the `AuthRepository` for database access and the `AuthService` to handle password verification and JWT token generation (Access + Refresh).
> 3. **API Endpoints:** Draft the FastAPI router for `/register` and `/login`. Ensure endpoints are documented for Swagger/OpenAPI.
> 4. **Frontend Foundation:** Generate the Angular `AuthService` with methods for `login()`, `logout()`, and `handleTokenRefresh()`. 
>
> Please provide the code in a modular structure that follows the existing folder hierarchy established in Sprint 1. Ensure all code is **stateless and production-grade**."

---

### **Why this command works for today’s goals:**
*   **Targeted Sprint 2 Scope:** It specifically targets the `User` and `Role` entities and JWT logic identified in your **Recommended Sprint-2 Scope**.
*   **Enforces Architectural Standards:** By specifying the **Repository and Service Layer patterns**, it prevents the AI from generating "tutorial-style" flat code, keeping your project at the **Solution Architect** level.
*   **Ensures Technical Parity:** It references the exact versions (PostgreSQL 17, Angular 19) verified in your **Sprint-1 Day 2 Completion Report** to ensure environment compatibility.

### **Your Next Step (Human Responsibility):**
As the **Lead Developer**, once the AI generates this code, your responsibility is to **Review and Approve** it before implementation. Specifically, verify that the **Alembic migration script** is created to reflect the new `users` and `roles` tables in your PostgreSQL container.
