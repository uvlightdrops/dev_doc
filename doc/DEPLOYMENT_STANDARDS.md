---
title: Cross-Project Deployment Standards
project: dev_doc
scope: architecture
purpose: Define deployment patterns and standards across all KI ecosystem projects
created: 2026-11-02 04
updated: 2026-11-10 23
update_reason: Validate YAML metadata and align semantic blocks with section headings
status: active
agent_project: kicli-code-assist
owner_team: ecosystem-architecture
applies_to:
- kicli-code-assist
- ki-knowledge
- ki-ecosystem
- yaml-cfg-wizard
semantic_blocks:
- deployment-architecture-overview
- directory-structure-per-project
- standard-stages
- configuration-loading-process
- service-definition-schema
- environment-variables
- troubleshooting-common-issues
- migration-path-from-legacy-deployments
- cross-project-references
- documentation-maintenance
- references
---

# Cross-Project Deployment Standards (KI Ecosystem)

**Last Updated**: 2026-11-02T04:25:00+01:00  
**Canonical Source**: This document  
**Scope**: All KI ecosystem projects using stackctl + ki_core configuration

---

<a id="1-deployment-architecture-overview"></a>

## 1. Deployment Architecture Overview [deployment-architecture-overview]

All KI ecosystem projects follow this 3-layer deployment model:

```
Layer 3: Orchestration (stackctl)
  ├─ read: stage selection (KI_STAGE env var)
  ├─ read: active_layers() from ki_core
  └─ action: start/stop/status for services

Layer 2: Configuration (ki_core)
  ├─ load: base config from config/defaults/stack.yaml
  ├─ merge: stage config from config/stages/{stage}.yaml
  ├─ apply: profile overrides from config/profiles/{profile}.yaml
  └─ result: unified stack configuration

Layer 1: Service Definitions
  ├─ services: API, frontend, workers, etc.
  ├─ ports: documented per service
  ├─ env: environment variables per stage
  └─ health: startup checks and timeouts
```

### Design Principles

1. **Single Source of Truth**: Config lives in `config/` hierarchy, not duplicated in `deploy/`
2. **Stage-Based Overrides**: Different scenarios use `.yaml` overrides, not separate deployment files
3. **Environmental Isolation**: `KI_STAGE` selects complete configuration set
4. **Ki-core Integration**: Configuration loading uses `ki_core:load_config` for automatic merging
5. **Minimal Duplication**: Each stage file contains only sections that differ from base

---

<a id="2-directory-structure-per-project"></a>

## 2. Directory Structure (Per Project) [directory-structure-per-project]

All projects follow this pattern:

```
project_root/
├── config/
│   ├── defaults/
│   │   └── stack.yaml                    # Canonical base configuration
│   ├── stages/
│   │   ├── .active_stage                 # Marker file (stage name)
│   │   ├── development.yaml              # Dev overrides
│   │   ├── staging.yaml                  # Staging overrides (if applicable)
│   │   ├── production.yaml               # Prod overrides
│   │   └── minimal.yaml                  # Minimal/CI overrides
│   ├── profiles/
│   │   └── [optional project-specific]
│   └── runtime/
│       └── [dynamic config at runtime]
├── doc/
│   └── deployment/
│       ├── README.md                     # Quick-start guide
│       ├── ARCHITECTURE.md               # Project-specific rationale
│       ├── IMPLEMENTATION.md             # Setup guide
│       └── [project-specific docs]
└── pyproject.toml
    └── [tool.stackctl] section
```

---

<a id="3-standard-stages"></a>

## 3. Standard Stages [standard-stages]

All projects define these 4+ stages in `config/stages/`:

### 3.1 Development (`development.yaml`)

**Purpose**: Local development with hot reload

**Characteristics**:
- Frontend: HMR enabled (Vite, Next.js, etc.)
- Backend: Reload-on-save
- Logging: Debug level
- Ports: Multiple (API, frontend separate)
- Example ports: API :8000, Frontend :3000-5173

**Use Case**: `export KI_STAGE=development && stackctl -t local start`

**Example override** (minimal, inherits base):
```yaml
# config/stages/development.yaml
services:
  frontend:
    env:
      REACT_APP_API_URL: http://localhost:8000
      VITE_API_URL: http://localhost:8000
```

### 3.2 Staging (`staging.yaml`)

**Purpose**: Pre-production testing (optional, if needed)

**Characteristics**:
- Same as production but with reduced performance requirements
- May use test database or staging external APIs
- Health checks enabled
- Logging: Info level

**Use Case**: `export KI_STAGE=staging && stackctl -t remote start`

### 3.3 Production (`production.yaml`)

**Purpose**: Production deployment

**Characteristics**:
- Frontend: Pre-built, static files served by backend
- Backend: Single unified service
- Logging: Warning level
- Ports: Single port (e.g., 8000)
- Security: All env vars for credentials

