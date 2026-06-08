# Project Naming Standards - refine.digital

## Purpose

This document establishes **comprehensive, enforceable naming conventions** across all aspects of Infrastructure-as-Code (IaC) projects to ensure:
- Consistency across GitHub repos, local folders, and infrastructure resources
- Discoverability and clarity for both humans and AI tools
- Automation-friendly patterns that enable tooling and validation
- Prevention of naming conflicts and ambiguity

**This is the single source of truth for ALL naming in refine.digital IaC projects.**

---

## Quick Reference

| Item | Format | Example | Pattern |
|------|--------|---------|---------|
| GitHub Repo | kebab-case | `infrastructure-vpc` | `^[a-z0-9-]+$` |
| Local Folder | kebab-case | `infrastructure-vpc` | `^[a-z0-9-]+$` |
| Terraform Resource | snake_case | `vpc_main` | `^[a-z0-9_]+$` |
| Terraform Variable | snake_case | `vpc_cidr_block` | `^[a-z0-9_]+$` |
| Terraform Module | kebab-case | `vpc-module` | `^[a-z0-9-]+$` |
| YAML Key | snake_case | `environment_name` | `^[a-z0-9_]+$` |
| Environment | lowercase | `dev`, `staging`, `prod` | `^[a-z]+$` |
| Shell Variable | UPPER_SNAKE | `VPC_ID` | `^[A-Z0-9_]+$` |
| Shell Function | snake_case | `deploy_infrastructure` | `^[a-z0-9_]+$` |
| Config File | kebab-case | `nginx-config` | `^[a-z0-9-]+$` |
| VS Code Workspace | kebab-case | `infrastructure-vpc.code-workspace` | `^[a-z0-9-]+\.code-workspace$` |

---

## 1. GitHub Repository Naming

### Rules

**Format**: `kebab-case` (lowercase with hyphens)

**Pattern**: `^[a-z0-9-]+$`

**Structure**: `{category}-{purpose}[-{environment}]`

**Max Length**: 50 characters

**Requirements**:
- Start with a letter
- Use hyphens to separate words
- No consecutive hyphens
- No trailing hyphens
- Descriptive but concise
- Include category prefix for organization

### Categories

| Category | Prefix | Description | Example |
|----------|--------|-------------|---------|
| Infrastructure | `infrastructure-` | Core infrastructure resources | `infrastructure-vpc` |
| Kubernetes | `k8s-` | Kubernetes clusters and resources | `k8s-cluster-prod` |
| Applications | `app-` | Application deployments | `app-api-gateway` |
| Modules | `module-` | Reusable Terraform modules | `module-vpc` |
| Tools | `tool-` | Automation and tooling | `tool-deployment-scripts` |
| Configurations | `config-` | Configuration management | `config-nginx` |
| Documentation | `docs-` | Documentation repos | `docs-infrastructure` |

### Examples

**Good**:
```
infrastructure-vpc
infrastructure-eks-cluster
k8s-monitoring-stack
app-api-gateway
module-vpc
tool-terraform-validator
config-nginx-prod
docs-architecture
```

**Bad**:
```
Infrastructure_VPC          # Wrong case and separator
InfrastructureVPC           # Not kebab-case
infrastructure_vpc          # Wrong separator (use hyphen not underscore)
Infra-VPC                   # Mixed case
my-vpc                      # Missing category prefix
infrastructure--vpc         # Consecutive hyphens
-infrastructure-vpc         # Leading hyphen
infrastructure-vpc-         # Trailing hyphen
```

### GitHub Repository Settings

**Description Format**: `{One-line description} | {Tech stack}`

Example:
```
Description: VPC infrastructure with public and private subnets | Terraform, AWS
```

**Topics** (tags):
```
terraform
infrastructure-as-code
aws
vpc
refine-digital
```

**Repository Features**:
- ✅ Issues enabled
- ✅ Projects enabled (if needed)
- ✅ Wiki disabled (use docs/ folder instead)
- ✅ Branch protection on `main`

---

## 2. Local Folder Structure

### Root Project Folder

**Rule**: Must match GitHub repository name exactly

**Format**: `kebab-case`

