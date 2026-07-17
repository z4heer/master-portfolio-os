Good — no `__init__.py` files and no pytest config means pytest will use its default rootdir-based "prepend" import mode, which works fine for a single test file with no naming collisions. No changes needed there.

Here's the verification sequence, in the order that catches problems earliest and cheapest:

## 1. Static checks first (fastest, catches typos/syntax before touching the DB)

```bash
black --check app tests
ruff check app tests
mypy app
```

If `black` fails, run `black app tests` to auto-fix, then re-check. If `ruff` or `mypy` fail, paste me the output — don't fix blind, some of it may point at pre-existing issues unrelated to our changes.

## 2. Confirm migration chain is sane before touching the DB

```bash
alembic heads
alembic history --verbose | head -30
```

**What to check:** `alembic heads` should show exactly one head — `c7d8e9f0a1b2` (shipping_address migration). If it shows two heads or a different revision, the migration chain has diverged from what I assumed and needs rechaining before you run anything. Paste me the output if it's not a single clean head at `c7d8e9f0a1b2`.

## 3. Apply migrations against a real Postgres instance

Since you're on Docker:

```bash
# from repo root
docker compose up -d db   # or whatever your db service is named
docker compose ps         # confirm it's healthy
cd backend
alembic upgrade head
```

**Expected:** both new migrations (`b1c2d3e4f5a6`, `c7d8e9f0a1b2`) apply cleanly with no errors. Then verify the columns actually landed:

```bash
docker compose exec db psql -U <your_db_user> -d <your_db_name> -c "\d products"
docker compose exec db psql -U <your_db_user> -d <your_db_name> -c "\d orders"
```

Check `products` has `sku` and `image_url`, and `orders` has `shipping_address NOT NULL`.

## 4. Run the new tests

```bash
pytest tests/unit/orders/test_order_service_price_snapshot.py -v
```

**Expected:** 3 passed. If the price-snapshot test fails, it means something in `OrderService.create_order` isn't behaving as I described — worth investigating before moving on, since that's the core Task 2 guarantee.

## 5. Full suite (confirm nothing else broke)

```bash
pytest -v
```

## 6. One manual smoke test worth doing (migrations + schema mismatches don't always show up in unit tests since I used mocks, not a real DB session)

Start the app and hit the endpoint for real:

```bash
docker compose up -d
curl -X POST http://localhost:8000/api/v1/orders \
  -H "Authorization: Bearer <a_valid_customer_token>" \
  -H "Content-Type: application/json" \
  -d '{
    "items": [{"product_id": "<a_real_product_uuid>", "quantity": 1}],
    "shipping_address": "123 Test St, Testville"
  }'
```

**Expected:** `201 Created`, response body includes `shipping_address` and `items[].unit_price`. This is the one check that exercises the real DB session + SQLAlchemy mapping (mocks in the unit test can't catch a bad column type or a migration that didn't actually apply).

Also worth a negative check — omit `shipping_address` from the body and confirm you get a `422` (Pydantic validation), not a 500.

---

Run through 1–6 and paste me anything that fails (full traceback, not just the last line) — then we'll move to closing out Task 1's backend DTOs once you send `product_request.py`/`product_response.py` and `product_repository.py`.
