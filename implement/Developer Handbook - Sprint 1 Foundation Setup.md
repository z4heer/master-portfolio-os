# E-Commerce Platform

## Developer Handbook - Sprint 1 Foundation Setup

### Project Overview

The project establishes the foundational architecture for a modern e-commerce platform using:

* FastAPI (Backend)
* PostgreSQL (Database)
* Redis (Cache)
* Angular 19 (Frontend)
* Docker Compose (Local Infrastructure)
* GitHub (Source Control)

---

## Repository Structure

ecommerce-platform/

├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── core/
│   │   ├── db/
│   │   ├── models/
│   │   ├── repositories/
│   │   ├── schemas/
│   │   ├── services/
│   │   └── main.py
│   │
│   ├── Dockerfile
│   ├── requirements.txt
│   └── .env
│
├── frontend/
│   └── ecommerce-frontend/
│
├── docs/
│
├── docker-compose.yml
├── README.md
└── .gitignore

---

## Technology Stack

Backend

* FastAPI
* SQLAlchemy
* PostgreSQL 17
* Redis 8
* Pydantic Settings

Frontend

* Angular 19
* Angular Material
* SCSS
* Angular Routing

DevOps

* Docker Desktop
* Docker Compose
* WSL2 Ubuntu

---

## Infrastructure Components

Container Name:

* ecommerce-backend
* ecommerce-postgres
* ecommerce-redis

Ports:

Backend:
8000

PostgreSQL:
5432

Redis:
6379

Frontend:
4200

---

## Health Check Endpoint

GET /health

Expected Response:

{
"api": "UP",
"postgres": "UP",
"redis": "UP"
}

---

## Swagger

URL:

http://localhost:8000/docs

---

## Frontend URL

http://localhost:4200

---

## Startup Commands

Infrastructure:

docker compose up -d

Check Containers:

docker ps

Backend Logs:

docker logs ecommerce-backend

Stop Environment:

docker compose down

---

## Git Workflow

Create Feature Branch:

git checkout -b feature/<feature-name>

Commit:

git add .
git commit -m "Meaningful message"

Push:

git push origin feature/<feature-name>

---

## Known Decisions

Authentication:
JWT + Refresh Token

Roles:
ADMIN
CUSTOMER

Database:
PostgreSQL

Cache:
Redis

Architecture:
Modular Monolith

API Versioning:
api/v1

---

## Sprint-2 Planned Work

Authentication

* User Model
* Role Model
* Login API
* Register API
* JWT
* Refresh Token

Frontend

* Login Page
* Register Page
* Auth Service
* Auth Guard
* HTTP Interceptor
