# Context: Docker Image Builder

**Applies to:** `solutions/imgs/saas-img-*`

## Role in the Platform

Image builders create the **base Docker images** that pin all library versions for the platform. Microservices and apps do NOT manage their own library versions — they inherit them from these images.

| Image | Consumers | Key pinned libs |
|---|---|---|
| `saas-img-core` | All Python microservices (`solutions/ms/`) | `omni-pro`, `omni-pro-base`, `omni-pro-grpc`, `omni-pro-redis` |
| `saas-img-apps` | All Django apps (`solutions/apps/`) | Django, DRF, omni-pro-base |

## Current pinned versions (saas-img-core)

```
omni-pro==0.1.418
omni-pro-base==0.30.0
omni-pro-grpc==0.0.159
omni-pro-redis==0.0.22
```

## Project Layout

```
saas-img-{name}/
├── .docker/            ← Dockerfile(s) and build scripts
├── requirements/       ← pinned dependency files
│   ├── base.txt
│   └── production.txt
├── scripts/            ← build/push automation scripts
├── terraform/          ← ECR repository and related infra
└── .newrelic/          ← New Relic config (saas-img-core only)
```

## Critical Rules

- **Bumping a library version here** propagates to ALL consumers — coordinate with team
- **Test downstream** before releasing a new image: run at least one ms and one app against the new image
- Version bumps in `saas-ms-library` → must update `saas-img-core` → must update all ms
- Images are published to AWS ECR — check `terraform/` for the ECR repo config
- Never add business logic here — only dependency management and base configuration
