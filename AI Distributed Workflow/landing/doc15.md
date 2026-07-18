A portfolio website is your **digital resume + project showcase + personal brand**. It should convince a recruiter or client in **5–10 minutes** that you can build, deploy, and maintain software professionally.

# Portfolio Website Roadmap (Start to End)

## Phase 1 – Planning

### Define Your Goal

Choose your primary audience:

- Recruiters
- Freelance clients
- Startup founders
- Open source community
- Enterprise employers

For each, decide what you want them to do:

- View your projects
- Download your resume
- Contact you
- Visit your GitHub
- Book a meeting

---

## Phase 2 – Content Collection

Gather:

- Professional photo (optional but helpful)
- Short introduction
- Resume (PDF)
- GitHub profile
- LinkedIn profile
- Email
- Project screenshots
- Project architecture diagrams
- Demo videos (optional)
- Certifications
- Blog posts (optional)

---

## Phase 3 – Design

Recommended pages:

### Home

- Name
- Role
- Short introduction
- Call-to-action buttons
- Tech stack highlights

### About

- Professional summary
- Career journey
- Skills
- Interests

### Projects

Each project should include:

- Overview
- Problem statement
- Features
- Technology stack
- Architecture
- Screenshots
- Live demo link
- GitHub repository
- Challenges and lessons learned

### Skills

Organize by category:

- Languages
- Frontend
- Backend
- Databases
- Cloud
- DevOps
- AI
- Tools

### Experience

If applicable:

- Employment
- Freelance work
- Internships
- Major personal projects

### Certifications

List certifications with issuer and completion date.

### Blog (Optional)

Write about:

- Technical topics
- Project learnings
- Tutorials

### Contact

Include:

- Email
- LinkedIn
- GitHub
- Contact form (optional)

---

## Phase 4 – Technology Stack

Frontend:

- Angular (fits your current stack)

Backend (if needed):

- FastAPI

Database:

- PostgreSQL (only if dynamic content is required)

Hosting:

- Cloudflare Pages
- Vercel
- Netlify

Version Control:

- GitHub

CI/CD:

- GitHub Actions

Analytics (optional):

- Google Analytics or another privacy-conscious alternative

---

## Phase 5 – Development

Build reusable components:

- Navbar
- Footer
- Hero section
- Project cards
- Skill cards
- Timeline
- Contact form
- Theme switcher (optional)

Focus on:

- Responsive design
- Fast loading
- Accessibility basics
- Clear navigation

---

## Phase 6 – Project Showcase

For each project include:

- Purpose
- Key features
- Screenshots
- Live demo
- GitHub link
- Tech stack
- Architecture diagram
- Database design
- API documentation (if applicable)
- Future improvements

---

## Phase 7 – Resume

Provide:

- Online view (optional)
- PDF download
- Keep it updated with current projects and skills

---

## Phase 8 – SEO

Add:

- Page title
- Meta description
- Open Graph tags
- Favicon
- Sitemap
- Robots.txt

This helps search engines and social media previews.

---

## Phase 9 – Testing

Test:

- Mobile
- Tablet
- Desktop
- Chrome
- Firefox
- Edge
- Keyboard navigation
- Broken links
- Contact form (if used)

---

## Phase 10 – Deployment

Typical free setup:

- GitHub repository
- GitHub Actions
- Cloudflare Pages or Vercel
- Custom domain (optional)
- HTTPS enabled

---

## Phase 11 – Monitoring

Monthly:

- Check uptime
- Verify links
- Update resume
- Add new projects
- Review analytics

---

# Suggested Folder Structure

```text
portfolio/

├── src/
├── assets/
│   ├── images/
│   ├── icons/
│   ├── resume/
│   └── screenshots/
├── docs/
├── public/
├── .github/
│   └── workflows/
├── README.md
└── LICENSE
```

---

# Project Card Template

Each project should answer:

- What problem does it solve?
- Why did you build it?
- Which technologies were used?
- What architectural decisions were made?
- What challenges did you face?
- How did you test it?
- Is there a live demo?
- Is the source code available?

---

# Free Services

| Purpose              | Suggested Free Service                   |
| -------------------- | ---------------------------------------- |
| Source Control       | GitHub                                   |
| Hosting              | Cloudflare Pages / Vercel                |
| Domain (optional)    | Free subdomain or low-cost custom domain |
| CI/CD                | GitHub Actions                           |
| Database (if needed) | Neon / Supabase                          |
| Storage              | Cloudinary / Supabase Storage            |
| Monitoring           | UptimeRobot                              |

---

# Maintenance

Weekly:

- Check that the site is online.
- Verify project links.

Monthly:

- Add completed projects.
- Update your resume.
- Refresh screenshots if the UI changed.
- Review dependency updates.

Quarterly:

- Improve content.
- Update your skills section.
- Remove outdated information.

---

# Portfolio Checklist

Before sharing your portfolio, make sure it includes:

- A clear introduction
- Professional contact information
- Downloadable resume
- GitHub profile link
- LinkedIn profile link
- 3–6 high-quality projects
- Live demos (where practical)
- Screenshots
- Technology stack
- Architecture explanations
- Responsive design
- Fast loading
- HTTPS
- Clear README for each linked project

## Suggested Build Order

1. Create the GitHub repository.
2. Build the Home page.
3. Add About and Skills.
4. Create the Projects page with detailed case studies.
5. Add Resume and Contact pages.
6. Test across devices.
7. Deploy to Cloudflare Pages or Vercel.
8. Configure GitHub Actions for automatic deployments.
9. Connect a custom domain if desired.
10. Keep it updated as you complete new projects.

## If you follow this process, your portfolio becomes more than a website—it becomes a professional showcase of your engineering, documentation, testing, deployment, and maintenance practices, which is exactly what employers and clients look for.

---

##

The video outlines a practical alternative to cloud coding agents by combining **OpenCode + Ollama** to create a completely local AI development environment. Since you've already been exploring local AI agents, Ollama, OpenRouter, Roo Code, and Cline, this approach fits well into your overall workflow.

## Executive Summary

The main idea is simple:

- **Ollama** runs the LLM locally.
- **OpenCode** acts as the coding agent and terminal interface.
- **VS Code** remains your IDE.
- **Git** tracks changes.
- **Playwright** performs browser testing.
- Everything runs on your own machine without paying for Claude Code subscriptions or API tokens.

This gives you:

- No per-token cost
- Better privacy
- Offline development
- Full control over models
- Faster experimentation

The tradeoff is that local models are generally weaker than Claude Opus or GPT-5.5 on very complex reasoning tasks.

---

# Why OpenCode instead of Claude Code?

Claude Code was primarily designed around Anthropic models.

When local models are plugged into it:

- system prompts become very large
- excessive tool calls occur
- context is consumed quickly
- responses become slower
- weaker models struggle

OpenCode was designed to be lightweight.

Advantages include:

- fewer unnecessary prompts
- smaller context usage
- faster execution
- better compatibility with Ollama
- easier configuration

---

# Recommended Architecture

```
VS Code

      │

OpenCode CLI

      │

 Ollama Server

      │

Local Model

      │

Project Folder

      │

Git
```

Optional additions:

```
Playwright

Docker

PostgreSQL

Redis

FastAPI

Angular

OpenRouter (fallback)

GitHub
```

This creates a complete local development environment.

---

# Installation

## Step 1

Install Node.js LTS

Verify

```
node -v

npm -v
```

---

## Step 2

Install Ollama

```
ollama serve
```

Verify

```
ollama list
```

---

## Step 3

Install OpenCode

```
npm install -g opencode-ai
```

or

```
npx opencode
```

depending on the current release.

---

## Step 4

