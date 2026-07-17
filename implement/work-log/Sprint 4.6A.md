I agree. At this point, don't ask Gemini CLI to "implement everything." Large prompts often lead to context drift and inconsistent results.

Instead, use a **Principal Engineer controlled execution plan** with small, reviewable phases. Each phase should end with validation before proceeding. This is how enterprise teams typically drive AI-assisted implementation.

---

# Gemini CLI Master Prompt

## Sprint 4.6A Commit 1.1 — Engineering Stabilization

```text
You are acting as a Principal Software Engineer, Senior FastAPI Architect, SQLAlchemy 2.x Expert, Enterprise Backend Engineer, QA Lead and Technical Reviewer.

Project:
Enterprise E-Commerce Platform

Current Status

- RC1 Engineering Baseline Approved
- Sprint 4.6A Commit 1 completed
- PostgreSQL migration validation passed
- Architecture frozen

Architecture (MUST NOT CHANGE)

- Repository Pattern
- Service Layer
- Dependency Injection
- Modular Monolith
- FastAPI
- SQLAlchemy 2.x
- Alembic
- PostgreSQL
- Redis

Do NOT

- redesign architecture
- introduce new patterns
- modify APIs unless required
- introduce CQRS
- introduce DDD
- introduce Event Bus
- introduce Microservices

Work incrementally.

IMPORTANT

After every phase:

1. Run validation.
2. Fix issues.
3. Produce completion report.
4. Wait for next instruction.

Do NOT continue automatically to the next phase.

The objective is production-quality engineering, not rapid feature development.
```

---

# Phase 1 — Backend Engineering Review

### Prompt to Gemini CLI

```text
Phase 1

Perform a complete backend engineering review.

Review only.

Do not redesign.

Validate:

- configuration
- dependency injection
- repository layer
- service layer
- SQLAlchemy 2.x usage
- transaction handling
- exception handling
- security
- logging

Apply only production-quality improvements.

After implementation execute:

black --check .
ruff check .
mypy .
pytest

Deliver:

- Files Modified
- Engineering Improvements
- Issues Found
- Issues Fixed
- Remaining Issues
- Validation Results

Stop after Phase 1.
```

---

# Review

Approve Phase 1 before continuing.

---

# Phase 2 — Bootstrap Framework

```text
Implement app/bootstrap.

Create:

bootstrap.py

initialize_roles.py

initialize_permissions.py

initialize_categories.py

initialize_settings.py

initialize_admin.py

Requirements

- idempotent
- safe
- production quality
- structured logging

Do not modify architecture.

Validate implementation.

Provide completion report.

Stop.
```

---

# Review

Verify:

* clean implementation
* no duplicate code
* idempotent execution

Only then continue.

---

# Phase 3 — Seed Framework

```text
Implement

app/seeds/

Create

base.py

users.py

categories.py

products.py

inventory.py

orders.py

reviews.py

coupons.py

Create

scripts/seed_database.py

Support

master-only

demo-only

reset

normal execution

Framework must be

- modular
- deterministic
- reusable
- idempotent

Validate.

Provide report.

Stop.
```

---

# Review

Confirm:

* no duplicated records
* clean architecture
* reusable seed modules

---

# Phase 4 — Master Data

```text
Populate master data.

Implement

Roles

Categories

Settings

Admin User

Everything must be idempotent.

Validate.

Provide report.

Stop.
```

---

# Review

Verify

* Roles
* Categories
* Settings
* Admin account

---

# Phase 5 — Demo Data

```text
Populate realistic demo data.

Customers

Products

Inventory

Orders

Reviews

Coupons

Generate realistic relationships.

No duplicate records.

Validate.

Provide report.

Stop.
```

---

# Review

Verify:

* Product count
* User count
* Orders
* Inventory
* Reviews

---

# Phase 6 — Dashboard Readiness

```text
Review backend APIs used by dashboard.

Verify live database values.

Replace remaining placeholders if required.

Do not redesign APIs.

Validate.

Provide report.

Stop.
```

---

# Review

Confirm:

* KPI data
* Recent Orders
* Revenue
* Inventory metrics

---

# Phase 7 — Documentation

```text
Update documentation.

Create

Bootstrap Guide

Seed Guide

Demo Data Guide

Execution Guide

Update README.

Provide report.

Stop.
```

---

# Phase 8 — Final QA

```text
Execute complete validation.

black --check .

ruff check .

mypy .

pytest

Validate Alembic migrations.

Verify bootstrap.

Verify seed framework.

Verify dashboard readiness.

Generate final engineering report.

Stop.
```

---

# Expected Final Deliverables

At the end of Commit 1.1, Gemini CLI should provide:

* ✅ Files Added
* ✅ Files Modified
* ✅ Bootstrap Framework Summary
* ✅ Seed Framework Summary
* ✅ Master Data Summary
* ✅ Demo Data Summary
* ✅ Dashboard Readiness Report
* ✅ QA Report
* ✅ Black/Ruff/MyPy/Pytest Results
* ✅ Alembic Validation Results
* ✅ Remaining Technical Debt (if any)
* ✅ Recommendation for Sprint 4.6A Commit 2

---

# Execution Workflow

Follow this disciplined workflow rather than one large implementation:

```text
Phase 1
    ↓
Review
    ↓
Commit

Phase 2
    ↓
Review
    ↓
Commit

Phase 3
    ↓
Review
    ↓
Commit

Phase 4
    ↓
Review
    ↓
Commit

Phase 5
    ↓
Review
    ↓
Commit

Phase 6
    ↓
Review
    ↓
Commit

Phase 7
    ↓
Review
    ↓
Commit

Phase 8
    ↓
Final QA
    ↓
RC1 Stable
```

## Recommendation

I recommend **one Git commit per phase**. That gives you eight small, reviewable commits instead of one very large change set. It makes regressions much easier to isolate, lets you review Gemini CLI's work before proceeding, and gives you clean milestones in your repository. Once Phase 8 passes all quality gates, tag the repository (for example, `rc1-stable`) before starting **Sprint 4.6A Commit 2**.
