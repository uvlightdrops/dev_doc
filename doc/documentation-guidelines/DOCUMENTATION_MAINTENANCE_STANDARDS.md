---
title: Documentation Maintenance Standards
project: KI-Ecosystem
scope: Cross-project guidelines
created: 2026-11-01 23
updated: "2026-11-11 15"
update_reason: Sachbezogene Blockinhalte, Feature-/Architekturtrennung und Dokumentationsstatus präzisiert
status: active
author: Copilot
purpose: Metadata, structure and maintenance rules for shared documentation
agent_project: ki-ecosystem
semantic_blocks:
- executive-summary
- yaml-header-standard
- block-metadaten
- inhalt-struktur
- projekt-uebergreifende-doku
- update-workflow
- audit-kriterien-fuer-code-review
- current-docs-audit
- tools-automation
- adoption-timeline
- references
---

# Documentation Maintenance Standards

<a id="executive-summary"></a>

## Executive Summary [executive-summary]

Dokumentation ist Code und muss wie Code gepflegt werden. Dieses Dokument definiert Standards für:
- **YAML-Header** (Metadaten für Maschinen)
- **Inhalt-Struktur** (Lesbarkeit für Menschen)
- **Projekt-übergreifende Konsistenz**
- **Update-Tracking** (wer, wann, warum)

---

<a id="1-yaml-header-standard"></a>

## 1. YAML-Header Standard [yaml-header-standard]

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

## Blockmetadaten [block-metadaten]

```yaml block-meta
updated: "2026-11-11 15"
status: active
relations:
  - target: "SEMANTIC_KNOWLEDGE_ORGANIZATION.md#vision-semantic-knowledge-network"
    type: reference
    reason: "Semantisches Netzwerk verwendet diesen verbindlichen Blockvertrag"
```

**Verbindlicher Standard für neue oder gezielt überarbeitete semantische
Blöcke:** Jeder Block besitzt die drei lokalen Attribute `updated`, `status`
und `relations` als sichtbaren YAML-Codeblock mit Kennzeichnung
`yaml block-meta` unmittelbar nach der H2-Überschrift. Seine stabile ID steht
nur in der Überschrift. Die Attribute stehen nicht zusätzlich im Dateiheader.

````markdown
## Quellenmodell [quellenmodell]

```yaml block-meta
updated: "2026-11-11 05"
status: active
relations:
  - target: "../features/QUELLEN.md#registrierung"
    type: reference
    reason: "Implementierter Ablauf zu diesem Modell"
```

Der Inhalt des Blocks folgt hier.
````

Die Kennzeichnung unterscheidet lokale Metadaten von normalen YAML-Beispielen.
Metadaten werden nicht in HTML-Kommentaren versteckt. Der Dateiheader enthält
nur dokumentweite Angaben; eine optionale reine `semantic_blocks`-ID-Liste
ist ein Index, keine zweite Quelle für lokale Attribute. Sie kann entfallen,
weil die IDs aus den Überschriften lesbar sind.
Block-IDs sind eindeutig innerhalb einer Datei, stabil, kebab-case und
höchstens 38 Zeichen lang. Jede echte H2-Überschrift besitzt genau einen
lokalen Metadatenblock; Überschriften innerhalb von Codebeispielen zählen nicht.

| Attribut | Verbindliche Bedeutung |
|---|---|
| `updated` | Letzte inhaltliche oder lokale Metadatenänderung dieses Blocks als zitierter String `YYYY-MM-DD HH`, ohne Minuten oder Zeitzone |
| `status` | `draft` für unvollständige oder noch nicht ausreichend geklärte Dokumentation, `active` für im angegebenen Umfang hinreichend vollständigen und gepflegten Inhalt, `archived` für historischen, nicht mehr gepflegten Inhalt |
| `relations` | Liste lokaler Beziehungen mit `target`, `type`, `reason`; ausdrücklich `[]`, wenn keine vorhanden sind |

`active` ist keine Produktionsfreigabe und kein Beleg, dass ein beschriebenes
Ziel bereits implementiert ist. Dokumentstatus und Blockstatus können sich
unterscheiden. Dokumentweite Zeitstempel, Statuswerte und Relationen bleiben
Dokumentkontext; sie werden nicht als eigene Blockwerte vererbt.

Relationsziele verwenden einen relativen Dateipfad, bei Blockbezügen ergänzt
um `#block-id`. Ein Bezug innerhalb derselben Datei verwendet `#block-id`.
Die Auflösung erfolgt relativ zur Datei, nicht zum Überschriftentext.
Standardtypen sind `depends-on`, `reference`, `parent` und `child`.
Feature und Konzept werden auf den tatsächlich betroffenen Blöcken
gegenseitig verbunden, beispielsweise `depends-on` ↔ `reference`.
Dateibeziehungen werden nur zusätzlich gesetzt, wenn auch das gesamte
Dokument diesen Bezug hat; Blockrelationen müssen nicht dupliziert werden.

Bei Änderungen werden der betroffene Block und der Dokumentzeitstempel
aktualisiert, nicht sämtliche unberührten Blöcke. Ziele, Block-IDs und
passende Rückbeziehungen sind zu prüfen.
`last_verified` oder andere Freshness-Angaben können ergänzen, ersetzen aber
weder `updated` noch `status` oder `relations`.

