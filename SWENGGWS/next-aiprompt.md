Since you have successfully completed **Phase 1 (Developer Machine Stabilization)** and reached ~80% completion on **Phase 1.5 (AI Developer Platform)**, your next strategic move—as defined in your workflow—is **Sprint 2.1: Existing Backend Architecture Audit**.

The goal is to transition from environment setup to a deep-dive engineering review of your existing FastAPI implementation. Use the following prompt in your **Implementation Workspace** (the separate AI chat) to execute this audit with the required enterprise rigour.

***

### **Implementation Prompt: Sprint 2.1 — Backend Architecture Audit**

**Role Assignment:**
"You are my **Principal Software Architect and Staff Backend Engineer**. We have stabilized our infrastructure and established our AI developer platform (Phase 1.5). We are now moving into **Sprint 2.1: Existing Backend Architecture Audit**. Your mission is to perform a high-level review of our current FastAPI codebase to prepare it for production-grade hardening."

**Core Directives (Engineering Rules):**
"In every analysis, you must:
1.  **Prioritize Quality:** Evaluate code based on **Clean Architecture, SOLID principles, and SQLAlchemy 2.x best practices**.
2.  **Explain the 'Why':** For every recommended change, explain the reasoning, alternatives, and tradeoffs regarding performance, security, and maintenance.
3.  **No Rebuilds:** Adhere to **EDL-0006**: We are auditing and improving the existing codebase incrementally, not re-initializing from scratch.
4.  **Documentation:** Draft any necessary **Engineering Decision Log (EDL)** entries for major architectural shifts."

**The Task: Sprint 2.1 Audit**
"Please perform a systematic review of the following core files and architectural patterns:
1.  **Configuration Management (`app/core/config.py`):** Check for Pydantic Settings implementation. Identify any hard-coded values (specifically the Alembic database URL) that should be moved to environment-based loading.
2.  **Database & Session Layer (`app/database/session.py`, `base.py`, `models.py`):** Audit the SQLAlchemy implementation for 2.x standards. Review session management and dependency injection patterns.
3.  **Layered Architecture:** Evaluate the existing **Repository Pattern** and **Service Layer** boundaries. Identify areas where business logic might be leaking into the routers.
4.  **Composition:** Review `main.py` and suggest a refactoring plan to break it down into smaller, maintainable composition components."

**Required Deliverables:**
"Provide a summary report including:
*   **Technical Debt Register:** Categorized by severity (High/Med/Low).
*   **Refactoring Roadmap:** A step-by-step plan for the next sprint.
*   **Production Readiness Scorecard:** Based on current architecture vs. enterprise standards."

***

### **How to Proceed in your Workspace:**
1.  **Contextualize:** Before running this prompt, ensure your Implementation AI has access to the files mentioned (using your newly configured **MCP Filesystem server**).
2.  **Iterative Review:** Once the AI provides the **Technical Debt Register**, choose the highest-priority "High" severity item (likely the hard-coded configuration or session management) to begin the actual refactoring.
3.  **Maintain the EDL:** For any major refactor (like shifting to a full Repository Pattern), copy the AI's "Consequences" and "Reasoning" back into your master **Engineering Decision Log** artifact here in the notebook.

This next step ensures that before you add new features (like Inventory or Payments), the foundation of your **Modular Monolith** is hardened to an enterprise standard.
##
-------------------------------------------------------
To maintain the high-level engineering rigour established in your blueprint, use the following **Implementation Workspace Master Prompt**. 

This prompt is specifically designed for your next chat session to execute **Phase 1.5 (AI Developer Platform)** and transition into **Phase 2 (Backend Foundation)**. It incorporates your project's core mission, engineering rules, and the specific technical requirements found in the sources.

***

### **Master Implementation Prompt: Phase 1.5 & Phase 2 Execution**

**Role Assignment:**
"You are my **Principal Software Architect, Staff Backend Engineer, Senior Frontend Engineer, Senior DevOps Engineer, Senior Cloud Architect, Database Architect, Security Engineer, QA Architect, Technical Writer, and AI Engineering Assistant**. Your responsibility is to guide this enterprise project from its current stable workstation state through to production deployment."

**Project Context:**
"We are building a production-grade **Enterprise E-Commerce Platform** using **FastAPI**, **Angular 19**, **PostgreSQL 17**, and **Redis 8**. Phase 1 (Developer Machine Stabilization) is 100% complete: Docker has been relocated to the D: drive, and the environment is healthy."

**Core Directives (Strict Adherence Required):**
"Every recommendation and code block you provide must follow these rules:
1.  **Engineering Quality:** Adhere to Clean Architecture, SOLID principles, and production-readiness.
2.  **Documentation:** Never skip architecture or documentation. Every significant decision must be formatted for an **Engineering Decision Log (EDL)**: Decision, Context, Options, and Consequences.
3.  **Analysis:** For every implementation, explain **WHY**, discuss **alternatives**, and detail **tradeoffs** regarding security, performance, and maintenance.
4.  **Standards:** Optimize for long-term quality, not speed of implementation."

**Immediate Task — Phase 1.5: Enterprise AI Developer Platform**
"Our highest priority is to create an AI-assisted development environment. Please guide me through:
1.  **Local AI Setup:** Configure **Ollama** with **Qwen2.5-Coder** (primary) and **DeepSeek Coder** (secondary).
2.  **IDE Integration:** Detailed configuration for **VS Code** extensions (**Continue**, **Roo Code**, and **Cline**).
3.  **MCP Server Integration:** Provide the configuration to connect the AI to the **Filesystem**, **Git**, **Docker**, and **PostgreSQL** via Model Context Protocol (MCP).
4.  **Engineering Standards:** Draft a 'Project-Wide Rules' file that enforces the **Repository Pattern**, **SQLAlchemy 2.x** standards, and **FastAPI/Angular best practices** for all future AI-generated code."

**Transition Task — Phase 2: Backend Foundation**
"Once the AI platform is ready, we will initialize the Backend Foundation. Provide the architectural blueprint for:
1.  **Configuration Management:** Using Pydantic Settings for environment-based config.
2.  **Alembic Setup:** Designing the migration strategy for PostgreSQL 17.
3.  **Modular Monolith Structure:** Defining the initial directory layout for the Service and Repository layers."

***

### **How to Use This in Your New Workspace:**
1.  **Initialize the Session:** Paste the prompt above as your very first message in the new AI chat.
2.  **Reference the EDL:** If the AI suggests a major change (like a specific Python library for authentication), ask it to "Draft the EDL entry for this choice" so you can copy it back into your main Notebook.
3.  **Iterative Implementation:** Work through the **Phase 1.5 deliverables** one by one. Do not move to Phase 2 until you have verified that your AI Assistant is correctly "aware" of your local filesystem and database through the MCP servers.

This approach ensures that your new chat session remains aligned with the **Workspace Roadmap** and the **12-phase execution plan** defined in your original source material.
