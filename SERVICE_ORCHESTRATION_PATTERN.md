---
title: Service Orchestration Pattern (stackctl + Layer 1)
project: Cross-Project
scope: Architecture & Deployment Standards
status: active
created: 2026-11-05T23:15:00+01:00
updated: 2026-11-05T23:15:00+01:00
update_reason: Synthesized from kicli-code-assist OPTION_C + ki-knowledge production implementation
agent_project: stackctl, kicli-code-assist, ki-knowledge
cross_references:
  - kicli-code-assist/doc/integration/OPTION_C_INTEGRATION_GUIDE.md
  - ki-knowledge/config/defaults/stack.yaml
  - server-mngm/SERVER_MANAGEMENT_README.md (historical, not implemented)
---

# Service Orchestration Pattern: stackctl + Optional Layer 1 Supervision

## Executive Summary

**Problem:** Projects manage service lifecycles inconsistently (ghost processes, no central visibility, manual coordination).

**Solution:** Two-layer pattern using stackctl (Layer 2: full orchestration) with optional project-specific Layer 1 (single-service supervision for development).

**Current Status:**
- ✅ **stackctl (Layer 2):** Fully implemented, proven across ki-knowledge production, kicli-code-assist design
- ⚠️ **Layer 1 (Supervision):** Designed (OPTION_C_INTEGRATION_GUIDE.md) but **not yet implemented** in any project
- ℹ️ **Legacy:** `~/dev_doc/server-mngm/` contains historical proposals from Oct 2028; not part of current implementation