**Location**: `~/ProjectFiles/{category}/{repo-name}`

**Example**:
```
~/ProjectFiles/
├── Infrastructure/
│   ├── infrastructure-vpc/           # ✅ Matches GitHub repo
│   └── infrastructure-eks-cluster/   # ✅ Matches GitHub repo
├── Kubernetes/
│   └── k8s-monitoring-stack/         # ✅ Matches GitHub repo
└── Applications/
    └── app-api-gateway/              # ✅ Matches GitHub repo
```

### Sub-folders within Projects

**Format**: `kebab-case` or `snake_case` (language-dependent)

**Terraform Projects**:
```
infrastructure-vpc/
├── modules/              # kebab-case (standard)
│   ├── vpc/
│   ├── subnets/
│   └── nat-gateway/
├── environments/         # kebab-case
│   ├── dev/
│   ├── staging/
│   └── prod/
├── scripts/              # kebab-case
└── docs/                 # kebab-case
```

**General IaC Projects**:
```
{project-name}/
├── .github/              # kebab-case (GitHub standard)
│   └── workflows/
├── .vscode/              # kebab-case (VS Code standard)
├── terraform/            # kebab-case
├── kubernetes/           # kebab-case
├── scripts/              # kebab-case
├── configs/              # kebab-case
├── docs/                 # kebab-case
├── tests/                # kebab-case
└── examples/             # kebab-case
```

**Required Root Files**:
```
{project-name}/
├── README.md             # REQUIRED
├── CONTRIBUTING.md       # Required for team projects
├── .gitignore            # REQUIRED
├── .editorconfig         # REQUIRED
├── .gitattributes        # Required
└── LICENSE               # Required for open-source
```

---

## 3. Terraform Naming Conventions

### Terraform Files

**Format**: `snake_case.tf` or descriptive name

**Standard Files**:
```
main.tf                   # Primary resources
variables.tf              # Input variables
outputs.tf                # Output values
versions.tf               # Provider versions
providers.tf              # Provider configuration
terraform.tfvars          # Variable values (gitignored)
backend.tf                # Backend configuration
locals.tf                 # Local values
data.tf                   # Data sources
```

**Module-specific Files**:
```
{resource-type}.tf        # e.g., vpc.tf, subnets.tf, nat_gateway.tf
```

### Terraform Resources

**Format**: `snake_case`

**Pattern**: `^[a-z0-9_]+$`

**Structure**: `{resource_type}_{purpose}[_{environment}]`

**Examples**:
```hcl
# Good
resource "aws_vpc" "main" { }
resource "aws_subnet" "public_a" { }
resource "aws_subnet" "public_b" { }
resource "aws_nat_gateway" "main" { }
resource "aws_eks_cluster" "prod" { }

# Bad
resource "aws_vpc" "Main" { }              # Capital letter
resource "aws_vpc" "my-vpc" { }            # Hyphens not allowed
resource "aws_vpc" "VPC" { }               # All caps
resource "aws_subnet" "PublicSubnetA" { }  # CamelCase
```

### Terraform Variables

**Format**: `snake_case`

**Pattern**: `^[a-z0-9_]+$`

**Structure**: `{context}_{property}[_{qualifier}]`

**Examples**:
```hcl
# Good
variable "vpc_cidr_block" { }
variable "environment_name" { }
variable "instance_type" { }
variable "subnet_availability_zones" { }
variable "enable_nat_gateway" { }
variable "cluster_version" { }

# Bad
variable "vpcCidrBlock" { }      # CamelCase
variable "vpc-cidr-block" { }    # Hyphens
variable "CIDR" { }              # All caps
variable "cidr" { }              # Too vague (missing context)
```

### Terraform Outputs

**Format**: `snake_case`

**Pattern**: `^[a-z0-9_]+$`

**Structure**: `{resource}_{property}`

**Examples**:
```hcl
# Good
output "vpc_id" { }
output "subnet_ids" { }
output "nat_gateway_ips" { }
output "cluster_endpoint" { }
output "cluster_name" { }

# Bad
output "vpcId" { }              # CamelCase
output "vpc-id" { }             # Hyphens
output "id" { }                 # Too vague
output "VPC_ID" { }             # All caps
```

