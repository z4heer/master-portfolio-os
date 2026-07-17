Excellent idea. For an enterprise developer, the "happy path" cheat sheet is only about **30%** of the work. The remaining **70%** is spent diagnosing failures, fixing bugs, validating deployments, and performing root-cause analysis.

Below is a second cheat sheet that focuses on real-world engineering scenarios.

---

# Enterprise E-Commerce Platform

# DevOps, Debugging & Troubleshooting Cheat Sheet

**Stack**

* WSL Ubuntu
* Docker Compose
* FastAPI
* SQLAlchemy 2.x
* PostgreSQL 17
* Redis 8
* Angular 19
* Git

---

# 1. Environment Won't Start

## Check container status

```bash
docker compose ps -a
```

Shows whether containers exited, restarted repeatedly, or are unhealthy.

---

## View backend startup errors

```bash
docker compose logs backend --tail=100
```

Displays the latest backend startup logs to identify import errors, missing dependencies, or configuration issues.

---

## View database logs

```bash
docker compose logs postgres --tail=100
```

Helps diagnose PostgreSQL startup failures, authentication errors, or storage problems.

---

## View Redis logs

```bash
docker compose logs redis --tail=100
```

Checks Redis initialization and persistence-related issues.

---

## Restart everything

```bash
docker compose down
docker compose up -d
```

Performs a clean restart of all services after configuration or dependency changes.

---

# 2. Docker Debugging

## Check resource usage

```bash
docker stats
```

Identifies containers consuming excessive CPU or memory.

---

## List Docker networks

```bash
docker network ls
```

Verifies Docker networking used for inter-container communication.

---

## Inspect network

```bash
docker network inspect ecommerce_default
```

Confirms containers are attached to the expected network and can resolve each other.

---

## Inspect container

```bash
docker inspect backend
```

Displays environment variables, mounts, network configuration, and runtime metadata.

---

## Execute shell

```bash
docker compose exec backend bash
```

Allows interactive inspection of the running application environment.

---

# 3. Backend Debugging

## Check installed packages

```bash
pip list
```

Confirms required Python packages are installed inside the container.

---

## Verify environment variables

```bash
printenv
```

Ensures configuration values such as database URLs and secrets are correctly injected.

---

## Verify Python version

```bash
python --version
```

Confirms the expected runtime version is being used.

---

## Verify FastAPI routes

```bash
python -c "from app.main import app; print([r.path for r in app.routes])"
```

Lists registered API endpoints to detect missing routers or failed imports.

---

# 4. Database Troubleshooting

## Check database availability

```bash
docker compose exec postgres pg_isready
```

Confirms PostgreSQL is accepting client connections.

---

## Connect to PostgreSQL

```bash
docker compose exec postgres psql -U postgres ecommerce
```

Provides direct access to inspect schemas, tables, and data.

---

## List active connections

```sql
SELECT * FROM pg_stat_activity;
```

Shows connected sessions and identifies blocked or idle transactions.

---

## Detect locks

```sql
SELECT * FROM pg_locks;
```

Helps identify lock contention causing slow queries or deadlocks.

---

## Check table size

```sql
SELECT pg_size_pretty(pg_total_relation_size('products'));
```

Reports storage usage for a specific table.

---

## Explain query

```sql
EXPLAIN ANALYZE
SELECT * FROM products;
```

Shows execution plans to identify inefficient queries and missing indexes.

---

# 5. Alembic Migration Issues

## Current migration

```bash
alembic current
```

Verifies the migration version currently recorded in the database.

---

## Migration history

```bash
alembic history
```

Displays the complete migration timeline.

---

## Show SQL without applying

```bash
alembic upgrade head --sql
```

Reviews generated SQL before executing schema changes.

---

## Stamp database

```bash
alembic stamp head
```

Marks the database as being at a specific migration without executing SQL, useful after restoring a backup or reconciling environments.

---

## Validate migration ledger

```sql
SELECT version_num
FROM alembic_version;
```

Confirms the database migration ledger matches the expected application version.

---

# 6. Redis Debugging

## Verify connectivity

```bash
docker compose exec redis redis-cli ping
```

Checks Redis responsiveness.

---

## Monitor live Redis activity

```bash
docker compose exec redis redis-cli monitor
```

Displays every Redis command executed in real time, invaluable for debugging cache behavior.

---

## View memory usage

```bash
docker compose exec redis redis-cli info memory
```

Reports cache memory consumption and eviction status.

---

## Cache statistics

```bash
docker compose exec redis redis-cli info stats
```

Shows hit/miss ratios and overall cache performance.

---

# 7. API Testing

## Health endpoint

```bash
curl http://localhost:8000/health
```

Verifies the application is running and reachable.

---

## Swagger

```
http://localhost:8000/docs
```

Interactive API testing and request validation.

---

## OpenAPI

```
http://localhost:8000/openapi.json
```

Confirms the generated API specification is valid.

---

# 8. Unit Testing

## Backend

```bash
pytest
```

Runs the complete Python unit test suite.

