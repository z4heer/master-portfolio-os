Excellent. Based on the current baseline, I would **freeze feature development** and move entirely into **Engineering Stabilization (RC1 Readiness)**. At 92–93% complete, every hour spent on stabilization provides more value than adding functionality.

# Current RC1 Status

| Area           | Status                      |
| -------------- | --------------------------- |
| Architecture   | ✅ Frozen                    |
| Backend APIs   | ✅ Stable                    |
| Product Module | ✅ Complete                  |
| Order Module   | ✅ Complete                  |
| Authentication | ✅ Complete                  |
| Postman SIT    | ✅ Passed                    |
| Database       | ✅ Bootstrap + Seed Complete |
| Angular Build  | ✅ PASS                      |
| Remaining Work | Validation & Hardening      |

---

# Priority 1 — Stabilize Validation Suite

These are the last engineering blockers before RC1.

---

# Task 1 — Fix SQLAlchemy Mapper Registration Failures (Highest Priority)

From your description:

> relationship("User") resolution fails during isolated pytest execution.

This is almost always caused by one of these:

* model import order
* registry not initialized
* circular imports
* isolated test importing only one model
* relationship string resolution before User model is registered

---

## Step 1

Review every model.

Instead of scattered imports, ensure models are loaded centrally.

Example:

```python
app/models/__init__.py

from .user import User
from .product import Product
from .order import Order
from .order_item import OrderItem
from .refresh_token import RefreshToken
```

Tests should import

```python
import app.models
```

before metadata creation.

---

## Step 2

Avoid partial imports like

```python
from app.models.order import Order
```

inside tests.

Instead

```python
import app.models

from app.models.order import Order
```

This ensures registry population.

---

## Step 3

Check every relationship.

Good:

```python
user = relationship(
    "User",
    back_populates="orders"
)
```

Bad:

```python
relationship(User)
```

when imports become circular.

---

## Step 4

If tests create metadata

```python
Base.metadata.create_all(bind=engine)
```

ensure

```python
import app.models
```

happens first.

Otherwise SQLAlchemy cannot resolve

```
relationship("User")
```

---

## Step 5

If using pytest fixtures

verify fixture ordering.

Good:

```python
@pytest.fixture(scope="session")
def engine():
    import app.models

    engine = create_engine(TEST_DATABASE_URL)

    Base.metadata.create_all(engine)

    yield engine

    Base.metadata.drop_all(engine)
```

---

# Task 2 — Verify Alembic

Run inside Docker only.

```
docker compose exec backend alembic current

docker compose exec backend alembic history

docker compose exec backend alembic heads
```

Expected

```
Current == Head
```

Then

```
docker compose exec backend alembic upgrade head
```

Should report

```
No migrations to apply
```

Then

```
docker compose exec backend alembic downgrade -1
docker compose exec backend alembic upgrade head
```

to verify reversibility.

---

# Task 3 — Pytest Stabilization

Run

```
pytest -vv
```

then

```
pytest --maxfail=1
```

Fix failures one by one.

Priority

1. mapper errors

2. fixture failures

3. validation failures

4. response schema

5. repository tests

---

# Task 4 — Ruff

```
ruff check .
```

Fix

* unused imports
* unused variables
* import ordering
* duplicate imports

Avoid blanket ignores unless justified.

---

# Task 5 — Black

```
black --check .
```

If formatting differs:

```
black .
```

---

# Task 6 — MyPy

Run

```
mypy app
```

Typical fixes:

```
Optional[...]

Missing return

dict[str, Any]

cast()

Protocol typing
```

Avoid

```
# type: ignore
```

unless absolutely necessary.

---

# Task 7 — Angular Lint

Run

```
ng lint
```

Expected issues

### Remove

```
unused imports
```

### Replace

```
any
```

with

```
Product

Order

OrderItem

User

Observable<Product>

Signal<Order[]>
```

### Remove

```
console.log()
```

except where explicitly allowed.

### Verify

```
OnPush
```

still satisfied.

---

# Task 8 — Full Validation Pipeline

Run in this order.

```
python -m compileall app

ruff check .

black --check .

mypy app

pytest

ng build

ng test

ng lint
```

Every command should complete successfully.

---

# Task 9 — Full Integration Validation

Re-run Postman collection.

Customer

* Login
* Browse products
* Product details
* Create order
* Order history
* Order details

Admin

* Login
* Create product
* Update product
* Delete product (if supported)
* List orders
* Update order status

Health

```
GET /health
```

Redis

Database

API

must all report healthy.

---

# RC1 Exit Criteria

| Validation        | Target    |
| ----------------- | --------- |
| Backend Build     | ✅         |
| Angular Build     | ✅         |
| Pytest            | 100% PASS |
| ng test           | 100% PASS |
| ng lint           | PASS      |
| Ruff              | PASS      |
| Black             | PASS      |
| MyPy              | PASS      |
| Alembic Upgrade   | PASS      |
| Alembic Downgrade | PASS      |
| Postman SIT       | PASS      |
| Docker Startup    | PASS      |
| Health Endpoint   | PASS      |

---

# Estimated Remaining Effort

| Activity                        |            Estimate |
| ------------------------------- | ------------------: |
| SQLAlchemy mapper stabilization |           2–3 hours |
| Alembic validation              |       30–45 minutes |
| Pytest fixes                    |           2–4 hours |
| Angular lint cleanup            |           1–2 hours |
| Final validation pipeline       |              1 hour |
| RC1 integration verification    |           1–2 hours |
| **Total**                       | **~7.5–12.5 hours** |

## Implementation Sequence

To minimize rework, follow this order:

1. **Fix SQLAlchemy mapper registration issues** so all backend tests can execute reliably.
2. **Validate Alembic migrations** (upgrade, downgrade, and current/head consistency).
3. **Resolve remaining pytest failures** until the suite passes.
4. **Clean up Angular lint warnings** and type/style issues.
5. **Run the complete validation pipeline** (compile, Ruff, Black, MyPy, pytest, ng build, ng test, ng lint).
6. **Execute the final end-to-end integration validation** with Docker, health checks, and the Postman SIT collection.

This sequence keeps the architecture unchanged while driving the project to a production-ready RC1 baseline.
