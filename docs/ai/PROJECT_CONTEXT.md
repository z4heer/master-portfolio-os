# Enterprise E-Commerce Platform

## Project Type

Portfolio-grade Enterprise E-Commerce Platform.

Goal:

Build a production-quality backend and frontend demonstrating enterprise software engineering practices suitable for Senior Python Backend Engineer / FastAPI Engineer / AI Solutions Engineer interviews.

---

# Technology Stack

Backend

- Python 3.12
- FastAPI
- SQLAlchemy 2.x
- Alembic
- PostgreSQL
- Redis
- Pydantic Settings

Frontend

- Angular 19
- Angular Material
- Angular Signals
- Standalone Components
- OnPush Change Detection

Development

- Docker
- Docker Compose
- Git
- VS Code

---

# Architecture

Architecture is frozen.

Must preserve:

- Repository Pattern
- Service Layer
- Dependency Injection
- Modular Monolith

Do NOT introduce:

- CQRS
- DDD
- Event Bus
- Microservices
- Generic Repository redesign

---

# Backend Modules

Authentication

- JWT
- Refresh Token
- RBAC

Catalog

- Products
- Categories
- Search

Inventory

Orders

Checkout

Dashboard

Admin

Redis Cache

---

# Frontend Modules

Authentication

Dashboard

Product Catalog

Product Details

Search

Category Filter

Cart

Checkout

Orders

Responsive Layout

Angular Material

---

# Current Status

RC1 Engineering Baseline Approved

Completed

- Authentication
- RBAC
- Product Catalog
- Search
- Cart
- Checkout
- Orders
- Dashboard Foundation
- Docker
- PostgreSQL
- Redis

Architecture frozen.

---

# Current Sprint

Sprint 4.6A

Commit 1.1

Engineering Stabilization

Objectives

1. Backend Quality Stabilization

2. Bootstrap Framework

3. Database Seed Framework

4. Master Data

5. Demo Data

6. Dashboard Readiness

7. Documentation

8. Final QA

---

# Engineering Standards

Follow

- SOLID
- Clean Code
- SQLAlchemy 2.x Best Practices
- FastAPI Best Practices

Code should be

- typed
- production-ready
- modular
- testable

---

# Quality Gates

Must pass

black --check

ruff check

mypy

pytest

Alembic migration validation

No architecture changes.

---

# Working Rules

Never redesign architecture.

Never introduce unnecessary abstractions.

Never generate pseudo code.

Generate production-ready implementation.

When modifying an existing file:

Request that file before making changes.

Always explain

- design
- dependencies
- files modified

before generating code.

After implementation provide

- validation commands
- expected output
- git commit message
- implementation summary
