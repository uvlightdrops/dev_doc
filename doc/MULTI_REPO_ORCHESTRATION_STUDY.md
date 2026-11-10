---
# Multi-Repo Orchestration: Architecture Study
# Original source creation date: 2026-10-28 (hour not recorded).
# created records the first Git appearance, not an inferred source creation hour.
References: stackctl, server_management (ki-core), ki.yaml, stack.yaml
update_reason: Validate YAML metadata and align semantic blocks with section headings
historical_status: Foundation Phase
project: dev_doc
scope: architecture
purpose: Study of repository, service and configuration orchestration options
created: 2026-11-05 11
agent_project: ki-ecosystem
status: draft
semantic_blocks:
- executive-summary
- bestehende-implementierungen
- das-overlap-problem
- best-practice-patterns-forschung
- architektur-optionen
- empfehlung-fuer-kicli-code-assist
- offene-fragen
- referenzen-quellen
- naechste-schritte
updated: 2026-11-10 23
---

<a id="executive-summary"></a>

## Executive Summary [executive-summary]

Bei polyrepo-Architekturen (wie `/home/flow/dev_flow/`) entstehen drei orthogonale Probleme:

1. **Git Orchestration** - Mehrere Repos gleichzeitig pullen/pushen/branchen
2. **Service Orchestration** - Deployment & Start/Stop/Restart Services
3. **Configuration Management** - Welche Config gehört wohin?

Bestehende Lösungen: **stackctl** (Deployment-fokussiert) und **server_management** (Prozess-fokussiert).

**Kernfrage:** Sind diese zwei Tools oder sollten sie eins sein?

---

<a id="1-bestehende-implementierungen"></a>

## 1. Bestehende Implementierungen [bestehende-implementierungen]

### stackctl (Bestehend in /home/flow/dev_flow/stackctl)

**Zweck:** Full-Stack Orchestration mit Git-Sync + Build + Deployment

**Git-Fähigkeiten:**
```bash
stackctl clone                  # Fehlende Repos klonen (mit siblings in pyproject.toml)
stackctl checkout <branch>      # Alle Repos auf Branch umstellen
stackctl pull                   # FF-only auf aktuellem Branch
stackctl push                   # Push saubere Repos
stackctl repo-status            # Git-Status aller Repos anzeigen
```

**Deployment-Fähigkeiten:**
```bash
stackctl spec                   # Konfiguration anzeigen
stackctl start [service]        # Start local/minikube/k8s
stackctl stop [service]         # Stop
stackctl restart [service]      # Restart
stackctl status                 # Stack-Status anzeigen
stackctl -t minikube start      # Images bauen, deployen
stackctl -t k8s start           # Push zu Registry + Deploy
stackctl logs [-f] [service]    # Service-Logs streamen
```

**Konfiguration:** Stack-Spec in `stack.yaml` oder via config_loader (ki_core:load_config)

```yaml
stack:
  name: demo
  target: local                 # Default execution target
  services:
    api:
      kind: process             # oder: compose, external
      command: ["python", "app.py"]
      port: 8090
      env: {DB_HOST: "localhost"}
      k8s:
        expose: true            # NodePort (minikube) oder Ingress
        volumes: {data: {mount: /data, size: 20Gi}}
    db:
      kind: compose
      compose: {dir: deploy/postgres, service: postgres}
      port: 5432
  k8s:
    namespace: demo
    images: {app: {dockerfile: deploy/docker/Dockerfile}}
    registry: registry.example.com/team  # Für -t k8s
    ingress: {host: demo.example.com}
```

**Besonderheiten:**
- `kind: compose` vs `kind: process` vs `kind: external`
- Ziel-Overlays für Unterschiede: `targets: {minikube: {...}, k8s: {...}}`
- Platzhalter: `{target} {python} {root} {host} {port} {services[x].port}`
- Multi-Repo Build-Context im Dockerfile
- Image-Tagging per Build-ID für Auto-Rollout

---

### server_management (Neu in ki-core, Sept 2026)

**Zweck:** Dev-time Process Supervision für einzelne Services

**CLI-Fähigkeiten (neu):**
```bash
kicli-assist server status                          # Zeige laufende Prozesse
kicli-assist server start --service kicli-backend   # Start einzelner Service
kicli-assist server stop --service kicli-backend    # Stop
kicli-assist server restart --service kicli-backend # Restart
```

**Konfiguration:** In `ki.yaml` unter `services:`

```yaml
services:
  kicli-backend:
    command: ["python", "app.py"]
    cwd: /home/flow/dev_flow/kicli-code-assist
    port: 8000
    pidfile: ~/.kicli/services/kicli-backend.pid
  frontend:
    command: ["npm", "start"]
    cwd: /home/flow/dev_flow/kicli-code-assist/web
    port: 3000
    pidfile: ~/.kicli/services/frontend.pid
```

