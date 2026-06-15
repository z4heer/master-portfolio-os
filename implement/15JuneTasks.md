Acknowledged. With your administrative and tracking status updated in your **Technical Execution Workbook** and **Portfolio OS**, we will now shift to a **"Backend-First" execution strategy**. You will complete the entire backend for the next functional requirement—the **Product Catalog**—before beginning the frontend implementation.

Based on the **Sprint 3 (Product Module)** requirements defined in your blueprints, here are your actionable steps to complete the Product backend.

### **1. Database & Infrastructure Actions**
Before building APIs, you must establish the data structures for your products and inventory.

*   **Define Entities:** Create the SQLAlchemy models for the `products` and `inventory` tables.
    *   **Products Table:** Include `id` (UUID), `name`, `description`, `price`, `category`, and `image_url`.
    *   **Inventory Table:** Establish a one-to-one relationship with products, tracking `stock_quantity` and `reserved_quantity`.
*   **Execute Migrations:** Use **Alembic** to generate and run the migration script for these new tables. This addresses the technical debt identified in your earlier report.
*   **Seed Initial Data:** Create a script to populate the database with generic products to test your search and filtering logic.

### **2. API Development Actions (FastAPI)**
Following the **Repository and Service Layer patterns**, develop the following endpoints for the Product Catalog:

*   **Public Catalog APIs:**
    *   `GET /api/v1/products`: Implement search, category filtering, and price-range filtering.
    *   `GET /api/v1/products/{id}`: Fetch detailed information for a single product.
*   **Admin Management APIs (Protected by RBAC):**
    *   `POST /api/v1/products`: Create new items (Admin only).
    *   `PUT /api/v1/products/{id}`: Update product details and inventory levels.
    *   `DELETE /api/v1/products/{id}`: Soft delete or remove items from the catalog.
*   **Performance Enhancement:** Integrate **Redis caching** into the `GET /products` endpoint to ensure <1s response times for catalog browsing as defined in your KPIs.

### **3. AI Developer Role Prompt (Backend Focus)**
Use this command to activate your **Developer AI** to generate the production-grade backend code for this module:

> "Act as a **Senior FastAPI Developer**. We are implementing the **Product Catalog Backend** using a Backend-First strategy.
>
> **Task Requirements:**
> 1. **Models:** Create SQLAlchemy models for `Product` and `Inventory` with UUIDs and appropriate relational links.
> 2. **Repository Layer:** Implement a `ProductRepository` that handles complex filtering (by category, price range, and name search).
> 3. **Service Layer:** Build a `ProductService` that integrates **Redis caching**. It should check Redis for the 'all_products' key before hitting the database.
> 4. **API Router:** Generate the FastAPI router for the endpoints defined in the API Catalog, ensuring proper **RBAC (Admin only)** for creation and update methods.
> 5. **Migrations:** Provide the Alembic migration command template to initialize these tables."

### **Next Steps Following Backend Completion**
Once these backend tasks are verified (using Postman or your Swagger UI at `/docs`), your next action will be the **Frontend Implementation**:
*   Building the **Product Listing** and **Search Bar** in Angular.
*   Developing the **Product Details** view.
*   Integrating the frontend with the newly created backend APIs.

By completing the backend first, you ensure that the "Source of Truth" for your data and business logic is fully established and testable before you start building UI components.
