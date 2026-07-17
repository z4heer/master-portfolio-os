# Enterprise E-Commerce Platform

# Bug Fix & Enhancement Cheat Sheet

## (FastAPI + SQLAlchemy + PostgreSQL + Redis + Angular 19)

---

# Universal Rules (Applies Everywhere)

## ✅ DO

* Understand the root cause before changing code.
* Reproduce the issue consistently.
* Fix one logical issue at a time.
* Make the smallest possible change.
* Run affected tests before committing.
* Verify API contracts haven't changed.
* Check logs before assuming the cause.
* Keep changes backward compatible whenever possible.
* Update documentation if behavior changes.

---

## ❌ DON'T

* Don't "try random fixes."
* Don't mix refactoring with bug fixes.
* Don't change database schema without a migration.
* Don't disable validation just to make code work.
* Don't bypass authorization.
* Don't commit broken builds.
* Don't ignore warnings from Ruff, MyPy, or Angular.

---

# 1. Database (PostgreSQL)

## Before Any Change

✔ Backup database

✔ Check migration history

✔ Understand table relationships

✔ Review indexes

✔ Verify foreign keys

---

## Bug Fix Checklist

* NULL constraint violations
* Foreign key violations
* Duplicate records
* Missing indexes
* Wrong joins
* Transaction rollback
* Deadlocks
* Incorrect default values

---

## Enhancement Checklist

* Add nullable column first
* Backfill data
* Add NOT NULL later
* Add indexes
* Update migration
* Update SQLAlchemy model
* Update Pydantic schema
* Update tests

---

## DO

* Use Alembic only
* Use transactions
* Use constraints
* Use indexes wisely

---

## DON'T

Never

```sql
DROP TABLE
```

Never edit production schema manually.

Never edit alembic_version.

Never modify old migrations after merge.

---

# 2. SQLAlchemy

## Before Fixing

Check

* relationships
* cascade
* lazy loading
* eager loading
* session lifecycle

---

## Common Bugs

DetachedInstanceError

N+1 Query

Circular relationship

Session closed

IntegrityError

Duplicate insert

Cascade delete

---

## DO

Use

```
joinedload()

selectinload()

Session.begin()
```

---

## DON'T

Don't

```
session.commit()
```

inside loops.

Don't keep sessions open forever.

Don't manually manage transactions everywhere.

---

# 3. Alembic

## Before Migration

Verify

```
alembic current

alembic history
```

---

## Always

Autogenerate

Review generated SQL

Review constraints

Review indexes

Test upgrade

Test downgrade

---

## Never

Modify previous migrations

Delete migration files

Skip migration reviews

---

# 4. FastAPI Backend

## Before Fix

Understand

Request

↓

Validation

↓

Service

↓

Repository

↓

Database

↓

Response

---

## Common Bugs

422 Validation

401 Authentication

403 Authorization

404 Resource

409 Conflict

500 Internal Error

Serialization

Dependency Injection

Async issues

---

## Enhancement Checklist

Routes

DTO

Service

Repository

Tests

Swagger

RBAC

Logging

Caching

---

## DO

Validate request

Validate response

Raise HTTPException

Use dependency injection

Keep business logic inside services

---

## DON'T

No database code inside routes.

No business logic inside controllers.

No direct SQL in API layer.

---

# 5. Authentication

## Before Fix

Verify

JWT

Refresh Token

Expiration

RBAC

Current User

Roles

Permissions

---

## Never

Disable auth

Hardcode user

Bypass middleware

Skip RBAC

---

# 6. Redis

## Before Fix

Check

Cache key

TTL

Invalidation

Serialization

Cache misses

---

## Common Bugs

Old cache

Missing invalidation

Wrong key

Large object

Memory issue

---

## Enhancement

Add cache

Invalidate cache

Monitor cache

Measure hit ratio

---

## Never

Cache sensitive data

Store passwords

Cache everything blindly

Forget cache invalidation

---

# 7. Angular

## Before Fix

Understand

Component

↓

Service

↓

API

↓

Backend

↓

Database

---

## Common Bugs

Signal not updating

Observable not subscribed

Memory leak

OnPush issue

Routing

Guards

Forms

Material binding

---

## Enhancement

Update model

Update service

Update component