**Besonderheiten:**
- ServiceController mit subprocess + signal handling
- Graceful shutdown: SIGTERM (5s) → SIGKILL
- PID-Datei Tracking & Orphan-Detection
- Service-Registry in `~/.kicli/services.json` (JSON)
- Auto-Registrierung beim Start

---

<a id="2-das-overlap-problem"></a>

## 2. Das Overlap-Problem [das-overlap-problem]

### Scenario 1: Ich bin Developer
```bash
# Option A - Mit stackctl:
stackctl -t local start api        # Startet Stack-Service "api"

# Option B - Mit server_management:
kicli-assist server start --service kicli-backend
```
**Frage:** Welcher Befehl ist richtig? Beide?

### Scenario 2: Ich will Konfiguration ändern
```yaml
# stack.yaml:
stack:
  services:
    api: {kind: process, command: [...]}

# vs. ki.yaml:
services:
  kicli-backend:
    command: [...]
```
**Frage:** Wo definiere ich neue Services?

### Scenario 3: Multi-Repo Deployment
```bash
# stackctl:
stackctl clone               # Clone ki-core, yaml-cfg, ki-knowledge
stackctl checkout devel      # Alle auf devel
stackctl -t local start      # Start alle Services

# server_management kann das nicht!
# Es ist project-local (nur kicli-assist Services)
```

---

<a id="3-best-practice-patterns-forschung"></a>

## 3. Best Practice Patterns (Forschung) [best-practice-patterns-forschung]

### Pattern 1: Google Polyrepo (CitC)
- Zentrale Konfiguration pro Service
- CLI Tool orchestriert lokal + remote
- Build & Deployment getrennt
- Service-Dependencies in Konfiguration

### Pattern 2: Meta Monorepo (Plural)
- Single Git Repo mit Workspaces
- Unified Build System (Buck/Bazel)
- Dependency Graph zwischen Services
- Änderungen triggern Auto-Tests

### Pattern 3: Node.js Polyrepo (npm workspaces)
```bash
npm install            # Alle workspaces installieren
npm run build -ws      # Alle bauen
```
- Zentrales package.json mit workspace-array
- Shared tooling via root Konfiguration

### Pattern 4: Rust Workspace
```rust
// Cargo.toml
[workspace]
members = ["api", "worker", "cli"]
```
- Einheitliches Build System
- Shared dependencies
- `cargo build -p api` für einzelne Crate

### Pattern 5: systemd / Supervisor
```ini
[program:api]
command=/usr/bin/python app.py
autostart=true
autorestart=true
```
- Local Process Supervisor
- Config-getrieben
- Health Checks & Auto-Restart

---

<a id="4-architektur-optionen"></a>

## 4. Architektur-Optionen [architektur-optionen]

### Option A: Alles in stackctl (Consolidation)

**Vorteile:**
- Ein Tool für alles (Git + Build + Deploy + Process Control)
- Einheitliche Konfiguration (nur stack.yaml)
- Single Source of Truth

**Nachteile:**
- stackctl wird sehr groß & komplex
- Process Control ist orthogonal zu Deployment
- Nicht alle Projekte brauchen Deployment (nur Process Control)

**Implementierung:**
```bash
# stackctl würde auch process-level Supervision können:
stackctl -t local logs api            # Logs streamen
stackctl -t local health-check api    # Health-Check
stackctl -t local env api             # Environment zeigen
```

---

### Option B: server_management als stackctl-Adapter

**Vorteile:**
- Klare Verantwortung: stackctl = Deployment, server_management = Process Level
- Kleine, fokussierte Tools

**Nachteile:**
- Duplikate: Konfiguration in stack.yaml + ki.yaml
- Integration komplex (Fragen: "Wer startet zuerst?")

**Implementierung:**
```python
# ki-core/server_management/adapters/stackctl.py
class StackctlAdapter:
    def start(self, service_name: str):
        """Rufe stackctl -t local start auf"""
        subprocess.run(["stackctl", "-t", "local", "start", service_name])
```

---

### Option C: Saubere Layer-Trennung (EMPFOHLEN)

**Struktur:**

```
┌─────────────────────────────────────────┐
│  Layer 3: Full-Stack Orchestration      │
│  (stackctl -t minikube/k8s)             │
├─────────────────────────────────────────┤
│  Layer 2: Local Deployment              │
│  (stackctl -t local)                    │
├─────────────────────────────────────────┤
│  Layer 1: Process Supervision           │
│  (kicli-assist server / systemd)        │
├─────────────────────────────────────────┤
│  Foundation: Configuration              │
│  (ki.yaml + stack.yaml)                 │
└─────────────────────────────────────────┘
```

**Layer 1: Process Supervision** (server_management)
- Startet/Stoppt einzelne Prozesse
- Konfiguration: ki.yaml `services:`
- Zielgruppe: Developer (Debug, Live-Reload)
- Nicht automatisch; manuell aufgerufen