**Bestandspflege:** Bisherige reine ID-Listen bleiben ein Index, aber keine
vollständige Umsetzung dieses Standards. Bereits im Header gespeicherte
Blockattribute werden bei Überarbeitung zum betreffenden Abschnitt verlagert,
nicht zusätzlich kopiert. Fehlende lokale Werte werden als fehlend
ausgewiesen, nicht aus dem Dokument erfunden. Historische Änderungszeiten
dürfen nicht geschätzt werden; für tatsächlich heute überarbeitete Blöcke
gilt die aktuelle Stunde. Keine automatische Massenkonvertierung.
Diese Metadatenkonvention allein implementiert noch keine Anzeige oder
Auswertung in konsumierenden Anwendungen.

---

<a id="2-inhalt-struktur"></a>

## 2. Inhalt-Struktur [inhalt-struktur]

```yaml block-meta
updated: "2026-11-11 15"
status: active
relations: []
```

Der Inhalt eines Blocks arbeitet aus, was seine Überschrift benennt.
Dokumentation beschreibt den Gegenstand direkt, statt das Gespräch, die
Überlegungen des Agenten oder den Arbeitsverlauf nachzuerzählen. Solche
Reflexion bleibt im Dialog; Änderungsherkunft steht in Metadaten und Git.

`features/` beschreibt ausschließlich implementierte Fähigkeiten, ihre
Bedienung und tatsächlichen Grenzen. `architecture/` enthält die zugehörigen
Überlegungen, Modelle, Alternativen und Begründungen. Betroffene Feature-
und Architekturblöcke sind gegenseitig verlinkt, nicht doppelt ausgeführt.

Dokument- und Blockstatus zeigen den Stand der Dokumentation:
`draft` bedeutet unvollständig oder noch zu klären, `active` im angegebenen
Umfang hinreichend vollständig und gepflegt, `archived` historisch und nicht
mehr gepflegt. Konkrete Dokumentationslücken werden bei Bedarf benannt;
Vollständigkeitsprozente werden nicht erfunden. Auch ein Feature-Dokument
mit Status `draft` beschreibt nur bereits implementierte Funktionen.
Implementierungsgrad und Produktionsreife sind davon unabhängig.

Zukunftsideen sollen Platz behalten. Ihre dauerhafte Ablage ist vorerst offen;
sie werden klar vom implementierten Bestand unterschieden. Daraus wird
weder ein neuer Ordner noch ein Planungsprozess oder eine verbindliche
Roadmap ohne ausdrückliche Entscheidung abgeleitet.

---

<a id="3-projekt-übergreifende-doku"></a>

## 3. Projekt-übergreifende Doku [projekt-uebergreifende-doku]

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
| "CLI Architecture Patterns" | `/dev_doc/doc/cli-management/archive/CLI_ARCHITECTURE_ROADMAP.md` | Alle Projekte implementieren das |
| "kicli-assist Phase 5.4 Complete" | `kicli-code-assist/doc/phases/PHASE_5_4_SUMMARY.md` | Projekt-spezifisch |
| "Multi-Repo Orchestration Study" | `/dev_doc/doc/MULTI_REPO_ORCHESTRATION_STUDY.md` | Betrifft stackctl + ki-ecosystem |
| "Agent Autonomy Architecture" | `kicli-code-assist/doc/architecture/` + Link in `/dev_doc/` | Core architektur projekt-spezifisch, aber Referenz in dev_doc |

### Linking & Referencing

**Dev_doc** sollte Index-Dateien haben, die auf projekt-spezifische Docs verweisen:

```markdown
# Agent Architecture Overview

See detailed implementations:
- [kicli-code-assist: Agent Integration Architecture](../dev_flow/kicli-code-assist/doc/architecture/AGENT_ARCHITECTURE.md)
- [ki-knowledge: Coordinator Implementation](../dev_flow/ki-knowledge/doc/architecture/COORDINATOR.md)
- [Shared Patterns: CLI Design](../cli-management/archive/CLI_ARCHITECTURE_ROADMAP.md)
```

---

<a id="4-update-workflow"></a>

## 4. Update Workflow [update-workflow]

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

<a id="5-audit-kriterien-für-code-review"></a>

## 5. Audit-Kriterien (für Code Review) [audit-kriterien-fuer-code-review]

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

<a id="6-current-docs-audit"></a>

## 6. Current Docs Audit [current-docs-audit]

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

<a id="7-tools--automation"></a>

## 7. Tools & Automation [tools-automation]

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

<a id="8-adoption-timeline"></a>

## 8. Adoption Timeline [adoption-timeline]

| Phase | Aktion | Target | Deadline |
|-------|--------|--------|----------|
| **Phase 1** | Standards definieren (DIESES DOC) | All | 2026-11-01 |
| **Phase 2** | Audit & Cleanup kicli-code-assist | kicli-code-assist | 2026-11-08 |
| **Phase 3** | Audit & Cleanup ki-core, ki-knowledge | ki-* projects | 2026-11-15 |
| **Phase 4** | Automation script in CI/CD | All | 2026-11-22 |
| **Ongoing** | Beim Schreiben beachten | All | ab 2026-11-01 |

---

<a id="references"></a>

## References [references]

- [Documentation Organization Guide](/home/flow/dev_doc/doc/documentation-guidelines/DOCUMENTATION_ORGANIZATION_GUIDE.md)
- [Script Organization Standards](../script_organization_standards.md)
- [COPILOT_MEMORY_MAP](/home/flow/dev_doc/doc/copilot-memory/COPILOT_MEMORY_MAP.md)