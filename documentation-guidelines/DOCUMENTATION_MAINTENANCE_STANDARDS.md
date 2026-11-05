---
title: "Documentation Maintenance Standards"
project: "KI-Ecosystem"
scope: "Cross-project guidelines"
created: 2026-11-01T23:58:00+01:00
updated: 2026-11-01T23:58:00+01:00
update_reason: "Initial standards establishment for documentation quality"
status: "active"
author: "Copilot"
---

# Documentation Maintenance Standards

## Executive Summary

Dokumentation ist Code und muss wie Code gepflegt werden. Dieses Dokument definiert Standards für:
- **YAML-Header** (Metadaten für Maschinen)
- **Inhalt-Struktur** (Lesbarkeit für Menschen)
- **Projekt-übergreifende Konsistenz**
- **Update-Tracking** (wer, wann, warum)

---

## 1. YAML-Header Standard

**ALLE .md Dateien MÜSSEN mit YAML-Header starten:**

```yaml
---
project: "kicli-code-assist"
scope: "architecture"
created: 2026-11-01 23
updated: 2026-11-01 23
update_reason: "Konkrete Änderung oder Grund für Update"
status: "active"
agent_project: "kicli-code-assist"
---
```

### Feld-Definitionen

| Feld | Format | Beschreibung | Beispiel |
|------|--------|-------------|---------|
| `project` | String | Projekt-Zugehörigkeit oder "cross-project" | "kicli-code-assist" |
| `scope` | String | Kategorie des Dokuments | "architecture, phases, tracking, guides" |
| `created` | YYYY-MM-DD HH | Erstellungsdatum + Stunde (keine Minuten, keine TZ) | "2026-11-01 23" |
| `updated` | YYYY-MM-DD HH | Letztes Update + Stunde | "2026-11-01 23" |
| `update_reason` | String | Kurze Begründung für Update | "Added Phase 6 section", "Fixed deprecated info" |
| `status` | Enum | Doc-Status | `active`, `archived`, `draft`, `deprecated` |
| `agent_project` | String | (Optional) Agent-Session/Projekt, das diesen Doc erstellt hat | "kicli-code-assist" |

### Datumsformat (WICHTIG!)

**Nur**: `YYYY-MM-DD HH` (Datum + Stunde)
- ✅ `2026-11-01 23` (Stunde, einfach)
- ❌ `2026-11-01T23:58:00+01:00` (zu komplex)
- ❌ `2026-11-01` (keine Stunde)

---

## 2. Inhalt-Struktur

### Klarheit: Aktuell vs. Vision vs. Problem

Jedes Dokument muss **explizit kennzeichnen**, welche Info welche Kategorie ist:

```markdown
## Status Quo (Aktuell, ab 2026-11-01T23:00:00+01:00)

[Beschreibung des HEUTIGEN Zustands]

## Vision (Geplant, Zielstand)

[Beschreibung des ZIELZUSTANDS]

## Known Issues & Blockers

[Probleme, die behoben werden müssen]
```

### Beispiel-Struktur

```markdown
---
title: "Agent Integration Architecture"
status: "active"
updated: 2026-11-01T23:58:00+01:00
---

# Agent Integration Architecture

## Current State (as of 2026-11-01T23:58:00+01:00)

- ✅ Phase 5.4 Complete (Autonomy 10/10)
- ✅ Git + Testing + QC working
- ❌ No dashboard integration yet
- ❌ LLM transparency logging incomplete

## Vision (Phase 6+)

- [ ] Real-time agent telemetry dashboard
- [ ] Agent reasoning trace UI
- [ ] Cost tracking per agent operation
- [ ] Integration with monitoring stack

## Known Blockers

| Blocker | Impact | Assigned | ETA |
|---------|--------|----------|-----|
| LLM call transparency | High | @uvlightdrops | 2026-11-15 |
| Dashboard rendering | Medium | - | TBD |
```

---

## 3. Projekt-übergreifende Doku

### Dezentralisierung: Was wohin?

```
/home/flow/dev_doc/
  ├─ Generic patterns & standards (ALLE Projekte)
  ├─ Admin guides (cluster, deployment, secrets)
  └─ Developer guidelines (CLI patterns, agent architecture, etc.)

/home/flow/dev_flow/PROJECT/doc/
  ├─ architecture/     → Design decisions, component models
  ├─ phases/          → Phase completion reports & summaries
  ├─ guides/          → How-to documentation
  ├─ tracking/        → Status, roadmap, open issues
  └─ archive/         → Obsolete but preserved docs
```

### Beispiel: Wo gehört was?

| Doku | Zielort | Grund |
|------|---------|-------|
| "CLI Architecture Patterns" | `/dev_doc/CLI_ARCHITECTURE_ROADMAP.md` | Alle Projekte implementieren das |
| "kicli-assist Phase 5.4 Complete" | `kicli-code-assist/doc/phases/PHASE_5_4_SUMMARY.md` | Projekt-spezifisch |
| "Multi-Repo Orchestration Study" | `/dev_doc/MULTI_REPO_ORCHESTRATION_STUDY.md` | Betrifft stackctl + ki-ecosystem |
| "Agent Autonomy Architecture" | `kicli-code-assist/doc/architecture/` + Link in `/dev_doc/` | Core architektur projekt-spezifisch, aber Referenz in dev_doc |

