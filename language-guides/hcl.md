# HCL (HashiCorp Configuration Language) Standards

## Table of Contents

1. [Overview](#1-overview)
2. [File Naming Conventions](#2-file-naming-conventions)
3. [Folder Structure](#3-folder-structure)
4. [HCL Syntax and Formatting](#4-hcl-syntax-and-formatting)
5. [Naming Conventions](#5-naming-conventions)
6. [Documentation Requirements](#6-documentation-requirements)
7. [Tool-Specific Standards](#7-tool-specific-standards)
8. [Automation Tools](#8-automation-tools)
9. [VS Code Setup](#9-vs-code-setup)
10. [Examples](#10-examples)

---

## 1. Overview

HCL (HashiCorp Configuration Language) is used across multiple HashiCorp tools:

- **Terraform** (`.tf`) - Infrastructure as Code
- **Packer** (`.pkr.hcl`) - Machine image building
- **Vault** (`.hcl`) - Secret management policies
- **Nomad** (`.nomad`, `.hcl`) - Workload orchestration
- **tflint** (`.tflint.hcl`) - Terraform linting configuration
- **Consul** (`.hcl`) - Service mesh configuration

**Note**: For Terraform-specific standards (`.tf` files), see [terraform.md](terraform.md). This guide covers other HCL uses.

---

## 2. File Naming Conventions

### General HCL Files

**Format**: `kebab-case.hcl` or tool-specific extension

**Pattern**: `^[a-z0-9-]+\.(hcl|pkr\.hcl|nomad)$`

### Packer Templates

**Format**: `kebab-case.pkr.hcl`

**Examples**:
```
✅ ubuntu-20.04.pkr.hcl
✅ amazon-linux.pkr.hcl
✅ custom-image.pkr.hcl

❌ UbuntuImage.pkr.hcl      # CamelCase
❌ ubuntu_image.pkr.hcl     # Underscore
❌ ubuntu.hcl               # Missing .pkr
```

### Vault Policies

**Format**: `kebab-case-policy.hcl` or `kebab-case.hcl`

**Examples**:
```
✅ admin-policy.hcl
✅ read-only-policy.hcl
✅ terraform-policy.hcl

❌ Admin_Policy.hcl
❌ readOnlyPolicy.hcl
```

### Nomad Job Specs

**Format**: `kebab-case.nomad` or `kebab-case.hcl`

**Examples**:
```
✅ web-app.nomad
✅ batch-job.nomad
✅ api-service.hcl

❌ WebApp.nomad
❌ web_app.nomad
```

### Configuration Files

**Format**: `tool-name.hcl` or `.tool-name.hcl`

**Examples**:
```
✅ .tflint.hcl
✅ consul.hcl
✅ vault.hcl
✅ packer.hcl
```

---

## 3. Folder Structure

### Packer Project Structure

```
packer/
├── ubuntu/
│   ├── ubuntu-20.04.pkr.hcl
│   ├── ubuntu-22.04.pkr.hcl
│   └── variables.pkr.hcl
├── amazon-linux/
│   ├── amazon-linux-2.pkr.hcl
│   └── variables.pkr.hcl
├── scripts/
│   ├── setup.sh
│   └── cleanup.sh
└── README.md
```

### Vault Policies Structure

```
vault/
├── policies/
│   ├── admin-policy.hcl
│   ├── read-only-policy.hcl
│   ├── terraform-policy.hcl
│   └── app-policy.hcl
├── config/
│   └── vault.hcl
└── README.md
```

### Nomad Jobs Structure

```
nomad/
├── jobs/
│   ├── web-app.nomad
│   ├── api-service.nomad
│   └── batch-job.nomad
├── config/
│   └── nomad.hcl
└── README.md
```

---

## 4. HCL Syntax and Formatting

### Indentation

**Rule**: 2 spaces (consistent with Terraform)

```hcl
# Good
source "amazon-ebs" "ubuntu" {
  ami_name      = "custom-ubuntu-20.04"
  instance_type = "t2.micro"
  
  source_ami_filter {
    filters = {
      name = "ubuntu/images/*ubuntu-focal-20.04-amd64-server-*"
    }
  }
}

# Bad
source "amazon-ebs" "ubuntu" {
    ami_name      = "custom-ubuntu-20.04"  # 4 spaces
	instance_type = "t2.micro"              # Tab
}
```

### Block Alignment

Align `=` in assignment blocks:

```hcl
# Good
variable "instance_type" {
  type        = string
  description = "EC2 instance type"
  default     = "t2.micro"
}

# Less ideal (but valid)
variable "instance_type" {
  type = string
  description = "EC2 instance type"
  default = "t2.micro"
}
```

### Comments

```hcl
# Single-line comment

/*
 * Multi-line comment
 * For longer descriptions
 */

variable "example" {
  type        = string
  description = "Example variable"  # Inline comment
}
```

### String Interpolation

```hcl
# Good
ami_name = "${var.base_name}-${var.version}"

# Also acceptable for simple variables
ami_name = var.base_name
```

---

## 5. Naming Conventions

### General Rules

- **Blocks/resources**: `snake_case`
- **Variables**: `snake_case`
- **Files**: `kebab-case.hcl`

### Packer

```hcl
# Source names: snake_case
source "amazon-ebs" "ubuntu_base" {  }

# Build names: snake_case
build {
  name = "ubuntu_production"
  
  sources = ["source.amazon-ebs.ubuntu_base"]
}

# Variable names: snake_case
variable "ami_prefix" {
  type = string
}
```

### Vault Policies

```hcl
# Path names: use service/resource pattern
path "secret/data/terraform/*" {
  capabilities = ["read", "list"]
}

path "auth/token/create" {
  capabilities = ["create", "update"]
}
```

### Nomad Jobs

```hcl
# Job names: snake_case
job "web_application" {
  datacenters = ["dc1"]
  type        = "service"
  
  # Group names: snake_case
  group "web_servers" {
    count = 3
    
    # Task names: snake_case
    task "nginx_server" {
      driver = "docker"
    }
  }
}
```

---

## 6. Documentation Requirements

### File-Level Comments

```hcl
/*
 * Ubuntu 20.04 Base Image
 * 
 * Builds a custom Ubuntu 20.04 AMI with:
 * - Docker installed
 * - CloudWatch agent
 * - Security hardening
 * 
 * Owner: Platform Team
 * Last Updated: 2025-11-07
 */

source "amazon-ebs" "ubuntu" {
  # Configuration...
}
```

### Variable Documentation

```hcl
# Packer variables
variable "ami_prefix" {
  type        = string
  description = "Prefix for AMI name - will be suffixed with timestamp"
  default     = "custom-ubuntu"
}

variable "instance_type" {
  type        = string
  description = "EC2 instance type for building AMI (t2.micro recommended for cost)"
  default     = "t2.micro"
}

# Validation example
variable "environment" {
  type        = string
  description = "Environment name (dev, staging, prod)"
  
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}
```

---

## 7. Tool-Specific Standards

### 7.1 Packer

#### Complete Template Example

```hcl
# ubuntu-20.04.pkr.hcl

packer {
  required_plugins {
    amazon = {
      version = ">= 1.0.0"
      source  = "github.com/hashicorp/amazon"
    }
  }
}

# Variables
variable "ami_prefix" {
  type        = string
  description = "Prefix for AMI name"
  default     = "custom-ubuntu"
}

variable "region" {
  type        = string
  description = "AWS region"
  default     = "us-east-1"
}

variable "instance_type" {
  type        = string
  description = "Instance type for building"
  default     = "t2.micro"
}

# Locals for computed values
locals {
  timestamp = regex_replace(timestamp(), "[- TZ:]", "")
  ami_name  = "${var.ami_prefix}-${local.timestamp}"
}

# Source configuration
source "amazon-ebs" "ubuntu" {
  ami_name      = local.ami_name
  instance_type = var.instance_type
  region        = var.region
  
  source_ami_filter {
    filters = {
      name                = "ubuntu/images/*ubuntu-focal-20.04-amd64-server-*"
      root-device-type    = "ebs"
      virtualization-type = "hvm"
    }
    most_recent = true
    owners      = ["099720109477"]  # Canonical
  }
  
  ssh_username = "ubuntu"
  
  tags = {
    Name        = local.ami_name
    Environment = "base"
    OS          = "Ubuntu"
    Version     = "20.04"
    ManagedBy   = "packer"
  }
}

# Build configuration
build {
  name    = "ubuntu_base"
  sources = ["source.amazon-ebs.ubuntu"]
  
  # Update system
  provisioner "shell" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get upgrade -y"
    ]
  }
  
  # Install Docker
  provisioner "shell" {
    script = "scripts/install-docker.sh"
  }
  
  # Install CloudWatch agent
  provisioner "shell" {
    script = "scripts/install-cloudwatch.sh"
  }
  
  # Cleanup
  provisioner "shell" {
    inline = [
      "sudo apt-get clean",
      "sudo rm -rf /tmp/*",
      "history -c"
    ]
  }
  
  # Post-processor (optional)
  post-processor "manifest" {
    output = "manifest.json"
  }
}
```

#### Packer Best Practices

1. **Use variables** for all configurable values
2. **Tag AMIs** comprehensively
3. **Use scripts** for complex provisioning
4. **Clean up** temporary files
5. **Version lock** plugins

### 7.2 Vault Policies

#### Admin Policy Example

```hcl
# admin-policy.hcl

# Full access to secrets
path "secret/*" {
  capabilities = ["create", "read", "update", "delete", "list"]
}

# Manage auth methods
path "auth/*" {
  capabilities = ["create", "read", "update", "delete", "list", "sudo"]
}

# Manage policies
path "sys/policies/*" {
  capabilities = ["create", "read", "update", "delete", "list", "sudo"]
}

# System health
path "sys/health" {
  capabilities = ["read", "sudo"]
}

# Audit backends
path "sys/audit" {
  capabilities = ["read", "sudo"]
}

path "sys/audit/*" {
  capabilities = ["create", "update", "delete", "sudo"]
}
```

#### Read-Only Policy Example

```hcl
# read-only-policy.hcl

# Read-only access to secrets
path "secret/data/*" {
  capabilities = ["read", "list"]
}

# List secret paths
path "secret/metadata/*" {
  capabilities = ["list"]
}

# Deny all other access
path "*" {
  capabilities = ["deny"]
}
```

#### Terraform Policy Example

```hcl
# terraform-policy.hcl

# Terraform needs to manage secrets
path "secret/data/terraform/*" {
  capabilities = ["create", "read", "update", "delete", "list"]
}

# Terraform state in Vault (optional)
path "secret/data/tfstate/*" {
  capabilities = ["create", "read", "update"]
}

# Token management
path "auth/token/create" {
  capabilities = ["create", "update"]
}

path "auth/token/renew-self" {
  capabilities = ["update"]
}
```

#### Vault Best Practices

1. **Principle of least privilege**: Grant minimum necessary permissions
2. **Use wildcards carefully**: `*` can be dangerous
3. **Document policies**: Explain why permissions are granted
4. **Separate policies**: One policy per role/team
5. **Use deny explicitly**: Be explicit about denials

### 7.3 Nomad Jobs

#### Service Job Example

```hcl
# web-app.nomad

job "web_application" {
  datacenters = ["dc1"]
  type        = "service"
  
  # Update strategy
  update {
    max_parallel     = 1
    min_healthy_time = "10s"
    healthy_deadline = "3m"
    auto_revert      = true
  }
  
  group "web_servers" {
    count = 3
    
    # Network configuration
    network {
      port "http" {
        static = 8080
      }
    }
    
    # Service registration
    service {
      name = "web-app"
      port = "http"
      
      tags = [
        "web",
        "frontend",
        "production"
      ]
      
      check {
        type     = "http"
        path     = "/health"
        interval = "10s"
        timeout  = "2s"
      }
    }
    
    # Main task
    task "nginx_server" {
      driver = "docker"
      
      config {
        image = "nginx:latest"
        ports = ["http"]
      }
      
      # Resource allocation
      resources {
        cpu    = 500  # MHz
        memory = 256  # MB
      }
      
      # Environment variables
      env {
        ENVIRONMENT = "production"
      }
    }
  }
}
```

#### Batch Job Example

```hcl
# batch-job.nomad

job "data_processing" {
  datacenters = ["dc1"]
  type        = "batch"
  
  periodic {
    cron             = "0 2 * * *"  # Daily at 2 AM
    prohibit_overlap = true
  }
  
  group "processors" {
    task "process_data" {
      driver = "docker"
      
      config {
        image   = "my-processor:latest"
        command = "python"
        args    = ["process.py"]
      }
      
      resources {
        cpu    = 1000
        memory = 1024
      }
    }
  }
}
```

---

## 8. Automation Tools

### packer fmt

```bash
# Format Packer files
packer fmt .

# Check formatting
packer fmt -check .

# Format specific file
packer fmt ubuntu-20.04.pkr.hcl
```

### packer validate

```bash
# Validate template
packer validate ubuntu-20.04.pkr.hcl

# Validate with variables
packer validate -var-file=vars.pkrvars.hcl ubuntu-20.04.pkr.hcl
```

### vault fmt

```bash
# Format Vault policy files
vault policy fmt admin-policy.hcl

# Check all HCL files
for f in *.hcl; do vault policy fmt "$f"; done
```

### nomad fmt

```bash
# Format Nomad job spec
nomad job fmt web-app.nomad

# Check formatting
nomad job fmt -check web-app.nomad
```

### HCL Linting

Use **hclfmt** for general HCL formatting:

```bash
# Install
go install github.com/hashicorp/hcl/v2/cmd/hclfmt@latest

# Format file
hclfmt -w file.hcl
```

---

## 9. VS Code Setup

### Recommended Extensions

1. **HashiCorp HCL** (HashiCorp)
   - ID: `hashicorp.hcl`
   - Features: Syntax highlighting for HCL

2. **HashiCorp Terraform** (HashiCorp)
   - ID: `hashicorp.terraform`
   - Features: Formatting, validation

### Workspace Settings

```json
{
  "[hcl]": {
    "editor.defaultFormatter": "hashicorp.hcl",
    "editor.formatOnSave": true,
    "editor.tabSize": 2,
    "editor.insertSpaces": true
  },
  "files.associations": {
    "*.hcl": "hcl",
    "*.pkr.hcl": "hcl",
    "*.nomad": "hcl",
    ".tflint.hcl": "hcl"
  }
}
```

---

## 10. Examples

### Example 1: Complete Packer Template

See [7.1 Packer](#71-packer) section above.

### Example 2: tflint Configuration

```hcl
# .tflint.hcl

plugin "terraform" {
  enabled = true
  preset  = "recommended"
}

plugin "aws" {
  enabled = true
  version = "0.25.0"
  source  = "github.com/terraform-linters/tflint-ruleset-aws"
}

rule "terraform_naming_convention" {
  enabled = true
  
  format = "snake_case"
  
  resource {
    format = "snake_case"
  }
  
  variable {
    format = "snake_case"
  }
  
  output {
    format = "snake_case"
  }
}

rule "terraform_required_version" {
  enabled = true
}

rule "terraform_unused_declarations" {
  enabled = true
}

rule "terraform_deprecated_index" {
  enabled = true
}
```

### Example 3: Consul Configuration

```hcl
# consul.hcl

datacenter = "dc1"

data_dir = "/opt/consul/data"

log_level = "INFO"

server = true

bootstrap_expect = 3

ui_config {
  enabled = true
}

bind_addr = "0.0.0.0"

client_addr = "0.0.0.0"

advertise_addr = "10.0.1.10"

retry_join = [
  "10.0.1.11",
  "10.0.1.12"
]

performance {
  raft_multiplier = 1
}

telemetry {
  prometheus_retention_time = "24h"
  disable_hostname          = false
}
```

---

## Summary

### Key Takeaways

1. **File naming**: Use `kebab-case.hcl` or tool-specific extensions (`.pkr.hcl`, `.nomad`)
2. **Indentation**: Always 2 spaces
3. **Naming**: Use `snake_case` for blocks, variables, resources
4. **Documentation**: Add file-level and block-level comments
5. **Formatting**: Use tool-specific formatters (`packer fmt`, `vault policy fmt`, `nomad job fmt`)
6. **Validation**: Always validate before deploying

### Quick Reference

| Tool | File Extension | Formatter | Validator |
|------|---------------|-----------|-----------|
| Packer | `.pkr.hcl` | `packer fmt` | `packer validate` |
| Vault | `.hcl` | `vault policy fmt` | `vault policy write` |
| Nomad | `.nomad`, `.hcl` | `nomad job fmt` | `nomad job validate` |
| tflint | `.tflint.hcl` | `hclfmt` | N/A (config file) |
| Consul | `.hcl` | `hclfmt` | `consul validate` |

### Validation Checklist

- [ ] File name follows convention (`kebab-case`)
- [ ] 2-space indentation (no tabs)
- [ ] Variables use `snake_case`
- [ ] Blocks use `snake_case`
- [ ] File-level documentation comment
- [ ] Passes format check (`packer fmt -check`, etc.)
- [ ] Passes validation (`packer validate`, etc.)
- [ ] No hardcoded secrets
- [ ] Tags/labels applied appropriately

---

**Document Version**: 1.0.0  
**Last Updated**: 2025-11-07  
**Maintained By**: refine.digital Platform Team  
**Related**: 
- [terraform.md](terraform.md) - For Terraform-specific HCL standards
- [PROJECT-NAMING-STANDARDS.md](../governance/PROJECT-NAMING-STANDARDS.md)
