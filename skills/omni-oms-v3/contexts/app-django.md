# Context: Django Integration App

**Applies to:** `solutions/apps/saas-app-*`

## Role in the Platform

Django apps are the **integration layer** — they do NOT contain core business logic. They:
- Expose HTTP/REST APIs to external systems (Magento, carriers, ERPs, etc.)
- Call microservices via gRPC clients to execute business operations
- Manage Django ORM models for app-level state (config, tasks, tenants)

Business logic lives in the microservices. Apps orchestrate and translate.

## Stack

- **Language:** Python 3
- **Framework:** Django + Django REST Framework
- **Base image:** `saas-img-apps`
- **Database:** PostgreSQL (via Django ORM)
- **MS communication:** gRPC via `core/` client wrappers
- **Auth:** AWS Cognito (via `omni-pro` wrappers)

## Project Layout

```
{app_name}/
├── apps.py             ← AppConfig.ready() — registers signals
├── models/             ← Django ORM models (inherit OmniModel)
├── views/              ← DRF ViewSets
├── serializers/        ← DRF serializers
├── permissions.py      ← custom Django permission classes
├── forms/              ← Django forms (if applicable)
├── migrations/         ← Django DB migrations
├── signals/            ← lifecycle event handlers
└── core/               ← gRPC client wrappers per microservice
    ├── api_client.py   ← base async gRPC client
    ├── catalog/        ← gRPC calls to saas-ms-catalog
    ├── sale/           ← gRPC calls to saas-ms-sale
    ├── stock/          ← gRPC calls to saas-ms-stock
    └── ...
```

## Core Patterns

### 1. Model base class
```python
from omni_pro_base.models import OmniModel

class Config(OmniModel):
    tenant = models.ForeignKey(Tenant, on_delete=models.CASCADE)
    ...
```

### 2. gRPC client calls
```python
# In views or services — never call ms directly from models
from .core.catalog import CatalogClient

async def get_families(tenant_id):
    async with CatalogClient(tenant_id) as client:
        return await client.family_list(request)
```

### 3. Signal-driven lifecycle
```python
# apps.py
class OmsConfig(AppConfig):
    def ready(self):
        from . import signals  # noqa — triggers signal registration
```

### 4. Permission layer
```python
from .permissions import OmniPermission

class FamilyViewSet(viewsets.ModelViewSet):
    permission_classes = [OmniPermission]
```

## Do NOT

- Put business logic in Django views — delegate to microservices via gRPC
- Call microservices directly from Django model methods or signals (use services/views)
- Bypass `OmniModel` base class for new models
- Create migrations without reviewing multi-tenant impact
