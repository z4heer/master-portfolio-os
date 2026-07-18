Once local development is complete, think of the rest of the lifecycle as preparing your application for real users. Here's a practical, production-oriented guide that fits a free-tier portfolio workflow.

# Post-Local Development Lifecycle

```text
Local Development
        │
        ▼
Feature Complete
        │
        ▼
Code Freeze
        │
        ▼
Code Review
        │
        ▼
Testing
        │
        ▼
Security Review
        │
        ▼
Performance Optimization
        │
        ▼
Documentation
        │
        ▼
Production Build
        │
        ▼
CI/CD Validation
        │
        ▼
Deploy Staging
        │
        ▼
User Acceptance Testing
        │
        ▼
Deploy Production
        │
        ▼
Monitoring
        │
        ▼
Maintenance
```

---

# Stage 1 – Feature Complete

Goal:

- All planned features implemented.

Checklist:

- No placeholder code.
- No TODOs for critical functionality.
- Configuration via environment variables.
- Stable API contracts.

AI roles:

- Developer Agent
- Architect Agent

---

# Stage 2 – Code Freeze

No new features.

Only:

- Bug fixes
- Documentation
- Refactoring
- Testing

---

# Stage 3 – Code Review

Review:

- Clean Architecture
- SOLID principles
- Error handling
- Logging
- Naming
- Folder structure
- Code duplication

AI prompt:

> Review this project as a senior software engineer. Identify maintainability, security, and performance issues.

---

# Stage 4 – Testing

## Unit Tests

- Backend services
- Utility functions
- Business logic

## Integration Tests

- Database
- API endpoints
- Authentication
- Authorization

## Frontend Tests

- Components
- Forms
- Routing

## Manual Testing

- Login
- Registration
- Search
- Cart
- Checkout
- Admin
- Error handling

---

# Stage 5 – Security Review

Verify:

- Password hashing
- JWT validation
- SQL injection protection
- XSS protection
- CSRF considerations
- CORS configuration
- HTTPS in production
- Secrets not committed to Git

Useful tools:

- GitHub Dependabot
- Dependency vulnerability scanners

---

# Stage 6 – Performance

Backend:

- Optimize database queries.
- Add indexes where appropriate.
- Use caching if needed.
- Avoid N+1 query patterns.

Frontend:

- Lazy loading
- Route splitting
- Image optimization
- Minification
- Compression

---

# Stage 7 – Documentation

Create or update:

- README
- API documentation
- Deployment guide
- Environment variable guide
- Architecture diagram
- ER diagram
- Screenshots
- License

---

# Stage 8 – Production Build

Backend:

- Build Docker image.
- Use production configuration.
- Disable debug mode.

Frontend:

- Production Angular build.
- Verify optimized assets.

---

# Stage 9 – CI/CD

GitHub Actions can automatically:

- Install dependencies
- Run linting
- Execute tests
- Build backend image
- Build frontend
- Validate Docker configuration

---

# Stage 10 – Staging Deployment

Deploy to a staging environment that mirrors production as closely as free tiers allow.

Example stack:

- Frontend: Cloudflare Pages or Vercel
- Backend: Render
- Database: Neon PostgreSQL

Test:

- Authentication
- CRUD operations
- Emails (if applicable)
- File uploads
- Payment sandbox (if applicable)

---

# Stage 11 – User Acceptance Testing (UAT)

Test the application as an end user.

Checklist:

- Navigation
- Mobile responsiveness
- Validation messages
- Error pages
- Performance
- Accessibility basics

---

# Stage 12 – Production Deployment

Typical free-tier stack:

Frontend

- Cloudflare Pages
- Vercel
- Netlify

Backend

- Render
- Fly.io (if suitable)
- Railway (check current free offering)

Database

- Neon PostgreSQL
- Supabase PostgreSQL

Storage

- Cloudinary
- Supabase Storage

---

# Stage 13 – Monitoring

Track:

- Uptime
- API response time
- Errors
- CPU and memory (where available)
- Database usage
- Disk usage
- SSL certificate status

Useful free services:

- UptimeRobot
- Better Stack (free tier)

---

# Stage 14 – Maintenance

Regular tasks:

- Apply dependency updates.
- Review security alerts.
- Back up the database.
- Renew secrets if necessary.
- Fix reported bugs.
- Improve performance based on usage.

---

# AI Agents Across the Lifecycle

| Stage            | AI Role                   |
| ---------------- | ------------------------- |
| Feature Complete | Developer                 |
| Code Review      | Reviewer                  |
| Testing          | QA Engineer               |
| Security         | Security Analyst          |
| Performance      | Performance Engineer      |
| Documentation    | Technical Writer          |
| CI/CD            | DevOps Engineer           |
| Deployment       | Cloud Engineer            |
| Monitoring       | Site Reliability Engineer |
| Maintenance      | Support Engineer          |

