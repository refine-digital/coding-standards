# Configuration Files Standards

## Table of Contents

1. [Overview](#1-overview)
2. [File Naming Conventions](#2-file-naming-conventions)
3. [Folder Structure](#3-folder-structure)
4. [Format-Specific Standards](#4-format-specific-standards)
5. [Documentation Requirements](#5-documentation-requirements)
6. [Best Practices](#6-best-practices)
7. [Automation Tools](#7-automation-tools)
8. [VS Code Setup](#8-vs-code-setup)
9. [Examples](#9-examples)
10. [Security Considerations](#10-security-considerations)

---

## 1. Overview

This guide covers standards for various configuration file formats:

- **`.conf`** - General configuration (Nginx, Apache, system configs)
- **`.env`** - Environment variables
- **`.ini`** - INI format configs
- **`.properties`** - Java properties files
- **`.toml`** - TOML configuration files

---

## 2. File Naming Conventions

### General Configuration Files

**Format**: `kebab-case.conf` or `service-name.conf`

**Pattern**: `^[a-z0-9-]+\.conf$`

**Examples**:
```
✅ nginx.conf
✅ nginx-custom.conf
✅ redis.conf
✅ postgresql.conf
✅ app-config.conf

❌ Nginx.conf                # Capital letter
❌ nginx_custom.conf         # Underscore
❌ nginxCustom.conf          # CamelCase
```

### Environment Files

**Format**: `environment.env` or `.env`

**Pattern**: `^\.?[a-z0-9-]+\.env$`

**Examples**:
```
✅ .env
✅ .env.example
✅ .env.local
✅ dev.env
✅ staging.env
✅ prod.env

❌ .ENV                      # All caps
❌ Production.env           # Capital letter
❌ prod_env                 # No extension
```

### INI Files

**Format**: `kebab-case.ini` or `service.ini`

**Examples**:
```
✅ config.ini
✅ app-config.ini
✅ database.ini

❌ Config.INI
❌ app_config.ini
```

### Properties Files

**Format**: `kebab-case.properties` or `application.properties`

**Examples**:
```
✅ application.properties
✅ database.properties
✅ app-config.properties

❌ Application.properties
❌ app_config.properties
```

### TOML Files

**Format**: `kebab-case.toml` or standard names

**Examples**:
```
✅ config.toml
✅ pyproject.toml           # Python standard
✅ Cargo.toml               # Rust standard
✅ app-config.toml

❌ Config.TOML
❌ app_config.toml
```

---

## 3. Folder Structure

### Standard Configuration Layout

```
project-root/
├── configs/                  # Configuration files
│   ├── nginx/
│   │   ├── nginx.conf
│   │   └── nginx-custom.conf
│   ├── redis/
│   │   └── redis.conf
│   └── app/
│       ├── dev.env
│       ├── staging.env
│       └── prod.env
├── .env.example              # Template (committed)
├── .env                      # Actual values (gitignored)
└── config.toml               # Main app config
```

### Environment-Specific Configs

```
configs/
├── common.conf               # Shared configuration
├── dev/
│   ├── app.conf
│   └── database.conf
├── staging/
│   ├── app.conf
│   └── database.conf
└── prod/
    ├── app.conf
    └── database.conf
```

---

## 4. Format-Specific Standards

### 4.1 .conf Files (Nginx, Apache)

#### Nginx Configuration

**Syntax**: Nginx-specific

**Style**:
- 4-space indentation
- Semicolons required
- Comments with `#`

**Example**:
```nginx
# Main Nginx configuration
# Owner: Platform Team

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
    use epoll;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    # Logging format
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    # Performance settings
    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;
    keepalive_timeout  65;
    types_hash_max_size 2048;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types text/plain text/css text/xml text/javascript 
               application/json application/javascript application/xml+rss;

    # Include virtual host configs
    include /etc/nginx/conf.d/*.conf;
}
```

**Nginx Best Practices**:
1. Separate concerns (main config vs. virtual hosts)
2. Use variables for repeated values
3. Enable gzip compression
4. Set appropriate timeouts
5. Log to separate files per service

#### Apache Configuration

**Example**:
```apache
# Apache 2.4 Configuration
# Owner: Platform Team

ServerRoot "/etc/httpd"
Listen 80

# Load necessary modules
LoadModule mpm_prefork_module modules/mod_mpm_prefork.so
LoadModule dir_module modules/mod_dir.so
LoadModule mime_module modules/mod_mime.so
LoadModule log_config_module modules/mod_log_config.so

# Server identification
ServerAdmin admin@example.com
ServerName example.com:80

# Document root
DocumentRoot "/var/www/html"

<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

# Logging
ErrorLog "logs/error_log"
LogLevel warn

LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
CustomLog "logs/access_log" combined

# Include additional configurations
IncludeOptional conf.d/*.conf
```

### 4.2 .env Files

#### Format

**Syntax**: `KEY=value` pairs

**Naming**: `UPPER_SNAKE_CASE` for keys

**Example**:
```bash
# Application Configuration
# Environment: Development
# Last Updated: 2025-11-07

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb
DATABASE_POOL_SIZE=10
DATABASE_TIMEOUT=5000

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=secret123

# API Keys (DO NOT COMMIT REAL KEYS)
API_KEY=your_api_key_here
SECRET_KEY=your_secret_key_here

# Application
NODE_ENV=development
PORT=3000
LOG_LEVEL=debug

# AWS (use IAM roles in production)
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

# Feature Flags
ENABLE_FEATURE_X=true
ENABLE_DEBUG_MODE=true
```

**Rules**:
1. **UPPER_SNAKE_CASE** for all keys
2. No spaces around `=`
3. Quote values with spaces: `KEY="value with spaces"`
4. Comments with `#`
5. Group related variables
6. No trailing commas or semicolons

**Security**:
- Never commit real secrets
- Use `.env.example` as template
- Add `.env` to `.gitignore`
- Use secret management in production (Vault, AWS Secrets Manager)

**Template (.env.example)**:
```bash
# Application Configuration Template
# Copy to .env and fill in actual values

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/dbname
DATABASE_POOL_SIZE=10

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=your_redis_password

# API Keys
API_KEY=your_api_key
SECRET_KEY=your_secret_key

# Application
NODE_ENV=development
PORT=3000
LOG_LEVEL=info
```

### 4.3 .ini Files

**Syntax**: Sections with key=value pairs

**Example**:
```ini
# Application Configuration
# Owner: Platform Team

[database]
host = localhost
port = 5432
name = mydb
user = dbuser
password = secret123
pool_size = 10

[redis]
host = localhost
port = 6379
db = 0

[application]
name = My Application
version = 1.0.0
environment = development
debug = true

[logging]
level = INFO
file = /var/log/app/application.log
max_size = 100MB
backup_count = 5

[api]
base_url = https://api.example.com
timeout = 30
retry_count = 3
```

**Rules**:
1. Sections in `[square brackets]`
2. Keys use `snake_case` or `lowercase`
3. No quotes needed for values (unless they contain special chars)
4. Comments with `#` or `;`
5. Whitespace around `=` is optional but recommended

### 4.4 .properties Files

**Syntax**: Java properties format

**Example**:
```properties
# Application Properties
# Environment: Production

# Database Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/mydb
spring.datasource.username=dbuser
spring.datasource.password=secret123
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA/Hibernate
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect

# Server Configuration
server.port=8080
server.servlet.context-path=/api

# Logging
logging.level.root=INFO
logging.level.com.example=DEBUG
logging.file.name=/var/log/app/application.log

# Application Specific
app.name=My Application
app.version=1.0.0
app.feature.x.enabled=true
```

**Rules**:
1. Keys use dot notation: `category.subcategory.property`
2. No spaces around `=` or `:`
3. Comments with `#` or `!`
4. Multiline values with `\` at end of line
5. Special characters escaped

### 4.5 .toml Files

**Syntax**: TOML (Tom's Obvious Minimal Language)

**Example**:
```toml
# Application Configuration
# Owner: Platform Team

[application]
name = "My Application"
version = "1.0.0"
environment = "production"
debug = false

[database]
host = "localhost"
port = 5432
name = "mydb"
user = "dbuser"
password = "secret123"  # Use secrets manager in production
pool_size = 10
timeout = 5000

[database.options]
ssl_mode = "require"
connect_timeout = 10

[redis]
host = "localhost"
port = 6379
db = 0
password = ""

[api]
base_url = "https://api.example.com"
timeout = 30
retry_count = 3

[api.headers]
"Content-Type" = "application/json"
"Accept" = "application/json"

[logging]
level = "INFO"
file = "/var/log/app/application.log"
format = "json"

# Logging levels per module
[logging.levels]
root = "INFO"
"app.services" = "DEBUG"
"app.models" = "DEBUG"

[features]
feature_x = true
feature_y = false
feature_z = true

[[servers]]
name = "primary"
host = "10.0.1.10"
port = 8080

[[servers]]
name = "backup"
host = "10.0.1.11"
port = 8080
```

**Rules**:
1. Sections in `[brackets]`
2. Tables use dot notation: `[section.subsection]`
3. Arrays of tables: `[[array]]`
4. Strings require quotes
5. Comments with `#`
6. Types: strings, integers, floats, booleans, dates, arrays

---

## 5. Documentation Requirements

### File Header Comments

**Template**:
```
# Configuration Name
# Purpose: Brief description
# Environment: dev/staging/prod
# Owner: Team Name
# Last Updated: YYYY-MM-DD
# Related: Link to docs or other configs
```

### Section Comments

```ini
# Database Configuration
# Connection settings for PostgreSQL database
# Timeout values are in milliseconds
[database]
host = localhost
timeout = 5000
```

### Inline Comments

```bash
API_KEY=xxx  # Replace with actual API key from secrets manager
PORT=3000    # Application HTTP port
```

---

## 6. Best Practices

### General

1. **Environment-specific configs**: Separate dev/staging/prod
2. **Template files**: Provide `.example` files
3. **Comments**: Document non-obvious settings
4. **Validation**: Validate configs on startup
5. **Defaults**: Provide sensible defaults
6. **Version control**: Commit templates, not secrets
7. **Documentation**: Link to relevant docs

### Security

1. **Never commit secrets**: Use `.gitignore`
2. **Use placeholders**: In template files
3. **Secrets management**: Use Vault, AWS Secrets Manager, etc.
4. **Rotate secrets**: Regular rotation schedule
5. **Access control**: Limit who can access configs
6. **Encryption**: Encrypt configs at rest if needed

### Maintainability

1. **Consistent naming**: Follow naming conventions
2. **Grouping**: Group related settings
3. **Comments**: Explain why, not what
4. **Validation**: Include validation rules/schemas
5. **Versioning**: Version your config format

---

## 7. Automation Tools

### Validation

#### .env files
```bash
# Install dotenv-linter
brew install dotenv-linter

# Lint .env file
dotenv-linter .env

# Fix issues
dotenv-linter fix .env
```

#### TOML files
```bash
# Install taplo
cargo install taplo-cli

# Format TOML
taplo format config.toml

# Validate TOML
taplo check config.toml
```

#### INI files
```bash
# Python configparser validation
python3 << EOF
import configparser
config = configparser.ConfigParser()
try:
    config.read('config.ini')
    print("✅ Valid INI file")
except Exception as e:
    print(f"❌ Invalid INI: {e}")
EOF
```

### Linting

**editorconfig** for consistency:

```ini
# .editorconfig
[*.conf]
indent_style = space
indent_size = 4
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.env]
indent_style = space
indent_size = 2
insert_final_newline = true

[*.{ini,properties}]
indent_style = space
indent_size = 2

[*.toml]
indent_style = space
indent_size = 2
```

---

## 8. VS Code Setup

### Recommended Extensions

1. **Better TOML** (bungcip)
   - ID: `bungcip.better-toml`

2. **DotENV** (mikestead)
   - ID: `mikestead.dotenv`

3. **EditorConfig** (EditorConfig)
   - ID: `editorconfig.editorconfig`

4. **Nginx Configuration** (William Desportes)
   - ID: `william-desportes.nginx`

### Workspace Settings

```json
{
  "[toml]": {
    "editor.defaultFormatter": "tamasfe.even-better-toml",
    "editor.formatOnSave": true
  },
  "[properties]": {
    "editor.insertSpaces": true,
    "editor.tabSize": 2
  },
  "[ini]": {
    "editor.insertSpaces": true,
    "editor.tabSize": 2
  },
  "files.associations": {
    "*.conf": "conf",
    ".env*": "dotenv",
    "*.properties": "properties",
    "*.ini": "ini"
  }
}
```

---

## 9. Examples

### Example 1: Complete Nginx Config

See [4.1 .conf Files](#41-conf-files-nginx-apache) section.

### Example 2: Multi-Environment Setup

```bash
# configs/common.env
# Shared across all environments
APP_NAME=my-application
LOG_FORMAT=json
TIMEOUT=30000

# configs/dev.env
# Development-specific
NODE_ENV=development
DEBUG=true
LOG_LEVEL=debug
DATABASE_URL=postgresql://localhost:5432/myapp_dev

# configs/staging.env
# Staging-specific
NODE_ENV=staging
DEBUG=false
LOG_LEVEL=info
DATABASE_URL=postgresql://staging-db:5432/myapp

# configs/prod.env
# Production-specific
NODE_ENV=production
DEBUG=false
LOG_LEVEL=warn
DATABASE_URL=postgresql://prod-db:5432/myapp
```

### Example 3: Config Loading Script

```bash
#!/usr/bin/env bash
# load-config.sh
# Loads environment-specific configuration

set -euo pipefail

ENVIRONMENT="${1:-dev}"
CONFIG_DIR="./configs"

# Load common config
if [[ -f "${CONFIG_DIR}/common.env" ]]; then
    export $(cat "${CONFIG_DIR}/common.env" | grep -v '^#' | xargs)
fi

# Load environment-specific config
if [[ -f "${CONFIG_DIR}/${ENVIRONMENT}.env" ]]; then
    export $(cat "${CONFIG_DIR}/${ENVIRONMENT}.env" | grep -v '^#' | xargs)
else
    echo "❌ Config file not found: ${CONFIG_DIR}/${ENVIRONMENT}.env"
    exit 1
fi

echo "✅ Loaded configuration for: ${ENVIRONMENT}"
```

---

## 10. Security Considerations

### Secrets Management

**DO NOT**:
```bash
# ❌ Bad - secrets in version control
DATABASE_PASSWORD=super_secret_password_123
API_KEY=sk-real-api-key-12345
AWS_SECRET_ACCESS_KEY=actual-secret-key
```

**DO**:
```bash
# ✅ Good - use placeholders in templates
DATABASE_PASSWORD=<from_vault>
API_KEY=<from_secrets_manager>
AWS_SECRET_ACCESS_KEY=<use_iam_role>
```

### .gitignore Pattern

```gitignore
# Environment files
.env
.env.local
.env.*.local
*.env

# Config files with secrets
*-secrets.conf
*-credentials.ini
secrets/

# But keep templates
!.env.example
!*.conf.example
```

### Encryption

For configs that must be in version control:

```bash
# Encrypt with ansible-vault
ansible-vault encrypt prod.env

# Decrypt when needed
ansible-vault decrypt prod.env

# Or use git-crypt for automatic encryption
git-crypt init
echo "*.env filter=git-crypt diff=git-crypt" >> .gitattributes
```

---

## Summary

### Key Takeaways

1. **File naming**: Use `kebab-case` for config files
2. **Secrets**: Never commit real secrets, use templates
3. **Environment-specific**: Separate configs per environment
4. **Comments**: Document purpose and important settings
5. **Validation**: Validate configs before deployment
6. **Version control**: Commit templates, not actual configs

### Quick Reference

| Format | Extension | Key Format | Comments |
|--------|-----------|------------|----------|
| Nginx/Apache | `.conf` | varies | `#` |
| Environment | `.env` | `UPPER_SNAKE` | `#` |
| INI | `.ini` | `snake_case` | `#` or `;` |
| Properties | `.properties` | `dot.notation` | `#` or `!` |
| TOML | `.toml` | `snake_case` | `#` |

### Validation Checklist

- [ ] File name follows `kebab-case` convention
- [ ] No real secrets committed
- [ ] Template file (`.example`) provided
- [ ] File header comment present
- [ ] Settings grouped logically
- [ ] Non-obvious settings documented
- [ ] Validation passes (dotenv-linter, taplo, etc.)
- [ ] Added to `.gitignore` if contains secrets
- [ ] Environment-specific configs separated

---

**Document Version**: 1.0.0  
**Last Updated**: 2025-11-07  
**Maintained By**: refine.digital Platform Team  
**Related**: [PROJECT-NAMING-STANDARDS.md](../governance/PROJECT-NAMING-STANDARDS.md)
