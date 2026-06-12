This is a good time to create a **Sprint 2 Developer Operations Cheat Sheet**. Keep this handy for every future sprint.

# 🚀 Daily Startup Checklist

## 1. Go to Project

```bash
cd ~/projects/ecommerce-platform
```

---

## 2. Start Docker Stack

```bash
docker compose up -d
```

Check containers:

```bash
docker ps
```

Expected:

```text
ecommerce-backend
ecommerce-postgres
ecommerce-redis
```

---

## 3. View Logs

Backend:

```bash
docker compose logs -f backend
```

Last 100 lines:

```bash
docker compose logs backend --tail=100
```

Postgres:

```bash
docker compose logs postgres
```

Redis:

```bash
docker compose logs redis
```

---

# 🐳 Docker Commands

## Stop Everything

```bash
docker compose down
```

---

## Restart Backend

```bash
docker compose restart backend
```

---

## Rebuild Backend

```bash
docker compose build backend --no-cache
```

---

## Rebuild Entire Stack

```bash
docker compose down

docker compose build --no-cache

docker compose up -d
```

---

## Enter Backend Container

```bash
docker exec -it ecommerce-backend bash
```

---

## Enter Postgres Container

```bash
docker exec -it ecommerce-postgres bash
```

---

# 🐍 Python Virtual Environment

## Activate

```bash
cd ~/projects/ecommerce-platform/backend

source venv/bin/activate
```

Prompt:

```text
(venv)
```

---

## Deactivate

```bash
deactivate
```

---

## Install Package

```bash
pip install package_name
```

Example:

```bash
pip install bcrypt==4.1.3
```

---

## Check Installed Package

```bash
pip show bcrypt
```

```bash
pip show passlib
```

---

## Generate Requirements

```bash
pip freeze > requirements.txt
```

---

# 🗄️ PostgreSQL Commands

## Connect

```bash
docker exec -it ecommerce-postgres psql -U ecommerce_user -d ecommerce_db
```

---

## List Tables

```sql
\dt
```

---

## Describe Table

```sql
\d users
```

```sql
\d roles
```

---

## View Users

```sql
SELECT * FROM users;
```

---

## View Roles

```sql
SELECT * FROM roles;
```

---

## Count Records

```sql
SELECT COUNT(*) FROM users;
```

---

## Exit

```sql
\q
```

---

# 🔄 Alembic Commands

## Current Version

```bash
alembic current
```

---

## Generate Migration

```bash
alembic revision --autogenerate -m "message"
```

Example:

```bash
alembic revision --autogenerate -m "create auth tables"
```

---

## Apply Migration

```bash
alembic upgrade head
```

---

## Migration History

```bash
alembic history
```

---

# 🔍 Project Verification

## Find Files

```bash
find . -type f
```

---

## Project Structure

```bash
tree
```

Specific folder:

```bash
tree app/modules/auth
```

---

## Search Text

```bash
grep -R "JWT" app
```

```bash
grep -R "verify_password" app
```

---

## Find Relationship Definitions

```bash
grep -R "relationship" app/modules/auth
```

---

# 🌐 FastAPI

## Run Locally

```bash
uvicorn app.main:app --reload
```

---

## Swagger

```text
http://localhost:8000/docs
```

---

## OpenAPI

```text
http://localhost:8000/openapi.json
```

---

# 🧪 Testing Commands

## Quick Security Test

```bash
python
```

```python
from app.core.security import hash_password

print(hash_password("Admin123"))
```

---

## Import Test

```bash
python -c "from app.modules.auth.models.user import User; print('OK')"
```

---

## JWT Decode (without verification)

```bash
python
```

```python
import jwt

print(jwt.decode(token, options={"verify_signature": False}))
```

---

# 🌿 Git Commands

## Status

```bash
git status
```

---

## Branches

```bash
git branch
```

---

## Create Backup Branch

```bash
git checkout -b backup-sprint3
```

---

## Add Files

```bash
git add .
```

---

## Commit

```bash
git commit -m "message"
```

---

## Push

```bash
git push origin main
```

---

## Pull Latest

```bash
git pull origin main --rebase
```

---

## View History

```bash
git log --oneline --decorate -10
```

---

## Tags

```bash
git tag
```

Create:

```bash
git tag -a sprint-3-complete -m "Sprint 3 Complete"
```

Push:

```bash
git push origin sprint-3-complete
```

---

# 🚑 Emergency Troubleshooting

## Container Restarting

```bash
docker compose logs backend --tail=200
```

---

## Verify Environment Variables

```bash
cat backend/.env
```

---

## Verify Installed Package in Container

```bash
docker exec -it ecommerce-backend pip show bcrypt
```

---

## Verify Running Containers

```bash
docker ps -a
```

---

## Check Backend Files in Container

```bash
docker exec -it ecommerce-backend find /app -type f
```

---

# 📋 Sprint Workflow (Recommended)

```text
1. git pull origin main --rebase

2. docker compose up -d

3. Verify Swagger

4. Implement ONE endpoint

5. Test in Swagger

6. Verify DB

7. Commit

8. Push

9. Tag Sprint Milestone
```

This cheat sheet covers about **90% of the commands** we used today during Sprint 2 and should save a lot of time during Sprint 3 and beyond.