**Layer 2: Local Deployment** (stackctl -t local)
- Startet alle Services einer Spec
- Konfiguration: stack.yaml
- Zielgruppe: Developer (Full-Stack lokal)
- Orchestriert: Git-Fetch + Build + Start

**Layer 3: Cloud Deployment** (stackctl -t minikube/k8s)
- Builds Docker Images
- Push zu Registry
- Kubernetes/Helm Deployment
- Zielgruppe: Ops (Production Rollout)

**Trennung Konfiguration:**

| Aspekt | ki.yaml | stack.yaml |
|--------|---------|-----------|
| **Zweck** | App Runtime Config | Deployment Spec |
| **Ownership** | App Team | DevOps Team |
| **Services** | Lokal verfügbare Services | Zu deployende Stack-Services |
| **Env-Variablen** | App Config (Secrets) | Build/Deploy-Zeit Config |
| **Zielspezifisch** | Nein | Ja (local, minikube, k8s) |

**Regeln:**
1. `ki.yaml` ist **Runtime Config** für die App
2. `stack.yaml` ist **Deployment Spec** für stackctl
3. `server_management` liest nur `ki.yaml`
4. `stackctl` liest `stack.yaml` (oder via config_loader)
5. Kein Cross-Lesen (stackctl liest nicht ki.yaml, server_management nicht stack.yaml)

---

<a id="5-empfehlung-für-kicli-code-assist"></a>

## 5. Empfehlung für kicli-code-assist [empfehlung-fuer-kicli-code-assist]

### Status Quo (nach Phase 5.5.1 Refactor)
- ✅ ki.yaml mit `services:` Sektion
- ✅ server_management CLI (start/stop/restart)
- ✅ Native Click Subcommands

### Nächste Phase: Mit stackctl Integrieren
- [ ] Generiere `stack.yaml` aus ki.yaml (oder umgekehrt)
- [ ] Teste `stackctl -t local start` auf kicli-code-assist
- [ ] Definiere welche Services in stack vs. ki gehören
- [ ] Dokumentiere Benutzer-Workflows

### Langfristig: Org-wide Pattern
- stackctl als Primary Orchestration Tool
- server_management als Dev-Helper (optional)
- Einheitliche Konfiguration Strategie

---

<a id="6-offene-fragen"></a>

## 6. Offene Fragen [offene-fragen]

1. **Konfiguration Ownership:** Wer definiert den "canonical" Service Name?
   - `stack.yaml: services: [api]` 
   - `ki.yaml: services: {kicli-backend: ...}`
   
2. **Environment Secrets:** Wie handled stackctl API Keys / Credentials?
   - Aktuell: Nur in stack.yaml Platzhalter
   - Sollte: Secret Management (Vault? Sealed Secrets?)

3. **Service Dependencies:** Muss "db" vor "api" starten?
   - stackctl hat keine `depends_on` (wie Docker Compose)
   - server_management hat keine Sequencing

4. **Health Checks:** Wie weiß stackctl ob ein Service "ready" ist?
   - Aktuell: Nur HTTP-Port-Check
   - Sollte: health_path, DB-Connectivity, etc.

5. **Logging & Monitoring:** Wer aggregiert Logs?
   - stackctl: `stackctl logs api`
   - server_management: Per PID tracking
   - Sollte: Einheitliches Log-Stream

---

<a id="7-referenzen--quellen"></a>

## 7. Referenzen & Quellen [referenzen-quellen]

### Bestehende Implementierungen
- `/home/flow/dev_flow/stackctl/README.md` - stackctl Doku
- `/home/flow/dev_flow/stackctl/src/stackctl/cli.py` - CLI Subcommands
- `/home/flow/dev_flow/ki-core/src/ki_core/server_management/` - server_management Modul
- `/home/flow/dev_flow/kicli-code-assist/ki.yaml` - Current Config

### Best Practice
- Google Workspaces / CitC
- Facebook Plural
- Kubernetes / Helm Patterns
- systemd Service Management

### Verwandte Docs
- `/home/flow/dev_doc/doc/yaml_config_administration.md` - YAML Config Best Practices
- `/home/flow/dev_doc/doc/cli-management/archive/CLI_ARCHITECTURE_ROADMAP.md` - CLI Architecture (ki-core)

---

<a id="8-nächste-schritte"></a>

## 8. Nächste Schritte [naechste-schritte]

1. **Review:** Nimm diese Study und entscheide: Option A, B, oder C?
2. **Entscheidung:** Kommuniziere Architektur-Plan
3. **Implementierung:** Refactor abhängig von Entscheidung
4. **Integration Testing:** Test stackctl + server_management zusammen
5. **Documentation:** Update User Guides & Architecture Docs