### Terraform Modules

**Folder Name Format**: `kebab-case`

**Module Structure**:
```
modules/
├── vpc/                  # ✅ kebab-case
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── eks-cluster/          # ✅ kebab-case with hyphen
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
└── nat-gateway/          # ✅ kebab-case
```

**Module Source Reference**:
```hcl
# Local modules
module "vpc" {
  source = "./modules/vpc"
}

# Git modules
module "vpc" {
  source = "git::https://github.com/refinedigital/module-vpc.git?ref=v1.0.0"
}
```

### Terraform AWS Resource Naming

**Format**: Uses AWS Name tag (kebab-case)

**Pattern**: `{project}-{resource}-{environment}`

**Examples**:
```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr_block

  tags = {
    Name        = "${var.project_name}-vpc-${var.environment}"  # infrastructure-vpc-prod
    Environment = var.environment
    Project     = var.project_name
    ManagedBy   = "terraform"
  }
}

resource "aws_subnet" "public_a" {
  tags = {
    Name = "${var.project_name}-subnet-public-a-${var.environment}"  # infrastructure-subnet-public-a-prod
  }
}
```

**Result**: Resources in AWS Console appear as:
```
infrastructure-vpc-prod
infrastructure-subnet-public-a-prod
infrastructure-nat-gateway-prod
infrastructure-eks-cluster-prod
```

---

## 4. YAML File Naming

### YAML Filenames

**Format**: `kebab-case.yml` or `kebab-case.yaml`

**Preference**: Use `.yml` for consistency

**Examples**:
```
# GitHub Actions
.github/workflows/
├── terraform-validate.yml
├── security-scan.yml
├── deploy-infrastructure.yml
└── pr-checks.yml

# Kubernetes
kubernetes/
├── deployment.yml
├── service.yml
├── ingress.yml
├── configmap.yml
└── secret.yml

# Docker Compose
docker-compose.yml
docker-compose.dev.yml
docker-compose.prod.yml

# CI/CD
.gitlab-ci.yml
.circleci/config.yml
```

### YAML Keys

**Format**: `snake_case`

**Pattern**: `^[a-z0-9_]+$`

**Examples**:
```yaml
# Good
environment_name: prod
vpc_cidr_block: "10.0.0.0/16"
enable_nat_gateway: true
subnet_availability_zones:
  - us-east-1a
  - us-east-1b

# Bad
environmentName: prod          # CamelCase
environment-name: prod         # kebab-case (not snake_case)
EnvironmentName: prod          # PascalCase
```

**Exception**: GitHub Actions and Kubernetes use their own conventions (keep standard syntax):
```yaml
# GitHub Actions (kebab-case - follow standard)
name: Terraform Validate
on:
  pull_request:
    branches: [main]

# Kubernetes (camelCase - follow standard)
apiVersion: v1
kind: Service
metadata:
  name: my-service    # kebab-case for resource names
spec:
  type: ClusterIP     # Standard Kubernetes syntax
```

---

## 5. Markdown File Naming

### Markdown Filenames

**Format**: `UPPER-KEBAB-CASE.md` for root docs, `kebab-case.md` for nested

**Root-level Files** (UPPERCASE):
```
README.md                 # REQUIRED - Project overview
CONTRIBUTING.md           # Contribution guidelines
CHANGELOG.md              # Version history
LICENSE.md                # License file
SECURITY.md               # Security policy
CODE_OF_CONDUCT.md        # Code of conduct
```

**Nested Documentation** (lowercase):
```
docs/
├── architecture.md
├── deployment-guide.md
├── troubleshooting.md
├── api-reference.md
└── examples/
    ├── basic-setup.md
    └── advanced-configuration.md
```

### Markdown Headings

**Format**: Use sentence case

**Examples**:
```markdown
# Project name              ✅
## Getting started          ✅
### Quick start guide       ✅

# PROJECT NAME              ❌
## GETTING STARTED          ❌
### Quick Start Guide       ❌
```

---

## 6. Shell Script Naming

### Shell Script Filenames

