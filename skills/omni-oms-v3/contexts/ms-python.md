# Context: Python gRPC Microservice

**Applies to:** `solutions/ms/saas-ms-*` (excluding `-go` suffix)

## Stack

- **Language:** Python 3
- **Protocol:** gRPC (proto definitions from `saas-protos` / `saas-lib-grpc`)
- **Framework:** `omni-pro` (proprietary, from `saas-ms-library`)
- **Base image:** `saas-img-core` — do NOT add library versions to requirements directly
- **Databases:** MongoDB (default), PostgreSQL (only in stock, sale), Redis (cache + tasks)
- **Observability:** New Relic + gRPC interceptors

## Project Layout

```
app/
├── __manifest__.py     ← service metadata: name, version, permissions per entity
├── app.py              ← entry point: registers services, starts gRPC server
├── api.py              ← gRPC reflection registry
├── models/             ← MongoDB documents (inherit BaseDocument / BaseEmbeddedDocument)
├── operations/         ← business logic classes + Response enums
├── validators/         ← input validation schemas
└── services/           ← gRPC Servicers (inherit operations + proto Servicer)
```

## Core Patterns

### 1. Service inheritance
```python
class FamilyService(AttributeService, GroupService, family_pb2_grpc.FamilyServiceServicer):
    pass
```
Services inherit from multiple operation classes + their proto Servicer.

### 2. Decorator-driven dependency injection
```python
@resources_decorator([Resource.MONGODB])           # injects context.db_manager
@resources_decorator([Resource.POSTGRESQL])        # injects context.pg_manager
@resources_decorator([Resource.REDIS])             # injects context.redis_manager
@resources_decorator([Resource.MONGODB], permission_code="CAN_FAMILY_CREATE")
```
Never instantiate DB clients manually — always use `@resources_decorator`.

### 3. Response pattern
```python
class FamilyResponse(EnumBaseMixin, Enum):
    NOT_FOUND = "FAMILY_001", _("Family not found")
    DUPLICATE  = "FAMILY_002", _("Family already exists")

return MessageResponse(family_pb2.FamilyResponse).response(message, success=True, status_code=200)
```

### 4. Model structure
```python
class Family(BaseDocument):      # MongoDB document
    name = StringField()
    audit = EmbeddedDocumentField(Audit)   # created_at, updated_at, active
```

### 5. Observability on every handler
```python
@newrelic.agent.function_trace()
@resources_decorator([Resource.MONGODB], permission_code="CAN_FAMILY_CREATE")
def FamilyCreate(self, request, context):
    ...
```

## Key Framework Files (saas-ms-library)

| File | Purpose |
|---|---|
| `omni/pro/decorators.py` | `@resources_decorator` — injection engine |
| `omni/pro/models/base.py` | `BaseDocument`, `BaseEmbeddedDocument`, `Audit` |
| `omni/pro/response.py` | `MessageResponse` builder |
| `omni/pro/database/mongo.py` | MongoDB tenant-aware manager |
| `omni/pro/database/postgres.py` | PostgreSQL manager |
| `omni/pro/redis.py` | Redis client manager |
| `omni/pro/user/access.py` | Permission checking |
| `omni/pro/webhook/` | Sync + async webhook handlers |

## Do NOT

- Add library dependencies directly to `requirements.txt` — versions come from `saas-img-core`
- Instantiate DB clients outside `@resources_decorator`
- Skip `@newrelic.agent.function_trace()` on public service methods
- Write raw SQL — use the PostgreSQL manager abstraction
