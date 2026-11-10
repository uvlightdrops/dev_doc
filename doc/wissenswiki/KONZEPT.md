---
project: dev_doc
agent_project: ki-knowledge
scope: architecture
created: null
updated: "2026-11-10 20"
update_reason: "Relocate shared documentation into doc/ and update references"
status: draft
semantic_blocks:
  - intent
  - model
  - implementation
  - view-config
  - future
relations:
  - target: BEDIENUNG.md
    type: references
    reason: "Bedienung und Grenzen der vorhandenen Umsetzung"
---

# Wissenswiki: Konzept

## Gemeinsamer Arbeitsraum [intent]

Ein kleiner lokaler Arbeitsraum soll Menschen und später auch KI bei der
Entwicklung und Verknüpfung von Gedanken unterstützen. Kleine Blöcke lassen
sich nebeneinander betrachten und gerichtet verbinden. Ein Essay kann eine
geordnete Zusammenstellung solcher Blöcke sein; Beziehungen und Reihenfolge
beschreiben dabei unterschiedliche Dinge.

## Modell und Herkunft [model]

Eigene Wiki-Blöcke liegen jeweils als Markdown mit stabiler ID und
YAML-Metadaten vor. Eine Beziehung hat Ausgang, Ziel und Typ. Geordnete
Bestandteile sind davon getrennt. Dokumentationsdomänen lesen vorhandene
Markdown-Dokumente und H2-Abschnitte; sie erklären diese nicht automatisch
zu fachlichen Wissensblöcken.

Dokumentattribute werden für die Ansicht an ihre Abschnitte weitergegeben und
als `dokument_metadaten` kenntlich gemacht. Abschnitts-ID, Dateipfad und
relativer Ordner sind Anzeige- und Identitätsinformationen, keine zusätzlich
in der Quelle behaupteten fachlichen Eigenschaften.

## Vorhandene Umsetzung [implementation]

Der lokale Viewer und Arbeitsraum gehören derzeit zu ki-knowledge. Das
Startskript nutzt getrennte Verzeichnisse für Wiki-Blöcke und zusätzliche
Dokumentbeziehungen; Dokumentationsquellen bleiben lesend. Die eigenständige
Netzansicht zeigt bewegliche Blasen und gerichtete, typisierte Verbindungen.
Die sieben auswählbaren Typen und Farben orientieren sich an
[Dokumentationsvisualisierung](../documentation-guidelines/DOCUMENTATION_VISUALIZATION.md);
andere vorhandene Typen bleiben sichtbar.

Die Anwendung ist im lokalen Netz ohne Authentifizierung und Verschlüsselung
zugänglich, sofern sie an alle Schnittstellen bindet. Sie ist nur für ein
vertrauenswürdiges Netz gedacht. Die [Bedienung](BEDIENUNG.md) beschreibt
Start, Grenzen und Speicherorte.

## Ansichtskonfiguration [view-config]

Die Darstellung ist von den Wissensdaten getrennt: Einstellungen liegen je
Browser, Serveradresse und Domäne im lokalen Browserspeicher. Eine Version
im Speicherschlüssel erlaubt spätere Änderungen des Formats. Gegenwärtig:

- `color`: einheitlich oder anhand des relativen Dokumentordners. Die Farbe
  wird deterministisch aus dem Ordnernamen abgeleitet; verschiedene Ordner
  können dieselbe Farbe erhalten. Die Legende ordnet sichtbare Farben zu.
- `relation`: kein Filter oder ein vorhandener Beziehungstyp. Beim Filtern
  müssen beide Endpunkte auch die übrigen Filter erfüllen; die Auswahl wird
  aufgehoben, wenn ein ausgewählter Block nicht mehr zum Ergebnis gehört.
- `attribute` und `value`: vorhandener Attributpfad und optionaler
  Teiltext. Verschachtelte Objekte werden mit Punktpfaden dargestellt,
  Listen als JSON. Der Filter beansprucht keine fachliche Typinterpretation.

Suche und Seitenwahl sind flüchtig, Positionen ebenfalls. Diese erste
Konfiguration ist weder ein serverseitig geteiltes Profil noch eine
benutzerdefinierte Farbzuordnung. Eine spätere gemeinsame oder exportierbare
Konfiguration muss Speicherort, Konflikte und Freigabe gesondert klären.

## Offene Ideen [future]

Ein integrierter Chat könnte Titel, Stichworte oder Abstracts aufgreifen und
Textvorschläge liefern. Deren Übernahme setzt menschliche Bestätigung voraus.
Mehrere Agenten mit einstellbaren Freiheitsgraden, Herkunftsprotokoll und
prüfbaren Vorschlägen sind eine Idee, keine vorhandene Funktion.

Eine spätere Visualisierung auf einer gedachten transparenten Kugel mit
Äquatorialnetz ist ebenfalls offen. Ebenso offen sind automatische
Wissenserschließung, dauerhafte Layouts, feinere Attributfilter und die Frage,
wie Dokumentationsabschnitte bewusst in fachliches Wissen überführt werden.
