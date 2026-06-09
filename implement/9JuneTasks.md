To execute your tasks for **Sprint 2: Authentication Foundation** as the Lead Developer, you should use the following specialized command. This prompt is designed to activate the **Developer AI** role defined in your "Team with AI OS" and ensure the output aligns with the **Service Layer and Repository patterns** established in your architecture blueprints.

### **AI Developer Role Command**

Copy and paste this command into your AI coding assistant:

> "Act as a **Senior Python (FastAPI) and Angular Developer**. We are beginning **Sprint 2: Authentication Foundation** for our Enterprise E-Commerce Modular Monolith. 
>
> **Project Context:**
> * **Backend:** FastAPI, PostgreSQL 17, SQLAlchemy 2.0, Alembic.
> * **Security:** JWT with Refresh Tokens, Password Hashing (Passlib/Bcrypt).
> * **Frontend:** Angular 19, Angular Material.
> * **Architecture:** Repository and Service Layer Patterns.
>
> **Your Task:**
> 1. **Database Schema:** Generate the SQLAlchemy models for `User` and `Role` entities. Include fields for `id` (UUID), `email` (unique), `password_hash`, and a relationship to the `Role` table.
> 2. **Backend Logic:** Create the `AuthRepository` for database access and the `AuthService` to handle password verification and JWT token generation (Access + Refresh).
> 3. **API Endpoints:** Draft the FastAPI router for `/register` and `/login`. Ensure endpoints are documented for Swagger/OpenAPI.
> 4. **Frontend Foundation:** Generate the Angular `AuthService` with methods for `login()`, `logout()`, and `handleTokenRefresh()`. 
>
> Please provide the code in a modular structure that follows the existing folder hierarchy established in Sprint 1. Ensure all code is **stateless and production-grade**."

---

### **Why this command works for today’s goals:**
*   **Targeted Sprint 2 Scope:** It specifically targets the `User` and `Role` entities and JWT logic identified in your **Recommended Sprint-2 Scope**.
*   **Enforces Architectural Standards:** By specifying the **Repository and Service Layer patterns**, it prevents the AI from generating "tutorial-style" flat code, keeping your project at the **Solution Architect** level.
*   **Ensures Technical Parity:** It references the exact versions (PostgreSQL 17, Angular 19) verified in your **Sprint-1 Day 2 Completion Report** to ensure environment compatibility.

### **Your Next Step (Human Responsibility):**
As the **Lead Developer**, once the AI generates this code, your responsibility is to **Review and Approve** it before implementation. Specifically, verify that the **Alembic migration script** is created to reflect the new `users` and `roles` tables in your PostgreSQL container.
