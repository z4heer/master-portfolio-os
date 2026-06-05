# Part 1: Solution Architect / Tech Lead Questions (Short Answers)

## 1. Why FastAPI instead of Spring Boot / Node.js?

**Answer:**

```text
FastAPI provides high performance, automatic Swagger documentation,
strong typing with Pydantic, rapid development, and lower boilerplate.

Suitable for modern API-first applications.
```

---

## 2. Why Redis in Sprint-1?

**Answer:**

```text
Redis is introduced early to establish infrastructure for:

- Session management
- JWT blacklisting
- Caching
- Rate limiting
- Performance optimization

Avoids infrastructure redesign later.
```

---

## 3. Why PostgreSQL instead of MySQL?

**Answer:**

```text
PostgreSQL offers:

- Better standards compliance
- Advanced indexing
- JSON support
- Strong ACID compliance
- Better scalability options

Suitable for enterprise applications.
```

---

## 4. How Angular communicates with FastAPI?

**Answer:**

```text
Angular sends HTTP requests to FastAPI REST APIs.

Example:

Angular
   ↓
HTTP
   ↓
FastAPI
   ↓
PostgreSQL / Redis
```

Base URL:

```text
http://localhost:8000/api/v1
```

---

## 5. Authentication Strategy?

**Answer:**

```text
JWT Access Token
+
Refresh Token

Access Token:
Short-lived

Refresh Token:
Used to obtain new access tokens securely.
```

---

## 6. Environment Management?

**Answer:**

```text
Separate configurations:

Development
UAT
Production

Managed through:

.env
environment.ts
Docker environment variables
```

---

## 7. Monolith vs Microservices?

**Answer:**

```text
Current approach:

Modular Monolith

Reason:

- Faster development
- Easier deployment
- Lower operational complexity

Can evolve into microservices later.
```

---

## 8. Migration Strategy?

**Answer:**

```text
Alembic

Benefits:

- Version controlled schema changes
- Rollback support
- Team synchronization
```

---

## 9. Logging & Monitoring?

**Answer:**

```text
Future Plan:

FastAPI Logging
Docker Logs
Application Audit Logs

Future:

Prometheus
Grafana
ELK
OpenTelemetry
```

---

## 10. Deployment Strategy?

**Answer:**

```text
Current:
Docker Compose

Future:
Kubernetes
AWS / Azure / GCP

Container-first deployment model.
```

---

# Part 2: Extended Developer Reference Guide

This is the section every new developer should read before changing code.

---

# High-Level Architecture

```text
Angular
   │
   ▼
FastAPI
   │
 ┌─┴────────────┐
 ▼             ▼
PostgreSQL   Redis
```

---

# Folder Responsibilities

## backend/app/api

Purpose:

```text
REST Endpoints
Request Entry Point
```

Example:

```python
@app.get("/products")
```

Future:

```text
auth.py
users.py
products.py
orders.py
cart.py
```

---

## backend/app/services

Purpose:

```text
Business Logic
```

Example:

```python
calculate_order_total()

create_user()

process_payment()
```

Rule:

```text
Never write business logic in API layer.
```

---

## backend/app/repositories

Purpose:

```text
Database access layer
```

Example:

```python
get_user_by_id()

save_order()
```

Rule:

```text
Only database operations.
```

---

## backend/app/models

Purpose:

```text
SQLAlchemy Entities
```

Future:

```python
User
Product
Order
Cart
```

Represents:

```text
Database Tables
```

---

## backend/app/schemas

Purpose:

```text
Request/Response Models
```

Example:

```python
UserCreate
UserResponse
LoginRequest
```

Rule:

```text
Never expose DB model directly.
```

---

## backend/app/core

Purpose:

```text
Infrastructure Code
```

Current:

```text
config.py
database.py
redis_client.py
```

Future:

```text
security.py
logging.py
exceptions.py
```

---

# Request Flow

Example:

```text
POST /users
```

Flow:

```text
Route
  ↓
Service
  ↓
Repository
  ↓
Database
```

Return:

```text
Database
  ↓
Repository
  ↓
Service
  ↓
API Response
```

---

# Database Ownership

## PostgreSQL

Stores:

```text
Users
Products
Orders
Payments
Inventory
```

Rule:

```text
Source of Truth
```

---

## Redis

Stores:

```text
Sessions
Tokens
Cache
Rate Limits
Temporary Data
```

Rule:

```text
Never store permanent business data.
```

---

# Frontend Folder Purpose

## core

Contains:

```text
Auth
Interceptors
Guards
Services
```

Shared across application.

---

## shared

Contains:

```text
Reusable Components
Buttons
Dialogs
Tables
```

---

## layouts

Contains:

```text
Header
Footer
Sidebar
Dashboard Layout
```

---

## features

Contains:

```text
Auth
Products
Cart
Orders
Admin
```

Business modules.

---

# Safe Change Areas

New developers can safely modify:

```text
features/*
schemas/*
services/*
```

with minimal risk.

---

# Sensitive Areas

Change carefully:

```text
database.py
docker-compose.yml
Dockerfile
config.py
security.py
```

A small mistake can break the application startup.

---

# Before Every Commit

Checklist:

```text
✓ Application starts

✓ /docs works

✓ /health works

✓ Angular builds

✓ No credentials committed

✓ docker compose up succeeds

✓ git status clean
```

---

# Current Technical Debt

Planned future work:

```text
Alembic migrations

JWT Authentication

Role Based Access Control

Global Exception Handling

Logging Framework

Unit Testing

CI/CD Pipeline

Monitoring

Kubernetes Deployment
```

This guide should be saved as:

```text
docs/Developer_Handbook.md
```

and updated at the end of every sprint. It becomes one of the most valuable documents for onboarding new developers and supporting future maintenance.