**Recommendation:** 
- Start with stackctl only (proven, minimal complexity)
- Add Layer 1 supervision later if needed for development ergonomics
- See [When to Use Each Layer](#when-to-use-each-layer) below

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│ Layer 2: Full-Stack Orchestration (stackctl)                   │
│                                                                  │
│ Scope: Complete project stack + dependencies + multi-target     │
│ Config: stack.yaml (root) + config/stages/*.yaml (per-stage)   │
│ Features: Service dependencies, health checks, K8s overlay      │
│ Discovery: From stack.yaml (services.X.kind, .health_path, etc) │
│ Commands: stackctl start/stop/logs/status                       │
│ Targets: local (process), minikube, k8s                         │
│                                                                  │
│ Implementation: ✅ ki-knowledge (4 services), kicli design       │
└─────────────────────────────────────────────────────────────────┘
              ↓ respects depends_on, health checks
              ↓ aggregates logs, manages lifecycle
              ↓ transitions configs per target
┌─────────────────────────────────────────────────────────────────┐
│ Layer 1: Single-Service Supervision (Optional)                  │
│                                                                  │
│ Scope: One service at a time, developer-focused debugging       │
│ Config: ki.yaml (app runtime config)                            │
│ Features: Manual control, live-reload (future), process tracking│
│ Discovery: From ki.yaml (services.X.command, .port, etc)        │
│ Commands: kicli-assist server start --service X                 │
│ Targets: local only (dev machine)                               │
│                                                                  │
│ Implementation: ⚠️ Designed but not implemented                 │
└─────────────────────────────────────────────────────────────────┘
              ↓ reads from same process logs
              ↓ independent of stack.yaml
              ↓ for development ergonomics only
```

---

## When to Use Each Layer

### Use stackctl (Layer 2) When:
✅ Starting entire application stack  
✅ Coordinating services with dependencies  
✅ Deploying to staging (minikube) or production (K8s)  
✅ Need aggregated logs from multiple services  
✅ Health checks must pass before dependents start  
✅ Environment setup (secrets, volumes, networking) required  

**Command:**
```bash
stackctl -t local start              # entire stack
stackctl -t minikube start           # staging
stackctl -t k8s start                # production (with Helm/K8s)
```

### Use Layer 1 (Optional) When:
⚠️ Debugging **single service** in isolation  
⚠️ Need **auto-reload** on code changes (future)  
⚠️ Want to **skip the full stack** setup  
⚠️ Rapidly iterating on one component  

**Command (if implemented):**
```bash
kicli-assist server start --service backend
kicli-assist server restart --service backend
kicli-assist server logs --service backend --follow
```

**Current Workaround:**
```bash
# Since Layer 1 not yet implemented, either:
# A) Use stackctl start/stop for single service (orchestrated)
stackctl start api

# B) Run process manually (raw, no discovery)
cd kicli-code-assist
python -m uvicorn app:app --port 8000
```

---

## Layer 2: stackctl Full-Stack Orchestration

### Configuration (stack.yaml)

**Minimal example (process-based, local target):**
```yaml
stack:
  name: my-app
  target: local
  services:
    db:
      kind: process
      command: ["sqlite3", "app.db"]
      port: 5432
      health_path: /health
      health_timeout: 5
      health_retries: 3
      
    api:
      port: 8000
      health_path: /docs
      health_timeout: 60
      health_retries: 120
      command: ["python", "-m", "uvicorn", "app:app", "--host", "{host}", "--port", "{port}"]
      depends_on: [db]              # ← Wait for db to be healthy
      
    frontend:
      port: 3000
      health_path: /
      command: ["npm", "start"]
      depends_on: [api]             # ← Wait for api to be healthy
```

**Complex example (ki-knowledge: Docker Compose + K8s overlay):**
```yaml
stack:
  name: ki-knowledge
  target: local
  services:
    postgres:
      kind: compose                 # ← Docker Compose service
      health_timeout: 60
      health_retries: 120
      compose: {dir: deploy/postgres, service: postgres, requires: [...]}
      port: 5432
      k8s:                          # ← K8s override
        image: pgvector/pgvector:pg16
        env: {POSTGRES_DB: $(KI_PG_DB)}
        volumes: {pgdata: {mount: /var/lib/postgresql/data, size: 10Gi}}
        
    ollama:
      kind: compose
      compose: {dir: deploy/ollama}
      port: 11434
      health_path: /api/tags
      post_start: ["{python}", "-m", "ki_knowledge.ollama_models"]  # ← Hook
      k8s: {image: ollama/ollama:latest, volumes: {...}}
      
    api:
      port: 8090
      health_path: /docs
      command: ["{python}", "-m", "uvicorn", "ki_knowledge.api:app", "--port", "{port}"]
      env: {KNOWLEDGE_API_URL: "http://{services[api].probe_host}:{services[api].port}/api"}
      k8s: {image: "@app", secret_env: true}
```

### Key Features

| Feature | How It Works | Example |
|---------|-------------|---------|
| **Service Dependencies** | `depends_on` array; start order respects topological sort | `api: depends_on: [db, cache]` |
| **Health Checks** | HTTP GET to `health_path`; retry with `health_timeout` + `health_retries` | `health_path: /health`, `health_timeout: 60`, `health_retries: 120` |
| **Multi-Target Overlays** | Different service definitions per target (local/minikube/k8s) | `targets.minikube.services[X].k8s.command: [...]` |
| **Environment Variables** | `$VAR` expansion from `.env`; service reference syntax `{services[X].port}` | `env: {DB_URL: "postgres://{services[db].probe_host}:5432"}` |
| **Post-Start Hooks** | Run script after service starts (e.g., model download) | `post_start: ["{python}", "-m", "download_models"]` |
| **Secrets Management** | `secret_env` + `secret_files` for K8s; `.env` files for local | `secret_env: true`, `secret_files: [creds.yaml]` |
| **Compose Services** | Delegate to Docker Compose for complex services | `kind: compose`, `compose: {dir: deploy/postgres}` |

### Commands

```bash
# Show resolved config
stackctl spec

# Show service status (with health)
stackctl status

# Start entire stack
stackctl start

# Start single service (with dependencies)
stackctl start api

# Stop services (reverse order of depends_on)
stackctl stop api db

# Restart service
stackctl restart api

# Stream aggregated logs
stackctl logs -f [service ...]

# Git orchestration
stackctl clone                    # Clone missing sibling repos
stackctl pull                     # Update all (ff-only)
stackctl push                     # Push clean repos
stackctl checkout <branch>        # All repos → branch

# Deployment targets
stackctl -t minikube start        # Deploy to local Kubernetes
stackctl -t k8s start             # Deploy to production cluster
```

---

## Layer 1: Optional Single-Service Supervision (Designed, Not Yet Implemented)

### When Implemented: Configuration (ki.yaml)

```yaml
services:
  backend:
    command: ["python", "-m", "uvicorn", "app:app"]
    cwd: /home/flow/dev_flow/kicli-code-assist
    port: 8000
    pidfile: ~/.kicli/services/backend.pid
    
  frontend:
    command: ["npm", "start"]
    cwd: /home/flow/dev_flow/kicli-code-assist/web
    port: 3000
    pidfile: ~/.kicli/services/frontend.pid
```

### When Implemented: Commands

```bash
# Show all services
kicli-assist server status

# Start single service (for debugging)
kicli-assist server start --service backend

# View logs in real-time
kicli-assist server logs --service backend --follow

# Restart service
kicli-assist server restart --service backend

# Stop service
kicli-assist server stop --service backend

# With auto-reload (future)
kicli-assist server start --watch --service backend
```

### Relationship to stackctl

**NOT** a replacement for stackctl. Rather:
- **stackctl** = orchestration authority (knows dependencies, targets, full config)
- **Layer 1** = convenience for single-service development (independence from full stack)
- **No conflict:** Can use either or both (just not simultaneously on same service)

---

## Real-World Example: ki-knowledge

### Project Structure
```
ki-knowledge/
├── stack.yaml                 ← Layer 2 config: 4 services, Compose + K8s
├── config/
│   ├── defaults/stack.yaml    ← Main config
│   ├── stages/
│   │   ├── dev.yaml
│   │   ├── staging.yaml
│   │   └── production.yaml
│   └── runtime/runtime.yaml   ← Per-host overrides
├── deploy/
│   ├── postgres/
│   │   ├── Dockerfile
│   │   ├── docker-compose.yaml
│   │   └── .env
│   ├── ollama/
│   │   └── docker-compose.yaml
│   └── docker/
│       └── Dockerfile
└── doc/guides/
    └── DJANGO_DEV.md          ← Developer guide (mentions stackctl)
```

### Services in stack.yaml

| Service | Type | Purpose | Health Check | Dependencies |
|---------|------|---------|------|---|
| **postgres** | Docker Compose | Vector database | TCP 5432 | - |
| **ollama** | Docker Compose | Local LLM embedding | GET /api/tags | - |
| **api** | Process | FastAPI knowledge service | GET /docs | postgres, ollama |
| **django** | Process | Web UI | GET / | postgres |

### Workflow: Staging Deployment

```bash
# 1. Prepare
cd ki-knowledge
stackctl checkout staging
stackctl pull

# 2. Deploy to minikube
stackctl -t minikube start
# Output:
#   Preparing minikube profile 'ki-knowledge'...
#   Building image '@app'...
#   Starting postgres service... (health check: TCP 5432)
#   postgres is healthy ✓
#   Starting ollama service... (health check: GET /api/tags)
#   Running post_start hook: download_models...
#   ollama is healthy ✓
#   Starting api (depends on: postgres, ollama)...
#   api is healthy ✓
#   Starting django (depends on: postgres)...
#   django is healthy ✓
#   All services ready!

# 3. View status
stackctl -t minikube status
# Shows: Pod statuses, service URLs, health status

# 4. Stream logs
stackctl logs -f api django
# Shows aggregated logs with [api] or [django] prefix
```

### Why This Works Without Layer 1

- **Simplicity:** One tool (stackctl) manages everything
- **Complexity is acceptable:** 4 services with Docker Compose + K8s overlay, but stackctl handles it
- **No need for manual supervision:** stackctl handles dependencies, health checks, lifecycle
- **For single-service development:** Developer can SSH to dev machine, run `docker exec -it postgres psql` or restart service manually if needed

---

## kicli-code-assist: Recommended Approach

### Current State
- ✅ Layer 2 (stackctl) designed in OPTION_C_INTEGRATION_GUIDE.md (not yet implemented)
- ⚠️ Layer 1 (kicli-assist server) designed (not yet implemented)

### Recommendation for MVP

**Start with Layer 2 only:**
```bash
# ~/kicli-code-assist/.env
KI_STAGE=development

# ~/kicli-code-assist/stack.yaml
stack:
  name: kicli-code-assist
  target: local
  services:
    backend:
      port: 8000
      health_path: /docs
      command: ["python", "-m", "uvicorn", "app:app", "--host", "{host}", "--port", "{port}"]
      
    frontend:
      port: 3000
      health_path: /
      command: ["npm", "start"]
      depends_on: [backend]

# Then:
stackctl start                # Start full stack
stackctl logs -f              # Watch logs
```

**Decide on Layer 1 later:**
- If development needs auto-reload on single-service changes → Implement Layer 1
- If stackctl workflow is sufficient → Skip Layer 1
- Minimum requirement: stackctl works smoothly

---

## Comparison Table: Design vs. Reality

| Aspect | Original Plan (Oct 28) | ki-knowledge Reality | kicli-code-assist Plan |
|--------|------|------|------|
| **Scope** | Multi-project from day 1 | Single project (good enough) | Single project (MVP) |
| **Layer 2 (stackctl)** | ✅ Designed | ✅ Implemented, production | ✅ Designed, awaiting implementation |
| **Layer 1 (supervision)** | ✅ Designed | ❌ Not used (stackctl sufficient) | ⚠️ Designed, deferred |
| **Service Discovery** | Registry in ~/.kicli/ | From stack.yaml | From stack.yaml |
| **Health Checks** | Optional supervisor | stackctl handles | stackctl handles |
| **Multi-Target** | Planned | ✅ Implemented (local/minikube/k8s) | Planned (local only for MVP) |
| **Secrets** | Not addressed | ✅ secret_env + secret_files | Not yet addressed |
| **Complexity** | Moderate (registry + CLI) | Lower (just stack.yaml) | Lower (just stack.yaml) |

### Why ki-knowledge Chose stackctl-Only

1. **stackctl already existed** → Reuse rather than build new system
2. **stack.yaml sufficient** → Explicit dependencies, health checks, hooks cover all use cases
3. **Docker Compose integration** → Complex services (postgres, ollama) already containerized
4. **Multi-target needs** → K8s overlay system elegant and proven
5. **Team capacity** → No time for new Layer 1 supervision system

### Lessons for kicli-code-assist

- Don't build Layer 1 unless you have time and a clear need
- Get stackctl working first (simpler, proven, less code)
- If Layer 1 is needed later, it's an isolated addition (doesn't break Layer 2)
- Focus on: stack.yaml correctness, health checks, environment config, secrets

---

## Configuration Best Practices

### Best Practice 1: Use {host} and {port} Placeholders

```yaml
# ✅ CORRECT
services:
  api:
    port: 8000
    command: ["python", "-m", "uvicorn", "app:app", "--host", "{host}", "--port", "{port}"]
    env:
      API_URL: "http://{services[api].probe_host}:{port}"

# ❌ WRONG: Hardcoded
services:
  api:
    port: 8000
    command: ["python", "-m", "uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
    env:
      API_URL: "http://localhost:8000"
```

**Why:** Placeholders work across targets (local, minikube, k8s); hardcoding breaks K8s deployments.

### Best Practice 2: Declare Dependencies Explicitly

```yaml
# ✅ CORRECT
services:
  api:
    depends_on: [db]
  frontend:
    depends_on: [api]

# ❌ WRONG: Implicit ordering
services:
  db: ...
  api: ...
  frontend: ...
# (stackctl can't infer dependencies from order)
```

**Why:** stackctl validates DAG, prevents circular deps, guarantees correct start order.

### Best Practice 3: Set Reasonable Health Check Timeouts

```yaml
# ✅ CORRECT: Fast services (process/flask/django)
services:
  api:
    health_timeout: 5
    health_retries: 3

# ✅ CORRECT: Slow services (docker, k8s, downloads)
services:
  ollama:
    health_timeout: 60
    health_retries: 120
    post_start: ["{python}", "-m", "download_models"]

# ❌ WRONG: Way too short
services:
  db:
    health_timeout: 0.1
    health_retries: 1
# (will timeout before container starts)
```

**Why:** Different services have different startup times; adjust per service.

### Best Practice 4: Separate secrets from app config

```yaml
# ✅ CORRECT: stack.yaml references external secrets
stack:
  services:
    api:
      env:
        DB_PASSWORD: $(KI_PG_PASSWORD)    # From .env or secret_env
  k8s:
    secret_env:
      KI_PG_PASSWORD: <value from secret>
    secret_files: [creds.yaml]            # Mounted to /app/creds.yaml

# ✅ CORRECT: ki.yaml is app-only config (no secrets)
services:
  api:
    command: ["python", "app.py"]
    port: 8000

# ❌ WRONG: Secrets in ki.yaml
services:
  api:
    api_key: "sk-1234567890"              # DON'T PUT SECRETS HERE

# ❌ WRONG: Secrets in stack.yaml literal
stack:
  services:
    api:
      env:
        DB_PASSWORD: "postgres-password"   # DON'T HARDCODE SECRETS
```

**Why:** Secrets must be vaulted, never committed. stack.yaml uses `$(VAR)` expansion for this.

### Best Practice 5: Use Multi-Target Overlays

```yaml
# ✅ CORRECT: Declare target-specific differences
stack:
  services:
    api:
      command: ["python", "app.py"]
      env: {DEBUG: "true"}
  targets:
    minikube:
      services:
        api:
          env: {DEBUG: "false"}
    k8s:
      services:
        api:
          env: {DEBUG: "false"}
          k8s: {image: "@app"}

# ❌ WRONG: Separate files for each target
# (Hard to maintain, easy to diverge)
```

**Why:** Single source of truth with explicit overlays is clearer than multiple files.

---

## Troubleshooting

### Q: I ran `stackctl start` but a service didn't start?

```bash
# Check: Did dependencies start successfully?
stackctl status

# Check: Is health check failing?
stackctl logs api      # Look for error messages

# Check: Is health check timeout too short?
# Edit stack.yaml: increase health_timeout + health_retries
```

### Q: I want to debug a single service while the stack runs?

```bash
# Option 1: Use stackctl logs (not stopping stack)
stackctl logs -f api

# Option 2: SSH to dev machine, run service directly
cd kicli-code-assist/backend
python -m uvicorn app:app --port 8000

# Option 3: (future) Use Layer 1 if implemented
kicli-assist server start --service backend
```

### Q: stackctl says circular dependency detected?

```yaml
# ❌ WRONG
services:
  a:
    depends_on: [b]
  b:
    depends_on: [a]

# ✅ CORRECT: Reorganize to remove cycle
services:
  common:                    # Shared resource
    port: 5432
  a:
    depends_on: [common]
  b:
    depends_on: [common]
```

**Fix:** Refactor services to have a DAG (directed acyclic graph) of dependencies.

### Q: Health check returns 200 but stackctl says "failed"?

```bash
# Check 1: Is the health_path correct?
curl http://localhost:8000/docs
# Should return 200

# Check 2: Is stackctl querying the right port?
stackctl spec | grep "health_path"

# Check 3: Is there a network issue?
# stackctl might be checking from different network context (docker bridge, etc)
# → Add health_interval: 1 to retry more frequently
```

### Q: I ran Layer 1 (kicli-assist server) but it conflicts with stackctl?

```bash
# Both can manage same process, but use ONE or the OTHER per session:

# ✅ CORRECT: stackctl for full orchestration
stackctl start
stackctl logs -f

# ✅ CORRECT: Layer 1 for single-service debugging (if implemented)
kicli-assist server start --service backend
kicli-assist server logs --service backend -f

# ❌ WRONG: Mixing both
stackctl start                    # stackctl manages everything
kicli-assist server restart --service backend  # Conflicts!
```

---

## Migration Path: Old System → stackctl-Based

### Before (Manual or Makefile-Based)

```bash
# Scattered commands
cd kicli-code-assist
python -m uvicorn app:app &
cd web
npm start &
# Then manually stop with pkill, no dependency tracking
```

### After (stackctl-Based)

```bash
# Unified, declarative
cd kicli-code-assist
stackctl start           # All services, all dependencies, all health checks
stackctl logs -f         # All logs in one place
stackctl stop            # Clean shutdown in reverse order
```

### What Changes

| Aspect | Before | After |
|--------|--------|-------|
| **Config** | Scattered in Makefile, .env, shell scripts | Single stack.yaml + .env for secrets |
| **Dependencies** | Manual (hope backend starts before frontend) | Declared: `depends_on: [backend]` |
| **Health Checks** | None (manual verification) | Automatic: HTTP GET + retry logic |
| **Visibility** | Separate terminals for each service | Aggregated logs: `stackctl logs` |
| **Lifecycle** | Manual start/stop/kill | `stackctl start/stop/restart` |
| **Staging Deploy** | Manual commands per environment | `stackctl -t minikube start` (same config) |
| **Debugging Single Service** | Kill everything, restart one | `stackctl logs -f api` (no restart) |

---

## Phased Implementation Plan

### Phase 1: Get stackctl Working (MVP)
- [x] Understand stack.yaml structure
- [ ] Write initial stack.yaml with 2-3 services
- [ ] Test: `stackctl start` starts all in correct order
- [ ] Test: `stackctl status` shows health
- [ ] Test: `stackctl logs -f` works
- [ ] Integrate into `make dev-up` or similar

**Timeline:** 1-2 days

### Phase 2: Add Secrets & Staging Config (if needed)
- [ ] Extract secrets to `.env` file (.gitignore'd)
- [ ] Add `config/stages/staging.yaml` with Helm/K8s overlays
- [ ] Test: `stackctl -t minikube start` works

**Timeline:** 1-2 days (depends on infrastructure)

### Phase 3: Evaluate Layer 1 (optional, if needed)
- [ ] Review OPTION_C_INTEGRATION_GUIDE.md for kicli-assist server design
- [ ] Decide: Is it worth implementing for development ergonomics?
- [ ] If yes: Implement Layer 1 CLI in kicli-code-assist
- [ ] If no: Document why Layer 2-only is sufficient

**Timeline:** 1 week (if implemented)

---

## References

**In this repository:**
- `kicli-code-assist/doc/integration/OPTION_C_INTEGRATION_GUIDE.md` — Layer 1 + Layer 2 design
- `kicli-code-assist/stack.yaml` — Example config

**In ki-knowledge (reference implementation):**
- `ki-knowledge/config/defaults/stack.yaml` — Production-grade stack config (4 services, K8s)
- `ki-knowledge/doc/guides/DJANGO_DEV.md` — Developer guide (mentions stackctl)
- `stackctl` project — Source of truth for stackctl behavior

**Historical (informational only, not recommended to implement):**
- `~/dev_doc/server-mngm/SERVER_MANAGEMENT_README.md` — Oct 28 design for Layer 1 supervision system
- `~/dev_doc/server-mngm/SERVER_MANAGEMENT_DESIGN.md` — Architectural analysis of Options A, B, C

---

## FAQ

**Q: Do we need Layer 1 for kicli-code-assist?**

A: No for MVP. Start with stackctl (Layer 2 only). If developers later complain about having to run the full stack for single-service changes, implement Layer 1. But many projects (like ki-knowledge) don't need it.

**Q: Why did ki-knowledge skip Layer 1?**

A: Because stackctl is sufficient. The team didn't have time to build both, and stackctl solved the core problems (dependencies, health checks, logging, multi-target). Layer 1 would add convenience but not necessity.

**Q: Can I use just ki.yaml without stack.yaml?**

A: No. ki.yaml is for Layer 1 (if implemented). Layer 2 (stackctl) requires stack.yaml. They serve different purposes.

**Q: What if stack.yaml gets too complex?**

A: Break it into multiple files: `config/defaults/stack.yaml` (base) + `config/stages/dev.yaml`, `config/stages/staging.yaml` (overrides). stackctl merges them using the 8-layer config resolver (see ki-core docs).

**Q: Does stackctl work with systemd/supervisord services?**

A: Yes. Set `kind: systemd` or `kind: supervisord` in stack.yaml. stackctl delegates to the host's service manager. (Details in stackctl README.)

**Q: How do I deploy to production without stackctl installed there?**

A: Use K8s target (`stackctl -t k8s start`). Builds Docker images locally, pushes to registry, deploys via Helm. Production machines don't need stackctl; they run K8s/Helm.

---

## Document Maintenance

**Status:** Active (Nov 5, 2026)  
**Frequency:** Update if Layer 1 is implemented in kicli-code-assist, or if stackctl/ki-knowledge patterns change  
**Owner:** Service orchestration decisions (architecture)  
**Reviewers:** kicli-code-assist team lead, stackctl maintainers

---

**Version:** 1.0  
**Last Updated:** 2026-11-05  
**Approved By:** [Pending review]
