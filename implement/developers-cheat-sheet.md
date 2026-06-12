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
##
---
##
Before starting **Manual Integration Testing**, follow this startup checklist. This is exactly how a developer would prepare their local environment for a sprint demo or QA verification.

# Manual Integration Test Startup Checklist

## Step 1 - Open Required Terminals

Open 4 terminals:

```text
Terminal 1 → PostgreSQL
Terminal 2 → Backend (FastAPI)
Terminal 3 → Frontend (Angular)
Terminal 4 → Testing / Utility Commands
```

---

# Step 2 - Verify PostgreSQL

## Check Service

Linux:

```bash
sudo systemctl status postgresql
```

Expected:

```text
active (running)
```

If not running:

```bash
sudo systemctl start postgresql
```

---

## Verify Database Exists

```bash
psql -U postgres
```

Inside PostgreSQL:

```sql
\l
```

Verify:

```text
ecommerce_db
```

exists.

---

## Verify Tables

```sql
\c ecommerce_db

\dt
```

Expected:

```text
users
roles
alembic_version
```

---

# Step 3 - Verify Environment Variables

Navigate to backend:

```bash
cd ~/projects/ecommerce-platform/backend
```

Verify:

```bash
ls -la
```

Expected:

```text
.env
```

exists.

Example:

```env
DATABASE_URL=postgresql+asyncpg://postgres:password@localhost/ecommerce_db

SECRET_KEY=your-secret-key

JWT_ALGORITHM=HS256
```

---

# Step 4 - Activate Python Virtual Environment

Backend folder:

```bash
cd ~/projects/ecommerce-platform/backend
```

Activate:

```bash
source .venv/bin/activate
```

Verify:

```bash
which python
```

Expected:

```text
.../.venv/bin/python
```

---

# Step 5 - Verify Dependencies

```bash
pip list
```

Verify:

```text
fastapi
uvicorn
sqlalchemy
alembic
asyncpg
python-jose
passlib
bcrypt
```

---

# Step 6 - Run Database Migration

Only if not already applied.

```bash
alembic current
```

Verify current revision.

Apply latest:

```bash
alembic upgrade head
```

Expected:

```text
Running upgrade
```

No errors.

---

# Step 7 - Seed Roles

Verify:

```sql
SELECT * FROM roles;
```

Expected:

```text
ADMIN
CUSTOMER
```

If empty:

```sql
INSERT INTO roles(id,name)
VALUES
(gen_random_uuid(),'ADMIN'),
(gen_random_uuid(),'CUSTOMER');
```

---

# Step 8 - Start FastAPI

Terminal 2:

```bash
cd ~/projects/ecommerce-platform/backend
source .venv/bin/activate

uvicorn app.main:app --reload
```

Expected:

```text
Application startup complete.
Uvicorn running on http://127.0.0.1:8000
```

---

# Step 9 - Verify Backend

Open browser:

```text
http://localhost:8000/docs
```

Expected:

```text
Swagger UI
```

Verify:

```text
POST /register
POST /login
```

visible.

---

# Step 10 - Verify CORS Configuration

Check:

```python
CORSMiddleware
```

Expected:

```python
allow_origins=[
    "http://localhost:4200"
]
```

Without this Angular login may fail.

---

# Step 11 - Start Angular

Terminal 3:

```bash
cd ~/projects/ecommerce-platform/frontend/ecommerce-frontend
```

Verify packages:

```bash
npm install
```

Start:

```bash
ng serve
```

Expected:

```text
Local: http://localhost:4200
```

---

# Step 12 - Verify Angular Environment

Check:

```text
src/environments/environment.ts
```

Expected:

```typescript
export const environment = {
  apiUrl: 'http://localhost:8000/api/v1'
};
```

---

# Step 13 - Open Browser Developer Tools

Open:

```text
F12
```

Keep these tabs visible:

```text
Network
Console
Application → Local Storage
```

These are needed during testing.

---

# Step 14 - Health Check Before Testing

Verify all URLs:

| Service    | URL                                                      | Expected |
| ---------- | -------------------------------------------------------- | -------- |
| FastAPI    | [http://localhost:8000/docs](http://localhost:8000/docs) | Opens    |
| Angular    | [http://localhost:4200](http://localhost:4200)           | Opens    |
| PostgreSQL | psql connection                                          | Works    |

---

# Step 15 - Ready State

All must be green:

```text
□ PostgreSQL running

□ Database reachable

□ Roles seeded

□ Migration applied

□ Virtual environment active

□ FastAPI running

□ Swagger opens

□ Angular running

□ Browser DevTools open

□ CORS configured

□ No startup errors
```

Only after all boxes are checked should you begin the Manual Integration Test sequence:

```text
1. Register User
2. Verify DB Record
3. Verify Password Hash
4. Login User
5. Verify JWT
6. Verify Angular Login
7. Verify Local Storage
8. Verify Logout
```

This preparation step typically saves 80–90% of the "why is login not working?" troubleshooting time during Sprint 2 testing.