### Linking & Referencing

**Dev_doc** sollte Index-Dateien haben, die auf projekt-spezifische Docs verweisen:

```markdown
# Agent Architecture Overview

See detailed implementations:
- [kicli-code-assist: Agent Integration Architecture](../dev_flow/kicli-code-assist/doc/architecture/AGENT_ARCHITECTURE.md)
- [ki-knowledge: Coordinator Implementation](../dev_flow/ki-knowledge/doc/architecture/COORDINATOR.md)
- [Shared Patterns: CLI Design](./CLI_ARCHITECTURE_ROADMAP.md)
```

---

## 4. Update Workflow

### Wenn du eine Doku anfasst:

1. **Öffne die Datei**
2. **Update den YAML-Header:**
   ```yaml
   updated: 2026-11-01T23:58:00+01:00
   update_reason: "Konkrete Änderung"
   ```
3. **Editiere Inhalte**
4. **Commit mit Nachricht:** `docs: update X - [reason]`

### Beispiel-Commits:

```bash
git commit -m "docs: update PHASE_5_4_SUMMARY.md - clarify autonomy levels"
git commit -m "docs: mark CONFIG_GUIDE as deprecated - use CLI_ARCHITECTURE instead"
git commit -m "docs: add blocking issues in TRACKING doc"
```

---

## 5. Audit-Kriterien (für Code Review)

Bevor Dokumentation merged wird, checken:

- [ ] YAML-Header vorhanden mit allen Pflichtfeldern?
- [ ] Uhrzeit im Format `ISO 8601 + TZ` (mit `T` und `+01:00`)?
- [ ] Status korrekt (`active`, `draft`, `deprecated`, `archived`)?
- [ ] **Aktuell vs. Vision vs. Blockers** explizit gekennzeichnet?
- [ ] Veraltet markiert (wenn deprecated)?
- [ ] Projekt-/Scope-Feld sinnvoll gesetzt?
- [ ] Wenn cross-project: auch im `dev_doc` Index erwähnt?
- [ ] Keine hardcodierte Pfade (`/home/flow/dev_flow/...`), sondern relative oder `{PROJECT_DIR}`?

---

## 6. Current Docs Audit

### Status: `kicli-code-assist/doc/`

**Probleme erkannt:**
- ❌ YAML-Header teils fehlend (z.B. `PHASE_5_4_SUMMARY.md`)
- ❌ Uhrzeit fehlt in Timestamps (nur Datum)
- ❌ `updated` wird nicht gepflegt
- ⚠️ Manche Docs sind deprecated, aber nicht markiert
- ⚠️ Status quo vs. Vision nicht klar getrennt

**Aktionen nötig:**
1. Alle `.md` Dateien in `doc/` durchgehen
2. YAML-Header hinzufügen/korrigieren
3. `status` Feld setzen basierend auf Aktualität
4. Veraltete Docs in `archive/` verschieben + als `deprecated` markieren
5. `update_reason` für recent edits hinzufügen

---

## 7. Tools & Automation

### Script: Validate Markdown Headers

```bash
# Check all markdown files for YAML headers
python3 -c "
import os, yaml, re
from datetime import datetime

for root, dirs, files in os.walk('.'):
    for f in files:
        if f.endswith('.md'):
            path = os.path.join(root, f)
            with open(path) as fp:
                content = fp.read()
                if not content.startswith('---'):
                    print(f'❌ Missing YAML header: {path}')
                else:
                    try:
                        yaml_str = content.split('---')[1]
                        meta = yaml.safe_load(yaml_str)
                        # Check for timezone in timestamps
                        for ts_field in ['created', 'updated']:
                            if ts_field in meta and '+' not in str(meta[ts_field]):
                                print(f'⚠️  No timezone in {ts_field}: {path}')
                    except Exception as e:
                        print(f'❌ Parse error in {path}: {e}')
"
```

---

## 8. Adoption Timeline

| Phase | Aktion | Target | Deadline |
|-------|--------|--------|----------|
| **Phase 1** | Standards definieren (DIESES DOC) | All | 2026-11-01 |
| **Phase 2** | Audit & Cleanup kicli-code-assist | kicli-code-assist | 2026-11-08 |
| **Phase 3** | Audit & Cleanup ki-core, ki-knowledge | ki-* projects | 2026-11-15 |
| **Phase 4** | Automation script in CI/CD | All | 2026-11-22 |
| **Ongoing** | Beim Schreiben beachten | All | ab 2026-11-01 |

---

## References

- [Documentation Organization Guide](/home/flow/dev_doc/DOCUMENTATION_ORGANIZATION_GUIDE.md)
- [Script Organization Standards](/home/flow/dev_doc/script_organization_standards.md)
- [COPILOT_MEMORY_MAP](/home/flow/dev_doc/COPILOT_MEMORY_MAP.md)

