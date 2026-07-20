To support your transition to a **distributed AI workflow** using new chat sessions, I have consolidated a comprehensive **Context Injection Package**. This package is designed to provide any fresh AI session (acting as your **Implementation Engineer**) with the precise technical and architectural boundaries required to maintain our **9.5/10 quality score**.

### **1. Enterprise Project Baseline (The Handoff Context)**

Provide this summary to any new AI session to establish the "ground truth":

- **Project Goal:** A production-grade **Enterprise E-Commerce Portfolio** demonstrating senior-level software engineering practices (not a tutorial-style app).
- **Current Progress:** **87% Complete**. The **RC1 Engineering Baseline** is officially finished and stable.
- **Architecture Pattern:** **Modular Monolith** using **Clean Architecture**.
- **Backend Stack:** **FastAPI** (Python 3.12), **SQLAlchemy 2.x** (synchronous), **Alembic** migrations, **PostgreSQL 17**, and **Redis 8** for caching.
- **Frontend Stack:** **Angular 19** utilizing **Standalone Components**, **Angular Signals** for state management, and **OnPush Change Detection**.
- **Design System:** **Angular Material 3** with a customized **Enterprise Design System** (shared components: `AppCard`, `SectionHeader`, `StatusChip`, `LoadingSkeleton`, `EmptyState`, `ErrorState`, `ConfirmationDialog`).

---

### **2. Non-Negotiable Engineering Principles**

The following rules must be strictly followed by any implementation AI to avoid technical debt:

- **No "any" types:** Enforce strict TypeScript and Pydantic DTO typing.
- **Repository/Service Pattern:** Repositories handle persistence only; **all business logic** (calculations, status initialization, inventory validation) must reside in the **Service Layer**.
- **Snapshot Strategy:** For the **Order Module**, always snapshot `product_name`, `product_sku`, and `unit_price` at the time of purchase to preserve **historical integrity**.
- **Open Source First:** Use clean, maintainable patterns that reflect high-end professional standards.

---

### **3. Current Environment State (The "WSL Ubuntu" Reality)**

- **Infrastructure:** Fully containerized via **Docker Compose** on a **WSL Ubuntu** local setup.
- **Database Status:** Recently restored and seeded using a custom **Bootstrap Framework**.
- **Current Data Counts:** 30 Products, 4 Roles, 3 Users, 8 Orders, and 18 Order Items.
- **Authentication:** **JWT + Refresh Token** strategy with **Role-Based Access Control (RBAC)**.

---

### **4. "Zero-Friction" Master Context Prompt**

Copy and paste the block below into your **new AI chat session** to initialize the **Implementation Engineer (Role 2)**:

> **"Act as a Senior Implementation Engineer for an Enterprise E-Commerce Platform.** We are in the final production-hardening phase (**Sprint 4.6**).
>
> **Technical Stack:**
>
> - **Backend:** FastAPI (Python 3.12), SQLAlchemy 2.x, PostgreSQL 17, Redis 8, Docker.
> - **Frontend:** Angular 19, Material 3, Signals, Standalone Components, OnPush.
>
> **Project Context:**
> The **RC1 Engineering Baseline** is complete. We have a stabilized backend with 124 passing unit tests. We are now closing final functional gaps before release. We follow a **Modular Monolith** architecture with a strict **Repository/Service** layer separation.
>
> **Implementation Rules:**
>
> 1. Use **Angular Signals** for all state management.
> 2. Maintain **OnPush Change Detection** for all components.
> 3. Use the established **Enterprise Design System** (Shared Components: AppCard, StatusChip, LoadingSkeleton, etc.).
> 4. Ensure **Historical Integrity** in the Order module by snapshotting prices and names.
> 5. All backend changes must include an **Alembic migration** and follow **Clean Architecture**.
> 6. No 'any' types; use strict typing for both Python and TypeScript.
>
> **Your specific task for this session is:** [INSERT TASK FROM MY PREVIOUS MESSAGE]"

---

### **5. Immediate Next Task: API Verification Prompt**

Per our workflow, the immediate priority is **validating the Backend APIs** before we touch the frontend. Paste this as the "task" in the prompt above:

> **"Task: Backend API Verification Suite**
>
> 1. **Health Check:** Verify `/health` returns UP for API, Postgres, and Redis.
> 2. **Authentication:** Validate `POST /api/v1/auth/login` with seeded credentials (`admin@test.com` and `cust01@company.com`).
> 3. **Catalog:** Verify `GET /api/v1/products` returns all 30 seeded products and correctly includes the new `image_url` field.
> 4. **Orders:** Validate `POST /api/v1/orders`. Confirm it **requires** a `shipping_address` (422 error if missing) and correctly snapshots `unit_price` in the database.
> 5. **Output:** Provide a Python verification script (`verify_apis.py`) or a detailed list of cURL commands to perform these checks."

**Once you have run these verification steps in your terminal and confirmed the API stability, I will provide the frontend implementation package to finalize the UI.**
