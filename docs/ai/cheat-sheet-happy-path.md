# Enterprise E-Commerce Platform — Daily Development Cheat Sheet

**Environment**

* OS: WSL2 Ubuntu
* Backend: FastAPI + Python 3.12
* Database: PostgreSQL 17
* Cache: Redis 8
* ORM: SQLAlchemy 2.x
* Migration: Alembic
* Frontend: Angular 19
* Containers: Docker Compose

---

# 1. Start Development Environment

### Start all services

```bash
docker compose up -d
```

Starts all containers in the background so the complete application stack is available.

---

### Verify running containers

```bash
docker compose ps
```

Shows the health and status of every service to confirm the environment is running correctly.

---

### View service logs

```bash
docker compose logs -f backend
```

Streams backend logs in real time for debugging API and application issues.

---

### Restart backend only

```bash
docker compose restart backend
```

Restarts just the FastAPI container after configuration or dependency changes.

---

# 2. Backend Development

### Open backend container

```bash
docker compose exec backend bash
```

Provides an interactive shell inside the backend container where Python tools and dependencies are installed.

---

### Run FastAPI tests

```bash
pytest
```

Executes the backend test suite to verify business logic before committing code.

---

### Run specific test

```bash
pytest tests/test_orders.py
```

Runs only the specified test module for faster development feedback.

---

### Format code

```bash
black .
```

Formats Python code consistently according to project standards.

---

### Lint project

```bash
ruff check .
```

Detects style issues, unused imports, and common programming mistakes.

---

### Auto-fix lint issues

```bash
ruff check . --fix
```

Automatically corrects many lint violations while preserving intended functionality.

---

### Type checking

```bash
mypy app
```

Performs static type analysis to catch potential runtime errors early.

---

# 3. PostgreSQL Operations

## Open PostgreSQL shell

```bash
docker compose exec postgres psql -U postgres
```

Connects directly to the PostgreSQL server for administration and troubleshooting.

---

## Connect to application database

```bash
docker compose exec postgres psql -U postgres ecommerce
```

Opens the enterprise application database for querying tables and validating data.

---

## List databases

```sql
\l
```

Displays every database hosted by the PostgreSQL instance.

---

## List tables

```sql
\dt
```

Shows all application tables to verify migrations and schema creation.

---

## Describe table

```sql
\d products
```

Displays columns, indexes, constraints, and relationships for a specific table.

---

## Show migration version

```sql
SELECT * FROM alembic_version;
```

Confirms the migration currently applied to the database migration ledger.

---

## Exit PostgreSQL

```sql
\q
```

Closes the PostgreSQL interactive session.

---

# 4. Redis Operations

## Open Redis CLI

```bash
docker compose exec redis redis-cli
```

Starts the Redis command-line interface for inspecting cache behavior.

---

## Verify Redis

```redis
PING
```

Checks Redis connectivity and should return `PONG`.

---

## List cache keys

```redis
KEYS *
```

Displays cached keys during development (avoid using in production due to performance).

---

## View cached value

```redis
GET all_products
```

Retrieves a cached entry to verify application caching logic.

---

## Clear cache

```redis
FLUSHALL
```

Removes all cached data, forcing the application to rebuild cache entries.

---

# 5. Alembic Migration Management

## Check migration status

```bash
alembic current
```

Displays the migration version currently applied to the connected database.

---

## View migration history

```bash
alembic history
```

Lists all migrations in chronological order to understand schema evolution.

---

## Generate migration

```bash
alembic revision --autogenerate -m "add product images"
```

Creates a new migration by comparing SQLAlchemy models with the existing database schema.

---

## Apply migrations

```bash
alembic upgrade head
```

Applies all pending migrations to bring the database schema to the latest version.

---

## Roll back one migration

```bash
alembic downgrade -1
```

Reverts the most recently applied migration for controlled rollback during development.

---

## Show SQL only

```bash
alembic upgrade head --sql
```

Generates SQL statements without executing them, useful for reviewing migration scripts.

---

# 6. Docker Operations

## Build containers

```bash
docker compose build
```

Rebuilds container images after dependency or Dockerfile changes.

---

## Rebuild backend only

```bash
docker compose build backend
```

Recreates only the backend image, reducing build time during backend development.

---

## Stop services

```bash
docker compose down
```

Stops and removes running containers while preserving named volumes.

---

## Stop and remove volumes

```bash
docker compose down -v
```

Deletes containers and persistent volumes, providing a completely clean environment.

---

## View resource usage

```bash
docker stats
```

Monitors CPU and memory usage across all running containers.

---

# 7. Angular Development

## Install dependencies

```bash
npm install
```

Installs or updates frontend packages defined in `package.json`.

---

## Start Angular

```bash
ng serve
```

Runs the Angular development server with live reload enabled.

---

## Build production

```bash
ng build
```

Generates an optimized production-ready frontend build.

---

## Run tests

```bash
ng test
```

Executes Angular unit tests to validate frontend functionality.

---

## Lint

```bash
ng lint
```

Checks Angular and TypeScript code against configured coding standards.

---

# 8. Git Feature Branch Workflow

## Update main branch

```bash
git checkout main
git pull origin main
```

Synchronizes the local main branch with the latest changes from the remote repository.

---

## Create feature branch

```bash
git checkout -b feature/product-images
```

Creates an isolated branch for implementing a single feature without affecting the main codebase.

---

## Check status

```bash
git status
```

Shows modified, staged, and untracked files before committing changes.

---

## Stage changes

```bash
git add .
```

Stages all current modifications for the next commit.

