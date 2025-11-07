# Markdown Standards for IaC Projects

## Table of Contents

1. [File Naming Conventions](#1-file-naming-conventions)
2. [Folder Structure](#2-folder-structure)
3. [Markdown Syntax](#3-markdown-syntax)
4. [Heading Conventions](#4-heading-conventions)
5. [README.md Template](#5-readmemd-template)
6. [Documentation Best Practices](#6-documentation-best-practices)
7. [Automation Tools](#7-automation-tools)
8. [VS Code Setup](#8-vs-code-setup)
9. [Pre-commit Hooks](#9-pre-commit-hooks)
10. [Examples](#10-examples)
11. [Badge Conventions](#11-badge-conventions)
12. [Validation Rules](#12-validation-rules)

---

## 1. File Naming Conventions

### Root-Level Documentation

**Format**: `UPPER-CASE.md` or `UPPER_SNAKE_CASE.md`

**Pattern**: `^[A-Z_]+\.md$`

**Standard Files**:
```
README.md                 # REQUIRED - Project overview
CONTRIBUTING.md           # Contribution guidelines
CHANGELOG.md              # Version history
LICENSE.md                # License (or just LICENSE)
SECURITY.md               # Security policy
CODE_OF_CONDUCT.md        # Code of conduct
```

**Examples**:
```
✅ README.md
✅ CONTRIBUTING.md
✅ CHANGELOG.md
❌ readme.md
❌ Readme.md
❌ ReadMe.md
```

### Nested Documentation

**Format**: `kebab-case.md`

**Pattern**: `^[a-z0-9-]+\.md$`

**Location**: `docs/` folder

**Examples**:
```
docs/
├── architecture.md               ✅
├── deployment-guide.md           ✅
├── troubleshooting.md            ✅
├── api-reference.md              ✅
└── getting-started.md            ✅

# Bad examples
docs/
├── Architecture.md               ❌ (Capital letter)
├── deployment_guide.md           ❌ (Underscore)
├── API-Reference.md              ❌ (Capital letters)
```

---

## 2. Folder Structure

### Standard Documentation Layout

```
project-root/
├── README.md                     # REQUIRED - Main documentation
├── CONTRIBUTING.md               # How to contribute
├── CHANGELOG.md                  # Version history
├── LICENSE                       # License file
├── docs/                         # Detailed documentation
│   ├── README.md                 # Docs index
│   ├── architecture.md
│   ├── deployment/
│   │   ├── README.md
│   │   ├── aws-deployment.md
│   │   └── local-deployment.md
│   ├── guides/
│   │   ├── getting-started.md
│   │   └── advanced-usage.md
│   └── api/
│       └── api-reference.md
└── examples/
    └── README.md                 # Examples documentation
```

---

## 3. Markdown Syntax

### Standard: CommonMark + GFM

Use **CommonMark** (common markdown) with **GitHub Flavored Markdown (GFM)** extensions.

### Line Length

**Recommended**: 80-120 characters per line (for readability in diffs)

**Exception**: Tables, code blocks, URLs can exceed

```markdown
✅ Good - wrapped at ~80 chars
This is a paragraph that wraps at approximately eighty characters to ensure
readability in both editors and during code review.

❌ Bad - very long line
This is a paragraph that goes on and on without any line breaks which makes it hard to read in code review and also causes horizontal scrolling in many editors which is annoying.
```

### Headings

**Use ATX-style headings** (with `#`), not underline style

```markdown
✅ Good (ATX style)
# Heading 1
## Heading 2
### Heading 3

❌ Bad (underline style)
Heading 1
=========

Heading 2
---------
```

### Lists

**Unordered lists**: Use `-` (hyphen)

```markdown
✅ Good
- Item 1
- Item 2
  - Nested item 2.1
  - Nested item 2.2

❌ Bad (inconsistent markers)
- Item 1
* Item 2
+ Item 3
```

**Ordered lists**: Use `1.` for all items (auto-numbering)

```markdown
✅ Good (easier to maintain)
1. First item
1. Second item
1. Third item

✅ Also acceptable
1. First item
2. Second item
3. Third item
```

### Code Blocks

**Always specify language** for syntax highlighting

```markdown
✅ Good
\`\`\`bash
terraform init
terraform plan
\`\`\`

\`\`\`hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
\`\`\`

❌ Bad (no language)
\`\`\`
terraform init
\`\`\`
```

### Inline Code

Use backticks for inline code, commands, file names, variables

```markdown
Run `terraform apply` to deploy.
Edit the `main.tf` file.
Set the `vpc_cidr_block` variable.
```

### Links

**Prefer descriptive link text** over bare URLs

```markdown
✅ Good
See the [Terraform documentation](https://terraform.io/docs) for details.

❌ Bad
See https://terraform.io/docs for details.

❌ Also bad
Click [here](https://terraform.io/docs) for details.
```

**Internal links**: Use relative paths

```markdown
See [Architecture](docs/architecture.md) for design details.
See [Deployment Guide](docs/deployment/aws-deployment.md).
```

### Images

**Syntax**: `![Alt text](path/to/image.png)`

```markdown
![Architecture Diagram](docs/images/architecture.png)

<!-- With optional title -->
![VPC Layout](images/vpc-layout.png "VPC Network Layout")
```

**Best practices**:
- Always include alt text
- Store images in `docs/images/` or `images/`
- Use descriptive file names: `vpc-architecture.png` not `img1.png`

### Tables

Use GFM table syntax

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Value 1  | Value 2  | Value 3  |
| Value A  | Value B  | Value C  |

<!-- With alignment -->
| Left | Center | Right |
|:-----|:------:|------:|
| A    | B      | C     |
```

### Blockquotes

```markdown
> This is a blockquote.
> It can span multiple lines.

> **Note**: Important information here.

> **Warning**: Critical warning here.
```

---

## 4. Heading Conventions

### Heading Levels

**Rules**:
1. Use sentence case (not Title Case)
2. Don't skip levels (don't go from `#` to `###`)
3. Only one `#` (H1) per file - use for title
4. Start content with `##` (H2)

```markdown
✅ Good (sentence case, proper hierarchy)
# Infrastructure VPC module

## Overview

### Prerequisites

### Installation steps

## Configuration

### Environment variables

❌ Bad
# Infrastructure VPC Module          # Title Case
### Prerequisites                    # Skipped H2
## Installation Steps                # Title Case
```

### Heading Content

- Be concise and descriptive
- Don't end with punctuation (no periods, colons)
- Use present tense for action headings

```markdown
✅ Good
## Quick start
## Deploy infrastructure
## Troubleshoot common issues

❌ Bad
## Quick Start:                      # Colon
## Deploying Infrastructure.         # Period
## How do I troubleshoot?            # Question
```

---

## 5. README.md Template

### Complete Template for IaC Projects

```markdown
# Project Name

> One-line description of the project

[![Standards Check](https://github.com/user/repo/workflows/standards/badge.svg)](https://github.com/user/repo/actions)
[![Terraform](https://img.shields.io/badge/Terraform-%3E%3D1.0-blue)](https://terraform.io)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

## Overview

2-3 paragraph description of what this project does, why it exists, and what
problem it solves. Include key features and benefits.

## Features

- ✅ Feature 1 with brief description
- ✅ Feature 2 with brief description
- ✅ Feature 3 with brief description

## Prerequisites

Before using this project, ensure you have:

- Terraform >= 1.0
- AWS CLI configured with appropriate credentials
- Docker (for local testing)
- [Other requirements]

## Project Structure

\`\`\`
project-root/
├── terraform/
│   ├── modules/
│   └── environments/
├── scripts/
├── docs/
└── README.md
\`\`\`

## Quick Start

### Installation

\`\`\`bash
# Clone the repository
git clone https://github.com/user/repo.git
cd repo

# Install dependencies
terraform init
\`\`\`

### Basic Usage

\`\`\`bash
# Deploy to development
cd environments/dev
terraform plan
terraform apply
\`\`\`

## Configuration

### Environment Variables

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| \`AWS_REGION\` | AWS region to deploy to | Yes | - |
| \`ENVIRONMENT\` | Environment name | Yes | - |
| \`VPC_CIDR\` | VPC CIDR block | No | \`10.0.0.0/16\` |

### Terraform Variables

See [variables.tf](terraform/variables.tf) for all available variables.

Key variables:

- \`vpc_cidr_block\`: VPC CIDR block (default: \`10.0.0.0/16\`)
- \`environment\`: Environment name (\`dev\`, \`staging\`, \`prod\`)
- \`enable_nat_gateway\`: Enable NAT gateway (default: \`true\`)

## Deployment

### Development Environment

\`\`\`bash
cd environments/dev
terraform plan -out=tfplan
terraform apply tfplan
\`\`\`

### Production Environment

\`\`\`bash
cd environments/prod
terraform plan -out=tfplan
# Review plan carefully
terraform apply tfplan
\`\`\`

## Testing

\`\`\`bash
# Run tests
make test

# Run linting
make lint

# Run all checks
make check
\`\`\`

## Documentation

- [Architecture](docs/architecture.md) - System architecture and design
- [Deployment Guide](docs/deployment-guide.md) - Detailed deployment steps
- [Troubleshooting](docs/troubleshooting.md) - Common issues and solutions
- [API Reference](docs/api-reference.md) - API documentation

## Examples

See [examples/](examples/) directory for usage examples:

- [Basic VPC](examples/basic-vpc/) - Simple VPC setup
- [Multi-Region](examples/multi-region/) - Multi-region deployment
- [High Availability](examples/ha/) - HA configuration

## Standards Compliance

This project follows [refine.digital Development Standards](https://github.com/refine-digital/development-standards).

\`\`\`bash
# Check compliance
refine-standards check

# Auto-fix issues
refine-standards fix
\`\`\`

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for details on:

- Code of conduct
- Development process
- How to submit pull requests
- Coding standards

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history and release notes.

## License

This project is licensed under the MIT License - see [LICENSE](LICENSE) for details.

## Support

- **Issues**: [GitHub Issues](https://github.com/user/repo/issues)
- **Discussions**: [GitHub Discussions](https://github.com/user/repo/discussions)
- **Email**: support@example.com

## Acknowledgments

- Built with [Terraform](https://terraform.io)
- Follows [HashiCorp best practices](https://terraform.io/docs/cloud/guides/recommended-practices)
- Uses [refine.digital standards](https://github.com/refine-digital/development-standards)

---

**Maintainers**: [@username](https://github.com/username)  
**Version**: 1.0.0  
**Last Updated**: 2025-11-07
```

---

## 6. Documentation Best Practices

### Writing Style

**Be clear and concise**:
```markdown
✅ Good
Run `terraform init` to initialize the working directory.

❌ Bad (too verbose)
In order to initialize the Terraform working directory, you should execute
the command `terraform init` which will download all necessary provider plugins
and modules.
```

**Use active voice**:
```markdown
✅ Good
Deploy the infrastructure with `terraform apply`.

❌ Bad (passive voice)
The infrastructure can be deployed by running `terraform apply`.
```

**Use present tense**:
```markdown
✅ Good
This module creates a VPC with public and private subnets.

❌ Bad
This module will create a VPC...
```

### Code Examples

**Always provide complete, working examples**:

```markdown
✅ Good (complete example)
## Usage

\`\`\`hcl
module "vpc" {
  source = "./modules/vpc"

  vpc_name        = "my-vpc"
  vpc_cidr_block  = "10.0.0.0/16"
  environment     = "prod"
}
\`\`\`

❌ Bad (incomplete)
## Usage

\`\`\`hcl
module "vpc" {
  ...
}
\`\`\`
```

### Warnings and Notes

Use appropriate callouts:

```markdown
> **Note**: This is informational content.

> **Warning**: This is something users need to be careful about.

> **Important**: Critical information that must not be missed.

> **Tip**: Helpful suggestion or best practice.
```

### Cross-References

Link to related documentation:

```markdown
For more details, see:
- [Architecture documentation](docs/architecture.md)
- [Deployment guide](docs/deployment-guide.md)
- [Terraform module documentation](https://registry.terraform.io/modules/...)
```

---

## 7. Automation Tools

### markdownlint

**Purpose**: Lint markdown files for style and syntax

#### Installation

```bash
# Node.js
npm install -g markdownlint-cli

# Homebrew (macOS)
brew install markdownlint-cli
```

#### Configuration

**File**: `.markdownlint.yml`

```yaml
# markdownlint configuration for refine.digital standards

# Extend default rules
extends: default

# Customize rules
rules:
  # Line length
  MD013:
    line_length: 120
    heading_line_length: 80
    code_block_line_length: 120
    tables: false

  # Heading style (ATX)
  MD003:
    style: atx

  # Unordered list style
  MD004:
    style: dash

  # Ordered list item prefix
  MD029:
    style: one

  # Inline HTML (allow for badges, etc.)
  MD033:
    allowed_elements: ['br', 'img', 'a']

  # First line heading
  MD041: true

  # Heading capitalization (sentence case preferred)
  MD048:
    style: consistent

  # Code fence style
  MD046:
    style: fenced

  # Code fence language
  MD040: true

# Ignore patterns
ignores:
  - node_modules/
  - .terraform/
  - CHANGELOG.md        # Auto-generated
```

#### Usage

```bash
# Lint all markdown files
markdownlint '**/*.md'

# Lint specific file
markdownlint README.md

# Fix auto-fixable issues
markdownlint --fix '**/*.md'

# Use custom config
markdownlint -c .markdownlint.yml '**/*.md'
```

### Prettier (for markdown formatting)

```bash
# Install
npm install --save-dev prettier

# Format markdown
prettier --write "**/*.md"

# Check formatting
prettier --check "**/*.md"
```

**Configuration** (`.prettierrc.yml`):
```yaml
printWidth: 100
proseWrap: always
```

---

## 8. VS Code Setup

### Recommended Extensions

1. **Markdown All in One** (Yu Zhang)
   - ID: `yzhang.markdown-all-in-one`
   - Features: Preview, shortcuts, table of contents

2. **markdownlint** (David Anson)
   - ID: `davidanson.vscode-markdownlint`
   - Features: Linting, auto-fix

3. **Markdown Preview Enhanced**
   - ID: `shd101wyy.markdown-preview-enhanced`
   - Features: Enhanced preview

### Workspace Settings

**File**: `.vscode/settings.json`

```json
{
  "editor.formatOnSave": true,
  "[markdown]": {
    "editor.defaultFormatter": "yzhang.markdown-all-in-one",
    "editor.wordWrap": "on",
    "editor.quickSuggestions": false,
    "editor.rulers": [80, 120]
  },
  "markdown.extension.toc.levels": "2..6",
  "markdown.extension.list.indentationSize": "adaptive",
  "markdownlint.config": {
    "MD013": {
      "line_length": 120
    }
  },
  "files.associations": {
    "*.md": "markdown"
  }
}
```

### Extensions Configuration

**File**: `.vscode/extensions.json`

```json
{
  "recommendations": [
    "yzhang.markdown-all-in-one",
    "davidanson.vscode-markdownlint",
    "shd101wyy.markdown-preview-enhanced"
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
      - id: check-merge-conflict
      - id: end-of-file-fixer
        files: \.md$
      - id: trailing-whitespace
        files: \.md$

  - repo: https://github.com/igorshubovych/markdownlint-cli
    rev: v0.37.0
    hooks:
      - id: markdownlint
        args: [--config, .markdownlint.yml, --fix]

  - repo: https://github.com/pre-commit/mirrors-prettier
    rev: v3.1.0
    hooks:
      - id: prettier
        files: \.md$
        args: [--write]
```

### Installation

```bash
pip install pre-commit
pre-commit install
pre-commit run --all-files
```

---

## 10. Examples

### Example 1: Complete README.md

See [Section 5](#5-readmemd-template) for full template.

### Example 2: CONTRIBUTING.md

```markdown
# Contributing to Project Name

Thank you for considering contributing to this project!

## Code of Conduct

This project adheres to the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md).

## How to Contribute

### Reporting Issues

- Use GitHub Issues
- Include detailed description
- Provide reproducible example
- Mention your environment (OS, Terraform version, etc.)

### Pull Requests

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Make your changes following our standards
4. Test your changes
5. Commit with conventional commits: `feat: add new feature`
6. Push and create a pull request

### Development Setup

\`\`\`bash
git clone https://github.com/user/repo.git
cd repo
terraform init
\`\`\`

### Testing

\`\`\`bash
make test
make lint
\`\`\`

### Standards

Follow [refine.digital Development Standards](https://github.com/refine-digital/development-standards).

\`\`\`bash
refine-standards check
\`\`\`

## Review Process

1. Automated checks must pass
2. Code review by maintainers
3. Approval required before merge

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
```

### Example 3: CHANGELOG.md

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Nothing yet

## [1.0.0] - 2025-11-07

### Added
- Initial release
- VPC module with public and private subnets
- NAT gateway support
- Multi-AZ configuration

### Changed
- Updated Terraform requirement to >= 1.0

### Fixed
- Fixed subnet CIDR calculation

### Security
- Added encryption for EBS volumes

## [0.1.0] - 2025-10-01

### Added
- Project scaffolding
- Basic VPC configuration

[Unreleased]: https://github.com/user/repo/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/user/repo/compare/v0.1.0...v1.0.0
[0.1.0]: https://github.com/user/repo/releases/tag/v0.1.0
```

---

## 11. Badge Conventions

### Status Badges

Use shields.io for consistency:

```markdown
[![Build Status](https://github.com/user/repo/workflows/CI/badge.svg)](https://github.com/user/repo/actions)
[![Standards](https://img.shields.io/badge/standards-compliant-green)](https://github.com/refine-digital/development-standards)
[![License](https://img.shields.io/badge/license-MIT-blue)](LICENSE)
```

### Technology Badges

```markdown
[![Terraform](https://img.shields.io/badge/Terraform-%3E%3D1.0-623CE4?logo=terraform)](https://terraform.io)
[![AWS](https://img.shields.io/badge/AWS-Infrastructure-orange?logo=amazon-aws)](https://aws.amazon.com)
[![Docker](https://img.shields.io/badge/Docker-Containerized-blue?logo=docker)](https://docker.com)
```

### Version Badges

```markdown
[![Version](https://img.shields.io/github/v/release/user/repo)](https://github.com/user/repo/releases)
[![Latest Tag](https://img.shields.io/github/v/tag/user/repo)](https://github.com/user/repo/tags)
```

---

## 12. Validation Rules

### Automated Validation

```bash
# Lint markdown
markdownlint '**/*.md'

# Check links
markdown-link-check README.md

# Format check
prettier --check "**/*.md"
```

### CI/CD Integration

```yaml
# .github/workflows/markdown-validation.yml
name: Markdown Validation

on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Lint markdown
        uses: avto-dev/markdown-lint@v1
        with:
          config: '.markdownlint.yml'
          args: '**/*.md'

      - name: Check links
        uses: gaurav-nelson/github-action-markdown-link-check@v1
```

---

## Summary

### Key Takeaways

1. **File naming**:
   - Root files: `UPPER-CASE.md` (README.md, CONTRIBUTING.md)
   - Nested docs: `kebab-case.md`

2. **Headings**:
   - Sentence case
   - Don't skip levels
   - One H1 per file

3. **Style**:
   - CommonMark + GFM
   - Specify language in code blocks
   - Line length: 80-120 characters

4. **Automation**:
   - Use markdownlint for linting
   - Use prettier for formatting
   - Integrate in pre-commit hooks

5. **README.md must include**:
   - Overview
   - Prerequisites
   - Quick start
   - Configuration
   - Standards compliance section

### Quick Checklist

- [ ] File name follows convention (UPPER or kebab-case)
- [ ] Includes H1 title
- [ ] Uses sentence case for headings
- [ ] Code blocks specify language
- [ ] Links use descriptive text
- [ ] Images have alt text
- [ ] Passes markdownlint validation
- [ ] Line length < 120 characters
- [ ] No trailing whitespace
- [ ] Final newline present

---

**Document Version**: 1.0.0  
**Last Updated**: 2025-11-07  
**Maintained By**: refine.digital Platform Team  
**Related**: [PROJECT-NAMING-STANDARDS.md](../governance/PROJECT-NAMING-STANDARDS.md)
