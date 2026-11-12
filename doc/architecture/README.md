---
title: "Gemeinsame Architekturthemen"
project: dev_doc
agent_project: ki-ecosystem
scope: navigation
purpose: "Einstieg in projektübergreifende Architekturentwürfe und Frameworkrecherche."
created: "2026-11-12 07"
updated: "2026-11-12 07"
update_reason: "Architekturübersicht mit Layoutrecherche angelegt"
status: active
relations:
  - target: ../README.md
    type: parent
    reason: Gemeinsame Wissensdokumentation
---

# Gemeinsame Architekturthemen

Die Dokumente behandeln wiederverwendbare Verträge und Integrationsmuster.
Konkrete Produktentscheidungen und Implementierungsanleitungen bleiben
in den jeweiligen Projekten.

## Architekturübersicht [architecture-index]

```yaml block-meta
updated: "2026-11-12 07"
status: active
relations:
  - target: "../README.md#knowledge-folders"
    type: parent
    reason: Verzeichnisübersicht der gemeinsamen Wissensdokumentation
  - target: "../INDEX.md#architecture"
    type: reference
    reason: Themen- und Zielgruppenindex
  - target: "LAYOUT-PRESETS-UND-WORKSPACES.md#layout-ebenen"
    type: child
    reason: Frameworkvergleich und optionale Widgetkit-Erweiterung
```

| Datei | Status | Zweck | Semantische Blöcke (Auswahl) |
|---|---|---|---|
| [Layout-Presets und Workspaces](LAYOUT-PRESETS-UND-WORKSPACES.md) | active | Frameworkrecherche und abgegrenzter Widgetkit-Vorschlag, nicht implementiert | layout-ebenen, framework-vergleich, widgetkit-erweiterung |
| [Gemeinsame Textwerkstatt](ki-ecosystem-workbench.md) | draft | Stackübergreifende UI- und Datenverträge als Architekturentwurf | product-consistency, architecture-options, recommended-structure |
| [Live-Konfiguration](live-runtime-settings.md) | active | Zuständigkeiten und Grenzen gemeinsamer Laufzeitkonfiguration | responsibilities, invariants, activation-boundaries |
