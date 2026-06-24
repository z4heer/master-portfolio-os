To use your **Career Operations Center** effectively, you should leverage the different personas established in your sources. Each role serves a specific purpose in your 90-day plan, from high-level strategy to deep technical execution.

Below are the recommended roles and the specific prompts you should use to engage them, based on the documentation you have uploaded.

### 1. The Operations Manager (Strategy & Discipline)
**Purpose:** To keep you focused, prioritize your daily tasks, and enforce the "no distraction" rule.
*   **Context:** This role is governed by your `Operations_Manager_Charter.md`.
*   **When to use:** Every morning during your **Start Day (5 min)** routine.
*   **Proper Prompt:**
    > "Acting as my **Operations Manager** under the guidelines of my Charter, review my `Current_Status.md` and `90_Day_Plan.md`. What are my **top 3 priorities today** to ensure I stay on track for a ₹18L–25L role? Apply the **Decision Rule**: if a task doesn't directly improve my portfolio, resume, or interview readiness, flag it for postponement."

### 2. The Solution Architect (Technical Design)
**Purpose:** To ensure your project remains "portfolio-grade" and follows enterprise standards like the Repository Pattern or ACID compliance.
*   **Context:** This role uses the blueprints found in your `Architecture Decision Record.md` and `System Design.md`.
*   **When to use:** Before starting any new module (like the **Cart Module**) or when documenting your architecture.
*   **Proper Prompt:**
    > "Act as a **Solution Architect**. Review the `Architecture Decision Record.md` and the `Sequence Diagrams.md`. I am about to implement the **Cart Module**. Based on our 'Modular Monolith' architecture, what are the specific **Repository and Service layer patterns** I should follow to ensure this is enterprise-grade?"

### 3. The Technical Mentor / Senior Lead (Code Quality & Security)
**Purpose:** To critique your implementation, find security gaps (like the resolved **KL-001**), and prepare you for technical deep-dives.
*   **Context:** Uses technical specs from `Tech Spec Doc- EComm.md` and `PR Description.md`.
*   **When to use:** After finishing a feature or when preparing for an interview topic where your progress is low (e.g., **System Design** or **Redis** at 40-50%).
*   **Proper Prompt:**
    > "Acting as a **Senior Python Lead**, review my recent `Project Progress Report.md`. I am currently at 40% readiness for **System Design**. Challenge me with three 'Senior-level' interview questions regarding our current **PostgreSQL 17** and **Redis 8** integration, specifically focusing on how we handle **Atomic Inventory Validation**."

### 4. The Career Coach (Branding & Applications)
**Purpose:** To refine your public-facing image and ensure your resume/LinkedIn match your target salary of ₹18L–25L.
*   **Context:** Driven by `Target_Roles.md` and your `Career_Mission.md`.
*   **When to use:** During your **Work Block 3 (1 hr)** dedicated to job searching.
*   **Proper Prompt:**
    > "Act as my **Career Coach**. Review my `Career_Mission.md` and the 'Success Criteria for Next 30 Days'. Based on the technical wins in **Sprint 4** (RBAC, Inventory Logic), give me three specific bullet points for my **Resume** and a new **LinkedIn headline** that will attract recruiters for high-paying remote Python roles."

### **Pro-Tip for NotebookLM:**
Always start your prompt by explicitly naming the role you want the AI to play. This "anchors" the response to the specific logic and constraints found in that part of your documentation. For example, the **Operations Manager** will be much stricter about your time than the **Solution Architect**, who might encourage more complex (and time-consuming) designs.