---

# Recommended Git Branch Flow

```text
main
│
├── develop
│
├── feature/auth
├── feature/products
├── feature/orders
├── feature/cart
└── hotfix/*
```

Merge sequence:

Feature → Develop → Staging → Main

---

# Deliverables Before Calling a Project "Production Ready"

- Source code in GitHub
- Docker configuration
- CI/CD pipeline
- Passing tests
- API documentation
- README with setup instructions
- Environment variable template (`.env.example`)
- Database migration scripts
- Architecture documentation
- Deployment guide
- Live application URL
- Monitoring configured
- Backup and recovery plan

Following this lifecycle helps ensure your portfolio projects don't just work on your machine—they demonstrate professional engineering practices from implementation through deployment and ongoing maintenance.

---

##

A **payment sandbox** is a safe testing environment provided by a payment gateway. It lets you simulate payments, refunds, failures, and webhooks **without using real money or real customer cards**.

For portfolio projects, always integrate with a sandbox first, then switch to production credentials only when you're ready.

# Payment Development Lifecycle

```text
Local Development
        │
        ▼
Sandbox Account
        │
        ▼
Sandbox API Keys
        │
        ▼
Test Payments
        │
        ▼
Webhook Testing
        │
        ▼
Order Verification
        │
        ▼
Production Account
        │
        ▼
Live API Keys
        │
        ▼
Real Payments
```

# What You Can Test

In a sandbox you can safely test:

- Successful payments
- Failed payments
- Cancelled payments
- Invalid cards
- Refunds
- Partial refunds (if supported)
- Payment retries
- Webhook notifications
- Order status updates

No actual money moves.

# Popular Payment Providers

| Provider         | Sandbox | Notes                                            |
| ---------------- | ------- | ------------------------------------------------ |
| Stripe           | ✅      | Excellent documentation and developer experience |
| Razorpay         | ✅      | Very popular for businesses in India             |
| PayPal           | ✅      | Good for international payments                  |
| Square           | ✅      | Supported in selected countries                  |
| Cashfree         | ✅      | Popular in India                                 |
| PhonePe Business | ✅      | Check current developer program                  |
| PayU             | ✅      | Offers testing environment                       |

Availability and features can vary by country and account type.

# Typical Integration Flow

1. Customer adds items to cart.
2. Backend creates an order.
3. Payment gateway creates a payment session/order.
4. Frontend opens the payment page or widget.
5. Customer completes the payment in sandbox.
6. Gateway sends a webhook to your backend.
7. Backend verifies the payment signature/status.
8. Order status is updated.
9. Confirmation is shown to the customer.

# Environment Variables

Never hard-code API keys.

Typical configuration:

```text
PAYMENT_MODE=sandbox

PAYMENT_KEY=...

PAYMENT_SECRET=...

WEBHOOK_SECRET=...
```

Use different credentials for sandbox and production.

# Backend Responsibilities

Your backend should:

- Create payment orders
- Verify payment signatures
- Validate webhook events
- Update order status
- Record transaction details
- Handle refunds (if implemented)

Do **not** trust the frontend to confirm a payment.

# Frontend Responsibilities

The frontend should:

- Display the checkout page
- Launch the payment interface
- Show loading and success/failure states
- Send payment details to the backend for verification

# Testing Checklist

Verify that:

- Successful payment updates the order.
- Failed payment leaves the order in the correct state.
- User cancellation is handled gracefully.
- Duplicate webhook events do not create duplicate orders.
- Invalid signatures are rejected.
- Refund flow works (if supported).
- Network interruptions are handled.

# Production Checklist

Before accepting real payments:

- Switch to **live** API keys.
- Enable HTTPS.
- Secure webhook endpoints.
- Store secrets in environment variables or a secrets manager.
- Enable detailed logging (without logging sensitive payment data).
- Test with a small real transaction.
- Monitor payment failures.

# Recommended Choice for Portfolio Projects

For a **global portfolio**, Stripe's sandbox is often the easiest to learn because of its documentation and developer tools.

If your target audience or future work is **India**, it's valuable to also learn Razorpay's sandbox, as it's widely used by Indian businesses.

# AI Workflow for Payment Integration

| AI Role           | Task                                                              |
| ----------------- | ----------------------------------------------------------------- |
| Architect         | Design checkout and payment flow                                  |
| Backend AI        | Create payment APIs and webhook handlers                          |
| Frontend AI       | Build checkout UI and integrate payment widget                    |
| Security Reviewer | Review signature verification and secret handling                 |
| Tester            | Generate payment success, failure, refund, and webhook test cases |
| Documentation AI  | Write payment setup and deployment documentation                  |

