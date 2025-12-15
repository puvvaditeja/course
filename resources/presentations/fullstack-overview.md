---
marp: true
theme: default
paginate: true
---

<!-- _class: lead -->

# Full Stack Engineering
## The Complete Picture

---

# What is Full Stack Engineering?

A Full Stack Engineer builds **complete applications** from end to end:

```
┌─────────────────────────────────────────────────────────┐
│                      USER                               │
│                        ↓                                │
│  ┌─────────────────────────────────────────────────┐   │
│  │              FRONTEND (Client)                   │   │
│  │         What users see and interact with         │   │
│  └─────────────────────────────────────────────────┘   │
│                        ↓ API Calls                      │
│  ┌─────────────────────────────────────────────────┐   │
│  │              BACKEND (Server)                    │   │
│  │         Business logic and processing            │   │
│  └─────────────────────────────────────────────────┘   │
│                        ↓ Queries                        │
│  ┌─────────────────────────────────────────────────┐   │
│  │              DATABASE (Storage)                  │   │
│  │         Where data lives permanently             │   │
│  └─────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

---

# The Technology Stack in This Course

```
┌────────────────────────────────────────────────────────────┐
│  FRONTEND          │  Angular, TypeScript, RxJS            │
│                    │  HTML, CSS, Bootstrap, JavaScript     │
├────────────────────────────────────────────────────────────┤
│  BACKEND           │  Java, Spring Boot, Spring MVC        │
│                    │  REST APIs, Microservices             │
├────────────────────────────────────────────────────────────┤
│  DATABASE          │  MySQL, JDBC, Spring Data JPA         │
│                    │  Hibernate ORM                        │
├────────────────────────────────────────────────────────────┤
│  DEVOPS & CLOUD    │  Git, Docker, Kubernetes              │
│                    │  GCP, CI/CD Pipelines                 │
└────────────────────────────────────────────────────────────┘
```

---

# How It All Connects

```
User clicks button
       ↓
Angular Component handles event
       ↓
HTTP Service makes API call
       ↓
Spring Controller receives request
       ↓
Service Layer processes business logic
       ↓
Repository Layer queries database
       ↓
MySQL returns data
       ↓
Response travels back up the chain
       ↓
User sees the result
```

---

<!-- _class: lead -->

# Layer 1: Frontend
## The User Interface

---

# Frontend Technologies

**Structure (HTML)**
- The skeleton of web pages
- Elements, forms, tables, semantic markup

**Style (CSS)**
- Visual appearance and layout
- Flexbox, Grid, responsive design

**Behavior (JavaScript)**
- Interactivity and dynamic content
- DOM manipulation, events, async operations

**Framework (Angular)**
- Component-based architecture
- TypeScript, RxJS, routing, state management

---

# Frontend Architecture

```
┌─────────────────────────────────────────────────────┐
│                    Angular App                       │
├─────────────────────────────────────────────────────┤
│  Components     │  UI building blocks               │
│  Services       │  Business logic, API calls        │
│  Modules        │  Feature organization             │
│  Routing        │  Navigation between views         │
│  RxJS           │  Reactive data streams            │
└─────────────────────────────────────────────────────┘
                          ↓
              HTTP requests to Backend
```

---

<!-- _class: lead -->

# Layer 2: Backend
## The Business Logic

---

# Backend Technologies

**Language (Java)**
- Object-oriented programming
- Strong typing, platform independence
- Enterprise-grade reliability

**Framework (Spring)**
- Dependency Injection (IoC)
- Spring Boot for rapid development
- Spring MVC for web applications
- Spring Data JPA for database access

**Architecture (Microservices)**
- Small, independent services
- API Gateway, Service Discovery
- Fault tolerance, scalability

---

# Backend Architecture

```
┌─────────────────────────────────────────────────────┐
│                 Spring Boot Application              │
├─────────────────────────────────────────────────────┤
│  Controller Layer  │  Handle HTTP requests          │
│         ↓          │                                │
│  Service Layer     │  Business logic                │
│         ↓          │                                │
│  Repository Layer  │  Data access                   │
│         ↓          │                                │
│  Entity Layer      │  Data models                   │
└─────────────────────────────────────────────────────┘
                          ↓
                  Database queries