**Format**: `kebab-case.sh`

**Pattern**: `^[a-z0-9-]+\.sh$`

**Examples**:
```bash
# Good
scripts/
├── deploy-infrastructure.sh
├── validate-terraform.sh
├── setup-environment.sh
└── cleanup-resources.sh

# Bad
scripts/
├── deployInfrastructure.sh   # CamelCase
├── Deploy_Infrastructure.sh  # Mixed case and underscore
├── DEPLOY.sh                 # All caps
```

### Shell Variables

**Format**: `UPPER_SNAKE_CASE` for constants, `lower_snake_case` for local vars

**Examples**:
```bash
#!/usr/bin/env bash

# Constants (UPPER_SNAKE_CASE)
readonly ENVIRONMENT="prod"
readonly VPC_ID="vpc-123456"
readonly AWS_REGION="us-east-1"
readonly MAX_RETRIES=3

# Local variables (lower_snake_case)
local vpc_cidr_block="10.0.0.0/16"
local subnet_count=3
local deployment_status="pending"

# Export variables (UPPER_SNAKE_CASE)
export TF_VAR_environment="${ENVIRONMENT}"
export AWS_DEFAULT_REGION="${AWS_REGION}"
```

### Shell Functions

**Format**: `snake_case`

**Pattern**: `^[a-z0-9_]+$`

**Examples**:
```bash
# Good
deploy_infrastructure() { }
validate_terraform_config() { }
cleanup_old_resources() { }
get_vpc_id() { }

# Bad
deployInfrastructure() { }      # CamelCase
Deploy_Infrastructure() { }     # Mixed case
DEPLOY() { }                    # All caps
```

---

## 7. Configuration File Naming

### General Config Files

**Format**: Depends on file type and convention

**Examples**:
```
# Application configs (kebab-case or original convention)
nginx.conf
nginx-custom.conf
redis.conf
postgresql.conf

# Dot files (follow tool convention)
.editorconfig
.gitignore
.gitattributes
.terraformrc
.terraform-version
.tflint.hcl

# Environment configs (kebab-case)
configs/
├── dev.env
├── staging.env
├── prod.env
└── local.env
```

### Tool-Specific Configs

**Keep tool conventions**:
```
# Terraform
.terraform.lock.hcl
terraform.tfvars
terraform.tfvars.example

# Docker
Dockerfile
docker-compose.yml
.dockerignore

# Git
.gitignore
.gitattributes
.gitmodules

# VS Code
.vscode/settings.json
.vscode/extensions.json
```

---

## 8. Environment Naming

### Environment Names

**Format**: `lowercase` single word

**Standard Environments**:
```
dev          # Development
staging      # Staging/QA
prod         # Production
test         # Testing
sandbox      # Experimentation
```

**Usage**:
```hcl
# Terraform
variable "environment" {
  type        = string
  description = "Environment name"
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

# AWS resource tags
tags = {
  Environment = "prod"  # Always lowercase
}

# Branch naming
git checkout -b feature/vpc-setup-dev
git checkout -b hotfix/security-patch-prod
```

---

## 9. Git Branch Naming

### Branch Name Format

**Pattern**: `{type}/{description}[-{ticket}]`

**Types**:
- `feature/` - New features
- `bugfix/` - Bug fixes
- `hotfix/` - Critical production fixes
- `chore/` - Maintenance tasks
- `docs/` - Documentation updates
- `refactor/` - Code refactoring
- `test/` - Test updates

**Format**: `kebab-case` for description

**Examples**:
```bash
# Good
feature/vpc-setup
feature/eks-cluster-configuration
bugfix/terraform-state-lock
hotfix/security-group-rule
chore/update-dependencies
docs/add-deployment-guide
refactor/module-structure

# With ticket numbers
feature/vpc-setup-PROJ-123
bugfix/state-lock-PROJ-456

# Bad
feature/VPCSetup           # CamelCase
feature/vpc_setup          # Underscore
Feature/vpc-setup          # Capital type
featurevpcsetup            # Missing separator
feature/                   # Empty description
```

### Git Tags (Versions)

**Format**: Semantic Versioning `v{MAJOR}.{MINOR}.{PATCH}`