**Use Case**: 
```bash
export KI_STAGE=production
export KI_PROFILE=prod-secure  # if needed
stackctl -t remote start
```

**Example override**:
```yaml
# config/stages/production.yaml
services:
  web:
    port: 8000
    description: "Unified web service (API + static frontend)"
    env:
      LOG_LEVEL: WARNING
      FLASK_ENV: production
```

### 3.4 Minimal (`minimal.yaml`)

**Purpose**: CI/CD testing, server-only deployments

**Characteristics**:
- Backend/API only, no frontend
- No database migrations (unless required)
- Fast startup
- Used by GitHub Actions and automated tests

**Use Case**: `export KI_STAGE=minimal && stackctl -t local start`

---

<a id="4-configuration-loading-process"></a>

## 4. Configuration Loading Process [configuration-loading-process]

### 4.1 Stage Selection

**Precedence** (highest to lowest):
1. `KI_STAGE` environment variable (explicit selection)
2. `config/stages/.active_stage` file (persistent marker)
3. Project default (usually "development")

```bash
# Explicit: highest precedence
export KI_STAGE=production
.venv/bin/python -m stackctl -t local status

# Or persistent via marker file
echo "production" > config/stages/.active_stage
.venv/bin/python -m stackctl -t local status
```

### 4.2 Merge Strategy

**ki_core** implements 8-layer merge:

```
Layer 1: Schema defaults (lowest priority)
Layer 2: config/defaults/stack.yaml (base)
Layer 3: config/stages/{active}.yaml (stage override)
Layer 4: config/profiles/{active}.yaml (profile override)
Layer 5-8: [Runtime, environment, runtime-context, user-specific]
           (varies by application)
Layer 8: Explicit runtime config (highest priority)
```

**Key Rule**: Use `__replace__: true` in stage files to completely override a section:

```yaml
# config/stages/production.yaml
services:
  __replace__: true  # Replace entire services section, don't merge
  web:
    port: 8000
    # ... only production services
```

### 4.3 Verification Commands

```bash
# Show selected stage
echo "Active stage: $(cat config/stages/.active_stage)"

# Show merged configuration
.venv/bin/python -m stackctl spec

# Show config loading details
.venv/bin/python -c "from ki_core.config import active_layers; print(active_layers())"

# Validate configuration (dry-run)
.venv/bin/python -m stackctl spec > /dev/null && echo "OK"
```

---

<a id="5-service-definition-schema"></a>

## 5. Service Definition Schema [service-definition-schema]

All `stack.yaml` files follow this schema:

```yaml
stack:
  name: project-name
  target: local | remote  # Where to deploy
  services:
    service_name:
      description: "Human-readable description"
      port: 8000
      host: localhost | 0.0.0.0
      interpreter: /path/to/.venv/bin/python  # Or direct command
      run: python -m module | npm start | go run ./main.go
      env:
        VAR_NAME: value
        SECRET_VAR: ${ENV_SECRET}
      health:
        path: /health | /api/health | /liveness
        timeout: 10
        retries: 3
      depends_on:
        - other_service
```

**Example**:

```yaml
# config/defaults/stack.yaml
stack:
  name: kicli-assist
  target: local
  services:
    api:
      description: "Flask API backend"
      port: 8000
      host: localhost
      interpreter: .venv/bin/python
      run: python -m kicli_code_assist.web.server
      env:
        FLASK_ENV: development
        LOG_LEVEL: DEBUG
      health:
        path: /api/health
        timeout: 10
    frontend:
      description: "React frontend with HMR"
      port: 5173
      run: npm --prefix web/frontend start
      health:
        path: /
        timeout: 15
      depends_on:
        - api
```

---

<a id="6-environment-variables"></a>

## 6. Environment Variables [environment-variables]

### 6.1 Standard Variables (All Projects)

| Variable | Purpose | Example |
|----------|---------|---------|
| `KI_STAGE` | Active deployment stage | `development`, `production`, `minimal` |
| `KI_PROFILE` | Config profile (optional) | `prod-secure`, `local-debug` |
| `LOG_LEVEL` | Logging verbosity | `DEBUG`, `INFO`, `WARNING`, `ERROR` |
| `API_PORT` | Override API port (project-specific) | `8000`, `9000` |
| `FRONTEND_PORT` | Override frontend port | `3000`, `5173` |

### 6.2 Credentials & Secrets

**Rule**: Never commit `.env` files or secrets to version control.

**Setup**:
```bash
# Copy template and populate manually
cp config/.env.template config/.env.local
# Edit config/.env.local with actual values
# Add to .gitignore: config/.env.* (already done)
```

**Loading**:
```python
# In application code
from dotenv import load_dotenv
load_dotenv('config/.env.local')
```

