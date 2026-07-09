Following the **Proposed Execution Framework**, I have prepared the **Implementation Package** for **Sprint 4.4 — System Integration Testing (SIT) Phases 3 through 7**. 

With the environment and authentication foundations (Phases 1 & 2) now verified and passed, we move into the **Functional Integration** of the core business engine. This phase validates that your specialized modules—RBAC, Product Catalog, Cart, and Orders—operate as a unified enterprise system.

### **Role 1: Architecture Review & Design Decisions**
*   **Objective:** Validate end-to-end data integrity and security enforcement across the full user journey (Browse → Cart → Checkout → Order History).
*   **Security (RBAC):** Verification that the **Authorization Interceptor** and **Route Guards** correctly return a 403 Forbidden status when a Customer attempts to access Admin resources.
*   **State Integrity:** Ensure the **Angular Signals** managing the Cart and Order states stay synchronized with the backend during high-frequency updates (e.g., rapid quantity changes).
*   **Data Validation:** Confirm the backend correctly handles atomic inventory deductions during the checkout transition to prevent overselling.
*   **Performance:** Loading skeletons must be verified for all data-heavy views (Product List and Dashboard) to maintain perceived speed.

---

### **Role 2: AI Coding Assistant Prompt (Implementation Engineer)**
**Instructions:** Copy and paste the block below into your **AI Coding Assistant** chat to facilitate these functional validations.

> **"Act as an Implementation Engineer.** We are executing **SIT Phases 3–7** for the Enterprise E-Commerce Platform. Our goal is to verify functional integration across the application.
>
> **Task: Functional SIT Validation (Phases 3–7)**
>
> **1. Authorization (Phase 3):**
> * Create a temporary test utility or update `AuthService` to simulate an RBAC violation. 
> * Verify that attempting to access `/api/v1/admin/orders` as a 'CUSTOMER' results in a 403 error and the UI remains stable.
>
> **2. Product & Cart Integration (Phases 4–5):**
> * Verify the **Signal-based** Cart service correctly recalculates `grandTotal` when adding/removing products or changing quantities.
> * Ensure the `LoadingSkeleton` displays correctly in the `ProductListComponent` until the `ProductService` returns data.
>
> **3. Checkout & Orders Handshake (Phases 6–7):**
> * Validate that the `CheckoutComponent` correctly maps the Cart items to the `POST /api/v1/orders` request payload.
> * Ensure that upon successful checkout, the user is redirected to the **Order History** page and the new order is immediately visible.
>
> **Technical Requirements:**
> * Maintain **OnPush Change Detection** and **Angular Signals**.
> * Use the **Enterprise Design System** (AppCard, StatusChip) for all UI verification components.
> * Do **NOT** modify the backend API logic.
>
> **Deliverables:**
> * A summary of the functional verification results for these modules.
> * Any necessary logic refinements in `CartService` or `OrderService` to ensure integration stability.
> * A unit test for the `OrderService` verifying the retrieval of the order history list."

---

### **Role 3: Next Action Items for You (Technical Lead / QA)**
Following our framework, your role is now to oversee the execution of these integration tests:

1.  **Run the Prompt:** Execute the instructions above with your AI coding assistant.
2.  **Perform the End-to-End SIT (Phases 3-7):**
    *   **RBAC Check:** Log in as a Customer and attempt to navigate to an Admin route; verify the **403 Forbidden** handling.
    *   **Cart Check:** Add 3 items, update quantities, and verify the total matches your manual calculation.
    *   **Checkout Check:** Complete a purchase and verify the inventory stock in the database is reduced by the exact amount purchased.
3.  **Audit for Regressions:** Ensure that these integrations haven't broken the **Environment Check** or **Auth Flow** verified in the previous SIT milestone.
4.  **Return to this Chat:** Provide the test results or any identified defects for my **Principal Architect Review**.

Once these business flows are verified, we will be ready to proceed to the **Internal Demo** and the final **RC1 Sign-off**. **How would you like to proceed with the testing?**
