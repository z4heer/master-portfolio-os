##
This prompt is designed to activate the **Lead Developer AI** role, focusing on the **Cart & Orders Backend Module** (Action Item 4B). It incorporates your existing "Service Layer" and "Repository" patterns and addresses the specific requirements for inventory validation and order management found in your blueprints.

### **AI Lead Developer Command: Action Item 4B**

Copy and paste the following into your AI coding assistant:

> "Act as a **Lead Python Developer** specialized in **FastAPI** and **SQLAlchemy 2.0**. We are starting the **Cart & Orders Backend Module** for our Enterprise E-Commerce platform. 
>
> **Project Status:** 
> * **Completed:** Auth (JWT/RBAC) and Product Catalog (with Redis caching).
> * **Architecture:** Modular Monolith using **Service Layer** and **Repository** patterns.
> * **Tech Stack:** PostgreSQL 17, Redis 8, Docker.
>
> **Task Requirements:**
> 1. **Database Schema (SQLAlchemy):** 
>    * Create an `Order` model: `id` (UUID), `user_id` (FK to Users), `total_amount` (Numeric), `status` (Enum: PENDING, COMPLETED, CANCELLED), and `created_at`.
>    * Create an `OrderItem` model: `id` (UUID), `order_id` (FK to Orders), `product_id` (FK to Products), `quantity` (Integer), and `unit_price` (Numeric - must store the price at the time of purchase).
>
> 2. **Inventory Validation Service:** 
>    * Implement a logic in the `InventoryService` to validate stock levels. Before an order is created, it must check the `inventory` table to ensure `stock_quantity >= requested_quantity` for every item in the cart.
>    * If validation passes, create the order and **atomically decrement** the `stock_quantity` in the `inventory` table.
>
> 3. **API Development:** 
>    * Implement `POST /api/v1/orders`: This endpoint must require a **JWT (CUSTOMER role)**. It should accept a list of product IDs and quantities, trigger the inventory validation, and return the created order details.
>
> 4. **RBAC Fix (Security Alignment):** 
>    * Ensure that the `POST /api/v1/products` and `PUT /api/v1/products/{id}` endpoints created in the previous sprint are strictly enforced for the **ADMIN** role only.
>
> 5. **Alembic Migrations:** 
>    * Generate the script for the new `orders` and `order_items` tables.
>
> Please provide the solution in a modular format consistent with our established directory structure (repositories, services, schemas, and routers)."

---

### **Why this prompt is effective for Action Item 4B:**
*   **Inventory Accuracy:** It specifically targets the **>99% Inventory accuracy KPI** defined in your BRD by requiring atomic transactions and strict stock validation.
*   **Solution Architect Standards:** By using **UUIDs** and a **Service Layer**, the code remains decoupled and enterprise-grade, moving away from simple "tutorial" logic.
*   **Security Debt Management:** It forces the AI to address the **deferred RBAC issue** identified in your recent integration tests, ensuring your security model is tightened before moving forward.
*   **Transactional Integrity:** It requires the "unit_price" to be saved in the `Order_Items` table, reflecting a real-world business requirement where product prices may change over time but must remain fixed for historical orders.
##
Your plan to finalize the Product Module and transition into the **Cart & Orders** backend is a significant step toward completing your flagship project. Below are the specific AI prompts designed to help you execute these three critical tasks with "Solution Architect" level precision.

### **Prompt 1: Full-Cycle Backend Integration Testing (Plan Item 3)**
This prompt activates a **QA Engineer AI** role to verify that your stateless architecture, caching, and RBAC are functioning perfectly before you lock in your current progress.

> "Act as a **Senior QA Engineer**. I have completed the backend for the **Authentication** and **Product Catalog** modules of an Enterprise E-Commerce platform using **FastAPI, PostgreSQL, and Redis**. 
>
> Please provide a step-by-step **Integration Test Suite** that I can execute via Swagger UI or Postman to verify the following:
> 1. **End-to-End Auth Flow:** Registering an Admin, logging in, and verifying the `access_token` and `refresh_token` structures.
> 2. **Transactional Integrity:** Creating a product as an Admin and verifying it appears in both the `products` and `inventory` tables in PostgreSQL.
> 3. **Cache Validation:** Fetching the product list and verifying the `all_products` key exists in the Redis cache.
> 4. **RBAC Security Test:** Attempting to update a product using a 'Customer' role token to ensure the system returns a `403 Forbidden` error.
>
> For each test, provide the expected JSON response and a 'Solution Architect Tip' on what to look for in the logs."

### **Prompt 2: Professional Git Workflow: PR & Merge (Plan Item 2)**
This prompt helps you document your technical progress for potential employers by following an enterprise-grade Git lifecycle.

> "Act as a **Senior DevOps Engineer**. I have completed the 'Product Catalog' feature branch and verified it through integration testing. I am ready to merge this into the `main` branch. 
>
> Please guide me through:
> 1. The terminal commands to ensure my local `main` is up to date and my feature branch is ready.
> 2. Drafting a professional **Pull Request (PR) description** for my GitHub repository. Include sections for 'Changes Introduced' (Auth, Products, Redis Caching, RBAC), 'Verified Results' (Swagger Health Check), and 'Definition of Done Compliance'.
> 3. Best practices for a 'Squash and Merge' to keep my `main` branch commit history clean for portfolio reviews."

### **Prompt 3: Backend Development for Cart & Orders (Plan Item 4B)**
This prompt activates the **Lead Developer AI** to begin the next functional module, focusing on database schema design and business logic as defined in your BRD.

> "Act as a **Lead Python Developer**. We are moving into the **Cart & Orders Backend Module**. We are using the **Repository and Service Layer patterns**.
>
> **Task Requirements:**
> 1. **Database Schema:** Generate SQLAlchemy models for `Orders` and `Order_Items`. `Orders` should track `total_amount` and `status`; `Order_Items` must link `order_id` to `product_id` and store `unit_price` at the time of purchase.
> 2. **Inventory Logic:** Draft a Python service for **Inventory Validation**. It must check the `inventory` table's `stock_quantity` for every item in a proposed order before allowing the transaction to proceed.
> 3. **API Endpoints:** Create the FastAPI router for `POST /api/v1/orders`. This endpoint must be protected by JWT and include a call to the Inventory Validation service.
> 4. **Alembic:** Provide the migration steps to initialize these new tables without losing existing User or Product data."

### **Why these steps matter today:**
*   **Item 3 (Testing):** Ensures your core architecture is bug-free before adding the complexity of transactional order logic.
*   **Item 2 (PR/Merge):** Marks your **Product Module** as "Done" in your **Technical Execution Workbook** and creates a permanent record of your professional workflow on GitHub.
*   **Item 4B (Cart & Orders):** Begins the most complex part of the backend—handling state changes and data relationships—moving you closer to a functional MVP.

**Which of these would you like to review first after the AI generates the responses?** I recommend starting with the **Integration Test** to confirm your current stability.