Update HTML

Update routing

Update tests

Update error handling

---

## DO

Use Signals

Use standalone components

Use OnPush

Destroy subscriptions

Use trackBy

---

## DON'T

No business logic inside HTML.

No huge components.

No duplicate API calls.

No nested subscriptions.

---

# 8. API Contract

Whenever backend changes

Always verify

DTO

Swagger

Frontend interface

Angular service

Validation

---

## Never

Change response format silently.

Rename JSON fields without updating frontend.

---

# 9. Unit Testing

Every bug fix should include

✓ New test

✓ Regression test

✓ Edge case

✓ Negative case

✓ Happy path

---

## Backend

Test

Repository

Service

API

Validation

Authentication

RBAC

---

## Frontend

Test

Service

Component

Signals

Forms

Guards

Interceptors

---

# 10. Integration Testing

Verify

Backend ↔ Database

Backend ↔ Redis

Backend ↔ Angular

Authentication flow

Checkout flow

Order flow

Admin flow

Inventory flow

Search flow

Product flow

---

# 11. Logging

Always log

Errors

Warnings

Failures

Retries

Validation failures

Unexpected exceptions

---

Never log

Passwords

JWT

Secrets

Credit cards

Personal data

---

# 12. Git

One feature

↓

One branch

↓

One PR

↓

One review

↓

One merge

---

Never

Commit generated files

Commit secrets

Commit .env

Force push shared branches

Mix multiple features

---

# 13. Performance

Before enhancement

Measure first

CPU

Memory

Queries

Redis

Network

Angular bundle

API latency

---

Don't optimize blindly.

Measure

↓

Optimize

↓

Measure again

---

# 14. Production Safety

Before deployment

✔ Build passes

✔ Tests pass

✔ Migration reviewed

✔ Backup taken

✔ Environment verified

✔ Health endpoint OK

✔ Swagger OK

✔ Logs clean

✔ Cache verified

✔ Rollback plan available

---

# 15. Root Cause Analysis (RCA) Workflow

When a bug is reported:

```
Issue Report
      │
      ▼
Reproduce
      │
      ▼
Collect Logs
      │
      ▼
Identify Layer
(UI/API/Service/DB/Cache)
      │
      ▼
Find Root Cause
      │
      ▼
Implement Minimal Fix
      │
      ▼
Unit Tests
      │
      ▼
Integration Tests
      │
      ▼
Regression Tests
      │
      ▼
Code Review
      │
      ▼
Deploy
      │
      ▼
Monitor
```

---

# 16. Senior Engineer "Golden Rules"

| Area        | ✅ Always Do                                                               | ❌ Never Do                                                  |
| ----------- | ------------------------------------------------------------------------- | ----------------------------------------------------------- |
| Database    | Use Alembic migrations, review indexes, test upgrades/downgrades          | Edit production schema manually, modify applied migrations  |
| SQLAlchemy  | Keep transactions short, use eager loading where appropriate              | Commit inside loops, ignore session lifecycle               |
| FastAPI     | Keep controllers thin, validate inputs/outputs, centralize business logic | Put business logic in routes, bypass dependency injection   |
| Redis       | Define cache invalidation strategy before caching                         | Cache sensitive or stale data indefinitely                  |
| Angular     | Use Signals, OnPush, reusable components, strong typing                   | Duplicate state, heavy templates, nested subscriptions      |
| API         | Maintain backward-compatible contracts when possible                      | Change JSON formats without coordinating frontend updates   |
| Testing     | Add regression tests for every bug fix                                    | Close bugs without automated verification                   |
| Git         | Small focused commits, feature branches, peer review                      | Mix unrelated changes or commit directly to `main`          |
| Logging     | Log context, correlation IDs, and errors                                  | Log secrets, tokens, or personally identifiable information |
| Performance | Measure → optimize → measure again                                        | Optimize based on assumptions                               |
| Security    | Validate, authenticate, authorize, sanitize                               | Trust client input or expose internal errors                |
| Deployments | Verify backups, migrations, health checks, rollback plan                  | Deploy untested code or irreversible schema changes         |

This is the style of operational checklist used by senior engineers to reduce regressions, speed up code reviews, and keep changes safe across the database, backend, frontend, caching, and deployment layers.
