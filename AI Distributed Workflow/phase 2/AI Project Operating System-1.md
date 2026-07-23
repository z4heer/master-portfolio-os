# Recommended Model

## NotebookLM = Principal Architect + Project Memory

NotebookLM stores:

```text
Project Vision
Architecture
Coding Standards
Technology Stack
Current Progress
Sprint Backlog
Decision Logs
Database Schema
API Contracts
Known Issues
Technical Debt
```

NotebookLM never writes production code directly.

Its job is:

- Maintain project memory
- Create implementation plans
- Generate role-specific prompts
- Review completed work
- Track progress

---

# ChatGPT Sessions = Specialist Engineers

Example:

```text
NotebookLM
     │
     ▼
Generates Prompt
     │
     ▼
ChatGPT Backend Engineer
```

or

```text
NotebookLM
     │
     ▼
Generates Prompt
     │
     ▼
ChatGPT Frontend Engineer
```

or

```text
NotebookLM
     │
     ▼
Generates Prompt
     │
     ▼
ChatGPT QA Engineer
```

---

# Workflow Example

## Step 1

Open NotebookLM

Ask:

```text
Create implementation package for
Order History feature.

Include:

- Business requirements
- Architecture guidance
- Existing modules
- Database tables
- Coding standards
- Acceptance criteria
- Testing requirements

Role: Senior Backend Engineer
```

NotebookLM produces:

```text
Implementation Package
```

---

## Step 2

Copy package into Backend AI Chat.

Example:

```text
Act as Senior FastAPI Engineer.

Project Context:
[NotebookLM output]

Task:
Implement Order History API.

Follow:
- Clean Architecture
- Repository Pattern
- Existing coding standards
- Existing DTO conventions
```

---

## Step 3

Backend AI returns:

```text
DTOs

Repository

Service

Router

Tests
```

---

## Step 4

You implement.

---

## Step 5

Return result to NotebookLM.

Example:

```text
Feature Completed

Files Modified:

orders/router.py
orders/service.py
orders/repository.py

Summary:
Implemented order history endpoint.

Questions:
Should pagination be added?
```

---

## Step 6

NotebookLM updates:

```text
Progress

Technical Debt

Decision Log

Sprint Status
```

---

# Create Standard Prompt Templates

This is where the real productivity comes from.

---

## Backend Engineer Template

NotebookLM generates:

```text
ROLE

Senior Backend Engineer

PROJECT

Enterprise E-Commerce Platform

STACK

FastAPI
SQLAlchemy 2.x
PostgreSQL 17
Redis 8

ARCHITECTURE

Clean Architecture
Repository Pattern
Service Layer

RULES

No business logic in repositories
Use DTOs
Follow existing conventions
Add tests
Add migration if schema changes

TASK

[Task]

DELIVERABLES

Repository
Service
DTO
Router
Tests
```

---

## Frontend Engineer Template

```text
ROLE

Senior Angular Engineer

STACK

Angular 19
Signals
Standalone Components
Material 3

RULES

No any types
Signals only
OnPush
Reusable components

TASK

[Task]

DELIVERABLES

Components
Services
Models
Signals Store
Routing
Tests
```

---

## QA Template

```text
ROLE

Senior QA Engineer

TASK

Review feature

Generate:

SIT Cases
Regression Cases
Negative Cases
Edge Cases
Performance Risks
Security Risks
```

---

## Security Template

```text
ROLE

Application Security Engineer

Review:

Authentication
Authorization
Validation
OWASP

Generate:

Risks
Severity
Recommendations
```

---

# Development Loop

For every feature:

```text
NotebookLM
    │
    ▼
Implementation Package
    │
    ▼
AI Specialist
    │
    ▼
You Implement
    │
    ▼
QA AI
    │
    ▼
You Fix
    │
    ▼
NotebookLM Update
```

---

# What NotebookLM Should Track

Maintain one living document:

```text
PROJECT STATUS

Overall Completion

Current Sprint

Completed Features

Pending Features

Known Bugs

Technical Debt

Architecture Decisions

Testing Status

Release Readiness
```

Every completed task updates this.

---

# What NOT to Do

Avoid:

```text
One giant AI chat

Backend + Frontend + QA + DevOps

in same conversation
```

Eventually:

- Context becomes polluted
- AI forgets constraints
- Architecture drifts
- Inconsistent code appears

---

# Your Actual Role

You should operate as:

```text
Engineering Manager
+
Tech Lead
+
Integration Engineer
```

Your responsibilities:

- Request implementation packages from NotebookLM
- Delegate to specialized AI chats
- Review output
- Integrate code
- Report back to NotebookLM

The AI writes and reviews. You control direction and integration.

For your current project (~87% complete), this approach is likely the fastest path to finishing the remaining 10–15% while keeping architecture consistent and avoiding the context-window problems that typically appear near the end of large AI-assisted projects.
