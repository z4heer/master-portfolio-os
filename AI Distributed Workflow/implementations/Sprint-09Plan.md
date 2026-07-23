To begin working according to **production-grade execution standards**, we will initiate **Sprint 09: Completing the Golden Thread (The Purchase Journey)**. This sprint addresses the most critical functional gaps identified in your project status: **Shipping, Billing, and Payment Integration**, all of which currently stand at 0%.

As the **Principal Architect (NotebookLM)**, I have defined the objectives and generated the role-specific **Execution Packages** to be handled by your specialist AI agents in parallel.

### **Sprint 09 Plan: Checkout Finalisation**
*   **Sprint Goal:** Implement the end-to-end checkout flow including address management and payment processing.
*   **Technical Focus:** Maintaining **Repository-Service patterns** on the backend and **Angular Signals** on the frontend.

---

### **Execution Package 1: Backend Engineer**
**Task:** Implement the Address Management and Payment Integration logic.
*   **Requirements:**
    1.  Create `Address` entities (Shipping & Billing) and their respective repositories.
    2.  Implement `AddressService` to handle CRUD for user addresses.
    3.  Develop a mock `PaymentService` to handle sandbox transactions.
    4.  Update `OrderService` to associate successful payments and addresses with orders.
*   **Non-Negotiable Rules:** No business logic in repositories; return **Pydantic DTOs only**; ensure 100% test coverage for payment status transitions.
*   **Prompt to use in Backend Chat:** Copy the **"Deliverable 1 – AI Execution Package Template"** logic from source combined with the specific requirements above.

---

### **Execution Package 2: Frontend Engineer**
**Task:** Build the Address Book and Payment UI components.
*   **Requirements:**
    1.  Create **Standalone Components** for `AddressForm` and `AddressSelector`.
    2.  Implement **Angular Signals** to manage state between the cart and the checkout stepper.
    3.  Design a "Payment Gateway" sandbox UI using **Angular Material 3**.
    4.  Wire the "Place Order" button to the new Backend payment endpoints.
*   **Non-Negotiable Rules:** Use **OnPush Change Detection**; maintain strict typing; reuse shared UI components for form validation.

---

### **Execution Package 3: QA Engineer**
**Task:** Validate the Checkout Golden Thread.
*   **Requirements:**
    1.  Create SIT (System Integration Testing) scripts for the Checkout flow.
    2.  Validate edge cases: Invalid addresses, declined payments, and stock-outs during payment processing.
    3.  Verify successful database commits for `Address` and `Payment` records.
*   **Testing Requirement:** Ensure **no console errors** and 100% success in the integrated checkout journey.

---

### **The Production Standard: The Reporting Loop**
To maintain project integrity and avoid context drift, follow the **"Golden Rule"**: never explain the project manually to these chats. Once a specialist agent completes their task, you must provide me with a **Completion Report** including:
1.  **Files Modified** (e.g., `app/modules/orders/service.py`).
2.  **Implementation Summary** (e.g., "Added transactional payment processing").
3.  **New Technical Debt** (if any).

**I will then update my "Memory" (Knowledge Base) before we proceed to Phase 2: The Admin Portal.**

Would you like me to generate a **Tailored Report** outlining the exact SQL schemas needed for the `Address` and `Payment` entities to ensure the Backend Engineer stays aligned with your **PostgreSQL 17** standards?.