---

## Stop on first failure

```bash
pytest -x
```

Stops execution immediately after the first failing test, speeding up debugging.

---

## Verbose mode

```bash
pytest -v
```

Displays detailed information about each executed test.

---

## Coverage

```bash
pytest --cov=app
```

Measures test coverage across the backend application.

---

## Run one test

```bash
pytest tests/test_orders.py
```

Focuses on a single test module during feature development.

---

# 9. Angular Debugging

## Development server

```bash
ng serve
```

Starts the Angular application with live reload.

---

## Production build

```bash
ng build
```

Detects Ahead-of-Time compilation errors and production build issues.

---

## Unit tests

```bash
ng test
```

Runs frontend unit tests in a headless browser.

---

## Watch tests

```bash
ng test --watch
```

Automatically reruns tests after file changes for rapid feedback.

---

## Find TypeScript errors

```bash
npx tsc --noEmit
```

Performs a full TypeScript compilation check without generating output files.

---

# 10. Git Recovery

## View status

```bash
git status
```

Shows the current working tree state before making changes.

---

## Review changes

```bash
git diff
```

Displays unstaged modifications.

---

## Review staged changes

```bash
git diff --cached
```

Verifies exactly what will be committed.

---

## Restore file

```bash
git restore app/services/product_service.py
```

Discards local changes to a specific file.

---

## Undo last commit (keep changes)

```bash
git reset --soft HEAD~1
```

Removes the last commit while preserving all file modifications.

---

## Find regression

```bash
git bisect start
```

Begins a binary search through commit history to locate the commit that introduced a bug.

---

# 11. Integration Testing

## Backend health

```bash
curl http://localhost:8000/health
```

Validates the backend is operational.

---

## Database connectivity

```bash
docker compose exec backend python -c "from app.db.session import engine; print(engine)"
```

Confirms the backend can initialize the SQLAlchemy engine.

---

## Redis connectivity

```bash
docker compose exec backend python -c "from app.core.redis import redis_client; print(redis_client.ping())"
```

Verifies backend-to-Redis communication.

---

## Test API with authentication

```bash
curl -H "Authorization: Bearer <TOKEN>" \
http://localhost:8000/api/v1/products
```

Confirms JWT authentication and protected endpoint access.

---

# 12. Performance Investigation

## Running processes

```bash
top
```

Identifies high CPU usage inside the WSL environment.

---

## Disk usage

```bash
df -h
```

Checks available disk space, a common cause of Docker and database failures.

---

## Directory size

```bash
du -sh .
```

Measures the size of the current project directory.

---

## Open files

```bash
lsof
```

Lists open file descriptors to diagnose locked files or port conflicts.

---

# 13. Common Enterprise Failure Scenarios

| Issue                         | First Command                             | Next Investigation                                            |
| ----------------------------- | ----------------------------------------- | ------------------------------------------------------------- |
| Backend won't start           | `docker compose logs backend`             | Check imports, environment variables, dependency installation |
| PostgreSQL connection refused | `docker compose exec postgres pg_isready` | Verify DB URL, credentials, network                           |
| Redis unavailable             | `redis-cli ping`                          | Inspect Redis logs and container health                       |
| Alembic upgrade failed        | `alembic current`                         | Compare migration history and inspect `alembic_version`       |
| SQLAlchemy relationship error | `pytest -x`                               | Review model relationships and eager/lazy loading             |
| Angular build failed          | `npx tsc --noEmit`                        | Resolve TypeScript errors before rebuilding                   |
| Docker build failed           | `docker compose build --no-cache`         | Check Dockerfile, dependency installation, image layers       |
| API returns 500               | `docker compose logs backend`             | Review traceback, validate request payload and DB state       |
| Slow database query           | `EXPLAIN ANALYZE`                         | Add indexes, optimize joins, review execution plan            |
| Authentication failure        | `curl` with JWT                           | Verify token, expiry, RBAC, middleware configuration          |
| Unit tests failing            | `pytest -v -x`                            | Isolate first failure and inspect recent code changes         |
| Integration tests failing     | Health checks + container logs            | Verify service-to-service connectivity and environment parity |

---

# 14. Enterprise Bug-Fix Workflow

1. **Reproduce** the issue consistently.
2. **Collect evidence** from application logs, Docker logs, and stack traces.
3. **Identify the root cause** using targeted debugging (database, cache, API, frontend, or infrastructure).
4. **Implement the smallest safe fix** without altering unrelated components.
5. **Run unit tests** for the affected module.
6. **Execute integration tests** to validate interactions between services.
7. **Perform manual smoke testing** of the impacted user workflow.
8. **Review code quality** with `black`, `ruff`, `mypy`, and `ng lint`.
9. **Commit using a descriptive message** (e.g., `fix: resolve product image loading regression`).
10. **Document the fix** in the sprint handoff or changelog to preserve engineering context.

This version complements your first cheat sheet by focusing on the kinds of operational issues, debugging practices, and quality assurance steps that are common in enterprise development and production support.
