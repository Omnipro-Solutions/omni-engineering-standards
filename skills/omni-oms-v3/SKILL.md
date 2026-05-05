---
name: omni-oms-v3
description: Architecture context for the Omni OMS v3 platform. Load this skill when working on any project under solutions/ms, solutions/apps, solutions/libs, solutions/tfs, or solutions/imgs to instantly understand the stack, patterns, and conventions without exploring the codebase.
license: MIT
---

# Omni OMS v3 — Architecture Context

Use this skill to orient yourself before any task on the OMS v3 platform. Read the project path, identify the type, and load the matching context. Do NOT explore the project file tree to understand the stack — the context files already contain that knowledge.

---

## Step 1 — Identify project type from path

| Path pattern | Type | Load |
|---|---|---|
| `solutions/ms/saas-ms-*` (no `-go`) | Python gRPC Microservice | `contexts/ms-python.md` |
| `solutions/ms/saas-ms-*-go` | Go gRPC Microservice | `contexts/ms-go.md` |
| `solutions/apps/saas-app-*` | Django Integration App | `contexts/app-django.md` |
| `solutions/libs/` | Shared Python Library | `contexts/lib-python.md` |
| `solutions/tfs/` | Terraform / AWS IaC | `contexts/tf.md` |
| `solutions/imgs/` | Docker Image Builder | `contexts/img.md` |
| Cross-service or onboarding | Platform overview | `contexts/ARCHITECTURE.md` |

---

## Step 2 — Apply the context

Each context file contains:
- The role of that project type in the platform
- Stack and dependencies
- Project layout conventions
- Core code patterns with examples
- What NOT to do

Read it once at session start. Do not re-explore on every task.

---

## Platform Summary

OMS v3 is a **multi-tenant gRPC microservices platform**:

- **Microservices** (`solutions/ms/`) — core business logic, built on the proprietary `omni-pro` framework (`saas-ms-library`), communicate via gRPC, use MongoDB / PostgreSQL / Redis
- **Apps** (`solutions/apps/`) — Django integration layer, connects external systems (Magento, carriers, ERPs) to the ms core via gRPC clients
- **Libraries** (`solutions/libs/`) — shared framework, proto definitions, Redis/gRPC clients; `saas-ms-library` is the highest-impact repo
- **Images** (`solutions/imgs/`) — `saas-img-core` pins all library versions for ms; `saas-img-apps` for Django apps
- **Infrastructure** (`solutions/tfs/`) — Terraform modules for AWS (API Gateway, ECS, RDS, CloudWatch)

Cross-cutting: multi-tenancy via gRPC context, New Relic observability on every handler, Celery + Redis for async tasks, AWS Cognito for auth.