---

<a id="7-troubleshooting--common-issues"></a>

## 7. Troubleshooting & Common Issues [troubleshooting-common-issues]

### Issue: Stage Not Applied

**Symptom**: Configuration shows base values, not stage overrides

**Check**:
```bash
# Verify stage selection
echo $KI_STAGE
cat config/stages/.active_stage

# Show active layers
.venv/bin/python -c "from ki_core.config import active_layers; print(active_layers())"

# Check file exists
ls -la config/stages/${KI_STAGE}.yaml
```

**Fix**: 
```bash
# Explicitly set stage
export KI_STAGE=development
.venv/bin/python -m stackctl spec  # Should show dev config now
```

### Issue: Port Already in Use

```bash
# Find process using port
lsof -i :8000

# Kill process
kill -9 <PID>

# Restart services
.venv/bin/python -m stackctl -t local start
```

### Issue: Config Loader Not Found

**Symptom**: `ConfigError: 'config_loader' not found`

**Check**:
```bash
# Verify pyproject.toml [tool.stackctl] section
grep -A 5 "\[tool.stackctl\]" pyproject.toml

# Test config loader directly
.venv/bin/python -c "from ki_core import load_config; print(load_config())"
```

**Fix**: Ensure `pyproject.toml` has:
```toml
[tool.stackctl]
config_loader = "ki_core:load_config"
config_key = "stack"
```

---

<a id="8-migration-path-from-legacy-deployments"></a>

## 8. Migration Path (From Legacy Deployments) [migration-path-from-legacy-deployments]

### Old Approach (Discouraged)
```
deploy/stack-dev.yaml
deploy/stack-prod.yaml
[no unified config loader]
```

### New Approach (Standard)
```
config/defaults/stack.yaml
config/stages/development.yaml
config/stages/production.yaml
[ki_core config loader in pyproject.toml]
```

**Migration Steps**:

1. **Create `config/` directory structure**
   ```bash
   mkdir -p config/{defaults,stages,profiles}
   ```

2. **Extract base config to `config/defaults/stack.yaml`**
   - Merge from `deploy/stack-*.yaml` files
   - Keep common sections

3. **Create stage overrides**
   - `config/stages/development.yaml`: dev-specific only
   - `config/stages/production.yaml`: prod-specific only
   - Use `__replace__: true` for sections that differ completely

4. **Update `pyproject.toml`**
   ```toml
   [tool.stackctl]
   config_loader = "ki_core:load_config"
   config_key = "stack"
   ```

5. **Test**
   ```bash
   export KI_STAGE=development
   .venv/bin/python -m stackctl spec  # Should work
   ```

6. **Archive old deployment files**
   ```bash
   mkdir doc/deployment/archive
   mv deploy/stack-*.yaml doc/deployment/archive/
   ```

---

<a id="9-cross-project-references"></a>

## 9. Cross-Project References [cross-project-references]

**For project-specific deployment docs**, link to this document:

```markdown
---
cross_references:
  - "dev_doc/doc/DEPLOYMENT_STANDARDS.md: Cross-project standards"
  - "stackctl/doc/README.md: stackctl usage"
---
```

**Example**:

```markdown
# kicli-code-assist Deployment

For general deployment patterns, see 
[Cross-Project Deployment Standards](DEPLOYMENT_STANDARDS.md).

This guide covers kicli-code-assist specific configuration...
```

---

<a id="10-documentation-maintenance"></a>

## 10. Documentation Maintenance [documentation-maintenance]

### Header Standards

All deployment documentation should have YAML headers:

```yaml
---
title: "Document Title"
project: "project-name"  # Which project this affects
agent_project: "kicli-code-assist"  # Which agent last edited
scope: "deployment"  # Topic area
purpose: "What problem does this solve?"
created: YYYY-MM-DDTHH:MM:SS+01:00
updated: YYYY-MM-DDTHH:MM:SS+01:00
update_reason: "Why was it updated?"
status: "active" | "archived" | "proposal"
cross_references:
  - "Other related docs"
---
```

### Update Frequency

- **DEPLOYMENT_STANDARDS.md** (this file): When deployment architecture changes
- **Project-specific docs**: When project setup changes
- **stackctl/doc/README.md**: When stackctl usage changes
- **stage files** (`config/stages/*.yaml`): As needed for configuration changes

---

<a id="references"></a>

## References [references]

- **stackctl**: `/home/flow/dev_flow/stackctl/doc/README.md`
- **ki_core config**: ki-core documentation
- **Example projects**: kicli-code-assist, ki-knowledge, ki-ecosystem

---

**Document Maintainer**: kicli-code-assist Copilot  
**Last Reviewed**: 2026-11-02T04:25:00+01:00  
**Next Review**: When deployment architecture changes