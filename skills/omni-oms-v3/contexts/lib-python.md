# Context: Python Shared Library

**Applies to:** `solutions/libs/saas-lib-*`, `solutions/libs/saas-ms-library`, `solutions/libs/saas-protos`, `solutions/libs/sqlalchemy-celery-beat`

## Role in the Platform

Libraries are **shared dependencies** consumed by microservices and apps. Changes here affect every downstream consumer — treat them with extra care.

| Library | Purpose |
|---|---|
| `saas-ms-library` | Core omni-pro framework (decorators, models, DB managers, response builder) |
| `saas-lib-grpc` | gRPC transport utilities |
| `saas-lib-redis` | Redis client wrapper |
| `saas-protos` | `.proto` definitions — source of truth for all service contracts |
| `saas-protos` | `.proto` definitions — source of truth for all service contracts |
| `sqlalchemy-celery-beat` | Celery beat scheduler backed by SQLAlchemy |
| `general-cli-solutions` | Internal CLI tooling |

## Stack

- **Language:** Python 3
- **Packaging:** `setup.py` (not pyproject.toml)
- **Testing:** `requirements.txt` + `testing_requirements.txt`
- **Proto compilation:** `process_grpc.sh` in `saas-protos`

## Project Layout (typical)

```
{lib_name}/
├── setup.py            ← package definition + version
├── requirements.txt    ← runtime deps
├── {package}/          ← importable package
│   ├── __init__.py
│   └── ...
└── tests/
```

## For saas-protos specifically

```
saas-protos/
├── *.proto             ← service contract definitions (source of truth)
├── process_grpc.sh     ← compiles .proto → Python stubs
└── {service}_pb2*.py   ← generated files (do NOT edit manually)
```

## Critical Rules

- **Never edit generated `*_pb2*.py` files** — always edit the `.proto` source and recompile
- **Version bumps in `setup.py`** must be coordinated with `saas-img-core` / `saas-img-apps` updates
- **Breaking changes** in `saas-ms-library` or `saas-protos` require updating all downstream ms/apps
- **saas-ms-library** is the highest-impact library — a bug here affects every microservice
- Always run tests before bumping a library version: `pip install -e . && pytest`
