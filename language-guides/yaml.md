# YAML Standards for IaC Projects

## Table of Contents

1. [File Naming Conventions](#1-file-naming-conventions)
2. [Folder Structure](#2-folder-structure)
3. [YAML Syntax and Formatting](#3-yaml-syntax-and-formatting)
4. [Naming Conventions](#4-naming-conventions)
5. [Documentation Requirements](#5-documentation-requirements)
6. [Best Practices](#6-best-practices)
7. [Automation Tools](#7-automation-tools)
8. [VS Code Setup](#8-vs-code-setup)
9. [Pre-commit Hooks](#9-pre-commit-hooks)
10. [Examples](#10-examples)
11. [Common Patterns](#11-common-patterns)
12. [Validation Rules](#12-validation-rules)

---

## 1. File Naming Conventions

### YAML Filenames

**Format**: `kebab-case.yml`

**Pattern**: `^[a-z0-9-]+\.(yml|yaml)$`

**Preference**: Use `.yml` extension for consistency

**Rules**:
- Lowercase letters only
- Use hyphens to separate words
- Prefer `.yml` over `.yaml`
- Descriptive names that indicate purpose

### Examples

**Good**:
```
docker-compose.yml
terraform-validate.yml
deployment.yml
config.yml
dev.yml
staging.yml
prod.yml
github-actions-ci.yml
kubernetes-deployment.yml
```

**Bad**:
```
dockerCompose.yml          # CamelCase
Docker-Compose.yml         # Mixed case
docker_compose.yml         # Underscore
DEPLOYMENT.YML             # All caps
config.yaml                # Use .yml instead
terraform validate.yml     # Space in filename
```

### Standard Filenames

**GitHub Actions** (`.github/workflows/`):
```
ci.yml
cd.yml
terraform-validate.yml
security-scan.yml
deploy-infrastructure.yml
pr-checks.yml
lint.yml
test.yml
```

**Docker**:
```
docker-compose.yml
docker-compose.dev.yml
docker-compose.prod.yml
docker-compose.override.yml
```

**Kubernetes**:
```
deployment.yml
service.yml
ingress.yml
configmap.yml
secret.yml
namespace.yml
persistent-volume.yml
```

**CI/CD**:
```
.gitlab-ci.yml
.circleci/config.yml
.travis.yml
azure-pipelines.yml
```

**Configuration**:
```
config.yml
settings.yml
values.yml (Helm)
```

---

## 2. Folder Structure

### Standard YAML Organization

```
project-root/
├── .github/
│   └── workflows/              # GitHub Actions workflows
│       ├── ci.yml
│       ├── terraform-validate.yml
│       └── deploy.yml
├── kubernetes/                 # Kubernetes manifests
│   ├── base/
│   │   ├── deployment.yml
│   │   ├── service.yml
│   │   └── kustomization.yml
│   └── overlays/
│       ├── dev/
│       ├── staging/
│       └── prod/
├── configs/                    # Configuration files
│   ├── dev.yml
│   ├── staging.yml
│   └── prod.yml
├── helm/                       # Helm charts
│   └── my-chart/
│       ├── Chart.yml
│       ├── values.yml
│       └── templates/
├── docker-compose.yml          # Docker Compose (root)
└── .yamllint.yml              # YAML linting config
```

### Multi-Environment Configuration

```
configs/
├── common.yml                  # Shared configuration
├── dev.yml                     # Development overrides
├── staging.yml                 # Staging overrides
└── prod.yml                    # Production values
```

---

## 3. YAML Syntax and Formatting

### Indentation

**Rule**: Use **2 spaces** for indentation

**Never use tabs**

```yaml
# Good
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    environment:
      ENV: production

# Bad
services:
    web:                        # 4 spaces
        image: nginx:latest
\tweb:                          # Tab character
```

### Line Length

**Recommended**: 80-120 characters per line

**For long values**: Use YAML multiline strings

```yaml
# Good
description: >
  This is a long description that spans
  multiple lines for better readability.

# Also good
command: |
  #!/bin/bash
  set -euo pipefail
  terraform init
  terraform plan
```

### Quotes

**Rule**: Quote strings only when necessary

**When to quote**:
- Strings with special characters
- Strings that look like numbers/booleans
- Strings with leading/trailing whitespace

```yaml
# Good
name: my-service                # No quotes needed
version: "1.0"                  # Quotes prevent interpretation as number
description: "value: with colon"  # Quotes for special chars
enable: true                    # Boolean, no quotes

# Bad
name: "my-service"              # Unnecessary quotes
version: 1.0                    # Will be interpreted as number
enable: "true"                  # String "true" not boolean
```

### Lists

**Prefer**: Dash notation for lists

```yaml
# Good
ports:
  - 80
  - 443
  - 8080

# Also acceptable (inline)
ports: [80, 443, 8080]

# Bad (mixed styles)
ports:
  - 80
  - 443
  [8080, 9090]
```

### Maps/Dictionaries

```yaml
# Good
environment:
  NODE_ENV: production
  PORT: 3000
  DEBUG: false

# Also acceptable (inline for simple maps)
labels: {app: web, tier: frontend}
```

### Comments

**Rule**: Use `#` for comments, space after `#`

```yaml
# Good
# This is a comment
services:
  web:  # Inline comment
    image: nginx

# Bad
#No space after hash
services:
  web:
    image: nginx    #No space before comment
```

---

## 4. Naming Conventions

### YAML Keys

**Format**: `snake_case` (for custom keys)

**Pattern**: `^[a-z0-9_]+$`

**Exceptions**:
- GitHub Actions (kebab-case - follow standard)
- Kubernetes (camelCase - follow standard)
- Docker Compose (snake_case or kebab-case - follow docs)

### Custom Configuration Files

```yaml
# Good (snake_case for custom configs)
environment_name: prod
vpc_cidr_block: "10.0.0.0/16"
enable_nat_gateway: true
subnet_availability_zones:
  - us-east-1a
  - us-east-1b
max_instance_count: 10
database_connection_string: "postgres://..."

# Bad
environmentName: prod           # camelCase
environment-name: prod          # kebab-case (unless tool convention)
EnvironmentName: prod           # PascalCase
ENVIRONMENT_NAME: prod          # UPPER_SNAKE
```

### GitHub Actions (Follow Convention)

```yaml
# GitHub Actions uses kebab-case - follow it
name: Terraform Validate
on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  validate-terraform:           # kebab-case for job names
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code     # Natural language for step names
        uses: actions/checkout@v4
```

### Kubernetes (Follow Convention)

```yaml
# Kubernetes uses camelCase - follow it
apiVersion: v1
kind: Service
metadata:
  name: my-service              # kebab-case for resource names
  labels:
    app: web
spec:
  type: ClusterIP               # Standard K8s syntax
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 8080
```

### Docker Compose (Follow Convention)

```yaml
# Docker Compose - follow standard
version: '3.8'
services:
  web:                          # service names: lowercase
    image: nginx:latest
    container_name: my-web      # kebab-case or snake_case
    environment:
      NODE_ENV: production      # UPPER_SNAKE for env vars
    networks:
      - backend                 # lowercase network names
networks:
  backend:
    driver: bridge
```

---

## 5. Documentation Requirements

### File-Level Comments

**Always include**:
- Purpose of the file
- Owner/maintainer
- Last updated date
- Related documentation

```yaml
# Terraform Validation Workflow
# Purpose: Validates Terraform configurations on pull requests
# Owner: Platform Team
# Last Updated: 2025-11-07
# Related: docs/ci-cd.md

name: Terraform Validate
on:
  pull_request:
    branches: [main]
```

### Inline Documentation

```yaml
# Application configuration for production environment
environment: prod

# VPC CIDR block - must not overlap with other VPCs
vpc_cidr_block: "10.0.0.0/16"

# Enable NAT gateway for private subnet internet access
# Note: Adds $45/month cost per AZ
enable_nat_gateway: true

# Availability zones for high availability
# Minimum 2 AZs recommended for production
availability_zones:
  - us-east-1a
  - us-east-1b
```

### Complex Workflows

```yaml
# Multi-environment deployment workflow
# Deploys to dev, then staging, then prod with approvals
name: Deploy Infrastructure

on:
  workflow_dispatch:            # Manual trigger only
    inputs:
      environment:
        description: 'Target environment'
        required: true
        type: choice
        options:
          - dev
          - staging
          - prod

jobs:
  deploy:
    # Deploy job runs Terraform apply
    # Requires manual approval for staging/prod (configured in repo settings)
    runs-on: ubuntu-latest
    steps:
      - name: Deploy
        run: terraform apply -auto-approve
```

---

## 6. Best Practices

### Use Anchors and Aliases

**When**: Repeating configuration blocks

```yaml
# Define anchor
defaults: &defaults
  cpu: "1"
  memory: "1Gi"
  restart_policy: always

# Use alias
service_a:
  <<: *defaults
  image: nginx:latest

service_b:
  <<: *defaults
  image: redis:latest
  memory: "2Gi"             # Override specific value
```

### Multiline Strings

**Block Scalar (`|`)**: Preserves newlines

```yaml
script: |
  #!/bin/bash
  set -euo pipefail
  echo "Starting deployment"
  terraform apply -auto-approve
```

**Folded Scalar (`>`)**: Folds newlines to spaces

```yaml
description: >
  This is a long description that will
  be folded into a single line with
  spaces between the words.
```

**Chomp Modifiers**:
- `|+` - Keep trailing newlines
- `|-` - Strip trailing newlines
- `>+` - Keep trailing newlines (folded)
- `>-` - Strip trailing newlines (folded)

```yaml
# Strip final newline
script: |-
  terraform init
  terraform apply
```

### Environment-Specific Configuration

**Pattern 1: Separate Files**
```yaml
# config/base.yml
app_name: my-app
region: us-east-1

# config/dev.yml
environment: dev
instance_count: 1
debug: true

# config/prod.yml
environment: prod
instance_count: 3
debug: false
```

**Pattern 2: Single File with Environments**
```yaml
# config.yml
common:
  app_name: my-app
  region: us-east-1

environments:
  dev:
    instance_count: 1
    debug: true
  prod:
    instance_count: 3
    debug: false
```

### Validation in YAML

```yaml
# Use explicit types when important
version: !!str 1.0          # Force string, not float

# Document constraints in comments
max_retries: 3              # Valid range: 1-10
timeout: 300                # Timeout in seconds (max: 3600)
```

### Boolean Values

**Use**: `true`/`false` (lowercase)

**Avoid**: YAML accepts many boolean representations, stick to one

```yaml
# Good
enabled: true
debug: false

# Avoid (though valid YAML)
enabled: yes
debug: no
enabled: on
debug: off
enabled: True
debug: FALSE
```

---

## 7. Automation Tools

### yamllint

**Purpose**: Lint YAML files for syntax and style issues

#### Installation

```bash
# macOS
brew install yamllint

# Python pip
pip install yamllint

# npm
npm install -g yaml-lint
```

#### Configuration

**File**: `.yamllint.yml` (root of project)

```yaml
---
# yamllint configuration for refine.digital standards

extends: default

rules:
  # Line length
  line-length:
    max: 120
    level: warning

  # Indentation
  indentation:
    spaces: 2
    indent-sequences: true

  # Comments
  comments:
    min-spaces-from-content: 2

  # Trailing spaces
  trailing-spaces: enable

  # Empty lines
  empty-lines:
    max: 2

  # Document start (---)
  document-start:
    present: false          # Don't require --- at start

  # Truthy values
  truthy:
    allowed-values: ['true', 'false']
    check-keys: true

  # Quoted strings
  quoted-strings:
    quote-type: any
    required: only-when-needed

# Ignore patterns
ignore: |
  .terraform/
  node_modules/
  vendor/
  *.lock
```

#### Usage

```bash
# Lint all YAML files
yamllint .

# Lint specific file
yamllint docker-compose.yml

# Lint with custom config
yamllint -c .yamllint.yml .

# Fix auto-fixable issues (with some tools)
yamllint --fix .
```

### Prettier (for formatting)

**Purpose**: Auto-format YAML files

#### Installation

```bash
npm install --save-dev prettier
```

#### Configuration

**File**: `.prettierrc.yml`

```yaml
printWidth: 100
tabWidth: 2
useTabs: false
singleQuote: false
proseWrap: preserve
```

#### Usage

```bash
# Format YAML files
prettier --write "**/*.{yml,yaml}"

# Check formatting
prettier --check "**/*.{yml,yaml}"
```

---

## 8. VS Code Setup

### Recommended Extensions

1. **YAML** (Red Hat)
   - ID: `redhat.vscode-yaml`
   - Features: Validation, autocomplete, formatting

2. **Prettier** (Official)
   - ID: `esbenp.prettier-vscode`
   - Features: Auto-formatting

3. **YAML Sort** (Pascal Reitermann)
   - ID: `PascalReitermann93.vscode-yaml-sort`
   - Features: Sort YAML keys

### Workspace Settings

**File**: `.vscode/settings.json`

```json
{
  "editor.formatOnSave": true,
  "[yaml]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.insertSpaces": true,
    "editor.tabSize": 2,
    "editor.autoIndent": "advanced"
  },
  "[yml]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.insertSpaces": true,
    "editor.tabSize": 2
  },
  "yaml.validate": true,
  "yaml.format.enable": true,
  "yaml.format.singleQuote": false,
  "yaml.format.bracketSpacing": true,
  "yaml.schemas": {
    "https://json.schemastore.org/github-workflow.json": ".github/workflows/*.yml",
    "https://json.schemastore.org/docker-compose.json": "docker-compose*.yml",
    "https://raw.githubusercontent.com/compose-spec/compose-spec/master/schema/compose-spec.json": "docker-compose*.yml"
  },
  "files.associations": {
    "*.yml": "yaml",
    "*.yaml": "yaml"
  }
}
```

### Extensions Configuration

**File**: `.vscode/extensions.json`

```json
{
  "recommendations": [
    "redhat.vscode-yaml",
    "esbenp.prettier-vscode",
    "PascalReitermann93.vscode-yaml-sort"
  ]
}
```

---

## 9. Pre-commit Hooks

### Configuration

**File**: `.pre-commit-config.yaml`

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: check-yaml
        args: ['--unsafe']      # Allow custom tags
      - id: end-of-file-fixer
        files: \.(yml|yaml)$
      - id: trailing-whitespace
        files: \.(yml|yaml)$

  - repo: https://github.com/adrienverge/yamllint
    rev: v1.33.0
    hooks:
      - id: yamllint
        args: ['-c', '.yamllint.yml']
        files: \.(yml|yaml)$

  - repo: https://github.com/pre-commit/mirrors-prettier
    rev: v3.1.0
    hooks:
      - id: prettier
        files: \.(yml|yaml)$
        args: ['--write']
```

### Installation

```bash
# Install pre-commit
pip install pre-commit

# Or via Homebrew
brew install pre-commit

# Install hooks in repository
pre-commit install

# Run manually
pre-commit run --all-files
```

---

## 10. Examples

### Example 1: GitHub Actions Workflow

**File**: `.github/workflows/terraform-validate.yml`

```yaml
# Terraform Validation Workflow
# Validates Terraform configurations on pull requests
# Owner: Platform Team

name: Terraform Validate

on:
  pull_request:
    branches:
      - main
      - develop
    paths:
      - '**.tf'
      - '**.tfvars'
      - '.github/workflows/terraform-validate.yml'

env:
  TF_VERSION: '1.6.0'

jobs:
  validate:
    name: Validate Terraform
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3
        with:
          terraform_version: ${{ env.TF_VERSION }}

      - name: Terraform Format Check
        run: terraform fmt -check -recursive

      - name: Terraform Init
        run: terraform init -backend=false

      - name: Terraform Validate
        run: terraform validate

      - name: Run tflint
        uses: terraform-linters/setup-tflint@v4
        with:
          tflint_version: latest

      - name: Execute tflint
        run: tflint --recursive

      - name: Comment on PR
        if: failure()
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '❌ Terraform validation failed. Please check the logs and fix issues.'
            })
```

### Example 2: Docker Compose

**File**: `docker-compose.yml`

```yaml
version: '3.8'

# Shared configuration
x-common-variables: &common-env
  TZ: America/New_York
  LOG_LEVEL: info

services:
  web:
    image: nginx:latest
    container_name: infrastructure-web
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    environment:
      <<: *common-env
      NGINX_HOST: example.com
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./ssl:/etc/nginx/ssl:ro
      - web_logs:/var/log/nginx
    networks:
      - frontend
      - backend
    depends_on:
      - api
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  api:
    build:
      context: ./api
      dockerfile: Dockerfile
      args:
        NODE_ENV: production
    container_name: infrastructure-api
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      <<: *common-env
      NODE_ENV: production
      PORT: 3000
      DATABASE_URL: postgres://user:pass@db:5432/mydb
    volumes:
      - ./api:/app
      - /app/node_modules
    networks:
      - backend
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:15-alpine
    container_name: infrastructure-db
    restart: unless-stopped
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password
      POSTGRES_DB: mydb
    volumes:
      - db_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql:ro
    networks:
      - backend
    secrets:
      - db_password
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 10s
      timeout: 5s
      retries: 5

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge

volumes:
  web_logs:
    driver: local
  db_data:
    driver: local

secrets:
  db_password:
    file: ./secrets/db_password.txt
```

### Example 3: Kubernetes Deployment

**File**: `kubernetes/deployment.yml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: infrastructure-api
  namespace: production
  labels:
    app: api
    tier: backend
    version: v1.2.0
  annotations:
    kubernetes.io/change-cause: "Update to version 1.2.0"
spec:
  replicas: 3
  revisionHistoryLimit: 5
  selector:
    matchLabels:
      app: api
      tier: backend
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  template:
    metadata:
      labels:
        app: api
        tier: backend
        version: v1.2.0
    spec:
      serviceAccountName: api-sa
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        fsGroup: 1000
      containers:
        - name: api
          image: myregistry.io/api:1.2.0
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 3000
              protocol: TCP
          env:
            - name: NODE_ENV
              value: "production"
            - name: PORT
              value: "3000"
            - name: DATABASE_URL
              valueFrom:
                secretKeyRef:
                  name: api-secrets
                  key: database-url
          resources:
            requests:
              cpu: "250m"
              memory: "512Mi"
            limits:
              cpu: "1000m"
              memory: "1Gi"
          livenessProbe:
            httpGet:
              path: /health
              port: http
            initialDelaySeconds: 30
            periodSeconds: 10
            timeoutSeconds: 5
            failureThreshold: 3
          readinessProbe:
            httpGet:
              path: /ready
              port: http
            initialDelaySeconds: 15
            periodSeconds: 5
            timeoutSeconds: 3
            failureThreshold: 3
          volumeMounts:
            - name: config
              mountPath: /app/config
              readOnly: true
            - name: logs
              mountPath: /app/logs
      volumes:
        - name: config
          configMap:
            name: api-config
        - name: logs
          emptyDir: {}
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - weight: 100
              podAffinityTerm:
                labelSelector:
                  matchExpressions:
                    - key: app
                      operator: In
                      values:
                        - api
                topologyKey: kubernetes.io/hostname
```

### Example 4: Configuration File

**File**: `configs/prod.yml`

```yaml
# Production Environment Configuration
# Owner: Platform Team
# Last Updated: 2025-11-07

environment: prod

# AWS Configuration
aws:
  region: us-east-1
  account_id: "123456789012"

# VPC Configuration
vpc:
  cidr_block: "10.0.0.0/16"
  enable_dns_hostnames: true
  enable_dns_support: true
  enable_nat_gateway: true
  single_nat_gateway: false

# Subnet Configuration
subnets:
  public:
    - cidr: "10.0.1.0/24"
      az: us-east-1a
    - cidr: "10.0.2.0/24"
      az: us-east-1b
  private:
    - cidr: "10.0.10.0/24"
      az: us-east-1a
    - cidr: "10.0.11.0/24"
      az: us-east-1b

# EKS Configuration
eks:
  cluster_name: infrastructure-eks-prod
  kubernetes_version: "1.28"
  node_groups:
    - name: general
      instance_types:
        - t3.large
      desired_size: 3
      min_size: 2
      max_size: 5
      disk_size: 100

# Monitoring
monitoring:
  enable_cloudwatch: true
  log_retention_days: 30
  enable_prometheus: true
  enable_grafana: true

# Backup Configuration
backup:
  enabled: true
  retention_days: 30
  schedule: "0 2 * * *"  # Daily at 2 AM UTC

# Tags (applied to all resources)
tags:
  Environment: prod
  ManagedBy: terraform
  Project: infrastructure
  CostCenter: engineering
```

---

## 11. Common Patterns

### Multi-Stage CI/CD

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  # Stage 1: Validate
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Validate
        run: make validate

  # Stage 2: Test
  test:
    needs: validate
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: make test

  # Stage 3: Build
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build
        run: make build

  # Stage 4: Deploy
  deploy:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v4
      - name: Deploy
        run: make deploy
```

### Matrix Strategy

```yaml
name: Multi-Environment Test

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        environment: [dev, staging, prod]
        terraform_version: ['1.5.0', '1.6.0']
    steps:
      - uses: actions/checkout@v4
      - name: Test ${{ matrix.environment }}
        run: |
          echo "Testing environment: ${{ matrix.environment }}"
          echo "Terraform version: ${{ matrix.terraform_version }}"
```

### Reusable Workflows

**File**: `.github/workflows/terraform-deploy.yml`

```yaml
name: Reusable Terraform Deploy

on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
      terraform_version:
        required: false
        type: string
        default: '1.6.0'
    secrets:
      aws_access_key:
        required: true
      aws_secret_key:
        required: true

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to ${{ inputs.environment }}
        run: |
          echo "Deploying to ${{ inputs.environment }}"
          # Deployment logic here
```

**Usage**:

```yaml
name: Deploy Infrastructure

on:
  workflow_dispatch:

jobs:
  deploy-dev:
    uses: ./.github/workflows/terraform-deploy.yml
    with:
      environment: dev
    secrets:
      aws_access_key: ${{ secrets.AWS_ACCESS_KEY }}
      aws_secret_key: ${{ secrets.AWS_SECRET_KEY }}
```

---

## 12. Validation Rules

### Automated Validation

```bash
# Check YAML syntax
yamllint .

# Validate specific patterns
yamllint --strict .

# Check formatting
prettier --check "**/*.{yml,yaml}"
```

### CI/CD Integration

```yaml
# .github/workflows/yaml-validation.yml
name: YAML Validation

on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install yamllint
        run: pip install yamllint

      - name: Lint YAML files
        run: yamllint .

      - name: Check YAML formatting
        uses: actionsx/prettier@v2
        with:
          args: --check "**/*.{yml,yaml}"
```

### Schema Validation

**For custom configs**, define JSON Schema:

**File**: `schemas/config-schema.json`

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["environment", "aws", "vpc"],
  "properties": {
    "environment": {
      "type": "string",
      "enum": ["dev", "staging", "prod"]
    },
    "aws": {
      "type": "object",
      "required": ["region"],
      "properties": {
        "region": {
          "type": "string"
        }
      }
    }
  }
}
```

Validate in VS Code:

```json
// .vscode/settings.json
{
  "yaml.schemas": {
    "./schemas/config-schema.json": "configs/*.yml"
  }
}
```

---

## Summary

### Key Takeaways

1. **File naming**: Use `kebab-case.yml` (prefer `.yml`)
2. **Indentation**: Always 2 spaces, never tabs
3. **Keys**: Use `snake_case` for custom configs; follow tool conventions (GitHub Actions, K8s)
4. **Quotes**: Only when necessary
5. **Comments**: Document complex logic and ownership
6. **Automation**: Use `yamllint` and `prettier`
7. **Validation**: Integrate into pre-commit hooks and CI/CD

### Quick Reference

```yaml
# File naming
good: deployment.yml, terraform-validate.yml
bad: Deployment.yml, terraform_validate.yaml

# Indentation
good: 2 spaces
bad: tabs, 4 spaces

# Keys (custom configs)
good: environment_name, vpc_cidr_block
bad: environmentName, environment-name

# Booleans
good: true, false
bad: yes, no, on, off

# Comments
good: # This is a comment
bad: #No space
```

### Validation Checklist

- [ ] File uses `.yml` extension
- [ ] File name is `kebab-case`
- [ ] 2-space indentation (no tabs)
- [ ] Keys follow `snake_case` (or tool convention)
- [ ] File-level documentation comment
- [ ] Complex logic has inline comments
- [ ] Passes `yamllint` validation
- [ ] Formatted with `prettier`
- [ ] No trailing whitespace
- [ ] Final newline present

---

**Document Version**: 1.0.0
**Last Updated**: 2025-11-07
**Maintained By**: refine.digital Platform Team
**Related**: [PROJECT-NAMING-STANDARDS.md](../governance/PROJECT-NAMING-STANDARDS.md)
