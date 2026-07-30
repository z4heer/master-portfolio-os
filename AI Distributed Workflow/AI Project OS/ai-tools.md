To complete these projects in the **fastest possible timeframe** with the **lowest possible software costs**, you need an execution strategy that maximizes **free tier credits** and uses **agentic code generators** to write boilerplate for you.

Here is the exact cost-minimization breakdown and a week-by-week speed execution blueprint.

---

## 1. Zero-Dollar / Minimum Cost Tooling Strategy

You do **not** need expensive subscriptions like Cursor Pro, GitHub Copilot, or OpenAI Team plans to build this. You can run 100% of your stack on high-allowance free tiers:

| Component | Recommended Tool | Cost | Optimization Strategy |
| --- | --- | --- | --- |
| **IDE & AI Coding Agent** | **Google Antigravity 2.0 / CLI** | **$0** | Antigravity is free in preview and includes high rate-limit access to **Gemini 3.1 Pro / Flash**. Use it as your primary coding environment to generate full modules, test suites, and frontend components. |
| **LLM APIs (Backend)** | **Google AI Studio (Gemini 1.5 Flash & Pro)** | **$0** | Use the free tier of Google AI Studio for dev/testing. Flash handles vision/OCR and structured JSON parsing with zero cost up to ~15 RPM. |
| **Database & Vector Search** | **Local Docker / Cloud SQL Free Tier** | **$0** | Run PostgreSQL 17 locally with the `pgvector` container during development. Deploy to GCP Cloud SQL free trial ($300 credits) for showcase demos. |
| **Hosting & Deployment** | **GCP Cloud Run + Firebase Hosting** | **$0** | Scale to zero when inactive. Cloud Run gives 2 million free HTTP requests per month. |
| **Background GitHub Agent** | **Jules (Public Beta)** | **$0** | Assign Jules to write Pydantic schemas, unit tests, and Alembic migrations directly in GitHub background PRs while you write feature logic. |

---

## 2. Speed Strategy: "Write Once, Inherit Four Times"

Building 5 projects quickly isn't about working 80 hours a week—it's about constructing a **shared base layer** in Week 1.

```
                  ┌────────────────────────────────────────┐
                  │          WEEK 1: THE FOUNDATION        │
                  │  FastAPI + SQLAlchemy 2.x + PG17 + DI  │
                  │  Angular Base Layout + Signals Core    │
                  └───────────────────┬────────────────────┘
                                      │
         ┌────────────────────────────┼────────────────────────────┐
         ▼                            ▼                            ▼
  [ WEEK 2 ]                   [ WEEK 3 ]                   [ WEEK 4 ]
  Document Intelligence        AI Reporting Assistant       Workflow Engine & E-Com
  - Multimodal Vision          - Schema Introspection       - Background Workers
  - Pydantic v2 Extraction     - Read-only SQL Sandbox      - Async Task Runners
  - pgvector RAG               - Dynamic Chart Render       - Webhook Hooks

```

---

## 3. Accelerated Execution Timeline (4-Week Schedule)

### **Week 1: Core Modular Monolith Infrastructure (3–4 Days)**

* **Objective:** Establish the foundation so you never write boilerplate again.
* **Tasks:**
1. Initialize Python 3.12, FastAPI, and SQLAlchemy 2.x Sync ORM engine.
2. Configure Pydantic v2 BaseSettings and generic Repository pattern.
3. Spin up PostgreSQL 17 with `pgvector` enabled in Docker.
4. Initialize Angular Standalone workspace with a tabbed dashboard shell.


* **AI Tool Speedup:** Use **Google Antigravity** to run prompt commands like: *"Generate a generic Sync SQLAlchemy 2.x repository base class and a FastAPI dependency injection session provider."*

---

### **Week 2: AI Document Processing & RAG (5 Days)**

* **Objective:** Complete Project 1 (Invoice & Contract Intelligence).
* **Tasks:**
1. Build `/upload` endpoint accepting raw PDF bytes.
2. Implement Gemini 1.5 Flash multimodal call using `response_schema` bound to Pydantic v2 models for structured extraction.
3. Generate embeddings for document chunks and store them in PostgreSQL 17 using `pgvector`.
4. Build Angular side-by-side component (PDF viewer + extracted JSON form).


* **AI Tool Speedup:** Have **Jules** auto-generate test PDF samples and unit tests validating Pydantic schema constraints in the background.

---

### **Week 3: Text-to-SQL & AI Reporting Assistant (5 Days)**

* **Objective:** Complete Project 2 (Safe Reporting Copilot).
* **Tasks:**
1. Implement SQLAlchemy schema inspector to output clean database DDLS into Gemini prompt contexts.
2. Write AST/sqlglot parser to strictly whitelist `SELECT` queries and reject mutating operations (`DROP`, `DELETE`, `INSERT`).
3. Execute queries and format JSON output into rows + chart metadata (`chartType`, `xAxis`, `yAxis`).
4. Build Angular chat interface that dynamically renders Chart.js / ngx-charts based on backend metadata.



---

### **Week 4: Workflow Automation & Production Deployment (5 Days)**

* **Objective:** Complete Project 3 & 4 (Event Workflows & Deployment).
* **Tasks:**
1. Build lightweight event runner using FastAPI `BackgroundTasks` to execute chained DAG steps (Webhook $\rightarrow$ Gemini Processing $\rightarrow$ Database Update).
2. Dockerize the FastAPI Modular Monolith and deploy to **GCP Cloud Run**.
3. Deploy Angular build to **Firebase Hosting** or **Vercel** ($0 tier).
4. Record 60-second video walkthroughs for each module on Loom for your portfolio/freelance profile.



---

## 4. Key Rules for Maximum Velocity

1. **Rely on Native Pydantic v2 Schemas:** Do not write custom text parsers for AI output. Use Gemini's strict JSON mode (`response_mime_type="application/json"`) paired with `response_schema` to guarantee correct data format instantly.
2. **Never Build Authentication from Scratch:** Use Firebase Auth or Supabase Auth free tiers for JWT verification to avoid spending days on login flow logic.
3. **Keep the Database Local First:** Develop against local Docker container images for fast feedback loops, and only push to Google Cloud SQL during final deployment.
