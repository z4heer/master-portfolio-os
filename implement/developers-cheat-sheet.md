# Enterprise E-Commerce Platform - Developer Cheat Sheet

## Project Structure

```text
ecommerce-platform/
│
├── backend/
│   ├── app/
│   ├── alembic/
│   ├── tests/
│   └── docker-compose.yml
│
└── frontend/
    └── ecommerce-frontend/
```

---

# Docker Commands

## Start All Containers

```bash
docker compose up -d
```

## Stop All Containers

```bash
docker compose down
```

## Restart Containers

```bash
docker compose restart
```

## View Running Containers

```bash
docker ps
```

## View All Containers

```bash
docker ps -a
```

## View Logs

```bash
docker compose logs -f
```

## Backend Logs

```bash
docker compose logs -f backend
```

## PostgreSQL Logs

```bash
docker compose logs -f postgres
```

## Redis Logs

```bash
docker compose logs -f redis
```

---

# Backend Commands

## Navigate

```bash
cd ~/projects/ecommerce-platform/backend
```

## Activate Virtual Environment

```bash
source .venv/bin/activate
```

## Verify Python

```bash
python --version
```

## Install Dependencies

```bash
pip install -r requirements.txt
```

## Run FastAPI Locally

```bash
uvicorn app.main:app --reload
```

## Open Swagger

```text
http://localhost:8000/docs
```

## Open ReDoc

```text
http://localhost:8000/redoc
```

---

# Alembic Commands

## Current Version

```bash
alembic current
```

## Create Migration

```bash
alembic revision --autogenerate -m "message"
```

## Apply Migration

```bash
alembic upgrade head
```

## Rollback One Step

```bash
alembic downgrade -1
```

## Migration History

```bash
alembic history
```

---

# PostgreSQL Commands

## Connect

```bash
psql -U postgres
```

## List Databases

```sql
\l
```

## Connect Database

```sql
\c ecommerce_db
```

## Show Tables

```sql
\dt
```

## Describe Table

```sql
\d users
```

## Exit

```sql
\q
```

---

# Angular Commands

## Navigate

```bash
cd ~/projects/ecommerce-platform/frontend/ecommerce-frontend
```

## Install Packages

```bash
npm install
```

## Start Angular

```bash
ng serve
```

## Start on Custom Port

```bash
ng serve --port 4201
```

## Production Build

```bash
ng build
```

## Watch Build

```bash
ng build --watch
```

---

# Angular Generate Commands

## Service

```bash
ng g s core/auth/services/auth
```

## Component

```bash
ng g c features/auth/pages/login
```

## Guard

```bash
ng g guard core/auth/guards/auth
```

## Interceptor

```bash
ng g interceptor core/auth/interceptors/auth
```

## Interface

```bash
ng g interface core/auth/models/auth
```

---

# Testing Commands

## Run All Tests

```bash
pytest
```

## Run Unit Tests

```bash
pytest tests/unit -v
```

## Run Integration Tests

```bash
pytest tests/integration -v
```

## Run Single Test File

```bash
pytest tests/unit/core/test_security.py -v
```

## Coverage

```bash
pytest --cov=app
```

## Coverage Report

```bash
pytest --cov=app --cov-report=html
```

Open:

```text
htmlcov/index.html
```

---

# Git Commands

## Status

```bash
git status
```

## Pull Latest

```bash
git pull origin main
```

## Create Branch

```bash
git checkout -b feature/auth-foundation
```

## Stage Changes

```bash
git add .
```

## Commit

```bash
git commit -m "feat(auth): add login api"
```

## Push

```bash
git push origin feature/auth-foundation
```

## View History

```bash
git log --oneline --graph
```

## Switch to Main

```bash
git checkout main
```

---

# Sprint Testing URLs

## Backend

```text
http://localhost:8000
```

## Swagger

```text
http://localhost:8000/docs
```

## ReDoc

```text
http://localhost:8000/redoc
```

## Angular

```text
http://localhost:4200
```

---

# Daily Startup Routine

```bash
# 1
docker compose up -d

# 2
docker ps

# 3
docker compose logs backend --tail=50

# 4
cd frontend/ecommerce-frontend

# 5
ng serve

# 6
Open:
http://localhost:8000/docs

# 7
Open:
http://localhost:4200
```

---

# Daily Shutdown Routine

```bash
# Stop Angular
CTRL + C

# Stop Containers
docker compose down
```

---

# Sprint 2 Verification

```text
□ Docker running
□ PostgreSQL running
□ Redis running
□ Backend running
□ Swagger available
□ Angular running
□ Register works
□ Login works
□ JWT generated
□ Tokens stored
□ Unit tests pass
□ Git committed
```
