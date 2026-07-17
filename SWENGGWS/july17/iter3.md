Good catch running these — let's triage before touching anything else, because one of these is a real blocker and the rest are mostly pre-existing noise we shouldn't get tangled up in.

## 🔴 Blocking: broken migration chain (not caused by our changes)

```
KeyError: '9b2f4f6c7a81'
Revision 9b2f4f6c7a81 referenced from 9b2f4f6c7a81 -> d8a6f0b93c41 ... is not present
```

`d8a6f0b93c41` (the catalog master data migration, already in your repo before I touched anything) declares `down_revision = "9b2f4f6c7a81"` — but no migration file with that revision ID exists in `alembic/versions/`. That parent migration is either missing from the repo, was never committed, or got deleted. This isn't something I introduced — it was already broken; it just hadn't been exercised because nobody had run `alembic heads`/`upgrade` since that file landed.

**This blocks everything** — my two new migrations chain off `d8a6f0b93c41`, so until this gap is resolved, none of us can run `alembic upgrade head`.

I need two things before I can fix it safely (don't want to guess and rewrite migration history blind):

```bash
ls -la alembic/versions/
alembic current
```

`ls` tells me every revision ID actually present so I can find what should legitimately precede `d8a6f0b93c41`. `alembic current` tells me what revision your actual database thinks it's on right now — if the DB is already past the missing revision, the fix is just re-pointing `down_revision` to the correct real parent; if the DB predates it, we may need to reconstruct or stub the missing revision. Paste both outputs and I'll give you the exact fix.

**Do not run any `alembic` write commands (`upgrade`, `stamp`, `merge`) until we've confirmed this** — guessing here risks corrupting migration history.

## 🟡 Black — mixed: some ours, mostly pre-existing

Ours (fix these, they're part of this diff):
- `app/modules/orders/schemas/order_response.py`
- `app/modules/orders/schemas/order_request.py`
- `app/modules/orders/models/order.py`
- `app/modules/catalog/models/product.py`
- `tests/unit/orders/test_order_service_price_snapshot.py`

Pre-existing, not touched by us — **leave alone**:
- `app/core/logger.py`, `app/core/config.py`, `app/core/handlers.py`, `app/main.py`, `tests/conftest.py`

Reformatting files we didn't touch would pollute this commit's diff with unrelated changes and make review harder — that's the kind of scope creep your working rules explicitly want avoided. Run black scoped to just our files:

```bash
black app/modules/orders/schemas/order_response.py \
      app/modules/orders/schemas/order_request.py \
      app/modules/orders/models/order.py \
      app/modules/catalog/models/product.py \
      tests/unit/orders/test_order_service_price_snapshot.py

git status -s   # confirm only our 5 files changed
```

If you also want the 5 pre-existing files cleaned up, that should be its own separate formatting-only commit, not mixed into Task 2's feature commit.

## 🟢 Mypy — almost entirely pre-existing debt, one worth a look

Pre-existing, unrelated to our changes (confirmed by content — these are async/sync `Session` mismatches, missing type stubs, and `Settings` constructor issues that predate anything we touched):
- `security.py` stub errors (jose, passlib)
- `inventory_transaction_repository.py`, `auth_repository.py`, `order_repository.py` — all the same root cause: repositories type-hint `AsyncSession` but the routers actually pass a sync `Session` (see `order_router.py`'s `get_order_service` using `Depends(get_db)` → sync `Session`, but `OrderRepository.__init__` expects `AsyncSession`). This is a real, sprint-worthy bug but it's architectural and out of scope for Task 2 — flagging it, not fixing it blind.
- `config.py` `Settings` missing named args — pre-existing config/env issue.

One I want you to look at, not obviously pre-existing or ours:
```
app/database/seeds/seed_service.py:83: error: "Product" has no attribute "image_alt"; maybe "image_url"?
```
Your seed service references `product.image_alt`, which has never existed on the `Product` model (not before our change, not after) — mypy is only now suggesting `image_url` because the name is similar. This looks like a pre-existing typo/bug in `seed_service.py` unrelated to what we added, but I don't have that file to confirm. Can you paste `app/database/seeds/seed_service.py` around line 83? If it's meant to be `image_url` now that the column exists, that's a one-line fix; if it's something else entirely, we leave it as pre-existing debt.

**Ruff: clean, no action needed.**

---

Send me `ls -la alembic/versions/` + `alembic current` first — that's the one blocking issue standing between us and actually testing anything end-to-end.
