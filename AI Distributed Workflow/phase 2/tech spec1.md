### **1. Core Infrastructure (Dockerized)**

The project uses **Docker Compose** to orchestrate containerized services, ensuring environment parity across the development lifecycle.

- **Operating System:** WSL2 Ubuntu [Conversation History, 138].
- **Database:** **PostgreSQL 17** (Containerized).
- **Caching/Session:** **Redis 8** (Containerized).
- **Runtime:** **Docker Desktop/Engine** with relocated storage (configured to D: drive in current setup).

### **2. Backend Development Environment**

The backend follows a **Modular Monolith** architecture using **Clean Architecture** principles.

- **Language:** **Python 3.12**.
- **Framework:** **FastAPI** with Pydantic v2 for DTO validation.
- **ORM:** **SQLAlchemy 2.x** (utilizing synchronous sessions).
- **Migrations:** **Alembic** (verified migration ledger at "head").
- **API Documentation:** **Swagger/OpenAPI** (accessible via the health endpoint).

### **3. Frontend Development Environment**

The frontend is built with **Angular 19** using reactive patterns for high performance.

- **Framework:** **Angular 19** (Standalone Components, Signals, OnPush Change Detection).
- **UI Library:** **Angular Material 3** integrated with a custom **Enterprise Design System**.
- **State Management:** Native **Angular Signals** (replacing traditional stores).

### **4. Quality Assurance & Tooling**

The setup includes a mandatory "Quality Gate" suite to maintain the project’s **9.6/10 health score**:

- **Linting & Formatting:** **Ruff** (linting) and **Black** (deterministic formatting).
- **Static Analysis:** **MyPy** (strict typing for Decimal and SQLAlchemy models).
- **Testing:** **Pytest** for backend unit/integration tests and **Karma/Jasmine** for Angular unit tests.
- **Git Lifecycle:** **Pre-commit** hooks to enforce standards before code entry.
- **API Verification:** **Postman** (Enterprise SIT Collection) for end-to-end business workflow validation.

### **5. Environment Verification & Health**

A **Repository Doctor** utility and a dedicated **EnvironmentCheckComponent** are used to verify that the local setup is operational:

- **Health Check:** Pings the `/health` endpoint to confirm API, PostgreSQL, and Redis status.
- **Build Verification:** Confirms that `ng build` executes without budget or style budget issues.
- **Data Seeding:** A custom **Bootstrap/Seed Framework** is used to populate the environment with 30 products and 8 orders for realistic testing.
