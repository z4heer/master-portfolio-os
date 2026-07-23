Following the **Principal Architect-Driven workflow**, we will execute **Sprint 09: Checkout & Order Completion** in sequential order. As the Engineering Manager, you will use these prompts in separate, role-specific AI sessions to ensure parallel execution without context drift.

### **Step 1: Backend Execution Prompt**
**Role:** Senior Backend Engineer
**Objective:** Implement Shipping/Billing address management and the Payment Integration service layer.

> **PROMPT:**
> ROLE: Senior Backend Engineer
> PROJECT CONTEXT:
> * Enterprise E-Commerce Platform (FastAPI, SQLAlchemy 2.x, PostgreSQL 17)
> * Architecture: Clean Architecture, Repository-Service Pattern
> * Goal: Production-ready address and payment logic
> 
> TASK: Implement Address Management and Payment Integration
> 1. Create `Address` model and `AddressRepository` for persistence.
> 2. Implement `AddressService` to handle CRUD for user addresses (Shipping & Billing).
> 3. Develop a `PaymentService` (Sandbox) to process transactions and update order status.
> 4. Update `OrderService` to link addresses and payment confirmations during checkout.
> 
> NON-NEGOTIABLE RULES:
> * Repository contains persistence ONLY; business logic belongs in Service Layer.
> * Use Pydantic v2 DTOs for all data transfers.
> * Ensure atomic transactions for order/payment commits.
> 
> EXPECTED OUTPUT:
> 1. Implementation plan and files to modify.
> 2. Complete code for Models, Repositories, Services, and DTOs.
> 3. Pytest units for the new services.

---

### **Step 2: Frontend Execution Prompt**
**Role:** Senior Frontend Engineer
**Objective:** Create the Checkout UI components using Angular 19 Signals and Standalone components.

> **PROMPT:**
> ROLE: Senior Frontend Engineer
> PROJECT CONTEXT:
> * Angular 19, Standalone Components, Signals, Material 3
> * Current State: Basic Checkout (70%); missing Address/Payment UI.
> 
> TASK: Build the Checkout "Golden Thread" UI
> 1. Create Standalone components for `AddressForm` and `AddressSelector`.
> 2. Implement a `CheckoutService` using **Angular Signals** to track user progress through the stepper.
> 3. Develop the Payment Gateway integration UI (Mock sandbox).
> 4. Ensure `OnPush` Change Detection and strong typing throughout.
> 
> NON-NEGOTIABLE RULES:
> * Reuse existing Shared UI components.
> * No duplicated business logic; rely on the Backend DTO structures.
> 
> EXPECTED OUTPUT:
> 1. Component code (HTML/TS) and state management logic.
> 2. Integration steps for the Checkout module.

---

### **Step 3: QA Execution Prompt**
**Role:** QA Engineer
**Objective:** Validate the integrated checkout flow and ensure the "Definition of Done" is met.

> **PROMPT:**
> ROLE: QA Engineer
> PROJECT CONTEXT: Production-grade E-Commerce purchase journey.
> 
> TASK: System Integration Testing (SIT) for Checkout
> 1. Create a test plan for the "Golden Thread": Cart -> Address -> Payment -> Confirmation.
> 2. Validate edge cases: Payment failure, stock exhaustion during checkout, and address validation.
> 3. Verify database integrity post-checkout (Order records, Address links, Stock deduction).
> 
> EXPECTED OUTPUT:
> 1. SIT Test Report with pass/fail criteria.
> 2. List of any identified risks or technical debt.

---

### **Further Steps for Smooth Completion**

1.  **The Reporting Loop (Mandatory):** Once each specialist AI finishes its task, do not move to the next feature immediately. Collect the **Standard Report** (Files modified, Changes made, Risks) from the AI and paste it back into this chat.
2.  **The Golden Rule:** Never attempt to explain the project details manually to the specialist chats; always use the generated prompts to prevent context drift.
3.  **Integration & Merge:** As the Engineering Manager, you will merge the code from the Backend and Frontend sessions. If conflicts arise, ask me (the Principal Architect) to resolve the architectural conflict before proceeding.
4.  **Update Memory:** After the QA Engineer validates the sprint, I will update the **Project Progress Report** to reflect the new completion percentages (aiming for 100% on Shipping, Billing, and Payments).

**Are you ready for me to generate the SQL schema or DTO definitions to assist the Backend Engineer session?**