```

---

# REST API: The Bridge

Frontend and Backend communicate via REST APIs:

```
Frontend Request                    Backend Response
─────────────────                   ────────────────
GET /api/users          →          [{ id: 1, name: "John" }]
POST /api/users         →          { id: 2, name: "Jane" }
PUT /api/users/1        →          { id: 1, name: "Updated" }
DELETE /api/users/1     →          { success: true }
```

**REST Principles:**
- Stateless communication
- Resource-based URLs
- Standard HTTP methods (GET, POST, PUT, DELETE)
- JSON data format

---

<!-- _class: lead -->

# Layer 3: Database
## The Data Storage

---

# Database Technologies

**Relational Database (MySQL)**
- Structured data in tables
- SQL for queries
- ACID transactions
- Data integrity with constraints

**Data Access (JDBC)**
- Java Database Connectivity
- Direct SQL execution
- Foundation for ORM tools

**ORM (Hibernate/JPA)**
- Object-Relational Mapping
- Java objects ↔ Database tables
- Spring Data JPA for simplified access

---

# Database Architecture

```
┌─────────────────────────────────────────────────────┐
│                    MySQL Database                    │
├─────────────────────────────────────────────────────┤
│  Tables           │  users, products, orders        │
│  Relationships    │  Foreign keys, joins            │
│  Indexes          │  Performance optimization       │
│  Transactions     │  Data consistency               │
└─────────────────────────────────────────────────────┘

Java Entity                         Database Table
───────────                         ──────────────
@Entity                             CREATE TABLE users (
class User {                          id INT PRIMARY KEY,
  Long id;           ←→               name VARCHAR(100),
  String name;                        email VARCHAR(100)
  String email;                     );
}
```

---

<!-- _class: lead -->

# Layer 4: DevOps & Cloud
## Deployment & Operations

---

# DevOps Technologies

**Version Control (Git)**
- Track code changes
- Collaborate with teams
- Branching strategies

**Containerization (Docker)**
- Package applications with dependencies
- Consistent environments
- "Works on my machine" → "Works everywhere"

**Orchestration (Kubernetes)**
- Manage container deployments
- Auto-scaling, load balancing
- Self-healing applications

---

# Cloud & CI/CD

**Google Cloud Platform (GCP)**
- Compute Engine (VMs)
- Cloud SQL (Managed databases)
- Cloud Run (Serverless containers)
- Kubernetes Engine (GKE)

**CI/CD Pipeline**
```
Code Push → Build → Test → Deploy
    ↓         ↓       ↓       ↓
   Git     Maven   JUnit    GCP
```

Automate the entire process from code to production.

---

# Complete Request Flow

```
1. User interacts with Angular UI
                ↓
2. Angular sends HTTP request to API Gateway
                ↓
3. Gateway routes to appropriate Microservice
                ↓
4. Spring Controller receives request
                ↓
5. Service layer executes business logic
                ↓
6. Repository queries MySQL via JPA
                ↓
7. Data returns through all layers
                ↓
8. Angular renders response to user

All running in Docker containers on Kubernetes in GCP!
```

---

# Course Modules Mapped

| Layer | Modules |
|-------|---------|
| **Foundation** | Linux, Git, Agile |
| **Frontend Basics** | HTML, CSS, JavaScript, Bootstrap, jQuery |
| **Frontend Advanced** | TypeScript, Angular, RxJS |
| **Backend Core** | Java, Testing, Logging |
| **Backend Framework** | Spring Core, Boot, MVC, Data JPA |
| **API & Services** | REST APIs, Microservices |
| **Database** | MySQL, JDBC, Hibernate |
| **DevOps & Cloud** | Docker, Kubernetes, GCP, CI/CD |

---

# The Full Stack Engineer Mindset

You don't just write code. You:

- **Design** user experiences (Frontend)
- **Build** business logic (Backend)
- **Model** data relationships (Database)
- **Deploy** to production (DevOps)
- **Monitor** and maintain (Cloud)

**One person, complete capability.**

---

# Your Learning Journey

```
Week 1-2:   Foundation + Frontend basics
Week 3-5:   Database + Java fundamentals
Week 6-8:   Spring Framework + APIs
Week 9-10:  Angular + TypeScript
Week 11-13: DevOps + Cloud + Final Project
```

**3 Projects to build:**
1. Java + MySQL + JDBC (Console app)
2. Spring Boot + Angular (Web app)
3. Full Stack + Cloud Deployment (Production-ready)

---

<!-- _class: lead -->

# From Zero to Full Stack
## 13 Weeks. Complete Skills. Career Ready.