**Pattern**: `^v[0-9]+\.[0-9]+\.[0-9]+$`

**Examples**:
```bash
# Good
v1.0.0
v1.2.3
v2.0.0

# For modules/libraries
v1.0.0-beta
v1.0.0-rc1
v1.0.0-alpha

# Bad
1.0.0                      # Missing 'v' prefix
version-1.0.0              # Wrong format
v1.0                       # Missing patch version
V1.0.0                     # Capital V
```

---

## 10. VS Code Workspace Naming

### Workspace Files

**Format**: `{project-name}.code-workspace`

**Must match**: Repository/folder name

**Examples**:
```
infrastructure-vpc.code-workspace
k8s-monitoring-stack.code-workspace
app-api-gateway.code-workspace
```

**Location**: Store in project root

**Example Content**:
```json
{
  "folders": [
    {
      "path": "."
    }
  ],
  "settings": {
    "files.exclude": {
      "**/.terraform": true,
      "**/.terragrunt-cache": true
    },
    "terraform.format.enable": true
  }
}
```

### VS Code Settings

**File**: `.vscode/settings.json`

**Format**: Standard JSON (camelCase keys - VS Code convention)

```json
{
  "editor.formatOnSave": true,
  "terraform.format.enable": true,
  "files.trimTrailingWhitespace": true
}
```

---

## 11. README.md Structure

### Standard README Template

**File**: `README.md` (root of project)

**Required Sections**:

```markdown
# {Project Name}

> {One-line description}

[![Standards Check](badge-url)](action-url)
[![Terraform](https://img.shields.io/badge/Terraform-%3E%3D1.0-blue)](terraform-url)

## Overview

{2-3 paragraph detailed description}

## Prerequisites

- Terraform >= 1.0
- AWS CLI configured
- [Other requirements]

## Project Structure

\`\`\`
{tree output or manual structure}
\`\`\`

## Quick Start

### Setup

\`\`\`bash
{setup commands}
\`\`\`

### Deployment

\`\`\`bash
{deployment commands}
\`\`\`

## Configuration

### Variables

{Table of variables}

### Outputs

{Table of outputs}

## Environments

- **dev**: Development environment
- **staging**: Staging/QA environment
- **prod**: Production environment

## Development

### Local Development

\`\`\`bash
{dev commands}
\`\`\`

### Testing

\`\`\`bash
{test commands}
\`\`\`

## Standards Compliance

This project follows [refine.digital Development Standards](https://github.com/refinedigital/development-standards).

\`\`\`bash
# Check compliance
refine-standards check

# Auto-fix issues
refine-standards fix
\`\`\`

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md)

## License

{License information}

## Support

{Contact information}
```

---

## 12. Validation Rules

### Automated Validation

All naming conventions can be validated using:

```bash
# CLI tool
refine-standards check

# Or specific validations
refine-standards validate-name --repo "infrastructure-vpc"
refine-standards validate-name --terraform-resource "vpc_main"
refine-standards validate-name --branch "feature/vpc-setup"
```

### Validation Patterns

See `naming-validation-rules.json` for machine-readable regex patterns.

---

## 13. Common Patterns

### Multi-Environment Projects

**Structure**:
```
infrastructure-vpc/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── terraform.tfvars
│   │   └── backend.tf
│   ├── staging/
│   │   ├── main.tf
│   │   ├── terraform.tfvars
│   │   └── backend.tf
│   └── prod/
│       ├── main.tf
│       ├── terraform.tfvars
│       └── backend.tf
└── modules/
    └── vpc/
```

**Naming in Code**:
```hcl
# backend.tf
terraform {
  backend "s3" {
    bucket = "refine-terraform-state-prod"
    key    = "infrastructure-vpc/prod/terraform.tfstate"
    region = "us-east-1"
  }
}

# main.tf
module "vpc" {
  source = "../../modules/vpc"

  project_name = "infrastructure-vpc"
  environment  = "prod"
  vpc_name     = "infrastructure-vpc-prod"  # {project}-{resource}-{env}
}
```

### Shared Modules

**Repository**: `module-{name}`

