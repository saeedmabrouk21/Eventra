# Eventra

> **Eventra** is a production‑grade, event‑driven, multi‑tenant user management platform built with **Ruby on Rails**, **RabbitMQ**, **Sidekiq (Redis)**, **Elasticsearch/OpenSearch**, and deployed on **AWS ECS**.

This project demonstrates real‑world backend architecture: asynchronous processing, domain events, scalable background jobs, search indexing, and cloud‑native deployment.

---

## ✨ Key Features

* Multi‑tenant user management
* Event‑driven architecture using RabbitMQ
* Asynchronous background processing with Sidekiq
* Reliable retries, dead jobs, and monitoring
* Full‑text search and audit logs via Elasticsearch/OpenSearch
* Horizontally scalable deployment on AWS ECS
* Comprehensive test coverage with RSpec

---

## 🧠 High‑Level Architecture

```text
┌─────────────┐
│   Clients   │  (Web / Mobile / API)
└──────┬──────┘
       │ HTTP
       ▼
┌─────────────┐
│ Rails API   │
│ (Eventra)   │
└──────┬──────┘
       │ publishes domain events
       ▼
┌─────────────┐
│ RabbitMQ    │
│ (events)    │
└──────┬──────┘
       │ consumes
       ▼
┌──────────────────────┐
│ Bunny Consumers      │
│ (lightweight)        │
└──────┬───────────────┘
       │ enqueue jobs
       ▼
┌─────────────┐      ┌──────────────────┐
│ Sidekiq     │─────▶│ Redis             │
│ Workers     │      │ (queues, retries) │
└──────┬──────┘      └──────────────────┘
       │
       ▼
┌─────────────┐      ┌──────────────────┐
│ Database    │◀────▶│ Elasticsearch /  │
│ (MySQL/PG)  │      │ OpenSearch       │
└─────────────┘      └──────────────────┘
```

---

## 🔄 Event Flow Example (User Signup)

```text
User Signup (API)
   │
   ▼
Rails Controller
   │
   ├─ Persist user in DB
   └─ Publish `user.created` event
          │
          ▼
     RabbitMQ (events exchange)
          │
          ▼
     Bunny Consumer
          │
          └─ Enqueue Sidekiq Job
                 │
                 ▼
           Sidekiq Worker
                 │
                 ├─ Send welcome email
                 └─ Index user in search
```

---

## 🧩 Component Responsibilities

### Rails API

* Authentication & authorization
* Tenant isolation
* User CRUD operations
* Domain event publishing

**Example domain events:**

* `tenant.created`
* `user.created`
* `user.updated`
* `user.deleted`

---

### RabbitMQ

* Reliable event transport
* Decouples API from background processing
* Supports dead‑letter queues (DLQ)

RabbitMQ is used **only for event delivery**, not job execution.

---

### Bunny Consumers

* Subscribe to RabbitMQ queues
* Validate and deserialize events
* Translate events into background jobs

Consumers are:

* Stateless
* Horizontally scalable
* Deployed as separate ECS services

---

### Sidekiq + Redis

* Executes background jobs
* Handles retries with exponential backoff
* Manages scheduled and dead jobs

Used for:

* Email notifications
* Search indexing
* Audit processing

---

### Elasticsearch / OpenSearch

* Full‑text search for users
* Searchable audit logs
* Tenant‑scoped indexing

Supports:

* Name / email search
* Filtering by event type
* Date range queries

---

## ☁️ AWS ECS Deployment Architecture

```text
                ┌───────────────────┐
                │ Application Load  │
                │ Balancer (ALB)    │
                └─────────┬─────────┘
                          │
        ┌─────────────────┴─────────────────┐
        ▼                                   ▼
┌─────────────┐                     ┌─────────────┐
│ ECS Service │                     │ ECS Service │
│ Rails API   │                     │ Sidekiq     │
└─────────────┘                     └─────────────┘
        │                                   │
        ▼                                   ▼
┌─────────────┐                     ┌─────────────┐
│ RabbitMQ    │◀───────────────────▶│ Redis       │
└─────────────┘                     └─────────────┘
        │
        ▼
┌─────────────┐
│ ECS Service │
│ Consumers   │
└─────────────┘
```

Each service scales independently based on load.

---

## 🧪 Testing Strategy

* **RSpec** for request, model, and service specs
* Event publishing tests
* Consumer behavior tests (mocked RabbitMQ)
* Sidekiq job tests with retry scenarios

---


## 🚀 Why This Project Matters

Eventra demonstrates:

* Real‑world event‑driven design
* Proper separation of concerns
* Production‑grade background processing
* Cloud‑native scalability

It is intentionally designed to mirror **how modern SaaS backends are built in production**.

