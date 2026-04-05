# AWS Infrastructure Import to Terraform

A comprehensive solution for importing existing AWS infrastructure across multiple accounts into Terraform as Infrastructure as Code (IaC). This project establishes modular, scalable, and maintainable Terraform configurations with built-in drift detection and governance.

---

## Overview

This repository demonstrates the process of migrating existing AWS resources from multiple accounts into Terraform state. The solution provides a single source of truth, automated drift detection, and a repeatable framework for managing infrastructure at scale.

**Key Benefits:**
- Full Infrastructure as Code (IaC) adoption
- Automated drift detection via CI/CD
- Modular and reusable Terraform design
- Reduced manual errors and improved auditability

---

## Scope

Infrastructure across five AWS accounts is in scope:
- Shared Services
- Manufacturing Development
- Manufacturing Test
- Manufacturing Production
- Commercial Web Hosting

---

## Approach

The solution uses a **modular Terraform architecture** for consistency, reusability, and limited blast radius.

### Directory Structure

```bash
terraform/
├── modules/
│   ├── vpc/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── ec2/
│   └── rds/
└── accounts/
    ├── dev/
    │   ├── network/
    │   ├── security/
    │   ├── storage/
    │   ├── compute/
    │   │   ├── main.tf
    │   │   ├── variables.tf
    │   │   ├── outputs.tf
    │   │   ├── terraform.tfvars
    │   │   └── provider.tf
    │   ├── observability/
    │   └── dns/
    └── prod/
        ├── network/
        ├── security/
        ├── storage/
        ├── compute/
        │   ├── main.tf
        │   ├── variables.tf
        │   ├── outputs.tf
        │   ├── terraform.tfvars
        │   └── provider.tf
        ├── observability/
        └── dns/
```

> **Note:** This is a mono-repo structure where all accounts are managed under one repository. If you need stronger isolation between accounts (e.g., separate access controls, independent CI/CD pipelines, or different team ownership), consider using **separate repositories per account** instead of a mono-repo.

Each layer folder (e.g., `compute/`, `network/`) follows a standard file layout:
- `main.tf` – Resource and module declarations
- `variables.tf` – Input variable definitions
- `outputs.tf` – Exported values
- `terraform.tfvars` – Account/layer specific values
- `provider.tf` – AWS provider and role assumption config
- `imports.tf` – Declarative import blocks

---

## Import Process

1. Initialize Terraform in the target layer directory
2. Define import blocks in `imports.tf`
3. Use **Terraformer** to scan and generate configuration from live AWS resources
4. Refine configuration to use shared modules and `tfvars`
5. Run `terraform plan` to validate (should show imports with no changes)
6. Execute `terraform apply` to import resources into state
7. Verify with `terraform state list`

---

## Execution Plan

- Start with **Shared Services** account as pilot
- Import high-dependency resources first (IAM → Networking → Compute)
- Tag resources with `TERRAFORM_MANAGED = true`
- Set up CI/CD pipelines early for drift detection
- Scale the validated process to remaining accounts

---

## CI/CD and Drift Detection

GitHub Actions workflows run on every Pull Request:
- `terraform validate`
- `terraform plan` (results posted as PR comment)
- `terraform apply` only on merge to main

This setup ensures early drift detection and maintains infrastructure consistency.

---

## Technologies Used

- Terraform 1.5+
- AWS Provider
- Terraformer
- GitHub Actions
- S3 Backend + DynamoDB Locking

---

## Success Criteria

- All resources successfully imported into Terraform state
- `terraform plan` shows no unexpected changes
- CI/CD pipelines active with drift detection
- Resources properly tagged

---

## License
This repository is for demonstration and portfolio purposes. It serves as a reference for large-scale AWS infrastructure migration to Terraform.