**Example**: `module-vpc`

**Structure**:
```
module-vpc/
├── README.md
├── main.tf
├── variables.tf
├── outputs.tf
├── versions.tf
└── examples/
    ├── basic/
    │   └── main.tf
    └── advanced/
        └── main.tf
```

**Usage**:
```hcl
module "vpc" {
  source = "git::https://github.com/refinedigital/module-vpc.git?ref=v1.0.0"

  vpc_name     = "my-vpc"
  vpc_cidr     = "10.0.0.0/16"
  environment  = "prod"
}
```

---

## 14. Migration Guide

### Migrating Existing Projects

**Step 1: Check Current Naming**
```bash
cd /path/to/project
refine-standards check --report
```

**Step 2: Identify Violations**
Review report and create checklist:
- [ ] Repository name compliant
- [ ] Folder structure compliant
- [ ] Terraform resources named correctly
- [ ] YAML files named correctly
- [ ] README.md follows template

**Step 3: Create Migration Branch**
```bash
git checkout -b chore/naming-standards-migration
```

**Step 4: Apply Fixes**
```bash
# Automated fixes where possible
refine-standards fix --auto

# Manual fixes for resource names (requires Terraform state updates)
terraform state mv aws_vpc.oldName aws_vpc.new_name
```

**Step 5: Test & Deploy**
```bash
terraform plan  # Verify no infrastructure changes
git commit -m "chore: migrate to naming standards"
git push
```

---

## 15. Exceptions & Overrides

### When to Deviate

**Acceptable exceptions**:
1. **Third-party tools**: Keep their naming conventions (e.g., `.github/`, `node_modules/`)
2. **Cloud provider limitations**: AWS resource name restrictions
3. **Existing systems**: Legacy integration requirements
4. **Industry standards**: Well-established patterns (e.g., `Dockerfile`, `Makefile`)

### Documenting Exceptions

Add to project README.md:
```markdown
## Naming Exceptions

- `legacy-vpc-name`: Kept for backwards compatibility with existing systems
- `API_KEY`: Environment variable follows external service convention
```

---

## 16. Summary Checklist

Use this checklist for new projects:

- [ ] GitHub repo named in `kebab-case` with category prefix
- [ ] Local folder matches GitHub repo name exactly
- [ ] Terraform resources use `snake_case`
- [ ] Terraform variables use `snake_case`
- [ ] Terraform modules folders use `kebab-case`
- [ ] YAML files use `kebab-case.yml`
- [ ] YAML keys use `snake_case`
- [ ] Markdown files follow `UPPER-KEBAB` (root) or `kebab-case` (nested)
- [ ] Shell scripts use `kebab-case.sh`
- [ ] Shell variables: `UPPER_SNAKE` (constants), `lower_snake` (local)
- [ ] Shell functions use `snake_case`
- [ ] Branches follow `type/description` pattern
- [ ] Tags follow `v{major}.{minor}.{patch}` semantic versioning
- [ ] VS Code workspace named `{project}.code-workspace`
- [ ] README.md follows standard template
- [ ] All naming validated with `refine-standards check`

---

## Enforcement

### Automated Checks

**Pre-commit Hooks**:
```bash
# Install
refine-standards hooks install

# Validates:
# - File naming
# - Terraform resource naming
# - Branch naming
# - Commit message format
```

**GitHub Actions**:
```yaml
# .github/workflows/standards.yml
- name: Check naming standards
  run: refine-standards check --fail-on-violation
```

**Terraform Validation**:
```hcl
# Add to modules
variable "resource_name" {
  validation {
    condition     = can(regex("^[a-z0-9_]+$", var.resource_name))
    error_message = "Resource name must be snake_case."
  }
}
```

---

## References

- Main Standards: `README.md`
- Terraform Guide: `language-guides/terraform.md`
- YAML Guide: `language-guides/yaml.md`
- Shell Guide: `language-guides/shell.md`
- Validation Rules: `governance/naming-validation-rules.json`
- CLI Tool: `automation/refine-standards/`

---

**Document Version**: 1.0.0
**Last Updated**: 2025-11-07
**Maintained By**: refine.digital Platform Team