---

## Commit changes

```bash
git commit -m "feat: implement product image support"
```

Creates a descriptive commit that documents a logical unit of work.

---

## Push feature branch

```bash
git push origin feature/product-images
```

Publishes the feature branch for code review and collaboration.

---

## Rebase onto main

```bash
git fetch origin
git rebase origin/main
```

Replays feature branch commits on top of the latest main branch to maintain a clean history.

---

## Merge after approval

```bash
git checkout main
git merge feature/product-images
```

Integrates the reviewed feature into the main development branch.

---

# 9. Health Checks

## Backend

```bash
curl http://localhost:8000/health
```

Verifies that the FastAPI application is responding and operational.

---

## API Documentation

```text
http://localhost:8000/docs
```

Provides interactive Swagger UI for validating and testing REST endpoints.

---

## Angular

```text
http://localhost:4200
```

Confirms the frontend development server is accessible.

---

## PostgreSQL

```bash
docker compose exec postgres pg_isready
```

Checks whether PostgreSQL is accepting client connections.

---

## Redis

```bash
docker compose exec redis redis-cli ping
```

Validates Redis availability by expecting a `PONG` response.

---

# 10. Recommended Daily Development Sequence

1. `docker compose up -d` — Start the full application stack.
2. `docker compose ps` — Verify all containers are healthy.
3. `docker compose exec backend bash` — Enter the backend development environment.
4. `alembic current` — Confirm the database is at the expected migration version.
5. `ng serve` — Launch the Angular development server.
6. Develop and test features incrementally.
7. Run `pytest`, `ng test`, `ruff check .`, `black .`, and `mypy app` before committing.
8. Commit to a feature branch using descriptive, conventional commit messages.
9. Push the branch and open a pull request for review.
10. After merge, pull the latest `main` branch before starting the next feature.

This workflow aligns well with the enterprise architecture you've been building, emphasizing reproducible containerized development, controlled database migrations, consistent code quality, and a disciplined Git feature-branch process.

##
### **Updated Environment Record: WSL Ubuntu Local Setup**
Your project baseline is now formally recorded with the following local environment specifications:

| Component | Specification | Source |
| :--- | :--- | :--- |
| **Operating System** | **WSL2 Ubuntu (Local Setup)** | |
| **Runtime** | Python 3.12 | |
| **Containerization** | Docker Compose (v2+) | |
| **Primary Database** | PostgreSQL 17 (Containerized) | |
| **Caching Layer** | Redis 8 (Containerized) | |
| **Frontend Framework**| Angular 19 | |
| **Migration Tool** | Alembic | |
| **Quality Tooling** | Ruff, Black, Mypy, Pre-commit | |

---

### **Reference: Enterprise Development Cheat Sheet**
This one-time reference document consolidates the most frequent commands used in your current workflow.

#### **1. Docker & Infrastructure**
*   **Start Environment:** `docker-compose up -d` (Runs DB, Redis, and Backend in background).
*   **Stop Environment:** `docker-compose down`.
*   **View Logs:** `docker-compose logs -f backend`.
*   **Access Database CLI:** `docker exec -it ecommerce-postgres psql -U ecommerce_user -d ecommerce_db`.
*   **Check Redis Keys:** `docker exec -it ecommerce-redis redis-cli KEYS "*"`.

#### **2. Backend & Migrations (Python/FastAPI)**
*   **Activate Environment:** `source venv/bin/activate`.
*   **Apply Migrations:** `alembic upgrade head` (Brings DB to latest schema).
*   **Rollback Migration:** `alembic downgrade -1`.
*   **Run Unit Tests:** `pytest`.
*   **Start Backend Manually:** `uvicorn app.main:app --reload`.

#### **3. Frontend (Angular 19)**
*   **Start Dev Server:** `ng serve`.
*   **Run Frontend Tests:** `npm test` or `ng test`.
*   **Production Build:** `ng build --configuration production`.
*   **Linting:** `npm run lint` (Once configured in Sprint 4.0).

#### **4. Git Workflow**
*   **Create Feature Branch:** `git checkout -b feature/name`.
*   **Sync with Main:** `git pull origin main`.
*   **Commit Changes:** `git commit -m "feat(scope): descriptive message"`.

---

### **AI Prompt: Regular Cheat Sheet & Command Assistant**
Copy and paste this prompt into your **Role 2 (Implementation Engineer)** chat session whenever you need to generate a specific command list or a context-aware cheat sheet.

> **"Act as a Senior DevOps and Implementation Engineer.** I am working on an **Enterprise E-Commerce Platform** using **WSL Ubuntu** with a **Dockerized Python/FastAPI** backend and an **Angular 19** frontend.
>
> **Project Context:**
> * **Backend:** Python 3.12, SQLAlchemy 2.x, Alembic, PostgreSQL 17, Redis 8.
> * **Frontend:** Angular 19, Material 3, Signals, Standalone Components.
> * **Environment:** All services are containerized via Docker Compose.
>
> **Objective:** Generate a concise technical cheat sheet for [INSERT SPECIFIC ACTIVITY, e.g., 'Database Troubleshooting' or 'Daily Development'].
>
> **Requirements:**
> 1. Provide exact commands for **WSL Ubuntu** terminal.
> 2. Include **Docker exec** commands to interact with the containerized DB and Redis.
> 3. List **Alembic** commands for managing the migration ledger.
> 4. Include **Git** commands following a standard feature-branch workflow.
> 5. For every command, provide a brief 1-sentence explanation of what it does and why it is used in this enterprise architecture."
