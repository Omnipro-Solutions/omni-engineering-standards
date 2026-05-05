# Omni Platform — Architecture Map

## Quick Type Identification

Read the project path. The naming convention `{scope}-{type}-{name}` tells you everything:

| Path prefix | Type | Context file to load |
|---|---|---|
| `solutions/ms/saas-ms-*` | gRPC Microservice (Python) | `contexts/ms-python.md` |
| `solutions/ms/saas-ms-*-go` | gRPC Microservice (Go) | `contexts/ms-go.md` |
| `solutions/apps/saas-app-*` | Django Integration App | `contexts/app-django.md` |
| `solutions/libs/saas-lib-*` | Shared Python Library | `contexts/lib-python.md` |
| `solutions/libs/saas-ms-library` | Core Framework Library | `contexts/lib-python.md` |
| `solutions/libs/saas-protos` | gRPC Proto Definitions | `contexts/lib-python.md` |
| `solutions/tfs/saas-tf-*` | Terraform / AWS IaC | `contexts/tf.md` |
| `solutions/imgs/saas-img-*` | Docker Image Builder | `contexts/img.md` |

## Platform Overview

```
┌─────────────────────────────────────────────────┐
│  Django Apps  (solutions/apps/)                 │
│  Integration layer — connects external systems  │
│  to the core via gRPC clients                   │
└────────────────────┬────────────────────────────┘
                     │ gRPC
┌────────────────────▼────────────────────────────┐
│  Microservices  (solutions/ms/)                 │
│  Core business logic — multi-tenant, gRPC       │
│  Built on omni-pro framework (saas-ms-library)  │
│  Databases: MongoDB · PostgreSQL · Redis        │
└────────────────────┬────────────────────────────┘
                     │ imports
┌────────────────────▼────────────────────────────┐
│  Libraries  (solutions/libs/)                   │
│  saas-ms-library  → omni-pro framework          │
│  saas-lib-grpc    → gRPC transport              │
│  saas-lib-redis   → Redis client                │
│  saas-protos      → .proto definitions          │
└────────────────────┬────────────────────────────┘
                     │ pinned via
┌────────────────────▼────────────────────────────┐
│  Docker Images  (solutions/imgs/)               │
│  saas-img-core → base image for all ms          │
│  saas-img-apps → base image for Django apps     │
│  Pins: omni-pro==0.1.418, omni-pro-grpc==0.0.159│
└─────────────────────────────────────────────────┘

  Infrastructure: solutions/tfs/  (Terraform / AWS)
  Documentation:  solutions/docs/
```

## Cross-Cutting Concerns

- **Multi-tenancy**: tenant ID travels in gRPC context; each tenant has isolated DB connection
- **Observability**: every service method has `@newrelic.agent.function_trace()` + gRPC logging interceptors
- **i18n**: `omni.pro.locales.translator` for gettext translations
- **Permissions**: `@resources_decorator(..., permission_code="CAN_X_ACTION")` on every handler
- **Async tasks**: Celery + Redis for background jobs (`omni/pro/celery/celery_redis.py`)
- **Library versioning**: ms projects do NOT pin libs directly — they inherit from `saas-img-core`
