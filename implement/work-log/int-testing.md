Following the **Proposed Execution Framework**, I have prepared the **Step 1: Implementation Package** for **Sprint 4.4 — System Integration Testing (SIT) Phases 1 & 2**. 

This milestone transitions the project from isolated unit testing to formal **Enterprise Environment and Security Validation**.

---

### **1. Architecture Review & Design Decisions**
*   **Objective:** Validate the stability of the entire "Modular Monolith" stack (Angular 19, FastAPI, PostgreSQL, Redis) and the integrity of the Authentication/RBAC lifecycle.
*   **Test Strategy:** We will utilize a combination of automated health checks and manual end-to-end "Success Path" walkthroughs.
*   **Security Integrity:** Validation must confirm that JWT and Refresh Tokens are strictly managed in local storage and that protected routes effectively block unauthorized access with 401/403 responses.
*   **Infrastructure Check:** The **Health Endpoint** (`/health`) must be the primary source of truth for backend readiness, returning an "UP" status for all three core services.

---

### **2. Implementation Details**
*   **Focus Areas:** Environment configuration, health endpoints, registration logic, login/logout flows, and JWT persistence.
*   **Files to Modify/Review:**
    *   `src/environments/environment.prod.ts` (Verification of production endpoints).
    *   `src/app/core/services/auth.service.ts` (Token storage and clearing logic).
    *   `src/app/core/guards/auth.guard.ts` (Route protection validation).
*   **Files to Create:** 
    *   `src/app/testing/sit-manifest-ph1-2.json` (A structured checklist for automated/manual SIT results).

---

### **3. Role 2: AI Coding Assistant Prompt (Implementation Engineer)**
Copy and paste the block below into your **AI Coding Assistant** chat:

> **"Act as an Implementation Engineer.** We are executing **Sprint 4.4: System Integration Testing (SIT)** for an Enterprise E-Commerce Platform.
>
> **Task: SIT Phases 1 & 2 (Environment & Auth Validation)**
>
> **1. Environment Validation (Phase 1):**
> * Create a diagnostic script or utility in the Angular frontend that pings the FastAPI `/health` endpoint and verifies a `{ "api": "UP", "postgres": "UP", "redis": "UP" }` response.
> * Ensure the production build (`ng build`) executes without style or budget errors, especially for the shared Design System components.
>
> **2. Authentication Flow Validation (Phase 2):**
> * Review the `AuthService` to ensure that on `logout()`, all tokens (access and refresh) are explicitly cleared from LocalStorage and the **Signal-based** auth state is reset.
> * Implement a brief 'Security Audit' log in the console (for development only) that prints the presence/absence of tokens during navigation transitions to protected routes.
>
> **Technical Requirements:**
> * Maintain **OnPush Change Detection** and **Angular Signals** for UI state.
> * Ensure the **LoadingSkeleton** is correctly triggered during the environment check.
> * Do **NOT** change the existing API endpoints or database schema.
>
> **Deliverables:**
> * A diagnostic component/service for Phase 1.
> * Refined `AuthService` logic for Phase 2.
> * A brief unit test suite verifying that `AuthGuard` correctly redirects to `/login` when tokens are missing."

---

### **4. Acceptance Criteria (Definition of Done)**
*   ✅ **Phase 1 Pass:** Backend health returns "UP" for all services; Frontend production build succeeds.
*   ✅ **Phase 2 Pass:** A new user can register, login, and access the Dashboard; Logout successfully redirects and clears all local security state.
*   ✅ **Zero Redundancy:** No console errors or runtime exceptions during the "Success Path" walkthrough.
*   ✅ **Documentation:** Results are logged in the **SIT Execution Report**.

---

### **5. Next Steps for You (Role 3 — Technical Lead / QA)**
1.  **Execute the Prompt:** Run the prompt with your AI assistant.
2.  **Verify the Stack:** Ensure your **Docker Compose** containers are all healthy before starting tests.
3.  **Perform the Walkthrough:** Manually execute the "Register → Login → Logout" journey.
4.  **Return here** with any defects found or a confirmation that Phase 1 & 2 are **PASS**.

**I will wait for your report on the Environment and Auth flows before we proceed to Phase 3: RBAC and Product Module SIT.**
