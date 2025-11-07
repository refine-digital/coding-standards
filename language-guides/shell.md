# Shell Script Standards for IaC Projects

Comprehensive guide for shell script development at refine.digital.

**Document Version**: 1.0.0
**Last Updated**: 2025-11-07
**Maintained By**: refine.digital Platform Team

---

## Table of Contents

1. [File Naming Conventions](#file-naming-conventions)
2. [Folder Structure](#folder-structure)
3. [Shebang and Interpreter Selection](#shebang-and-interpreter-selection)
4. [Script Structure and Setup](#script-structure-and-setup)
5. [Naming Conventions](#naming-conventions)
6. [Best Practices](#best-practices)
7. [Error Handling and Traps](#error-handling-and-traps)
8. [Logging](#logging)
9. [Automation Tools](#automation-tools)
10. [VS Code Setup](#vs-code-setup)
11. [Pre-commit Hooks](#pre-commit-hooks)
12. [Common Patterns](#common-patterns)
13. [Example Scripts](#example-scripts)
14. [Security Considerations](#security-considerations)

---

## File Naming Conventions

### Shell Script Filenames

All shell scripts must use kebab-case naming with the `.sh` extension.

**Pattern**: `^[a-z0-9-]+\.sh$`

**Naming Convention**:

```
# Good examples
scripts/
├── deploy-infrastructure.sh           ✅
├── validate-terraform-config.sh       ✅
├── setup-development-environment.sh   ✅
├── cleanup-old-resources.sh           ✅
├── check-aws-compliance.sh            ✅
├── migrate-database.sh                ✅
└── generate-documentation.sh          ✅

# Bad examples
scripts/
├── deployInfrastructure.sh            ❌ (CamelCase)
├── Deploy_Infrastructure.sh           ❌ (Mixed case and underscore)
├── DEPLOY.sh                          ❌ (All caps)
├── deploy.sh                          ❌ (Too generic, no context)
├── deploy_infrastructure.sh           ❌ (Underscore instead of hyphen)
├── DeployInfrastructure              ❌ (No .sh extension)
└── deploy-infrastructure.bash         ❌ (Wrong extension, use .sh)
```

### Executable Scripts

Make scripts executable:

```bash
chmod +x scripts/deploy-infrastructure.sh
```

### Organizational Scripts

For utility or helper scripts, use `lib/` or `src/` directory:

```
scripts/
├── lib/
│   ├── logging.sh                     # Logging utilities
│   ├── error-handling.sh              # Error handling functions
│   └── aws-helpers.sh                 # AWS-specific helpers
├── src/
│   ├── validators/
│   │   ├── terraform-validator.sh
│   │   └── aws-validator.sh
│   └── deployers/
│       ├── infrastructure-deployer.sh
│       └── application-deployer.sh
├── deploy-infrastructure.sh           # Main scripts
└── validate-terraform-config.sh
```

---

## Folder Structure

### Standard Project Layout

```
{project-name}/
├── scripts/                           # Main scripts directory
│   ├── lib/                           # Shared libraries
│   │   ├── logging.sh                 # Logging utilities
│   │   ├── error-handling.sh          # Error handling
│   │   └── aws-helpers.sh             # AWS utilities
│   ├── src/                           # Source scripts
│   │   ├── validators/                # Validation scripts
│   │   │   ├── terraform-validator.sh
│   │   │   └── config-validator.sh
│   │   └── deployers/                 # Deployment scripts
│   │       └── infrastructure-deployer.sh
│   ├── deploy-infrastructure.sh       # Entry point scripts
│   ├── validate-configuration.sh
│   ├── cleanup-resources.sh
│   └── setup-environment.sh
├── .vscode/
│   ├── settings.json
│   └── extensions.json
├── .shellcheckrc                      # ShellCheck configuration
├── .shfmt                             # Shfmt configuration (optional)
├── .editorconfig
├── .gitignore
├── pre-commit-config.yaml             # Pre-commit hooks
├── README.md
└── docs/
    └── shell-scripts.md
```

### Scripts Directory Permissions

```bash
# Directory structure
drwxr-xr-x  scripts/
-rwxr-xr-x  scripts/deploy-infrastructure.sh
-rw-r--r--  scripts/lib/logging.sh
-rwxr-xr-x  scripts/lib/logging.sh (when sourced and executed)
```

---

## Shebang and Interpreter Selection

### Shebang Standards

**Always use**: `#!/usr/bin/env bash`

This is more portable than hardcoded paths like `#!/bin/bash`.

**Pattern**: `^#!/usr/bin/env bash$`

**Why Not `/bin/sh`**:
- Not all systems have Bash in `/bin/bash`
- Bash provides better features (arrays, associative arrays, regex)
- We explicitly support Bash-only features throughout standards
- More consistent with modern infrastructure tooling

### Complete Shebang Examples

```bash
#!/usr/bin/env bash
# Deploy infrastructure script
# Purpose: Deploy Terraform infrastructure to AWS
# Usage: ./scripts/deploy-infrastructure.sh [environment]

set -euo pipefail

# ... rest of script
```

### Shell Compatibility Notes

**Do NOT write POSIX-compatible shell scripts** unless explicitly required. We standardize on Bash features:

```bash
# Good - Uses Bash features
readonly ENVIRONMENTS=("dev" "staging" "prod")
declare -A CONFIG_MAP

# Avoid - POSIX sh only (unless specifically required)
# Use 'sh' instead of 'bash' only for:
# - System initialization scripts (rare)
# - Alpine Linux compatibility (document in README)
```

---

## Script Structure and Setup

### Mandatory Script Structure

Every script must follow this structure:

```bash
#!/usr/bin/env bash

################################################################################
# Script Name: script-name.sh
# Purpose: Brief description of what the script does
# Usage: ./script-name.sh [OPTIONS] [ARGUMENTS]
# Author: Your Name
# Date Created: YYYY-MM-DD
# Last Modified: YYYY-MM-DD
################################################################################

# Enable strict mode
set -euo pipefail

# Set up traps for cleanup
trap cleanup EXIT
trap error_handler ERR
trap signal_handler INT TERM

################################################################################
# Script Configuration
################################################################################

# Constants (UPPER_SNAKE_CASE)
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly SCRIPT_NAME="$(basename "${BASH_SOURCE[0]}")"
readonly PROJECT_ROOT="$(cd "${SCRIPT_DIR}/.." && pwd)"

# Configuration from environment
ENVIRONMENT="${ENVIRONMENT:-dev}"
AWS_REGION="${AWS_REGION:-us-east-1}"
DEBUG="${DEBUG:-false}"

################################################################################
# Logging and Error Functions
################################################################################

# logging functions here
log_info() { ... }
log_warn() { ... }
log_error() { ... }

# error handling functions here
error_handler() { ... }
cleanup() { ... }

################################################################################
# Helper Functions
################################################################################

# Validation functions
validate_input() { ... }
check_dependencies() { ... }

# Utility functions
get_vpc_id() { ... }
wait_for_resource() { ... }

################################################################################
# Main Function
################################################################################

main() {
    log_info "Starting deployment..."
    # Script logic here
}

################################################################################
# Entry Point
################################################################################

# Parse arguments
if [[ $# -eq 0 ]]; then
    show_usage
    exit 1
fi

# Run main function
main "$@"
exit 0
```

### Strict Mode: `set -euo pipefail`

**Always include this line after shebang**:

```bash
#!/usr/bin/env bash
set -euo pipefail
```

**What it does**:
- `set -e`: Exit on any error (unless using `||`)
- `set -u`: Exit if undefined variable is used
- `set -o pipefail`: Pipe failures cause script to fail

**Why it matters**: Prevents silent failures and unexpected behavior.

---

## Naming Conventions

### Variable Naming

Follow PROJECT-NAMING-STANDARDS.md conventions:

#### Constants (UPPER_SNAKE_CASE)

```bash
#!/usr/bin/env bash
set -euo pipefail

# Configuration constants (UPPER_SNAKE_CASE)
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly PROJECT_ROOT="$(dirname "${SCRIPT_DIR}")"
readonly LOG_FILE="${PROJECT_ROOT}/logs/deployment.log"
readonly MAX_RETRIES=3
readonly TIMEOUT_SECONDS=300

# Environment constants
readonly ENVIRONMENT="prod"
readonly AWS_REGION="us-east-1"
readonly AWS_ACCOUNT_ID="123456789012"

# Resource identifiers
readonly VPC_ID="vpc-12345678"
readonly CLUSTER_NAME="eks-prod-cluster"
readonly DATABASE_ENDPOINT="db.example.com"

# Feature flags
readonly ENABLE_LOGGING=true
readonly ENABLE_DRY_RUN=false
```

#### Local Variables (lower_snake_case)

```bash
# Local variables (lower_snake_case)
local vpc_cidr_block="10.0.0.0/16"
local subnet_count=3
local deployment_status="pending"
local retry_count=0

# Function parameters
get_vpc_info() {
    local vpc_id="$1"      # Parameter
    local region="${2:--}" # Parameter with default
}
```

#### Exported Variables (UPPER_SNAKE_CASE)

```bash
# Exported environment variables (UPPER_SNAKE_CASE)
export TF_VAR_environment="${ENVIRONMENT}"
export AWS_DEFAULT_REGION="${AWS_REGION}"
export AWS_PROFILE="production"
export KUBECONFIG="${HOME}/.kube/config"
```

### Function Naming

All functions use `snake_case` naming.

**Pattern**: `^[a-z0-9_]+$`

**Naming Convention**:

```bash
# Good function names
deploy_infrastructure() { }
validate_terraform_config() { }
cleanup_old_resources() { }
get_vpc_id() { }
wait_for_resource_ready() { }
check_aws_credentials() { }
print_usage_message() { }

# Bad function names
deployInfrastructure() { }          # CamelCase
Deploy_Infrastructure() { }         # Mixed case with underscore
DEPLOY() { }                        # All caps
deploy() { }                        # Too generic
```

### Naming Conventions by Context

```bash
# Getter functions
get_vpc_id() { ... }
get_subnet_ids() { ... }
get_current_timestamp() { ... }

# Validator/checker functions
validate_input() { ... }
check_dependencies() { ... }
is_resource_ready() { ... }

# Logging functions
log_info() { ... }
log_warn() { ... }
log_error() { ... }

# Cleanup/utility functions
cleanup_temporary_files() { ... }
cleanup() { ... }
print_usage() { ... }

# Handler functions
error_handler() { ... }
signal_handler() { ... }
```

---

## Best Practices

### Quoting Variables

Always quote variables to prevent word splitting:

```bash
# Good
cp "${file}" "${backup_dir}/"
if [[ "${status}" == "active" ]]; then
    echo "${message}"
fi

# Bad - AVOID
cp $file $backup_dir/          # Word splitting risk
if [[ $status == "active" ]]; then
    echo $message
fi
```

### Command Substitution

Prefer `$()` over backticks:

```bash
# Good
current_date="$(date '+%Y-%m-%d')"
vpc_id="$(aws ec2 describe-vpcs --query 'Vpcs[0].VpcId' --output text)"

# Avoid
current_date=`date '+%Y-%m-%d'`
vpc_id=`aws ec2 describe-vpcs --query 'Vpcs[0].VpcId' --output text`
```

### Function Documentation

Every function needs documentation:

```bash
################################################################################
# Function: validate_input
# Purpose: Validate command-line arguments and environment
# Arguments:
#   $1 - Environment name (dev, staging, prod)
#   $2 - Optional: Dry-run flag (--dry-run)
# Returns:
#   0 - Validation successful
#   1 - Validation failed (exits script)
# Output:
#   Logs validation messages to stdout
# Example:
#   validate_input "prod"
################################################################################
validate_input() {
    local environment="$1"
    local dry_run="${2:-false}"

    if [[ ! "${environment}" =~ ^(dev|staging|prod)$ ]]; then
        log_error "Invalid environment: ${environment}"
        exit 1
    fi
}
```

### Avoid Hardcoding Paths

Use variables for paths:

```bash
# Good
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly PROJECT_ROOT="$(cd "${SCRIPT_DIR}/.." && pwd)"
readonly CONFIG_DIR="${PROJECT_ROOT}/configs"

config_file="${CONFIG_DIR}/app-config.yml"

# Avoid
config_file="/Users/name/project/configs/app-config.yml"  # Hardcoded
```

### Use `local` in Functions

Always declare local variables in functions:

```bash
# Good
function deploy_infrastructure() {
    local environment="$1"
    local vpc_id=""
    local retry_count=0

    # Function logic
}

# Avoid - variables affect global scope
function deploy_infrastructure() {
    environment="$1"  # Affects global scope!
    vpc_id=""
}
```

### Default Values and Parameter Expansion

```bash
# Good - Parameter expansion with defaults
local region="${AWS_REGION:-us-east-1}"
local timeout="${TIMEOUT_SECONDS:-300}"
local dry_run="${DRY_RUN:-false}"

# For required parameters - fail if not set
local environment="${1:?ERROR: environment parameter required}"
```

---

## Error Handling and Traps

### Error Handler Function

Implement a comprehensive error handler:

```bash
################################################################################
# Error Handler
# Catches errors and provides context
################################################################################

error_handler() {
    local line_number="$1"
    local exit_code="$2"

    log_error "Script failed at line ${line_number} with exit code ${exit_code}"

    # Provide context about what failed
    log_error "Failed command: ${BASH_COMMAND}"

    # Clean up any partial resources
    cleanup

    exit "${exit_code}"
}

trap 'error_handler ${LINENO} $?' ERR
```

### Cleanup Function

Always implement cleanup for temporary resources:

```bash
################################################################################
# Cleanup Function
# Removes temporary files and resets state
################################################################################

cleanup() {
    local exit_code=$?

    # Remove temporary files
    if [[ -n "${TEMP_DIR:-}" && -d "${TEMP_DIR}" ]]; then
        log_info "Cleaning up temporary directory: ${TEMP_DIR}"
        rm -rf "${TEMP_DIR}"
    fi

    # Reset any state
    if [[ -f "${LOCK_FILE:-}" ]]; then
        rm -f "${LOCK_FILE}"
    fi

    # Return the original exit code
    return "${exit_code}"
}

trap cleanup EXIT
```

### Signal Handler

Handle interrupts gracefully:

```bash
################################################################################
# Signal Handler
# Handles Ctrl+C and termination signals
################################################################################

signal_handler() {
    log_warn "Received interrupt signal, cleaning up..."
    cleanup
    exit 130  # Standard exit code for SIGINT
}

trap signal_handler INT TERM
```

### Error Handling Patterns

```bash
# Pattern 1: Explicit error checking
if ! command_that_might_fail; then
    log_error "Command failed"
    return 1
fi

# Pattern 2: Check specific exit code
if ! result=$(aws s3 ls "${bucket_name}" 2>&1); then
    log_error "Failed to access S3 bucket: ${result}"
    return 1
fi

# Pattern 3: Return early from functions
validate_prerequisites() {
    if ! command -v terraform &>/dev/null; then
        log_error "terraform is not installed"
        return 1
    fi

    if ! command -v aws &>/dev/null; then
        log_error "aws cli is not installed"
        return 1
    fi

    return 0
}

# Pattern 4: Using || for error handling
command_that_might_fail || {
    log_error "Command failed"
    return 1
}
```

---

## Logging

### Logging Functions

Implement consistent logging:

```bash
################################################################################
# Logging Functions
################################################################################

# Color codes
readonly COLOR_RED='\033[0;31m'
readonly COLOR_YELLOW='\033[0;33m'
readonly COLOR_GREEN='\033[0;32m'
readonly COLOR_BLUE='\033[0;34m'
readonly COLOR_RESET='\033[0m'

################################################################################
# Log Info
# Logs informational messages
################################################################################
log_info() {
    local timestamp
    timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo -e "${COLOR_BLUE}[${timestamp}] INFO${COLOR_RESET}: $*" >&2

    if [[ -n "${LOG_FILE:-}" ]]; then
        echo "[${timestamp}] INFO: $*" >> "${LOG_FILE}"
    fi
}

################################################################################
# Log Warning
# Logs warning messages
################################################################################
log_warn() {
    local timestamp
    timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo -e "${COLOR_YELLOW}[${timestamp}] WARN${COLOR_RESET}: $*" >&2

    if [[ -n "${LOG_FILE:-}" ]]; then
        echo "[${timestamp}] WARN: $*" >> "${LOG_FILE}"
    fi
}

################################################################################
# Log Error
# Logs error messages
################################################################################
log_error() {
    local timestamp
    timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo -e "${COLOR_RED}[${timestamp}] ERROR${COLOR_RESET}: $*" >&2

    if [[ -n "${LOG_FILE:-}" ]]; then
        echo "[${timestamp}] ERROR: $*" >> "${LOG_FILE}"
    fi
}

################################################################################
# Log Debug
# Logs debug messages if DEBUG is enabled
################################################################################
log_debug() {
    if [[ "${DEBUG}" == "true" ]]; then
        local timestamp
        timestamp=$(date '+%Y-%m-%d %H:%M:%S')
        echo -e "${COLOR_GREEN}[${timestamp}] DEBUG${COLOR_RESET}: $*" >&2

        if [[ -n "${LOG_FILE:-}" ]]; then
            echo "[${timestamp}] DEBUG: $*" >> "${LOG_FILE}"
        fi
    fi
}

################################################################################
# Print Separator
# Prints a visual separator for sections
################################################################################
print_separator() {
    local length="${1:-80}"
    printf '%*s\n' "${length}" | tr ' ' '='
}
```

### Logging Best Practices

```bash
# Use log functions consistently
log_info "Starting deployment process"
log_debug "Environment: ${ENVIRONMENT}"
log_warn "Resource already exists, skipping creation"
log_error "Failed to authenticate with AWS"

# Log function entry and exit
function validate_config() {
    log_info "Validating configuration..."

    # Function logic

    log_info "Configuration validation complete"
}

# Log key decision points
if [[ -f "${CONFIG_FILE}" ]]; then
    log_info "Using existing configuration: ${CONFIG_FILE}"
else
    log_info "Creating new configuration: ${CONFIG_FILE}"
    create_default_config
fi
```

---

## Automation Tools

### ShellCheck Integration

**ShellCheck** is a static analysis tool for shell scripts.

#### Install ShellCheck

```bash
# macOS
brew install shellcheck

# Ubuntu/Debian
sudo apt-get install shellcheck

# Or from source
https://www.shellcheck.net/
```

#### ShellCheck Configuration (.shellcheckrc)

Create `/Users/refine/ProjectFiles/Coding Standards/development-standards/language-guides/.shellcheckrc`:

```ini
# ShellCheck configuration file
# Location: project root or scripts/ directory

# Exclude specific warnings
disable=SC2086,SC2181

# Enable strict checks
enable=all

# Source files to analyze
# (automatically finds all .sh files)
```

**Common ShellCheck Warnings to Disable (with caution)**:

- `SC2086`: Quote to prevent word splitting (we handle this explicitly)
- `SC2181`: Check exit code directly instead of $? (legacy patterns)
- `SC1091`: Not following source (common for sourced lib files)

#### Run ShellCheck

```bash
# Check single script
shellcheck scripts/deploy-infrastructure.sh

# Check all scripts
shellcheck scripts/*.sh

# Check with specific configuration
shellcheck --config=.shellcheckrc scripts/*.sh

# Check and output JSON
shellcheck -f json scripts/*.sh

# Check excluding subdirectories
shellcheck --exclude-dir=node_modules scripts/*.sh
```

### Shfmt Integration

**Shfmt** is a shell script formatter.

#### Install Shfmt

```bash
# macOS
brew install shfmt

# Ubuntu/Debian
curl https://storage.googleapis.com/shellcheck/latest/x86_64-unknown-linux-musl/shfmt -o shfmt
chmod +x shfmt

# Or from source
https://github.com/mvdan/sh
```

#### Shfmt Configuration (.shfmt)

```bash
# Shfmt formatting options
# Create file: .shfmt

# Formatting preferences
-i 4          # Indent with 4 spaces
-bn           # Binary operators at start of line
-sr           # Space after redirect operators
-kp           # Keep padding around pipes

# or more concisely:
# -i 4 -bn -sr -kp
```

#### Run Shfmt

```bash
# Format single script
shfmt -i 4 scripts/deploy-infrastructure.sh

# Format and write in-place
shfmt -i 4 -w scripts/deploy-infrastructure.sh

# Format all scripts
shfmt -i 4 -w scripts/*.sh

# Check formatting without changes
shfmt -i 4 -d scripts/*.sh
```

---

## VS Code Setup

### VS Code Extensions

Install the following extensions:

```json
{
    "extensions": [
        "timonwong.shellcheck",          # ShellCheck integration
        "foxundermoon.shell-format",     # Shfmt integration
        "ms-vscode.makefile-tools",      # Makefile support (optional)
        "ms-python.python"               # Python (for AWS scripts)
    ]
}
```

Create `.vscode/extensions.json`:

```json
{
    "recommendations": [
        "timonwong.shellcheck",
        "foxundermoon.shell-format",
        "ms-vscode.makefile-tools"
    ]
}
```

### VS Code Settings

Create `.vscode/settings.json`:

```json
{
    "[shellscript]": {
        "editor.defaultFormatter": "foxundermoon.shell-format",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
            "source.fixAll": true
        }
    },
    "shellformat.path": "/usr/local/bin/shfmt",
    "shellformat.args": [
        "-i",
        "4",
        "-bn",
        "-sr",
        "-kp"
    ],
    "shellcheck.enable": true,
    "shellcheck.run": "onSave",
    "shellcheck.executablePath": "/usr/local/bin/shellcheck",
    "shellcheck.ignorePatterns": {
        "**/*.zsh": true,
        "**/*.{ksh,csh}": true
    },
    "files.associations": {
        "*.sh": "shellscript",
        "Makefile": "makefile"
    },
    "editor.rulers": [
        80,
        120
    ],
    "editor.wordWrap": "off",
    "files.trimTrailingWhitespace": true,
    "files.insertFinalNewline": true
}
```

### VS Code Workspace Settings

Create `{project-name}.code-workspace`:

```json
{
    "folders": [
        {
            "path": "."
        }
    ],
    "settings": {
        "[shellscript]": {
            "editor.defaultFormatter": "foxundermoon.shell-format",
            "editor.formatOnSave": true
        },
        "shellcheck.enable": true,
        "shellcheck.run": "onSave"
    }
}
```

---

## Pre-commit Hooks

### Pre-commit Configuration

Create `.pre-commit-config.yaml`:

```yaml
repos:
  # ShellCheck
  - repo: https://github.com/shellcheck-py/shellcheck-py
    rev: v0.9.0.2
    hooks:
      - id: shellcheck
        args: ['--severity=warning']
        exclude: '^scripts/lib/.*\.sh$'  # Exclude sourced libraries if needed
        stages: [commit]

  # Shfmt
  - repo: https://github.com/scop/pre-commit-hooks
    rev: v1.26.0
    hooks:
      - id: shfmt
        args: ['-i', '4', '-bn', '-sr', '-kp', '-w']
        types: [shell]
        stages: [commit]

  # Trailing whitespace
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
        types: [shell]
      - id: end-of-file-fixer
        types: [shell]
      - id: check-merge-conflict
      - id: check-yaml
      - id: check-json

  # Custom Bash linting
  - repo: local
    hooks:
      - id: bash-lint-custom
        name: Custom Bash Lint
        entry: bash -c 'for file in "$@"; do bash -n "$file" || exit 1; done' --
        language: system
        types: [shell]
        stages: [commit]
```

### Install Pre-commit

```bash
# Install pre-commit
pip install pre-commit

# Install git hooks
cd /path/to/project
pre-commit install

# Run hooks on all files
pre-commit run --all-files

# Run specific hook
pre-commit run shellcheck --all-files
```

---

## Common Patterns

### Argument Parsing

```bash
################################################################################
# Argument Parsing
# Handles both positional and optional arguments
################################################################################

show_usage() {
    cat <<EOF
Usage: ${SCRIPT_NAME} [OPTIONS] [ARGUMENTS]

Options:
    -e, --environment ENV     Environment (dev, staging, prod) [default: dev]
    -r, --region REGION       AWS region [default: us-east-1]
    -d, --dry-run             Perform a dry-run (no changes)
    -v, --verbose             Enable verbose output
    -h, --help                Show this help message

Arguments:
    action                    Action to perform (deploy, destroy, validate)

Examples:
    ${SCRIPT_NAME} -e prod deploy
    ${SCRIPT_NAME} --environment staging --dry-run validate
    ${SCRIPT_NAME} -h

EOF
}

################################################################################
# Parse Arguments
################################################################################

parse_arguments() {
    while [[ $# -gt 0 ]]; do
        case "$1" in
            -e|--environment)
                ENVIRONMENT="$2"
                shift 2
                ;;
            -r|--region)
                AWS_REGION="$2"
                shift 2
                ;;
            -d|--dry-run)
                DRY_RUN=true
                shift
                ;;
            -v|--verbose)
                DEBUG=true
                shift
                ;;
            -h|--help)
                show_usage
                exit 0
                ;;
            -*)
                log_error "Unknown option: $1"
                show_usage
                exit 1
                ;;
            *)
                ACTION="$1"
                shift
                ;;
        esac
    done
}
```

### Retry Logic

```bash
################################################################################
# Retry Function
# Retries a command with exponential backoff
################################################################################

retry_with_backoff() {
    local max_attempts="${MAX_RETRIES:-3}"
    local timeout="${TIMEOUT_SECONDS:-5}"
    local attempt=1

    while [[ $attempt -le $max_attempts ]]; do
        if "$@"; then
            return 0
        fi

        if [[ $attempt -lt $max_attempts ]]; then
            local wait_time=$((timeout * (2 ** (attempt - 1))))
            log_warn "Attempt $attempt failed, retrying in ${wait_time}s..."
            sleep "$wait_time"
        fi

        ((attempt++))
    done

    log_error "All $max_attempts attempts failed"
    return 1
}

# Usage
retry_with_backoff aws s3 ls "${bucket_name}"
```

### Temporary Directory Handling

```bash
################################################################################
# Create Temporary Directory
# Creates a secure temporary directory with cleanup
################################################################################

create_temp_dir() {
    TEMP_DIR=$(mktemp -d)
    log_info "Created temporary directory: ${TEMP_DIR}"

    # Ensure cleanup on exit
    trap 'rm -rf "${TEMP_DIR}"' EXIT

    echo "${TEMP_DIR}"
}

# Usage
temp_dir=$(create_temp_dir)
config_file="${temp_dir}/config.yml"
```

### Validation Pattern

```bash
################################################################################
# Input Validation
# Validates command-line arguments and environment
################################################################################

validate_prerequisites() {
    local required_commands=("terraform" "aws" "jq")

    for cmd in "${required_commands[@]}"; do
        if ! command -v "$cmd" &>/dev/null; then
            log_error "Required command not found: $cmd"
            return 1
        fi
    done

    return 0
}

validate_environment() {
    if [[ ! "${ENVIRONMENT}" =~ ^(dev|staging|prod)$ ]]; then
        log_error "Invalid environment: ${ENVIRONMENT}"
        return 1
    fi

    if [[ -z "${AWS_REGION}" ]]; then
        log_error "AWS_REGION is not set"
        return 1
    fi

    return 0
}

validate_credentials() {
    if ! aws sts get-caller-identity &>/dev/null; then
        log_error "AWS credentials are not valid"
        return 1
    fi
}
```

### Conditional Execution

```bash
################################################################################
# Conditional Execution
# Checks before executing critical operations
################################################################################

# Check if resource exists before creating
if ! aws ec2 describe-vpcs --vpc-ids "${VPC_ID}" &>/dev/null; then
    log_info "Creating VPC..."
    aws ec2 create-vpc --cidr-block "10.0.0.0/16"
else
    log_info "VPC already exists, skipping creation"
fi

# Check dry-run mode
if [[ "${DRY_RUN}" == "true" ]]; then
    log_warn "DRY-RUN mode: Changes will NOT be applied"
    # Show what would happen
    terraform plan
else
    log_info "Applying changes..."
    terraform apply -auto-approve
fi
```

---

## Example Scripts

### Example 1: Deployment Script

**File**: `scripts/deploy-infrastructure.sh`

```bash
#!/usr/bin/env bash

################################################################################
# Script Name: deploy-infrastructure.sh
# Purpose: Deploy Terraform infrastructure to specified environment
# Usage: ./deploy-infrastructure.sh -e [environment] [-d|--dry-run] [-v|--verbose]
# Author: Platform Team
# Date Created: 2025-11-07
################################################################################

set -euo pipefail

################################################################################
# Script Configuration
################################################################################

readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly PROJECT_ROOT="$(cd "${SCRIPT_DIR}/.." && pwd)"
readonly SCRIPT_NAME="$(basename "${BASH_SOURCE[0]}")"

ENVIRONMENT="${ENVIRONMENT:-dev}"
AWS_REGION="${AWS_REGION:-us-east-1}"
DRY_RUN="${DRY_RUN:-false}"
DEBUG="${DEBUG:-false}"
LOG_FILE="${PROJECT_ROOT}/logs/deployment-$(date +%Y%m%d_%H%M%S).log"

# Create logs directory
mkdir -p "$(dirname "${LOG_FILE}")"

################################################################################
# Logging Functions
################################################################################

readonly COLOR_RED='\033[0;31m'
readonly COLOR_YELLOW='\033[0;33m'
readonly COLOR_GREEN='\033[0;32m'
readonly COLOR_BLUE='\033[0;34m'
readonly COLOR_RESET='\033[0m'

log_info() {
    local timestamp
    timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo -e "${COLOR_BLUE}[${timestamp}] INFO${COLOR_RESET}: $*" >&2
    echo "[${timestamp}] INFO: $*" >> "${LOG_FILE}"
}

log_warn() {
    local timestamp
    timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo -e "${COLOR_YELLOW}[${timestamp}] WARN${COLOR_RESET}: $*" >&2
    echo "[${timestamp}] WARN: $*" >> "${LOG_FILE}"
}

log_error() {
    local timestamp
    timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo -e "${COLOR_RED}[${timestamp}] ERROR${COLOR_RESET}: $*" >&2
    echo "[${timestamp}] ERROR: $*" >> "${LOG_FILE}"
}

log_debug() {
    if [[ "${DEBUG}" == "true" ]]; then
        local timestamp
        timestamp=$(date '+%Y-%m-%d %H:%M:%S')
        echo -e "${COLOR_GREEN}[${timestamp}] DEBUG${COLOR_RESET}: $*" >&2
        echo "[${timestamp}] DEBUG: $*" >> "${LOG_FILE}"
    fi
}

################################################################################
# Trap Functions
################################################################################

error_handler() {
    local line_number="$1"
    local exit_code="$2"

    log_error "Deployment failed at line ${line_number} (exit code: ${exit_code})"
    log_error "Failed command: ${BASH_COMMAND}"
    cleanup
    exit "${exit_code}"
}

signal_handler() {
    log_warn "Received interrupt signal, cleaning up..."
    cleanup
    exit 130
}

cleanup() {
    log_info "Cleaning up..."
    if [[ -n "${TEMP_DIR:-}" && -d "${TEMP_DIR}" ]]; then
        rm -rf "${TEMP_DIR}"
    fi
}

trap 'error_handler ${LINENO} $?' ERR
trap signal_handler INT TERM
trap cleanup EXIT

################################################################################
# Validation Functions
################################################################################

validate_prerequisites() {
    log_info "Validating prerequisites..."

    local required_commands=("terraform" "aws" "jq")
    for cmd in "${required_commands[@]}"; do
        if ! command -v "$cmd" &>/dev/null; then
            log_error "Required command not found: $cmd"
            return 1
        fi
    done

    log_debug "All required commands are available"
}

validate_environment() {
    log_info "Validating environment: ${ENVIRONMENT}"

    if [[ ! "${ENVIRONMENT}" =~ ^(dev|staging|prod)$ ]]; then
        log_error "Invalid environment: ${ENVIRONMENT}"
        return 1
    fi

    log_debug "Environment validation passed"
}

validate_aws_credentials() {
    log_info "Validating AWS credentials..."

    if ! aws sts get-caller-identity --region "${AWS_REGION}" &>/dev/null; then
        log_error "AWS credentials are not valid for region: ${AWS_REGION}"
        return 1
    fi

    local account_id
    account_id=$(aws sts get-caller-identity --query Account --output text)
    log_info "Using AWS account: ${account_id}"
}

################################################################################
# Deployment Functions
################################################################################

initialize_terraform() {
    log_info "Initializing Terraform for environment: ${ENVIRONMENT}"

    local env_dir="${PROJECT_ROOT}/environments/${ENVIRONMENT}"
    if [[ ! -d "${env_dir}" ]]; then
        log_error "Environment directory not found: ${env_dir}"
        return 1
    fi

    cd "${env_dir}"

    terraform init -upgrade
    log_info "Terraform initialization complete"
}

validate_terraform() {
    log_info "Validating Terraform configuration..."

    local env_dir="${PROJECT_ROOT}/environments/${ENVIRONMENT}"
    cd "${env_dir}"

    terraform validate
    terraform fmt -check -recursive

    log_info "Terraform validation passed"
}

plan_deployment() {
    log_info "Planning deployment for ${ENVIRONMENT}..."

    local env_dir="${PROJECT_ROOT}/environments/${ENVIRONMENT}"
    cd "${env_dir}"

    terraform plan -out=tfplan

    log_info "Plan saved to tfplan"
}

apply_deployment() {
    log_info "Applying deployment for ${ENVIRONMENT}..."

    local env_dir="${PROJECT_ROOT}/environments/${ENVIRONMENT}"
    cd "${env_dir}"

    if [[ "${DRY_RUN}" == "true" ]]; then
        log_warn "DRY-RUN: Skipping apply"
        return 0
    fi

    terraform apply tfplan
    log_info "Deployment applied successfully"
}

################################################################################
# Utility Functions
################################################################################

show_usage() {
    cat <<EOF
Usage: ${SCRIPT_NAME} [OPTIONS]

Options:
    -e, --environment ENV     Environment (dev, staging, prod) [default: dev]
    -r, --region REGION       AWS region [default: us-east-1]
    -d, --dry-run             Perform a dry-run (no apply)
    -v, --verbose             Enable verbose output
    -h, --help                Show this help message

Examples:
    ${SCRIPT_NAME} -e prod
    ${SCRIPT_NAME} --environment staging --dry-run
    ${SCRIPT_NAME} -e dev -v

EOF
}

parse_arguments() {
    while [[ $# -gt 0 ]]; do
        case "$1" in
            -e|--environment)
                ENVIRONMENT="$2"
                shift 2
                ;;
            -r|--region)
                AWS_REGION="$2"
                shift 2
                ;;
            -d|--dry-run)
                DRY_RUN=true
                shift
                ;;
            -v|--verbose)
                DEBUG=true
                shift
                ;;
            -h|--help)
                show_usage
                exit 0
                ;;
            -*)
                log_error "Unknown option: $1"
                show_usage
                exit 1
                ;;
            *)
                log_error "Unexpected argument: $1"
                show_usage
                exit 1
                ;;
        esac
    done
}

################################################################################
# Main Function
################################################################################

main() {
    log_info "Starting infrastructure deployment"
    log_info "Environment: ${ENVIRONMENT}"
    log_info "AWS Region: ${AWS_REGION}"
    log_info "Dry-run: ${DRY_RUN}"

    validate_prerequisites || exit 1
    validate_environment || exit 1
    validate_aws_credentials || exit 1

    initialize_terraform || exit 1
    validate_terraform || exit 1
    plan_deployment || exit 1
    apply_deployment || exit 1

    log_info "Infrastructure deployment completed successfully"
    log_info "Logs saved to: ${LOG_FILE}"
}

################################################################################
# Entry Point
################################################################################

parse_arguments "$@"
main
exit 0
```

### Example 2: Validation Script

**File**: `scripts/validate-terraform-config.sh`

```bash
#!/usr/bin/env bash

################################################################################
# Script Name: validate-terraform-config.sh
# Purpose: Validate Terraform configuration and AWS resources
# Usage: ./validate-terraform-config.sh [environment]
################################################################################

set -euo pipefail

################################################################################
# Configuration
################################################################################

readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly PROJECT_ROOT="$(cd "${SCRIPT_DIR}/.." && pwd)"
readonly SCRIPT_NAME="$(basename "${BASH_SOURCE[0]}")"

ENVIRONMENT="${1:-dev}"
AWS_REGION="${AWS_REGION:-us-east-1}"
DEBUG="${DEBUG:-false}"

################################################################################
# Logging
################################################################################

readonly COLOR_RED='\033[0;31m'
readonly COLOR_YELLOW='\033[0;33m'
readonly COLOR_GREEN='\033[0;32m'
readonly COLOR_BLUE='\033[0;34m'
readonly COLOR_RESET='\033[0m'

log_info() {
    echo -e "${COLOR_BLUE}[INFO]${COLOR_RESET} $*" >&2
}

log_pass() {
    echo -e "${COLOR_GREEN}[PASS]${COLOR_RESET} $*" >&2
}

log_fail() {
    echo -e "${COLOR_RED}[FAIL]${COLOR_RESET} $*" >&2
}

################################################################################
# Validation Functions
################################################################################

check_terraform_syntax() {
    log_info "Checking Terraform syntax..."

    local env_dir="${PROJECT_ROOT}/environments/${ENVIRONMENT}"
    if [[ ! -d "${env_dir}" ]]; then
        log_fail "Environment directory not found: ${env_dir}"
        return 1
    fi

    cd "${env_dir}"

    if terraform fmt -check -recursive &>/dev/null; then
        log_pass "Terraform formatting is correct"
    else
        log_fail "Terraform formatting is incorrect"
        return 1
    fi

    if terraform validate &>/dev/null; then
        log_pass "Terraform validation passed"
    else
        log_fail "Terraform validation failed"
        terraform validate
        return 1
    fi
}

check_aws_resources() {
    log_info "Checking AWS resources..."

    # Check VPC
    if aws ec2 describe-vpcs --region "${AWS_REGION}" &>/dev/null; then
        local vpc_count
        vpc_count=$(aws ec2 describe-vpcs --region "${AWS_REGION}" --query 'length(Vpcs)' --output text)
        log_pass "Found ${vpc_count} VPCs"
    else
        log_fail "Failed to describe VPCs"
        return 1
    fi
}

check_naming_conventions() {
    log_info "Checking naming conventions..."

    local env_dir="${PROJECT_ROOT}/environments/${ENVIRONMENT}"
    cd "${env_dir}"

    # Extract resource names from terraform
    local tf_files
    tf_files=$(find . -name "*.tf" -type f)

    while IFS= read -r tf_file; do
        if grep -q 'resource "' "${tf_file}"; then
            log_pass "Found resources in ${tf_file}"
        fi
    done <<< "${tf_files}"
}

################################################################################
# Main
################################################################################

main() {
    log_info "Validating Terraform configuration for ${ENVIRONMENT}"
    log_info "AWS Region: ${AWS_REGION}"

    check_terraform_syntax || return 1
    check_aws_resources || return 1
    check_naming_conventions || return 1

    log_pass "All validations passed!"
    return 0
}

main
exit $?
```

### Example 3: Cleanup Script

**File**: `scripts/cleanup-resources.sh`

```bash
#!/usr/bin/env bash

################################################################################
# Script Name: cleanup-resources.sh
# Purpose: Clean up temporary resources and old deployments
# Usage: ./cleanup-resources.sh -e [environment] [--force]
################################################################################

set -euo pipefail

################################################################################
# Configuration
################################################################################

readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly PROJECT_ROOT="$(cd "${SCRIPT_DIR}/.." && pwd)"
readonly SCRIPT_NAME="$(basename "${BASH_SOURCE[0]}")"

ENVIRONMENT="${ENVIRONMENT:-dev}"
AWS_REGION="${AWS_REGION:-us-east-1}"
FORCE="${FORCE:-false}"
DEBUG="${DEBUG:-false}"

################################################################################
# Logging
################################################################################

readonly COLOR_RED='\033[0;31m'
readonly COLOR_YELLOW='\033[0;33m'
readonly COLOR_GREEN='\033[0;32m'
readonly COLOR_BLUE='\033[0;34m'
readonly COLOR_RESET='\033[0m'

log_info() {
    echo -e "${COLOR_BLUE}[INFO]${COLOR_RESET} $*" >&2
}

log_warn() {
    echo -e "${COLOR_YELLOW}[WARN]${COLOR_RESET} $*" >&2
}

log_error() {
    echo -e "${COLOR_RED}[ERROR]${COLOR_RESET} $*" >&2
}

################################################################################
# Cleanup Functions
################################################################################

cleanup_terraform_cache() {
    log_info "Cleaning up Terraform cache..."

    local cache_dirs=(".terraform" ".terragrunt-cache" "tfplan" "tfstate.backup")

    for dir in "${cache_dirs[@]}"; do
        if find "${PROJECT_ROOT}" -name "${dir}" -type d | grep -q .; then
            log_info "Removing ${dir} directories..."
            find "${PROJECT_ROOT}" -name "${dir}" -type d -exec rm -rf {} + 2>/dev/null || true
        fi
    done

    log_info "Terraform cache cleanup complete"
}

cleanup_logs() {
    log_info "Cleaning up old logs..."

    local log_dir="${PROJECT_ROOT}/logs"
    if [[ -d "${log_dir}" ]]; then
        # Remove logs older than 30 days
        find "${log_dir}" -name "*.log" -type f -mtime +30 -delete
        log_info "Removed logs older than 30 days"
    fi
}

cleanup_aws_resources() {
    log_info "Identifying AWS resources for ${ENVIRONMENT}..."

    if [[ "${FORCE}" != "true" ]]; then
        log_warn "This will remove AWS resources. Use --force to confirm"
        return 0
    fi

    log_error "Resource deletion not implemented. Use terraform destroy instead."
}

################################################################################
# Utility Functions
################################################################################

show_usage() {
    cat <<EOF
Usage: ${SCRIPT_NAME} [OPTIONS]

Options:
    -e, --environment ENV     Environment (dev, staging, prod) [default: dev]
    -r, --region REGION       AWS region [default: us-east-1]
    --force                   Force cleanup (requires confirmation)
    -v, --verbose             Enable verbose output
    -h, --help                Show this help message

Examples:
    ${SCRIPT_NAME} -e dev
    ${SCRIPT_NAME} --environment staging --force
    ${SCRIPT_NAME} -h

EOF
}

parse_arguments() {
    while [[ $# -gt 0 ]]; do
        case "$1" in
            -e|--environment)
                ENVIRONMENT="$2"
                shift 2
                ;;
            -r|--region)
                AWS_REGION="$2"
                shift 2
                ;;
            --force)
                FORCE=true
                shift
                ;;
            -v|--verbose)
                DEBUG=true
                shift
                ;;
            -h|--help)
                show_usage
                exit 0
                ;;
            -*)
                log_error "Unknown option: $1"
                show_usage
                exit 1
                ;;
            *)
                shift
                ;;
        esac
    done
}

################################################################################
# Main
################################################################################

main() {
    log_info "Starting cleanup process for ${ENVIRONMENT}"

    cleanup_terraform_cache
    cleanup_logs
    cleanup_aws_resources

    log_info "Cleanup complete"
}

parse_arguments "$@"
main
exit 0
```

---

## Security Considerations

### Credential Handling

**Never hardcode credentials**:

```bash
# GOOD - Use environment variables
aws s3 ls "${S3_BUCKET}" --region "${AWS_REGION}"

# GOOD - Use AWS profiles
aws s3 ls "${S3_BUCKET}" --profile "${AWS_PROFILE}"

# BAD - Hardcoded credentials
aws s3 ls my-bucket --access-key AKIAIOSFODNN7EXAMPLE --secret-access-key wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

### Input Validation

Always validate user input:

```bash
# Validate environment parameter
if [[ ! "${environment}" =~ ^[a-z]+$ ]]; then
    log_error "Invalid environment: ${environment}"
    exit 1
fi

# Validate paths
if [[ ! -f "${config_file}" ]]; then
    log_error "Config file not found: ${config_file}"
    exit 1
fi

# Validate AWS account ID format
if [[ ! "${aws_account_id}" =~ ^[0-9]{12}$ ]]; then
    log_error "Invalid AWS account ID: ${aws_account_id}"
    exit 1
fi
```

### Preventing Command Injection

Always quote variables and use arrays for commands:

```bash
# GOOD - Quoted variables
echo "Processing: ${filename}"
cp "${source}" "${destination}"

# GOOD - Arrays for commands with arguments
declare -a cmd_args=("terraform" "apply" "-var-file=${env_file}")
"${cmd_args[@]}"

# BAD - Unquoted variables (injection risk)
echo Processing: $filename
cp $source $destination
```

### File Permissions

Set appropriate file permissions:

```bash
# Sensitive files should be readable only by owner
chmod 600 "${config_file}"
chmod 700 "${script_dir}"

# Executables
chmod 755 "${script_file}"
```

### Temporary File Security

Create secure temporary files:

```bash
# Good - mktemp creates files with secure permissions
temp_file=$(mktemp)
temp_dir=$(mktemp -d)

# Ensure cleanup
trap 'rm -f "${temp_file}"' EXIT

# Bad - Predictable names
temp_file="/tmp/config.txt"  # Vulnerable to race conditions
```

### Logging Sensitive Data

Never log passwords or credentials:

```bash
# Good - Log what's happening, not sensitive data
log_info "Authenticating to AWS..."
log_info "Using profile: ${AWS_PROFILE}"

# Bad - Logging sensitive data
log_debug "AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}"  # NEVER do this!
```

### Secure AWS API Calls

Use appropriate AWS CLI options:

```bash
# Good - Explicit region, profile, output format
aws ec2 describe-vpcs \
    --region "${AWS_REGION}" \
    --profile "${AWS_PROFILE}" \
    --output json \
    --query 'Vpcs[0].VpcId' \
    --output text

# Good - Error redirection
if ! aws sts get-caller-identity 2>/dev/null; then
    log_error "Authentication failed"
    exit 1
fi
```

---

## Summary Checklist

Use this checklist for new scripts:

- [ ] File named in `kebab-case.sh` format
- [ ] Located in appropriate `scripts/` subdirectory
- [ ] Shebang is `#!/usr/bin/env bash`
- [ ] `set -euo pipefail` on line after shebang
- [ ] Error handler implemented with trap
- [ ] Signal handler implemented (INT TERM)
- [ ] Cleanup function implemented with trap EXIT
- [ ] All constants use `UPPER_SNAKE_CASE`
- [ ] All local variables use `lower_snake_case`
- [ ] All functions use `snake_case` naming
- [ ] All variables properly quoted
- [ ] Logging functions implemented
- [ ] Input validation performed
- [ ] Function documentation provided
- [ ] Argument parsing implemented
- [ ] No hardcoded paths (use variables)
- [ ] ShellCheck validation passes
- [ ] Shfmt formatting applied
- [ ] Pre-commit hooks configured
- [ ] VS Code settings configured
- [ ] Credentials never hardcoded
- [ ] Temporary files cleaned up

---

## References

- [PROJECT-NAMING-STANDARDS.md](../governance/PROJECT-NAMING-STANDARDS.md)
- [Terraform Standards](./terraform.md)
- [ShellCheck Documentation](https://www.shellcheck.net/)
- [Google Shell Style Guide](https://google.github.io/styleguide/shellguide.html)
- [Bash Strict Mode](http://redsymbol.net/articles/unofficial-bash-strict-mode/)

---

**Document Version**: 1.0.0
**Last Updated**: 2025-11-07
**Maintained By**: refine.digital Platform Team
