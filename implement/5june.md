## Start of Day Commands

Go to project:

```bash
cd ~/projects/ecommerce-platform
```

Check status:

```bash
git status
```

Pull latest from GitHub:

```bash
git pull origin main
```

Verify:

```bash
git branch
```

Expected:

```text
* main
```

---

## After Making Changes

Check changed files:

```bash
git status
```

Commit:

```bash
git add .
git commit -m "Sprint-2 Authentication Foundation Started"
```

Push:

```bash
git push origin main
```

---

## Recommended Professional Workflow (Starting Sprint-2)

Instead of coding directly on `main`, use feature branches:

```bash
git checkout -b feature/auth-foundation
```

Work normally:

```bash
git add .
git commit -m "Add user model and JWT configuration"
git push origin feature/auth-foundation
```

When complete:

```bash
git checkout main
git pull origin main
git merge feature/auth-foundation
git push origin main
```

Benefits:

* Easy rollback
* Cleaner history
* Matches enterprise development practices
* Easier code reviews

---

## Useful Daily Commands Cheat Sheet

```bash
# Where am I?
pwd

# Current branch
git branch

# Check changes
git status

# Get latest code
git pull origin main

# Save work
git add .
git commit -m "message"

# Upload work
git push origin main

# See commit history
git log --oneline --decorate --graph -10

# See tags
git tag

# See containers
docker ps

# Start environment
docker compose up -d

# Stop environment
docker compose down
```

---

## Tomorrow's Startup Checklist

```text
✓ Open Docker Desktop
✓ Open Ubuntu/WSL
✓ cd ~/projects/ecommerce-platform
✓ git pull origin main
✓ docker compose up -d
✓ Verify /health
✓ Open VS Code (code .)
✓ Start Sprint-2 work
```

You've ended Sprint-1 in a clean state:

```text
✓ GitHub synced
✓ Tag created
✓ Docker working
✓ Backend working
✓ Frontend initialized
✓ Documentation prepared
```

That's exactly where you want to be before logging off. Have a good evening, and tomorrow you can start directly with **Sprint-2 Authentication & User Management Foundation**. 🚀

##
----

Excellent. Your environment passes the Foundation Setup prerequisites.

# Environment Verification Result

| Component          | Status |
| ------------------ | ------ |
| Docker Engine      | ✅ PASS |
| Docker Compose     | ✅ PASS |
| Python 3.10.11     | ✅ PASS |
| pip                | ✅ PASS |
| PostgreSQL Client  | ✅ PASS |
| Node.js 22.13.1    | ✅ PASS |
| npm                | ✅ PASS |
| Angular CLI 19.1.8 | ✅ PASS |
| WSL2               | ✅ PASS |

Current risk level:

```text
LOW
```

No upgrades required before Sprint-1.

---

# Important Recommendation Before Coding

Since Docker is running inside WSL2, I recommend keeping the source code inside Ubuntu rather than Windows.

Recommended:

```text
/home/<user>/projects/ecommerce-platform
```

Avoid:

```text
C:\Users\HP\...
```

Benefits:

* Faster Docker builds
* Faster npm install
* Better file watching for Angular
* Better Linux compatibility

---

# Sprint-1 Implementation Plan

We will complete the sprint in 7 milestones.

| Milestone | Objective                   |
| --------- | --------------------------- |
| M1        | Create Repository Structure |
| M2        | FastAPI Initialization      |
| M3        | PostgreSQL Setup            |
| M4        | Redis Setup                 |
| M5        | Angular Initialization      |
| M6        | Dockerization               |
| M7        | Verification                |

---

# Step 1 - Create Repository Structure

Inside Ubuntu:

```bash
mkdir -p ~/projects
cd ~/projects

mkdir ecommerce-platform
cd ecommerce-platform
```

Verify:

```bash
pwd
```

Expected:

```text
/home/<your-user>/projects/ecommerce-platform
```

---

## Create Base Folders

```bash
mkdir backend
mkdir frontend
```

Verify:

```bash
tree -L 1
```

Expected:

```text
.
├── backend
└── frontend
```

If `tree` is missing:

```bash
sudo apt install tree -y
```

---

# Step 2 - Initialize FastAPI Backend

Move into backend:

```bash
cd ~/projects/ecommerce-platform/backend
```

Create folders:

```bash
mkdir -p app/api
mkdir -p app/core
mkdir -p app/db
mkdir -p app/models
mkdir -p app/schemas
mkdir -p app/services
mkdir -p app/repositories
mkdir -p tests
```

Create files:

```bash
touch app/main.py
touch requirements.txt
touch Dockerfile
touch .env
```

Verify:

```bash
tree
```

Expected:

```text
backend
├── app
│   ├── api
│   ├── core
│   ├── db
│   ├── models
│   ├── repositories
│   ├── schemas
│   ├── services
│   └── main.py
├── tests
├── Dockerfile
├── requirements.txt
└── .env
```

---

# Step 3 - Add Backend Dependencies

Open:

```bash
nano requirements.txt
```

Paste:

```text
fastapi
uvicorn[standard]

sqlalchemy
psycopg2-binary

redis

python-dotenv

alembic

python-jose[cryptography]
passlib[bcrypt]

pydantic-settings
```

Save:

```text
CTRL+O
ENTER
CTRL+X
```

---

# Step 4 - Create Minimal FastAPI Application

Open:

```bash
nano app/main.py
```

Paste:

```python
from fastapi import FastAPI

app = FastAPI(
    title="E-Commerce Platform API",
    version="1.0.0"
)

@app.get("/")
def root():
    return {
        "message": "E-Commerce Platform API Running"
    }
```

Save and exit.

---

# Step 5 - Local Verification

Create virtual environment:

```bash
python3 -m venv venv
```

Activate:

```bash
source venv/bin/activate
```

Install:

```bash
pip install -r requirements.txt
```

Run:

```bash
uvicorn app.main:app --reload
```

Expected:

```text
INFO: Uvicorn running on http://127.0.0.1:8000
```

Open:

```text
http://localhost:8000/docs
```

Expected:

```text
Swagger UI loads successfully
```

---

# Checkpoint

At this point we should have:

* Repository created
* Backend structure created
* Dependencies installed
* FastAPI running
* Swagger UI working

**Do not proceed to PostgreSQL, Redis, Angular, or Docker yet.**

Please complete **Steps 1–5** and send:

1. Output of `tree`
2. Output of `uvicorn app.main:app --reload`
3. Screenshot or confirmation that `/docs` opens

Then I'll provide **Phase 2: PostgreSQL + Redis + Docker Compose setup**, which is where most projects encounter issues and where we'll verify container networking correctly.
