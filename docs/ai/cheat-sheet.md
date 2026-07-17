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
