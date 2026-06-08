# Terraform Language Standards for IaC Projects

Comprehensive guide for Infrastructure-as-Code development using Terraform at refine.digital.

**Document Version**: 1.0.0
**Last Updated**: 2025-11-07
**Maintained By**: refine.digital Platform Team

---

## Table of Contents

1. [File Naming Conventions](#file-naming-conventions)
2. [Folder Structure Best Practices](#folder-structure-best-practices)
3. [Code Style and Formatting](#code-style-and-formatting)
4. [Naming Conventions](#naming-conventions)
5. [Documentation Requirements](#documentation-requirements)
6. [Best Practices](#best-practices)
7. [Automation Tools](#automation-tools)
8. [VS Code Setup](#vs-code-setup)
9. [Pre-commit Hooks](#pre-commit-hooks)
10. [Code Examples](#code-examples)
11. [AWS Resource Naming and Tags](#aws-resource-naming-and-tags)
12. [Multi-Environment Patterns](#multi-environment-patterns)
13. [Module Versioning](#module-versioning)

---

## File Naming Conventions

### Standard Terraform Files

All Terraform files must use `.tf` extension with descriptive, lowercase names.

**Pattern**: `^[a-z0-9_-]+\.tf$`

**Required Files** (in every Terraform project or module):

```
main.tf                 # Primary resources and module calls
variables.tf            # Input variable definitions
outputs.tf              # Output value definitions
versions.tf             # Terraform version and provider versions
providers.tf            # Provider configuration and requirements
backend.tf              # Backend configuration
locals.tf               # Local value definitions
data.tf                 # Data source definitions
terraform.tfvars        # Variable values (GITIGNORED)
terraform.tfvars.example # Example variable template (in repo)
```

**Naming Convention**:

```
# Standard files (required)
main.tf                 ✅
variables.tf            ✅
outputs.tf              ✅
versions.tf             ✅
providers.tf            ✅
backend.tf              ✅

# Resource-specific files (optional but recommended)
vpc.tf                  ✅ (VPC resources)
subnets.tf              ✅ (Subnet resources)
nat_gateway.tf          ✅ (NAT Gateway resources)
security_groups.tf      ✅ (Security Group resources)
iam_roles.tf            ✅ (IAM resources)
rds.tf                  ✅ (RDS resources)
eks_cluster.tf          ✅ (EKS resources)
eks_node_groups.tf      ✅ (EKS node groups)

# Bad naming patterns
Main.tf                 ❌ (Capital letters)
VPC.tf                  ❌ (All caps)
vpc_MAIN.tf             ❌ (Mixed case)
main-file.tf            ❌ (Hyphens in resource files)
main.tfvars             ❌ (Wrong extension)
main.tf.bak             ❌ (Backup files belong in .gitignore)
```

### Module Files

Module files follow the same naming conventions. Standard module structure:

```
modules/
├── vpc/
│   ├── main.tf          ✅
│   ├── variables.tf     ✅
│   ├── outputs.tf       ✅
│   ├── versions.tf      ✅
│   └── README.md        ✅
├── eks-cluster/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   ├── versions.tf
│   └── README.md
└── nat-gateway/
    ├── main.tf
    ├── variables.tf
    ├── outputs.tf
    ├── versions.tf
    └── README.md
```

### Variable and Values Files

```
# Root module
terraform.tfvars              # Values for current environment (GITIGNORED)
terraform.tfvars.example      # Template for values (in repo)

# Environment-specific values
environments/dev/terraform.tfvars
environments/staging/terraform.tfvars
environments/prod/terraform.tfvars

# Bad patterns
dev.tfvars                    ❌ (Use environments/dev/)
prod.tfvars                   ❌ (Use environments/prod/)
values.tf                     ❌ (Use terraform.tfvars)
terraform.auto.tfvars         ⚠️  (Avoid auto-loading, be explicit)
```

---

## Folder Structure Best Practices

### Root Project Structure

```
infrastructure-vpc/
├── README.md                          # Project overview
├── CONTRIBUTING.md                    # Contribution guidelines
├── .gitignore                         # Git ignore rules
├── .gitattributes                     # Git attributes
├── .editorconfig                      # Editor configuration
├── .terraform-version                 # Terraform version
├── .tflint.hcl                        # tflint configuration
├── terraform.tfvars.example           # Variable template
│
├── .github/
│   └── workflows/
│       ├── terraform-validate.yml     # Validation workflow
│       ├── terraform-plan.yml         # Plan workflow
│       └── terraform-apply.yml        # Apply workflow
│
├── .vscode/
│   ├── settings.json                  # VS Code settings
│   └── extensions.json                # Recommended extensions
│
├── terraform/
│   └── main/
│       ├── main.tf                    # Root resources
│       ├── variables.tf
│       ├── outputs.tf
│       ├── versions.tf
│       ├── providers.tf
│       └── backend.tf
│
├── modules/
│   ├── vpc/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   │   ├── versions.tf
│   │   ├── README.md
│   │   └── examples/
│   │       └── basic/
│   │           └── main.tf
│   │
│   ├── eks-cluster/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   │   ├── versions.tf
│   │   └── README.md
│   │
│   └── nat-gateway/
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       ├── versions.tf
│       └── README.md
│
├── environments/
│   ├── dev/
│   │   ├── main.tf                    # Environment-specific resources
│   │   ├── terraform.tfvars           # Environment variables (GITIGNORED)
│   │   ├── terraform.tfvars.example   # Template
│   │   ├── backend.tf                 # Environment backend config
│   │   └── README.md
│   │
│   ├── staging/
│   │   ├── main.tf
│   │   ├── terraform.tfvars
│   │   ├── terraform.tfvars.example
│   │   ├── backend.tf
│   │   └── README.md
│   │
│   └── prod/
│       ├── main.tf
│       ├── terraform.tfvars
│       ├── terraform.tfvars.example
│       ├── backend.tf
│       └── README.md
│
├── scripts/
│   ├── init-terraform.sh              # Initialize Terraform
│   ├── plan-all-environments.sh        # Plan all environments
│   ├── apply-all-environments.sh       # Apply all environments
│   ├── validate-terraform.sh           # Validate configuration
│   └── README.md
│
├── docs/
│   ├── ARCHITECTURE.md                # Architecture overview
│   ├── DEPLOYMENT.md                  # Deployment guide
│   ├── TROUBLESHOOTING.md             # Troubleshooting guide
│   ├── SECURITY.md                    # Security considerations
│   └── examples/
│       ├── basic-setup.md
│       └── advanced-configuration.md
│
└── .terraform-docs.yml                # terraform-docs configuration
```

### Modules Structure

Each module should follow this structure:

```
modules/{module-name}/
├── README.md                # Module documentation
├── main.tf                  # Main resource definitions
├── variables.tf             # Input variables
├── outputs.tf               # Output values
├── versions.tf              # Provider version constraints
├── locals.tf                # Local values (optional)
├── data.tf                  # Data sources (optional)
│
├── examples/
│   ├── basic/
│   │   ├── main.tf
│   │   └── outputs.tf
│   │
│   ├── advanced/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── terraform.tfvars.example
│   │
│   └── complete/
│       ├── main.tf
│       ├── variables.tf
│       ├── terraform.tfvars.example
│       └── outputs.tf
│
└── tests/
    ├── unit.tf              # Unit tests (if using Terraform test)
    └── integration.tf       # Integration tests
```

### Environment Structure

```
environments/{environment}/
├── main.tf                  # Configuration for environment
├── terraform.tfvars         # Values (GITIGNORED)
├── terraform.tfvars.example # Template for values
├── backend.tf               # Backend configuration
├── providers.tf             # Provider overrides
├── variables.tf             # Environment variables (optional)
├── outputs.tf               # Environment outputs
├── README.md                # Environment documentation
└── .tfvars                  # Historical state (reference only)
```

---

## Code Style and Formatting

### Terraform Format Rules

Terraform uses `terraform fmt` for automatic formatting. All code must pass `terraform fmt` checks.

**Key Formatting Rules**:

```hcl
# Indentation: 2 spaces (enforced by terraform fmt)
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true

  tags = {
    Name        = "main"
    Environment = "prod"
  }
}

# Block alignment: Arguments aligned for readability
resource "aws_subnet" "public" {
  vpc_id              = aws_vpc.main.id
  cidr_block          = "10.0.1.0/24"
  availability_zone   = "us-east-1a"
  map_public_ip_on_launch = true

  tags = {
    Name = "public-subnet-a"
  }
}

# Line length: Keep lines under 100 characters
# If exceeding, wrap to next line

variable "availability_zones" {
  type        = list(string)
  description = "Availability zones for the VPC"
  default = [
    "us-east-1a",
    "us-east-1b"
  ]
}

# Complex arguments on separate lines
locals {
  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
    CreatedAt   = timestamp()
  }
}
```

### Formatting Blocks

**Resource blocks**:
```hcl
# Good: Clear structure
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  subnet_id     = aws_subnet.public.id

  vpc_security_group_ids = [
    aws_security_group.web.id
  ]

  tags = {
    Name = "web-server"
  }
}
```

**Data source blocks**:
```hcl
# Good: Clear structure with proper spacing
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"]  # Canonical

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}
```

**Variable blocks**:
```hcl
# Good: Description and validation
variable "vpc_cidr_block" {
  type        = string
  description = "CIDR block for the VPC"
  default     = "10.0.0.0/16"

  validation {
    condition     = can(cidrhost(var.vpc_cidr_block, 0))
    error_message = "VPC CIDR block must be valid."
  }
}

# Good: Type constraints for clarity
variable "environment" {
  type        = string
  description = "Environment name"

  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}
```

### Comments and Documentation

```hcl
# Use comments sparingly - code should be self-documenting
# Use comments for WHY, not WHAT

# BAD: Stating the obvious
# Create a VPC
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr_block
}

# GOOD: Explaining the reasoning
# Enable DNS to support ECS service discovery
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr_block
  enable_dns_hostnames = true
  enable_dns_support   = true
}

# GOOD: Explaining non-obvious configuration
# Use t3.small for cost optimization in dev; larger instances prevent throttling
resource "aws_instance" "web" {
  instance_type = var.environment == "prod" ? "t3.large" : "t3.small"
}

# GOOD: Documenting conditional logic
# Only create NAT Gateway if running in prod or staging
# Dev environment uses a NAT instance for cost savings
resource "aws_nat_gateway" "main" {
  count         = var.environment != "dev" ? 1 : 0
  allocation_id = aws_eip.nat.id
  subnet_id     = aws_subnet.public.id
}
```

---

## Naming Conventions

### Resource Naming

**Pattern**: `^[a-z0-9_]+$` (snake_case)

**Structure**: `{resource_type}_{purpose}[_{environment}]`

```hcl
# Good: Clear, descriptive resource names
resource "aws_vpc" "main" { }
resource "aws_subnet" "public_a" { }
resource "aws_subnet" "private_b" { }
resource "aws_nat_gateway" "main" { }
resource "aws_eks_cluster" "prod" { }
resource "aws_rds_instance" "postgres_main" { }
resource "aws_elasticache_cluster" "redis_cache" { }
resource "aws_s3_bucket" "terraform_state" { }
resource "aws_security_group" "alb_ingress" { }

# Bad: Unclear or improperly formatted
resource "aws_vpc" "VPC" { }              # All caps
resource "aws_vpc" "vpc-main" { }         # Hyphens not allowed
resource "aws_vpc" "myVpc" { }            # CamelCase
resource "aws_subnet" "PublicSubnetA" { } # CamelCase
resource "aws_instance" "web_server_1" { }  # Avoid numbers, use environment var
```

### Variable Naming

**Pattern**: `^[a-z0-9_]+$` (snake_case)

**Structure**: `{context}_{property}[_{qualifier}]`

```hcl
# Good: Context-specific, descriptive names
variable "vpc_cidr_block" { }
variable "vpc_enable_dns" { }
variable "environment_name" { }
variable "instance_type" { }
variable "subnet_availability_zones" { }
variable "enable_nat_gateway" { }
variable "enable_monitoring" { }
variable "cluster_version" { }
variable "database_engine" { }
variable "database_allocated_storage" { }
variable "tags" { }

# Bad: Vague or inconsistent naming
variable "cidr" { }              # Missing context
variable "az" { }                # Too abbreviated
variable "env" { }               # Abbreviation not allowed
variable "vpcCidr" { }           # CamelCase
variable "vpc-cidr" { }          # Hyphens
variable "VPC_CIDR" { }          # All caps
variable "x" { }                 # Single character
```

### Output Naming

**Pattern**: `^[a-z0-9_]+$` (snake_case)

**Structure**: `{resource}_{property}`

```hcl
# Good: Descriptive output names
output "vpc_id" {
  value = aws_vpc.main.id
}

output "vpc_cidr_block" {
  value = aws_vpc.main.cidr_block
}

output "subnet_ids" {
  value = aws_subnet.public[*].id
}

output "nat_gateway_ips" {
  value = aws_nat_gateway.main[*].public_ip
}

output "eks_cluster_endpoint" {
  value = aws_eks_cluster.main.endpoint
}

output "eks_cluster_name" {
  value = aws_eks_cluster.main.name
}

output "database_endpoint" {
  value = aws_db_instance.postgres.endpoint
}

# Bad: Unclear or overly abbreviated
output "id" { }              # Too vague
output "vpcId" { }           # CamelCase
output "vpc-id" { }          # Hyphens
output "VPC_ID" { }          # All caps
output "output1" { }         # No meaning
```

### Module Naming

**Folder Name Pattern**: `^[a-z0-9-]+$` (kebab-case)

**Module Call Pattern**: `snake_case` for the instance name

```hcl
# Good: Module folder naming (kebab-case)
modules/
├── vpc/
├── eks-cluster/
├── nat-gateway/
├── rds-instance/
└── security-group/

# Good: Module instantiation (snake_case for instance)
module "vpc" {
  source = "./modules/vpc"
}

module "eks_cluster" {
  source = "./modules/eks-cluster"
}

module "nat_gateway" {
  source = "./modules/nat-gateway"
}

module "postgres_database" {
  source = "./modules/rds-instance"
}

# Good: Multiple instances with descriptive names
module "vpc_main" {
  source = "./modules/vpc"
  vpc_name = "main"
}

module "vpc_secondary" {
  source = "./modules/vpc"
  vpc_name = "secondary"
}

# Bad: Module naming
modules/
├── VPCModule/        # CamelCase folder
├── vpc_folder/       # snake_case folder (use kebab-case)
└── my-vpc/           # Descriptive folder (keep generic)

module "VPC" { }      # CamelCase instance (use snake_case)
module "vpc-main" { } # Hyphens in instance (use snake_case)
```

### Data Source Naming

**Pattern**: `^[a-z0-9_]+$` (snake_case)

**Structure**: Similar to resources

```hcl
# Good: Descriptive data source names
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"]
}

data "aws_availability_zones" "available" {
  state = "available"
}

data "aws_caller_identity" "current" { }

data "aws_route53_zone" "main" {
  name = "example.com"
}

data "aws_vpc" "main" {
  filter {
    name   = "tag:Name"
    values = ["main-vpc"]
  }
}

# Bad: Data source naming
data "aws_ami" "ubuntu_20_04" { }     # Too specific, version in AMI itself
data "aws_ami" "IMAGE" { }             # All caps
data "aws_vpc" "VPC-Main" { }          # CamelCase
```

### Local Naming

**Pattern**: `^[a-z0-9_]+$` (snake_case)

**Structure**: `{context}_{value_type}`

```hcl
# Good: Descriptive local names
locals {
  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
  }

  vpc_name = "${var.project_name}-vpc-${var.environment}"

  subnet_names = [
    "${var.project_name}-subnet-public-a",
    "${var.project_name}-subnet-public-b",
    "${var.project_name}-subnet-private-a",
    "${var.project_name}-subnet-private-b"
  ]

  environment_config = var.environment == "prod" ? {
    instance_type = "t3.large"
    replicas      = 3
  } : {
    instance_type = "t3.small"
    replicas      = 1
  }
}

# Bad: Local naming
locals {
  tags = { }          # Too vague (use common_tags)
  x = "value"         # Single character
  CONST = "value"     # All caps for local (use for shell vars only)
  my-value = "value"  # Hyphens
}
```

---

## Documentation Requirements

### README.md for Modules

Every module must have a comprehensive README.md following this template:

```markdown
# {Module Name}

> One-line description of the module

## Overview

Detailed description of what this module does and when to use it.

## Usage

### Basic Example

\`\`\`hcl
module "vpc" {
  source = "./modules/vpc"

  vpc_name     = "main"
  vpc_cidr     = "10.0.0.0/16"
  environment  = "prod"
}
\`\`\`

### Advanced Example

\`\`\`hcl
module "vpc" {
  source = "./modules/vpc"

  vpc_name               = "main"
  vpc_cidr               = "10.0.0.0/16"
  environment            = "prod"
  enable_nat_gateway     = true
  enable_vpn_gateway     = true
  availability_zones     = ["us-east-1a", "us-east-1b"]
  enable_vpc_endpoints   = true

  tags = {
    Team        = "platform"
    CostCenter  = "engineering"
  }
}
\`\`\`

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| vpc_name | Name of the VPC | `string` | - | yes |
| vpc_cidr | CIDR block for VPC | `string` | - | yes |
| environment | Environment name | `string` | - | yes |
| enable_nat_gateway | Enable NAT Gateway | `bool` | `false` | no |
| tags | Tags to apply to all resources | `map(string)` | `{}` | no |

## Outputs

| Name | Description |
|------|-------------|
| vpc_id | ID of the VPC |
| vpc_cidr_block | CIDR block of the VPC |
| subnet_ids | IDs of subnets |
| nat_gateway_ips | Public IPs of NAT Gateways |

## Examples

See the `examples/` directory for additional examples:
- `examples/basic/` - Basic VPC setup
- `examples/advanced/` - Advanced configuration with private subnets
- `examples/complete/` - Complete production configuration

## Design Decisions

### Why enable_nat_gateway defaults to false?
Cost optimization for development environments. Production should explicitly enable.

### Security Groups in separate module?
Allows different release cycle. Security groups change more frequently than VPC structure.

## Testing

\`\`\`bash
# Validate module
cd modules/vpc
terraform validate

# Format check
terraform fmt -check

# Run tflint
tflint
\`\`\`

## Related Modules

- [nat-gateway](../nat-gateway/) - NAT Gateway module
- [security-group](../security-group/) - Security Group module

## Requirements

- Terraform >= 1.0
- AWS Provider >= 4.0

## License

Apache 2.0 - See LICENSE file
```

### Inline Comments

```hcl
# Comment style: Brief explanation of non-obvious logic

# Example 1: WHY something is done
# Enable DNS hostnames for ECS service discovery
resource "aws_vpc" "main" {
  enable_dns_hostnames = true
}

# Example 2: Complex conditional
# Use t3.large in production for better performance; t3.small in dev for cost
resource "aws_instance" "web" {
  instance_type = var.environment == "prod" ? "t3.large" : "t3.small"
}

# Example 3: Explaining non-obvious default
# Max retries set to 3 based on AWS Lambda timeout behavior
variable "max_retries" {
  type    = number
  default = 3  # AWS Lambda standard timeout is 15 seconds, 3 retries = 5 second intervals
}

# Example 4: Reference to external documentation
# See https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html
resource "aws_nat_gateway" "main" {
  allocation_id = aws_eip.nat.id
  subnet_id     = aws_subnet.public.id
}
```

### terraform-docs Configuration

Create `.terraform-docs.yml`:

```yaml
version: ">= 0.14.0"

formatter: markdown

content: |-
  # {{ .Name }}

  > {{ .Description }}

  ## Usage

  \`\`\`hcl
  {{ .Module }}
  \`\`\`

  ## Requirements

  {{ range .Requirements }}
  - {{ .Name }} {{ .Version }}
  {{- end }}

  ## Inputs

  {{ .Inputs }}

  ## Outputs

  {{ .Outputs }}

  ## Examples

  See `examples/` directory.

sort:
  enabled: true
  by: required

sections:
  inputs:
    - name: Required Inputs
      summary: These inputs are required
      # Placeholder content
    - name: Optional Inputs
      summary: These inputs are optional
      # Placeholder content

output:
  file: README.md
  mode: inject
  template: |-
    <!-- BEGIN_TF_DOCS -->
    {{ .Content }}
    <!-- END_TF_DOCS -->
```

### Commit Messages

Follow conventional commit format for clarity:

```bash
# Good commit messages
git commit -m "feat: add VPC module with public and private subnets"
git commit -m "fix: correct security group rule for RDS access"
git commit -m "refactor: simplify subnet naming in vpc module"
git commit -m "docs: update README with examples"
git commit -m "chore: update Terraform version constraint to >=1.2"

# Bad commit messages
git commit -m "Update stuff"
git commit -m "Fix bugs"
git commit -m "WIP"
git commit -m "final version"
git commit -m "changes"
```

---

## Best Practices

### Module Design

**Principle**: Modules should be focused, reusable, and composable.

```hcl
# GOOD: Focused module
# modules/vpc/main.tf - Only handles VPC and subnets
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr_block
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = merge(
    var.tags,
    { Name = "${var.project_name}-vpc" }
  )
}

resource "aws_subnet" "public" {
  for_each = var.public_subnets

  vpc_id            = aws_vpc.main.id
  cidr_block        = each.value.cidr
  availability_zone = each.value.availability_zone

  tags = merge(
    var.tags,
    { Name = "${var.project_name}-subnet-public-${each.key}" }
  )
}

# BAD: Module does too much
# This should be split into separate modules
resource "aws_vpc" "main" { }
resource "aws_subnet" "public" { }
resource "aws_nat_gateway" "main" { }
resource "aws_security_group" "web" { }
resource "aws_security_group_rule" "web_ingress" { }
resource "aws_rds_instance" "postgres" { }       # RDS belongs in separate module
resource "aws_elasticsearch_domain" "logs" { }   # ES belongs in separate module
```

**Composition Example**:

```hcl
# Root module composes smaller focused modules
module "vpc" {
  source = "./modules/vpc"

  vpc_cidr_block = var.vpc_cidr_block
  project_name   = var.project_name
  environment    = var.environment
}

module "nat_gateway" {
  source = "./modules/nat-gateway"

  vpc_id             = module.vpc.vpc_id
  public_subnet_id   = module.vpc.public_subnet_ids[0]
  private_subnet_ids = module.vpc.private_subnet_ids

  tags = local.common_tags
}

module "eks_cluster" {
  source = "./modules/eks-cluster"

  vpc_id           = module.vpc.vpc_id
  subnet_ids       = module.vpc.private_subnet_ids
  cluster_version  = var.kubernetes_version

  depends_on = [module.nat_gateway]
}
```

### State Management

**Best Practices**:

```hcl
# Good: Remote state in S3 with locking
terraform {
  backend "s3" {
    bucket         = "refine-terraform-state-prod"
    key            = "infrastructure-vpc/prod/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}

# Good: Separate state per environment
environments/
├── dev/
│   ├── backend.tf  # state in separate key
│   └── main.tf
├── staging/
│   ├── backend.tf  # state in separate key
│   └── main.tf
└── prod/
    ├── backend.tf  # state in separate key
    └── main.tf

# BAD: Local state
# No backend configured - state stored locally (NOT FOR PRODUCTION)

# BAD: Shared state
# Multiple environments share the same backend key - will cause conflicts
```

### Variable Organization

```hcl
# GOOD: variables.tf with all variables defined
variable "project_name" {
  type        = string
  description = "Name of the project"

  validation {
    condition     = can(regex("^[a-z0-9-]+$", var.project_name))
    error_message = "Project name must be kebab-case."
  }
}

variable "environment" {
  type        = string
  description = "Environment (dev, staging, prod)"

  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

variable "vpc_cidr_block" {
  type        = string
  description = "CIDR block for VPC"
  default     = "10.0.0.0/16"

  validation {
    condition     = can(cidrhost(var.vpc_cidr_block, 0))
    error_message = "Must be a valid CIDR block."
  }
}

# GOOD: terraform.tfvars organized by environment
# environments/prod/terraform.tfvars
project_name = "infrastructure-vpc"
environment  = "prod"
vpc_cidr_block = "10.0.0.0/16"

# BAD: Hardcoded values scattered in main.tf
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"  # ❌ Should be a variable
}

# BAD: All variables in main.tf
resource "aws_instance" "web" { }
variable "instance_type" { }
variable "instance_count" { }
# ... 50 more variables mixed in
```

### Output Organization

```hcl
# GOOD: outputs.tf with complete documentation
output "vpc_id" {
  value       = aws_vpc.main.id
  description = "ID of the VPC"
}

output "vpc_cidr_block" {
  value       = aws_vpc.main.cidr_block
  description = "CIDR block of the VPC"
}

output "public_subnet_ids" {
  value       = aws_subnet.public[*].id
  description = "IDs of public subnets"
}

output "private_subnet_ids" {
  value       = aws_subnet.private[*].id
  description = "IDs of private subnets"
}

output "nat_gateway_ips" {
  value       = aws_nat_gateway.main[*].public_ip
  description = "Public IPs of NAT Gateways"
  sensitive   = false  # Safe to expose
}

# BAD: Outputs without descriptions
output "vpc_id" {
  value = aws_vpc.main.id
}

output "output1" {
  value = random_value
}
```

### Resource Organization in main.tf

```hcl
# GOOD: Organized by logical grouping
# VPC
resource "aws_vpc" "main" { }

# Subnets
resource "aws_subnet" "public" { }
resource "aws_subnet" "private" { }

# Internet Gateway
resource "aws_internet_gateway" "main" { }

# NAT Gateway
resource "aws_eip" "nat" { }
resource "aws_nat_gateway" "main" { }

# Route Tables
resource "aws_route_table" "public" { }
resource "aws_route_table" "private" { }

# BAD: Random order
resource "aws_instance" "web" { }
resource "aws_vpc" "main" { }
resource "aws_security_group" "web" { }
resource "aws_subnet" "public" { }
resource "aws_route_table" "public" { }
```

---

## Automation Tools

### terraform fmt

Automatic code formatting.

```bash
# Format current directory
terraform fmt

# Format recursively
terraform fmt -recursive

# Check without modifying
terraform fmt -check -recursive

# Recommended: Add to pre-commit hooks
# See Pre-commit Hooks section
```

### terraform validate

Syntax validation.

```bash
# Validate configuration
terraform validate

# In CI/CD pipelines
terraform validate && echo "Validation passed"

# Combined with other checks
terraform validate && terraform fmt -check
```

### tflint

Linting and best practices checking.

**Installation**:
```bash
# macOS
brew install tflint

# Linux
curl https://raw.githubusercontent.com/terraform-linters/tflint/master/install_linux.sh | bash

# Docker
docker run --rm -v $(pwd):/code -w /code ghcr.io/terraform-linters/tflint
```

**Configuration** (`.tflint.hcl`):

```hcl
plugin "aws" {
  enabled = true
  version = "0.25.0"
  source  = "github.com/terraform-linters/tflint-ruleset-aws"
}

rule "terraform_naming_convention" {
  enabled = true

  variable {
    convention = "snake_case"
  }

  resource {
    convention = "snake_case"
  }

  module {
    convention = "snake_case"
  }

  output {
    convention = "snake_case"
  }

  local {
    convention = "snake_case"
  }
}

rule "terraform_unused_required_providers" {
  enabled = true
}

rule "terraform_required_providers" {
  enabled = true
}

rule "aws_resource_missing_tags" {
  enabled = true
  tags    = ["Name", "Environment", "Project"]
}
```

**Usage**:
```bash
# Lint current directory
tflint

# Lint recursively
tflint --recursive

# Lint with AWS plugin
tflint --init  # Download plugins first
tflint

# Format: Show detailed output
tflint --format json > results.json
```

### tfsec

Security scanning.

**Installation**:
```bash
# macOS
brew install tfsec

# Linux
curl -s https://raw.githubusercontent.com/aquasecurity/tfsec/master/scripts/install_linux.sh | bash

# Docker
docker run --rm -v $(pwd):/root aquasec/tfsec /root
```

**Configuration** (`.tfsec.yml`):

```yaml
minimum_severity: WARNING
excluded_checks:
  - AVD-AWS-0272  # Example: If you have a specific exception

rules:
  - id: AVD-AWS-0006
    description: "S3 bucket must have encryption"
    level: ERROR
```

**Usage**:
```bash
# Run security scan
tfsec

# Scan specific path
tfsec ./modules

# Output formats
tfsec --format json > security-report.json
tfsec --format sarif > security-report.sarif

# Filter by severity
tfsec --minimum-severity CRITICAL
```

### terraform-docs

Auto-generate module documentation.

**Installation**:
```bash
# macOS
brew install terraform-docs

# Linux
curl -Lo /tmp/terraform-docs.tar.gz https://github.com/terraform-docs/terraform-docs/releases/latest/download/terraform-docs-v0.16.0-linux-amd64.tar.gz
tar -xzf /tmp/terraform-docs.tar.gz -C /usr/local/bin
```

**Usage**:
```bash
# Generate documentation for module
cd modules/vpc
terraform-docs markdown table .

# Generate and inject into README
terraform-docs markdown table . > README.md

# With configuration file
terraform-docs -c .terraform-docs.yml

# Mark sections for injection
# In README.md:
# <!-- BEGIN_TF_DOCS -->
# { .Content }
# <!-- END_TF_DOCS -->
```

### Complete Automation Script

Create `scripts/validate-terraform.sh`:

```bash
#!/usr/bin/env bash
set -euo pipefail

# Colors
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m'

echo "Starting Terraform validation..."

# 1. Format check
echo -e "\n${YELLOW}Checking format...${NC}"
if terraform fmt -check -recursive; then
  echo -e "${GREEN}✓ Format check passed${NC}"
else
  echo -e "${RED}✗ Format check failed. Run: terraform fmt -recursive${NC}"
  exit 1
fi

# 2. Validation
echo -e "\n${YELLOW}Validating configuration...${NC}"
if terraform validate; then
  echo -e "${GREEN}✓ Validation passed${NC}"
else
  echo -e "${RED}✗ Validation failed${NC}"
  exit 1
fi

# 3. tflint
echo -e "\n${YELLOW}Running tflint...${NC}"
if tflint --recursive; then
  echo -e "${GREEN}✓ tflint passed${NC}"
else
  echo -e "${RED}✗ tflint found issues${NC}"
  exit 1
fi

# 4. tfsec
echo -e "\n${YELLOW}Running security scan...${NC}"
if tfsec --recursive --exit-code 0; then
  echo -e "${GREEN}✓ Security scan passed${NC}"
else
  echo -e "${YELLOW}⚠ Security scan found issues (see above)${NC}"
fi

echo -e "\n${GREEN}All checks passed!${NC}"
```

Make executable:
```bash
chmod +x scripts/validate-terraform.sh
./scripts/validate-terraform.sh
```

---

## VS Code Setup

### Extensions

Install these extensions:

1. **HashiCorp Terraform** (official)
   - Syntax highlighting
   - Code completion
   - Terraform formatting
   - State inspection

2. **Terraform** (alternative/backup)
   - Additional features
   - Linting integration

3. **CloudFormation** (AWS companion)
   - For CloudFormation templates

4. **AWS Toolkit**
   - AWS service integration
   - Lambda debugging

### Settings (`.vscode/settings.json`)

```json
{
  "[terraform]": {
    "editor.defaultFormatter": "hashicorp.terraform",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll": true
    },
    "editor.rulers": [80, 100, 120],
    "editor.tabSize": 2,
    "editor.insertSpaces": true,
    "editor.trimAutoWhitespace": true
  },
  "terraform.languageServer": {
    "enable": true,
    "logFilePath": "${workspaceFolder}/.terraform.log",
    "maxNumberOfProblems": 100,
    "ignoreDirectories": [".terraform", ".git"],
    "experimentalFeatures": {
      "validateOnSave": true,
      "prefillRequiredFields": true
    }
  },
  "terraform.format": {
    "enable": true,
    "ignoreExtensionIfFormatterIsDefined": false
  },
  "editor.formatOnSave": true,
  "editor.rulers": [80, 100, 120],
  "files.trimTrailingWhitespace": true,
  "files.insertFinalNewline": true,
  "files.exclude": {
    "**/.terraform": true,
    "**/.terragrunt-cache": true,
    "**/.terraform.lock.hcl": false
  }
}
```

### Extensions (`.vscode/extensions.json`)

```json
{
  "recommendations": [
    "hashicorp.terraform",
    "amazonwebservices.aws-toolkit-vscode",
    "eamodio.gitlens",
    "redhat.vscode-yaml",
    "esbenp.prettier-vscode",
    "shellformat.shell-format"
  ]
}
```

### Workspace File (`.code-workspace`)

```json
{
  "folders": [
    {
      "path": "."
    }
  ],
  "settings": {
    "terraform.languageServer.enable": true,
    "terraform.format.enable": true,
    "[terraform]": {
      "editor.defaultFormatter": "hashicorp.terraform",
      "editor.formatOnSave": true
    }
  }
}
```

---

## Pre-commit Hooks

Setup pre-commit hooks to validate before committing.

### Installation

```bash
# Install pre-commit framework
pip install pre-commit

# Navigate to repository
cd /path/to/infrastructure-vpc

# Create .pre-commit-config.yaml
touch .pre-commit-config.yaml

# Install hooks
pre-commit install

# (Optional) Run against all files
pre-commit run --all-files
```

### Configuration (`.pre-commit-config.yaml`)

```yaml
repos:
  # Terraform formatting
  - repo: https://github.com/antonbabenko/pre-commit-terraform
    rev: v1.81.0
    hooks:
      - id: terraform_fmt
        args: [--args=-recursive]

      - id: terraform_validate
        args: [--args=-json]

      - id: terraform_tflint
        args: [--args=--recursive]

      - id: terraform_docs
        args: [--args=--sort-by-required]

      - id: terraform_checkov
        args: [--framework terraform --quiet]

  # General file checks
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-added-large-files
        args: ['--maxkb=1000']
      - id: detect-private-key

  # Shell script checks
  - repo: https://github.com/shellcheck-py/shellcheck-py
    rev: v0.9.0.5
    hooks:
      - id: shellcheck
        args: [--severity=warning]

  # Markdown linting
  - repo: https://github.com/igorshubovych/markdownlint-cli
    rev: v0.33.0
    hooks:
      - id: markdownlint
        args: [--fix]

  # YAML linting
  - repo: https://github.com/adrienverge/yamllint
    rev: v1.29.0
    hooks:
      - id: yamllint
        args: [--strict]
```

### Manual Hook Testing

```bash
# Test hooks without committing
pre-commit run --all-files

# Run specific hook
pre-commit run terraform_fmt --all-files
pre-commit run terraform_validate --all-files
pre-commit run terraform_tflint --all-files

# Skip hooks (NOT RECOMMENDED)
git commit --no-verify
```

---

## Code Examples

### Example 1: Good VPC Configuration

**File**: `modules/vpc/main.tf`

```hcl
terraform {
  required_version = ">= 1.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region

  default_tags {
    tags = local.common_tags
  }
}

locals {
  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
    CreatedAt   = timestamp()
  }
}

# Main VPC
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr_block
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name = "${var.project_name}-vpc-${var.environment}"
  }
}

# Public subnets
resource "aws_subnet" "public" {
  for_each = var.public_subnets

  vpc_id            = aws_vpc.main.id
  cidr_block        = each.value.cidr_block
  availability_zone = each.value.availability_zone

  map_public_ip_on_launch = true

  tags = {
    Name = "${var.project_name}-subnet-public-${each.key}-${var.environment}"
    Type = "Public"
  }
}

# Private subnets
resource "aws_subnet" "private" {
  for_each = var.private_subnets

  vpc_id            = aws_vpc.main.id
  cidr_block        = each.value.cidr_block
  availability_zone = each.value.availability_zone

  tags = {
    Name = "${var.project_name}-subnet-private-${each.key}-${var.environment}"
    Type = "Private"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "${var.project_name}-igw-${var.environment}"
  }
}

# Elastic IPs for NAT Gateways
resource "aws_eip" "nat" {
  for_each = var.public_subnets

  domain = "vpc"

  depends_on = [aws_internet_gateway.main]

  tags = {
    Name = "${var.project_name}-eip-nat-${each.key}-${var.environment}"
  }
}

# NAT Gateways
resource "aws_nat_gateway" "main" {
  for_each = var.public_subnets

  allocation_id = aws_eip.nat[each.key].id
  subnet_id     = aws_subnet.public[each.key].id

  depends_on = [aws_internet_gateway.main]

  tags = {
    Name = "${var.project_name}-nat-${each.key}-${var.environment}"
  }
}

# Public route table
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block      = "0.0.0.0/0"
    gateway_id      = aws_internet_gateway.main.id
  }

  tags = {
    Name = "${var.project_name}-rt-public-${var.environment}"
  }
}

# Associate public route table with public subnets
resource "aws_route_table_association" "public" {
  for_each = aws_subnet.public

  subnet_id      = each.value.id
  route_table_id = aws_route_table.public.id
}

# Private route tables (one per NAT)
resource "aws_route_table" "private" {
  for_each = var.private_subnets

  vpc_id = aws_vpc.main.id

  tags = {
    Name = "${var.project_name}-rt-private-${each.key}-${var.environment}"
  }
}

# Private routes through NAT
resource "aws_route" "private_nat" {
  for_each = aws_route_table.private

  route_table_id         = each.value.id
  destination_cidr_block = "0.0.0.0/0"
  nat_gateway_id         = aws_nat_gateway.main[each.key].id
}

# Associate private route tables with private subnets
resource "aws_route_table_association" "private" {
  for_each = aws_subnet.private

  subnet_id      = each.value.id
  route_table_id = aws_route_table.private[each.key].id
}
```

**File**: `modules/vpc/variables.tf`

```hcl
variable "project_name" {
  type        = string
  description = "Name of the project (will be used in resource names)"

  validation {
    condition     = can(regex("^[a-z0-9-]+$", var.project_name))
    error_message = "Project name must be kebab-case."
  }
}

variable "environment" {
  type        = string
  description = "Environment name (dev, staging, prod)"

  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

variable "aws_region" {
  type        = string
  description = "AWS region"
  default     = "us-east-1"
}

variable "vpc_cidr_block" {
  type        = string
  description = "CIDR block for the VPC"

  validation {
    condition     = can(cidrhost(var.vpc_cidr_block, 0))
    error_message = "VPC CIDR block must be a valid IPv4 CIDR."
  }
}

variable "public_subnets" {
  type = map(object({
    cidr_block        = string
    availability_zone = string
  }))
  description = "Configuration for public subnets"

  validation {
    condition = alltrue([
      for subnet in var.public_subnets :
      can(cidrhost(subnet.cidr_block, 0))
    ])
    error_message = "All subnet CIDR blocks must be valid IPv4 CIDRs."
  }
}

variable "private_subnets" {
  type = map(object({
    cidr_block        = string
    availability_zone = string
  }))
  description = "Configuration for private subnets"

  validation {
    condition = alltrue([
      for subnet in var.private_subnets :
      can(cidrhost(subnet.cidr_block, 0))
    ])
    error_message = "All subnet CIDR blocks must be valid IPv4 CIDRs."
  }
}

variable "tags" {
  type        = map(string)
  description = "Additional tags to apply to all resources"
  default     = {}
}
```

**File**: `modules/vpc/outputs.tf`

```hcl
output "vpc_id" {
  value       = aws_vpc.main.id
  description = "ID of the VPC"
}

output "vpc_cidr_block" {
  value       = aws_vpc.main.cidr_block
  description = "CIDR block of the VPC"
}

output "public_subnet_ids" {
  value       = { for k, v in aws_subnet.public : k => v.id }
  description = "Map of public subnet IDs"
}

output "private_subnet_ids" {
  value       = { for k, v in aws_subnet.private : k => v.id }
  description = "Map of private subnet IDs"
}

output "nat_gateway_ips" {
  value       = { for k, v in aws_nat_gateway.main : k => v.public_ip }
  description = "Map of NAT Gateway public IPs"
}

output "internet_gateway_id" {
  value       = aws_internet_gateway.main.id
  description = "ID of the Internet Gateway"
}
```

### Example 2: Bad VPC Configuration (Anti-pattern)

```hcl
# ❌ BAD: Many violations

# Missing resource naming conventions
resource "aws_vpc" "VPC" {  # ❌ CamelCase
  cidr_block = "10.0.0.0/16"
  # ❌ Missing DNS settings
  # ❌ No tags
}

# ❌ Hardcoded values instead of variables
resource "aws_subnet" "PublicSubnetA" {  # ❌ CamelCase
  vpc_id            = aws_vpc.VPC.id  # ❌ Wrong reference
  cidr_block        = "10.0.1.0/24"   # ❌ Hardcoded
  availability_zone = "us-east-1a"    # ❌ Hardcoded
}

# ❌ Multiple resources in one block without organization
resource "aws_nat_gateway" "nat" {  # ❌ Too vague
  allocation_id = aws_eip.nat_ip.id
  subnet_id     = aws_subnet.PublicSubnetA.id
}

# ❌ No proper variable definitions
variable "cidr" {  # ❌ Too vague
  type = string
}

# ❌ No output definitions
# ❌ Missing version constraints
# ❌ No provider configuration
# ❌ No comments explaining decisions
```

### Example 3: Root Module Configuration

**File**: `environments/prod/main.tf`

```hcl
terraform {
  required_version = ">= 1.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  backend "s3" {
    bucket         = "refine-terraform-state-prod"
    key            = "infrastructure-vpc/prod/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}

provider "aws" {
  region = var.aws_region

  default_tags {
    tags = {
      Project     = var.project_name
      Environment = var.environment
      ManagedBy   = "Terraform"
      Repository  = "https://github.com/refinedigital/infrastructure-vpc"
    }
  }
}

module "vpc" {
  source = "../../modules/vpc"

  project_name = var.project_name
  environment  = var.environment
  aws_region   = var.aws_region

  vpc_cidr_block = var.vpc_cidr_block

  public_subnets = {
    a = {
      cidr_block        = "10.0.1.0/24"
      availability_zone = "${var.aws_region}a"
    }
    b = {
      cidr_block        = "10.0.2.0/24"
      availability_zone = "${var.aws_region}b"
    }
  }

  private_subnets = {
    a = {
      cidr_block        = "10.0.101.0/24"
      availability_zone = "${var.aws_region}a"
    }
    b = {
      cidr_block        = "10.0.102.0/24"
      availability_zone = "${var.aws_region}b"
    }
  }

  tags = var.additional_tags
}

module "eks_cluster" {
  source = "../../modules/eks-cluster"

  project_name = var.project_name
  environment  = var.environment

  vpc_id     = module.vpc.vpc_id
  subnet_ids = concat(
    values(module.vpc.public_subnet_ids),
    values(module.vpc.private_subnet_ids)
  )

  kubernetes_version = var.kubernetes_version

  depends_on = [module.vpc]
}
```

---

## AWS Resource Naming and Tags

### Naming Strategy

All AWS resources should have consistent, meaningful names.

**Pattern**: `{project}-{resource-type}-{identifier}-{environment}`

```hcl
# Example: infrastructure-vpc-prod
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr_block

  tags = {
    Name        = "${var.project_name}-vpc-${var.environment}"  # infrastructure-vpc-prod
    Environment = var.environment
    Project     = var.project_name
    ManagedBy   = "Terraform"
  }
}

# Example: infrastructure-subnet-public-a-prod
resource "aws_subnet" "public_a" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"

  tags = {
    Name        = "${var.project_name}-subnet-public-a-${var.environment}"
    Environment = var.environment
    Tier        = "Public"
  }
}

# Example: infrastructure-nat-gateway-a-prod
resource "aws_nat_gateway" "main_a" {
  allocation_id = aws_eip.nat_a.id
  subnet_id     = aws_subnet.public_a.id

  tags = {
    Name        = "${var.project_name}-nat-gateway-a-${var.environment}"
    Environment = var.environment
  }
}
```

### Required Tags

Implement a tagging strategy:

```hcl
# In locals.tf
locals {
  common_tags = {
    Project     = var.project_name          # Project identifier
    Environment = var.environment           # dev/staging/prod
    ManagedBy   = "Terraform"               # Infrastructure tool
    CreatedAt   = timestamp()                # Creation timestamp
    Repository  = var.repository_url        # Source control
    CostCenter  = var.cost_center           # For billing
    Owner       = var.owner_email           # Responsible team
    Compliance  = var.compliance_level      # Compliance requirement
  }
}

# Apply to all resources
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr_block

  tags = merge(
    local.common_tags,
    {
      Name = "${var.project_name}-vpc-${var.environment}"
    }
  )
}
```

---

## Multi-Environment Patterns

### Pattern 1: Environments as Directories

```
infrastructure-vpc/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   │   ├── terraform.tfvars
│   │   ├── terraform.tfvars.example
│   │   └── backend.tf
│   │
│   ├── staging/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   │   ├── terraform.tfvars
│   │   ├── terraform.tfvars.example
│   │   └── backend.tf
│   │
│   └── prod/
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       ├── terraform.tfvars
│       ├── terraform.tfvars.example
│       └── backend.tf
│
├── modules/
└── shared/
    ├── variables.tf  # Shared across environments
    ├── locals.tf
    └── data.tf
```

**Workflow**:
```bash
# Deploy dev
cd environments/dev
terraform init
terraform plan -var-file=terraform.tfvars
terraform apply

# Deploy staging
cd ../staging
terraform init
terraform plan -var-file=terraform.tfvars
terraform apply

# Deploy prod
cd ../prod
terraform init
terraform plan -var-file=terraform.tfvars
terraform apply
```

### Pattern 2: Single Root with Environment Variables

```
infrastructure-vpc/
├── main.tf              # Environment-aware configuration
├── variables.tf
├── outputs.tf
├── locals.tf
├── terraform.tfvars.dev
├── terraform.tfvars.staging
└── terraform.tfvars.prod
```

**Configuration**:
```hcl
# variables.tf
variable "environment" {
  type        = string
  description = "Environment (dev, staging, prod)"
}

# locals.tf
locals {
  environment_config = {
    dev = {
      instance_type   = "t3.small"
      desired_capacity = 1
      max_capacity     = 2
    }
    staging = {
      instance_type   = "t3.medium"
      desired_capacity = 2
      max_capacity     = 5
    }
    prod = {
      instance_type   = "t3.large"
      desired_capacity = 3
      max_capacity     = 10
    }
  }

  config = local.environment_config[var.environment]
}

# main.tf
resource "aws_instance" "web" {
  instance_type = local.config.instance_type
  # ... other config
}
```

**Workflow**:
```bash
# Deploy dev
terraform init
terraform plan -var-file=terraform.tfvars.dev
terraform apply -var-file=terraform.tfvars.dev

# Deploy staging
terraform plan -var-file=terraform.tfvars.staging
terraform apply -var-file=terraform.tfvars.staging

# Deploy prod
terraform plan -var-file=terraform.tfvars.prod
terraform apply -var-file=terraform.tfvars.prod
```

### Shared Values File

Create `environments/shared.tfvars`:

```hcl
# Values shared across all environments
project_name = "infrastructure-vpc"
aws_region   = "us-east-1"
cost_center  = "platform-team"
owner_email  = "platform@refine.digital"

# Environment-specific overrides
# environments/dev/terraform.tfvars:
environment          = "dev"
vpc_cidr_block      = "10.1.0.0/16"
enable_nat_gateway  = false  # Cost savings

# environments/prod/terraform.tfvars:
environment          = "prod"
vpc_cidr_block      = "10.0.0.0/16"
enable_nat_gateway  = true   # Required for production
```

---

## Module Versioning

### Git-based Module Versioning

For modules in separate repositories:

```hcl
# Use semver tags
module "vpc" {
  source = "git::https://github.com/refinedigital/module-vpc.git?ref=v1.0.0"

  # Configuration
  vpc_cidr_block = var.vpc_cidr_block
}

# Pin to specific commit
module "eks" {
  source = "git::https://github.com/refinedigital/module-eks-cluster.git?ref=abc123def456"

  # Configuration
}

# Use major version constraints
module "rds" {
  source = "git::https://github.com/refinedigital/module-rds.git?ref=v2"

  # Configuration
}
```

### Module Registry

For published modules:

```hcl
# Use Terraform Registry
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "~> 5.0"

  # Configuration
}

module "eks" {
  source  = "terraform-aws-modules/eks/aws"
  version = "~> 19.0"

  # Configuration
}
```

### Semantic Versioning

Follow semantic versioning for modules:

```
v{MAJOR}.{MINOR}.{PATCH}

v1.0.0       - Initial release
v1.1.0       - Backward compatible feature
v1.1.1       - Backward compatible bug fix
v2.0.0       - Breaking change
v2.0.0-beta  - Pre-release
v2.0.0-rc1   - Release candidate
```

**Git tags**:
```bash
# Create version tag
git tag v1.0.0
git push origin v1.0.0

# Create release on GitHub
# This makes it available for module references
```

### Module Documentation for Versioning

Document breaking changes in `CHANGELOG.md`:

```markdown
# Changelog

## v2.0.0 (2025-11-07)

### Breaking Changes
- `enable_nat_gateway` variable renamed to `nat_gateway_enabled`
- Output `nat_gateway_ips` changed from list to map
- Minimum Terraform version bumped to 1.2

### Migration Guide

Before (v1.x):
\`\`\`hcl
module "vpc" {
  source = "git::...?ref=v1.0.0"
  enable_nat_gateway = true
}

output "nat_ips" {
  value = module.vpc.nat_gateway_ips
}
\`\`\`

After (v2.x):
\`\`\`hcl
module "vpc" {
  source = "git::...?ref=v2.0.0"
  nat_gateway_enabled = true
}

output "nat_ips" {
  value = values(module.vpc.nat_gateway_ips)
}
\`\`\`

## v1.1.0 (2025-10-15)

### Features
- Added support for VPC Flow Logs
- New `enable_flow_logs` variable

### Bug Fixes
- Fixed route table association for private subnets

## v1.0.0 (2025-09-01)

Initial release
```

---

## Checklist for New Terraform Projects

Use this checklist when starting a new Terraform project:

- [ ] Repository created with kebab-case naming
- [ ] Local folder matches repository name
- [ ] `.terraform-version` file created
- [ ] `versions.tf` configured with provider constraints
- [ ] `terraform.tfvars.example` created
- [ ] `backend.tf` configured for remote state
- [ ] `.gitignore` includes `.terraform/`, `terraform.tfstate*`, `*.tfvars` (except .example)
- [ ] `main.tf` created with primary resources
- [ ] `variables.tf` created with input variables
- [ ] `outputs.tf` created with output definitions
- [ ] `README.md` created following template
- [ ] `.tflint.hcl` configured
- [ ] `.pre-commit-config.yaml` created
- [ ] `.vscode/settings.json` created
- [ ] `.github/workflows/` configured with validation
- [ ] All resources use snake_case naming
- [ ] All variables use snake_case naming
- [ ] Common tags applied to all resources
- [ ] Module structure planned and documented
- [ ] `terraform fmt -recursive` passes
- [ ] `terraform validate` passes
- [ ] `tflint --recursive` passes (or configured exceptions)
- [ ] `terraform-docs` README generated

---

## Quick Reference

### File Extensions
- Terraform files: `.tf`
- Values files: `.tfvars` (or `.tfvars.json`)
- Lock file: `.terraform.lock.hcl`

### Naming Formats
- **Resources**: `snake_case` - `aws_vpc.main`
- **Variables**: `snake_case` - `vpc_cidr_block`
- **Outputs**: `snake_case` - `vpc_id`
- **Modules (folder)**: `kebab-case` - `modules/vpc`
- **Modules (call)**: `snake_case` - `module "vpc"`
- **Files**: `snake_case.tf` or `descriptive.tf`
- **AWS Tags**: `kebab-case` - `infrastructure-vpc-prod`

### Key Commands
```bash
terraform init              # Initialize configuration
terraform validate          # Check syntax
terraform fmt -recursive    # Format code
terraform plan              # Preview changes
terraform apply             # Apply changes
terraform destroy           # Destroy resources
terraform state list        # List state
tflint                      # Lint code
tfsec                       # Security scan
terraform-docs              # Generate docs
```

---

**Document Version**: 1.0.0
**Last Updated**: 2025-11-07
**Maintained By**: refine.digital Platform Team
**Repository**: https://github.com/refinedigital/development-standards
