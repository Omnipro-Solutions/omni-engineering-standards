# Context: Terraform / AWS Infrastructure

**Applies to:** `solutions/tfs/saas-tf-*`, `solutions/tfs/general-tf-*`

## Role in the Platform

Terraform modules manage AWS infrastructure for the Omni platform.

| Module | Purpose |
|---|---|
| `saas-tf-apigateway` | AWS API Gateway — routes HTTP to microservices |
| `saas-tf-oms` | OMS infrastructure (ECS, RDS, networking, IAM) |
| `saas-tf-oms-metrics` | CloudWatch metrics and dashboards for OMS |
| `general-tf-solutions` | Shared/reusable Terraform modules |

## Stack

- **IaC:** Terraform (HCL)
- **Cloud:** AWS
- **State:** Remote backend (check `backend.tf` in each module)

## Project Layout (typical)

```
{module}/
├── main.tf         ← core resource definitions
├── variables.tf    ← input variables
├── outputs.tf      ← exported values
├── backend.tf      ← remote state config
├── versions.tf     ← provider version constraints
└── modules/        ← nested modules (if any)
```

## Critical Rules

- **Always run `terraform plan` before `terraform apply`** — never apply blindly
- **Never hardcode secrets** — use AWS SSM Parameter Store or Secrets Manager references
- **State is shared** — concurrent applies will corrupt state; coordinate with the team
- **`saas-tf-apigateway` changes** affect all ms routing — test in staging first
- Check `versions.tf` before adding providers — maintain provider version consistency
- Tag all resources: at minimum `env`, `project`, `managed-by = terraform`

## Workflow

```
1. terraform init      → verify: providers downloaded
2. terraform validate  → verify: no syntax errors
3. terraform plan      → verify: only expected changes
4. terraform apply     → verify: resources created/updated correctly
5. terraform output    → capture relevant output values
```
