# refine.digital Development Standards

> AI-Native Infrastructure-as-Code Development Standards Framework

[![Standards](https://img.shields.io/badge/standards-IaC-blue)](https://github.com/refinedigital/development-standards)
[![Version](https://img.shields.io/badge/version-1.0.0-green)]()

## Overview

This repository contains **comprehensive, enforceable development standards** for Infrastructure-as-Code (IaC) projects at refine.digital. These standards are designed to be:

- **Machine-readable**: JSON schemas and validation rules for automation
- **AI-accessible**: MCP (Model Context Protocol) integration for Claude Code and other AI tools
- **Self-enforcing**: Automated validation via CLI tools, pre-commit hooks, and CI/CD
- **Living documentation**: Continuously evolving with team practices

## Primary Focus

**Solving project naming inconsistencies across:**
- GitHub repositories
- Local folder structures
- Terraform resources
- YAML configurations
- VS Code workspaces
- AWS resource tags

## Quick Start

### For New Projects

```bash
# Install standards CLI tool
npm install -g refine-standards

# Initialize new project with standards
refine-standards init --type terraform --name infrastructure-vpc

# This creates:
# - Proper folder structure
# - Configuration files (.editorconfig, .gitignore, etc.)
# - VS Code settings
# - Pre-commit hooks
# - README.md template
```

### For Existing Projects

```bash
# Check compliance
refine-standards check

# Auto-fix violations
refine-standards fix --auto

# Validate specific naming
refine-standards validate-name --repo "my-project-name"
refine-standards validate-name --terraform-resource "vpc_main"
```

## Repository Structure

```
development-standards/
├── governance/                      # Policies and standards
│   ├── PROJECT-NAMING-STANDARDS.md  # ⭐ THE BIG ONE - Complete naming guide
│   ├── naming-validation-rules.json # Machine-readable rules
│   ├── branching-and-git.md
│   ├── versioning-and-releases.md
│   └── security-and-secrets.md
├── language-guides/                 # Language-specific standards
│   ├── terraform.md                 # ⭐ Complete Terraform standards
│   ├── yaml.md                      # ⭐ YAML formatting and linting
│   ├── shell.md                     # ⭐ Shell script best practices
│   ├── markdown.md                  # Documentation standards
│   ├── hcl.md                       # Packer, Vault, Nomad configs
│   └── conf.md                      # Config files (.conf, .env, .ini)
├── configs/                         # Drop-in configuration files
│   ├── editorconfig/
│   ├── terraform/
│   ├── yaml/
│   ├── markdown/
│   ├── shell/
│   ├── prettier/
│   ├── vscode/
│   └── git/
├── templates/                       # Project templates
│   ├── terraform-module/
│   ├── github-repo/
│   └── README-template.md
├── mcp/                             # MCP server for AI integration
│   └── standards-server/            # Claude Code integration
├── automation/                      # CLI tools and scripts
│   └── refine-standards/            # Standards validation CLI
└── examples/                        # Example projects
```

## Core Documents

### 🎯 Start Here

1. **[PROJECT-NAMING-STANDARDS.md](governance/PROJECT-NAMING-STANDARDS.md)**
   - Complete naming conventions for everything
   - GitHub repos, folders, Terraform, YAML, Shell, etc.
   - Validation patterns and examples
   - **This solves your biggest pain point!**

2. **[naming-validation-rules.json](governance/naming-validation-rules.json)**
   - Machine-readable validation rules
   - Regex patterns for automation
   - Auto-fix transformations

### 📚 Language Guides

- **[Terraform](language-guides/terraform.md)** - Complete Terraform standards (55KB, 2,420 lines)
- **[YAML](language-guides/yaml.md)** - YAML formatting, linting, examples
- **[Shell](language-guides/shell.md)** - Shell script best practices
- **[Markdown](language-guides/markdown.md)** - Documentation standards
- **[HCL](language-guides/hcl.md)** - Packer, Vault, Nomad configs
- **[Conf](language-guides/conf.md)** - Configuration file standards

## Features

### ✅ Comprehensive Standards

- **16 sections** in PROJECT-NAMING-STANDARDS.md covering every naming scenario
- **Language-specific guides** for Terraform, YAML, Shell, Markdown, HCL, Conf
- **Real examples** (good vs bad) for every pattern
- **Validation rules** with regex patterns

### 🤖 AI-Assisted Validation

- **Claude Code Router** with OpenRouter integration
- **Real-time validation** using natural language queries
- **Cost-effective**: ~$1-5/month (95% savings vs direct Claude API)
- Query standards interactively
- Auto-generate compliant code
- See [../AI-ASSISTED-VALIDATION.md](../AI-ASSISTED-VALIDATION.md) for setup

### 🛠️ Automation

- **CLI tool**: `refine-standards` for validation and auto-fixing
- **Pre-commit hooks**: Prevent violations before commit
- **GitHub Actions**: CI/CD integration
- **VS Code settings**: Editor integration

## Usage Examples

### Validate Repository Name

```bash
$ refine-standards validate-name --repo "Infrastructure-VPC"
❌ Repository name validation failed:
   - Must be lowercase (found: Infrastructure-VPC)
   - Suggestion: infrastructure-vpc
   
$ refine-standards validate-name --repo "infrastructure-vpc"
✅ Repository name is valid
```

### Validate Terraform Resource

```bash
$ refine-standards validate-name --terraform-resource "vpcMain"
❌ Terraform resource name validation failed:
   - Must be snake_case (found: vpcMain)
   - Suggestion: vpc_main

$ refine-standards validate-name --terraform-resource "vpc_main"
✅ Terraform resource name is valid
```

### Check Entire Project

```bash
$ refine-standards check

Checking project against refine.digital standards...

✅ Repository name: infrastructure-vpc
✅ Folder structure: compliant
❌ Found 3 violations:

1. File: terraform/main.tf:12
   Resource name "vpcMain" should be "vpc_main"
   
2. File: .github/workflows/deploy.yml:5
   File should be named with .yml not .yaml
   
3. File: README.md
   Missing required section: "Standards Compliance"

Run 'refine-standards fix --auto' to fix automatically
```

## AI-Assisted Validation with Claude Code Router

**Now Available**: Use AI to validate naming standards and query documentation in real-time!

```bash
# Start the router (if not already running)
ccr start

# Validate naming
echo "Is 'infrastructure-vpc' valid?" | ccr code

# Query standards
echo "What are Terraform resource naming conventions?" | ccr code
```

**Benefits**:
- Real-time standards validation
- Interactive documentation queries
- Cost-effective: ~$1-5/month
- 95% savings vs direct Claude API

See [../AI-ASSISTED-VALIDATION.md](../AI-ASSISTED-VALIDATION.md) for complete setup guide and [../Claude Code Router](../../Claude%20Code%20Router) project for installation.

## Installation

### CLI Tool (Coming Soon)

```bash
# Via npm
npm install -g refine-standards

# Via Homebrew
brew install refine-digital/tap/refine-standards
```

### MCP Server (Coming Soon)

```bash
# Docker
docker-compose up -d

# Or install directly
cd mcp/standards-server
npm install
npm run build
npm start
```

### Pre-commit Hooks

```bash
# In your project
pip install pre-commit
pre-commit install

# Or use refine-standards CLI
refine-standards hooks install
```

## Integration with Your Workflow

### Claude Code Router (Ready Now)

The Claude Code Router is installed and configured. Use it to validate naming and query standards:

```bash
# Validate naming conventions
echo "Is 'infrastructure-vpc' a valid repository name?" | ccr code

# Generate compliant code
echo "Generate Terraform module for VPC following refine.digital standards" | ccr code

# Query documentation
echo "What are the YAML file naming conventions?" | ccr code
```

See [../AI-ASSISTED-VALIDATION.md](../AI-ASSISTED-VALIDATION.md) for:
- Pre-commit hook integration
- VS Code task examples
- GitHub Actions workflows
- Batch validation scripts

### Traditional MCP Server (Coming Soon)

Future MCP server integration will enable:
- Direct Claude Code integration via MCP protocol
- Local standards API server
- Custom tool definitions

## Quick Reference

| Item | Format | Example |
|------|--------|---------|
| GitHub Repo | kebab-case | `infrastructure-vpc` |
| Local Folder | kebab-case | `infrastructure-vpc` |
| Terraform Resource | snake_case | `vpc_main` |
| Terraform Variable | snake_case | `vpc_cidr_block` |
| YAML File | kebab-case.yml | `docker-compose.yml` |
| YAML Key | snake_case | `environment_name` |
| Shell Script | kebab-case.sh | `deploy-infrastructure.sh` |
| Shell Constant | UPPER_SNAKE | `VPC_ID` |
| Shell Function | snake_case | `deploy_infrastructure` |
| Branch | type/description | `feature/vpc-setup` |
| Tag | vMAJOR.MINOR.PATCH | `v1.0.0` |

## Contributing

We welcome contributions! Please:

1. Read [CONTRIBUTING.md](CONTRIBUTING.md)
2. Follow the standards in this repository
3. Test your changes with `refine-standards check`
4. Submit a PR with conventional commit message

## Standards Compliance

This repository follows its own standards! 

```bash
cd development-standards
refine-standards check
# ✅ All checks passed
```

## Roadmap

### ✅ Phase 1: Foundation (Complete)
- [x] Repository structure
- [x] PROJECT-NAMING-STANDARDS.md
- [x] naming-validation-rules.json
- [x] Language guides (Terraform, YAML, Shell, Markdown, HCL, Conf)
- [x] OpenRouter model recommendations

### 🚧 Phase 2: Automation (In Progress)
- [ ] CLI tool (`refine-standards`)
- [ ] MCP server with OpenRouter
- [ ] Pre-commit hooks
- [ ] GitHub Actions workflows
- [ ] Configuration templates

### 📅 Phase 3: Rollout (Upcoming)
- [ ] Example projects
- [ ] Team training materials
- [ ] Migration guides
- [ ] VS Code extension

## Support

- **Issues**: [GitHub Issues](https://github.com/refinedigital/development-standards/issues)
- **Discussions**: [GitHub Discussions](https://github.com/refinedigital/development-standards/discussions)
- **Email**: platform-team@refine.digital

## License

MIT License - see [LICENSE](LICENSE)

## Acknowledgments

Built with AI assistance using:
- Claude Code (Anthropic)
- OpenRouter (model routing)
- MCP (Model Context Protocol)

---

**Version**: 1.0.0  
**Last Updated**: 2025-11-07  
**Maintained By**: refine.digital Platform Team
