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
##
Git Cheat sheet
Here is your consolidated, production-ready Git cheat sheet. It now includes the entire lifecycle: from starting your day, handling conflicts and stashes, updating your feature branch when `main` moves forward, and safely delivering your code via a Pull Request.

---

## 🚀 1. Daily Workflow (The "Start of Day" Routine)

| Step | Command | What it actually does |
| --- | --- | --- |
| **1. Go to main** | `git checkout main` | Switches your working directory to the `main` branch. |
| **2. Update main** | `git pull origin main --rebase` | Fetches remote changes and replays your local work on top. |
| **3. Start feature** | `git checkout -b feature/name` | Creates and immediately switches to a new isolated branch. |

---

## 🛠️ 2. Feature Development & Committing

| Task | Command | Pro-Tip / Explanation |
| --- | --- | --- |
| **Check status** | `git status` | Run this before and after *every* major command. |
| **Stage changes** | `git add .` | Tracks your modifications locally. |
| **Commit locally** | `git commit -m "feat: desc"` | Saves your staged changes into a local snapshot. |
| **Push Branch** | `git push origin feature/name` | Sends your local feature branch up to the remote repository. |

> ⚠️ **Senior Rule:** `git commit origin main` is invalid syntax. Committing is always local (`git commit`). Shipping code to a remote repository is always a push (`git push`).

---

## 📦 3. Shipping Changes to `main` (The PR Workflow)

In a professional setting, you never push directly to `main`. You use a Pull Request (PR):

```bash
# 1. Push your feature branch to the remote repo
git push origin feature/your-feature-name

# 2. Open the PR
# Go to your repository webpage (GitHub/GitLab) and click "Compare & pull request".

# 3. Merge the PR
# Once reviews pass and tests are green, click the "Merge" button on the webpage.

```

---

## ⚡ 4. Conflict & Desync Scenarios

### Scenario A: "I have uncommitted local changes but need to pull/switch branches"

If you try to pull or checkout with a dirty working directory, Git blocks you. Use the stash workflow:

```bash
git stash                  # 1. Save your dirty work to a temporary shelf
git checkout main          # 2. Switch to main safely
git pull origin main --rebase # 3. Update main
git checkout feature/name  # 4. Return to your feature branch
git stash pop              # 5. Bring your changes back and clear the shelf

```

### Scenario B: "My feature branch is outdated because main moved forward upstream"

If a teammate merged code into `main` while you were working, pull those changes down and rebase your branch on top of them:

```bash
git checkout main          # 1. Switch to main
git pull origin main       # 2. Get the absolute latest code
git checkout feature/name  # 3. Switch back to your feature branch
git rebase main            # 4. Rebase your feature branch on top of updated main

# 5. Force push is required AFTER a rebase if you already pushed this branch before
git push origin feature/your-feature-name --force-with-lease

```

### Scenario C: "Git rebase paused due to merge conflicts"

When Git pauses mid-rebase and says `CONFLICT (content)`, follow this sequence:

```bash
# 1. Open conflicted files in your IDE. Look for <<<<<<< HEAD, =======, >>>>>>>
# 2. Edit the files to keep the correct code and delete the Git conflict markers.

git add <resolved-file-path>   # 3. Stage the resolved file(s)
git rebase --continue          # 4. Tell Git to keep moving forward (DO NOT commit!)

# Abort Option: If the rebase goes completely wrong, run this to reset back to normal:
git rebase --abort

```

---

## 🧯 5. Emergency Undos

> ⚠️ **Senior Advice:** Only use `--hard` resets if you are 100% sure you want to permanently delete your local, uncommitted code.

```bash
git checkout -- <file>     # Discard changes in a specific file (before staging)
git reset HEAD <file>      # Unstage a file you accidentally ran 'git add' on
git commit --amend         # Modify your very last commit (fix a typo or add a missed file)
git reset --hard HEAD~1    # Nuke the last local commit completely (destructive!)

```

---

## ⚙️ 6. One-Time Global Setup

Run this once in your terminal to save yourself from typing `--rebase` every single time you pull:

```bash
git config --global pull.rebase true

```
---

## 🚨 The "Solo Developer / Fast-Track" Exception

If you are working completely alone on a personal project and *have* to merge your feature branch into `main` purely via the command line, you do a local merge:

```bash
# 1. Ensure your local main is up to date
git checkout main
git pull origin main --rebase

# 2. Merge your feature into main
git merge feature/your-feature-name

# 3. Push the newly updated main back to the server
git push origin main

```