Using a sandbox lets you build and demonstrate a complete, professional payment workflow without any financial risk, making it ideal for portfolio projects before moving to live transactions.

---

##

This is an important question because a portfolio project isn't just about deploying it once—it's about showing that you can operate it like a real application.

## Goal

Keep the application **stable, secure, and available** while minimizing cost on free plans.

---

# Option 1: Permanent Production (Recommended for Flagship Projects)

Keep 1–3 of your best projects online continuously.

Examples:

- E-commerce Platform
- ERP/CRM
- AI SaaS Application

Benefits:

- Share a live URL with recruiters.
- Demonstrate monitoring and maintenance.
- Build confidence that the application is production-ready.

Perform monthly:

- Check uptime.
- Review logs.
- Update dependencies.
- Verify SSL certificates.
- Test login and key user flows.
- Back up the database.

---

# Option 2: Periodic Live (Best for Free Plans)

Keep the project deployed only when needed.

For example:

- Deploy before interviews.
- Deploy before sharing your portfolio.
- Deploy when adding major features.
- Remove it afterward if free-tier limits are a concern.

This works well if your hosting provider has sleeping services or limited free resources.

---

# Suggested Portfolio Strategy

| Project Type          | Keep Live?                       |
| --------------------- | -------------------------------- |
| Main flagship project | Yes                              |
| Second major project  | Yes, if free resources allow     |
| Small demos           | Deploy only when needed          |
| Experimental projects | Local only or deploy temporarily |

---

# Maintenance Schedule

## Weekly (10–15 minutes)

- Confirm the application loads.
- Test login.
- Check API health.
- Ensure the database is reachable.
- Review any failed deployment notifications.

---

## Monthly (30–60 minutes)

- Update dependencies.
- Review GitHub security alerts.
- Verify backups.
- Check storage usage.
- Test core functionality.
- Review environment variables.

---

## Quarterly

- Refresh screenshots in your README.
- Improve documentation.
- Update architecture diagrams.
- Refactor older code if needed.
- Revisit performance.

---

## Every Six Months

- Upgrade major framework versions if practical.
- Review cloud provider changes to free tiers.
- Rotate API keys if appropriate.
- Clean up unused branches and old deployments.

---

# Monitoring Checklist

Track:

- Application uptime
- API response time
- Database connectivity
- Error rates
- SSL certificate validity
- Storage usage
- Memory usage (if exposed by your hosting)

Free tools:

- UptimeRobot
- Better Stack (free tier)

---

# Backups

For PostgreSQL:

Daily (if actively used):

- Database dump

Weekly:

- Full database backup

Monthly:

- Test restoring a backup to ensure it works

Also keep:

- Source code in GitHub
- Environment variable template (`.env.example`)
- Deployment documentation

---

# Security Maintenance

Regularly:

- Update dependencies.
- Review GitHub Dependabot alerts.
- Remove unused secrets.
- Verify HTTPS.
- Confirm no secrets are committed to Git.

---

# Cost Control

On free plans:

- Avoid unnecessary background jobs.
- Stop or remove unused demo environments.
- Delete old container images if applicable.
- Use managed free databases within their limits.
- Monitor quota usage before it becomes a problem.

---

# When to Retire a Project

A project can be archived when:

- You have a stronger replacement.
- The technology is outdated and no longer representative.
- The maintenance effort outweighs its value.

Keep:

- Source code
- Documentation
- Screenshots
- Architecture diagrams

Even if the live deployment is removed, these still showcase your work.

---

# Recruiter-Friendly Portfolio

For your portfolio, aim to have:

- **1 flagship application**: Live year-round.
- **2–3 additional projects**: Deployed when actively showcasing them or after major updates.
- **Remaining projects**: Available on GitHub with clear documentation and screenshots.

This provides a good balance between visibility and the limitations of free hosting.

# Annual Maintenance Calendar

| Frequency      | Tasks                                                                                   |
| -------------- | --------------------------------------------------------------------------------------- |
| Weekly         | Health check, basic functionality                                                       |
| Monthly        | Updates, security review, backups                                                       |
| Quarterly      | Documentation, performance review                                                       |
| Every 6 months | Framework upgrades, key rotation, cleanup                                               |
| Yearly         | Full architecture review, portfolio refresh, evaluate whether to keep each project live |

For most developers using free resources, **keeping one polished flagship project continuously online and maintaining the rest as on-demand deployments** is the most practical and sustainable long-term strategy. It demonstrates both development skills and operational discipline without incurring unnecessary costs.
