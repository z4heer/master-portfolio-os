Based on your goal of building **production-quality portfolio projects** while **maximizing free plans**, here's a practical workflow that balances quality, cost, and sustainability.

# Free Multi-Agent Workflow (Development → Deployment)

## Phase 1 – Planning

**Primary AI**

- Local: Qwen Coder or Gemma (Ollama)
- Cloud: Gemini Free or Claude (when free quota is available)

Deliverables:

- Requirements
- Feature list
- User stories
- Architecture
- Database design
- API contracts
- Development roadmap

Output:

- `/docs/requirements.md`
- `/docs/architecture.md`
- `/docs/api-spec.md`

---

## Phase 2 – Project Setup

Tools:

- GitHub
- VS Code
- Docker
- Ollama

AI Role:

- Project Setup Agent

Tasks:

- Create folder structure
- Configure Docker
- Configure linting
- Configure formatting
- Configure GitHub Actions
- Create README

---

## Phase 3 – Backend Development

Primary:

- Ollama (Qwen Coder)

Fallback:

- Gemini
- Claude
- GPT (through free quota)

Responsibilities:

- Database models
- FastAPI routes
- Authentication
- Business logic
- Unit tests

---

## Phase 4 – Frontend Development

Models:

- Gemini
- Qwen
- Claude

Responsibilities:

- Angular components
- Forms
- Routing
- State management
- Responsive UI

---

## Phase 5 – Code Review

Reviewer Agent

Responsibilities:

- SOLID principles
- Clean Architecture
- Performance
- Security
- Naming
- Refactoring

Ask questions like:

- "Review this code as a senior engineer."
- "Identify security risks."
- "Suggest performance improvements."

---

## Phase 6 – Testing

Testing Agent

Responsibilities:

- Unit tests
- Integration tests
- API testing
- Edge cases
- Manual test checklist

---

## Phase 7 – Documentation

Documentation Agent

Produces:

- README
- Installation guide
- API documentation
- Deployment guide
- Architecture diagrams
- Screenshots

---

## Phase 8 – DevOps

DevOps Agent

Responsibilities:

- Docker
- Docker Compose
- GitHub Actions
- Environment variables
- Secrets
- Production configuration

---

## Phase 9 – Deployment

Free options include:

Frontend:

- GitHub Pages (static sites)
- Cloudflare Pages
- Netlify
- Vercel

Backend:

- Render (free tier, subject to limits)
- Railway (check current free offering)
- Fly.io (check current free offering)

Database:

- Neon (PostgreSQL free tier)
- Supabase (free tier)

Storage:

- Cloudinary
- Supabase Storage

Monitoring:

- UptimeRobot
- Better Stack (free tier)

---

# Agent Responsibilities

| Agent         | Responsibility  |
| ------------- | --------------- |
| Planner       | Requirements    |
| Architect     | System design   |
| Backend       | APIs            |
| Frontend      | UI              |
| Reviewer      | Code quality    |
| Tester        | Testing         |
| Documentation | Docs            |
| DevOps        | Deployment      |
| Security      | Security review |

---

# AI Platform Usage

| Tool                 | Purpose                                              |
| -------------------- | ---------------------------------------------------- |
| Ollama               | Daily coding                                         |
| OpenRouter           | Access to multiple cloud models                      |
| Gemini               | Large-context reasoning                              |
| Claude               | Architecture and documentation                       |
| GitHub Copilot Free  | Editor assistance (if available for your account)    |
| VS Code AI extension | Inline help and code generation                      |
| v0ero.dev            | Occasional access to premium models (when available) |
| tasklit.ai           | Compare model outputs and solve difficult problems   |

---

# GitHub Workflow

```
main
 │
 ├── feature/auth
 ├── feature/cart
 ├── feature/orders
 ├── feature/payment
 └── feature/admin
```

Every feature follows:

```
Plan
↓

Generate

↓

Review

↓

Test

↓

Commit

↓

Push

↓

GitHub Actions

↓

Deploy
```

---

# Recommended Prompt Sequence

For each feature:

1. Product Manager

> Break this feature into implementation tasks.

2. Architect

> Design the API and database.

3. Backend Developer

> Implement the FastAPI backend.

4. Frontend Developer

> Build the Angular UI.

5. Reviewer

> Review for architecture, security, and performance.

6. Tester

> Generate comprehensive test cases.

7. Documentation Writer

> Update README and API docs.

---

# Folder Structure

```
project/

docs/

backend/

frontend/

docker/

tests/

.github/

prompts/

scripts/

deployment/
```

---

# Cost

| Item               | Free                                           |
| ------------------ | ---------------------------------------------- |
| GitHub             | ✅                                             |
| VS Code            | ✅                                             |
| Docker             | ✅                                             |
| Ollama             | ✅                                             |
| Qwen/Gemma         | ✅                                             |
| OpenRouter account | ✅ (pay only if you choose to use paid models) |
| Cloudflare Pages   | ✅                                             |
| GitHub Actions     | ✅ (within included limits)                    |
| Neon               | ✅ (free tier)                                 |
| Supabase           | ✅ (free tier)                                 |
| UptimeRobot        | ✅                                             |

---

# Principles for Working Within Free Plans

- Keep **daily coding local** with Ollama to avoid exhausting cloud quotas.
- Reserve **cloud AI** for architecture, complex debugging, and major design reviews.
- Commit to GitHub frequently so you can recover from mistakes.
- Treat AI as a team of specialists: ask one model to create, another to review, and another to test.
- Verify AI-generated code yourself before merging or deploying.

## End-to-End Workflow

```text
Idea
  │
  ▼
Planner AI
  │
  ▼
Architect AI
  │
  ▼
Task Breakdown
  │
  ├───────────────┐
  ▼               ▼
Backend AI     Frontend AI
  │               │
  └───────┬───────┘
          ▼
    Code Review AI
          ▼
      Testing AI
          ▼
 Documentation AI
          ▼
     GitHub Commit
          ▼
   GitHub Actions CI
          ▼
   Deploy to Free Hosts
          ▼
   Monitor & Improve
```

This workflow scales well from a single portfolio project to multiple projects while keeping costs close to zero. As your needs grow, you can selectively add paid AI or hosting services without redesigning the overall process.