Pull a coding model

Example

```
ollama pull qwen2.5-coder
```

or

```
ollama pull gemma3
```

---

## Step 5

Configure provider

Example

```
Provider:
Ollama

Endpoint:
http://localhost:11434

Model:
qwen2.5-coder
```

---

# Model Selection Guide

### Small laptops

8 GB RAM

Use

- Gemma 3 4B
- Qwen 2.5 Coder 3B

---

### 16 GB RAM

Good balance

Use

- Gemma 3 12B
- Qwen 2.5 Coder 7B
- DeepSeek Coder Lite

---

### 24 GB+

Use

- Qwen 2.5 Coder 14B
- Qwen 2.5 Coder 32B
- larger DeepSeek models

---

# Planning Workflow

One of the best lessons from the video is to **plan first**.

Instead of:

```
Build entire ecommerce application.
```

Break it into:

```
Authentication

Products

Orders

Payments

Admin

Reports
```

Then break again

```
Authentication

Create Login API

JWT

Refresh Token

Role Middleware

Frontend Login

Testing

Documentation
```

Small tasks dramatically improve local model performance.

---

# Recommended Folder Structure

```
project/

docs/

tasks/

001-login.md

002-orders.md

003-cart.md

004-payment.md

notes/

architecture/

prompts/
```

Each task becomes one conversation.

---

# Prompt Pattern

Instead of

> Build authentication.

Use

```
Goal

Implement JWT authentication.

Requirements

FastAPI

PostgreSQL

Refresh Tokens

Password hashing

Output

Only backend code.

Constraints

Do not modify frontend.
```

This greatly improves results.

---

# Debugging Workflow

1. Generate code

↓

2. Run application

↓

3. Read logs

↓

4. Fix errors

↓

5. Run tests

↓

6. Browser testing

↓

7. Commit

Local agents perform much better with this iterative loop than with huge one-shot requests.

---

# Playwright Integration

Use Playwright to:

- open browser
- click buttons
- fill forms
- verify pages
- capture screenshots
- detect JavaScript errors

Typical cycle:

```
Generate feature

↓

Run app

↓

Playwright tests

↓

Fix

↓

Repeat
```

---

# Git Workflow

For every completed task:

```
git status

git add .

git commit
```

Small commits help the AI understand changes and make rollbacks easier.

---

# Best Practices

Keep prompts:

- focused
- under one feature
- with clear outputs
- with constraints
- avoid multiple unrelated requests

---

# Context Management

Do not ask:

```
Build an ecommerce website.
```

Instead:

Session 1

```
Authentication
```

Session 2

```
Products
```

Session 3

```
Cart
```

Session 4

```
Orders
```

This keeps context manageable.

---

# Strengths

OpenCode + Ollama excels at:

- refactoring
- code generation
- API creation
- documentation
- debugging
- Docker files
- SQL
- Git
- unit tests
- code review

---

# Limitations

Local models may struggle with:

- very large repositories
- deep architectural reasoning
- long multi-file refactors
- advanced UI design
- novel algorithms

For those tasks, a cloud model (via OpenRouter or another provider) can be used selectively.

---

# Recommended Hybrid Setup

Given your environment (16 GB RAM, FastAPI, Angular, Docker, PostgreSQL, Redis, GitHub), a balanced setup is:

**Offline (daily development):**

- Ollama
- Qwen 2.5 Coder 7B
- Gemma 3 12B
- OpenCode
- VS Code
- Playwright
- Docker

**Online (only when needed):**

- OpenRouter
- GPT-5.5
- Claude Opus/Sonnet
- Gemini 2.5 Pro

This minimizes costs while still providing access to stronger models for complex architectural work.

Overall, the workflow shown in the video aligns well with modern AI-assisted software development: keep tasks small, plan before coding, validate continuously with tests, and use local models for routine work while reserving cloud models for the most demanding reasoning tasks.
