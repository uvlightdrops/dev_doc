---
title: "Layout-Presets und Workspaces"
project: dev_doc
agent_project: ki-ecosystem
scope: architecture
purpose: "Vergleicht Frameworkmuster für responsive Layouts, Perspektiven und gespeicherte Arbeitsbereiche und bewertet eine optionale Widgetkit-Erweiterung."
created: "2026-11-12 07"
updated: "2026-11-12 15"
update_reason: "CSS-Dateistruktur und schrittweisen Einstieg für wenige Grundlayouts konkretisiert"
status: active
relations:
  - target: README.md
    type: parent
    reason: Navigation der gemeinsamen Architekturthemen
---

# Layout-Presets und Workspaces

**Ergebnis:** Es gibt keinen einheitlichen branchenweiten
„Layout-Preset“-Mechanismus. Wiederkehrend ist die Trennung von
Anordnungsmechanik, benannter Vorgabe und gespeichertem Benutzerzustand.
Workflowregeln können darauf aufbauen, bleiben aber eine eigene Verantwortung.

Die folgenden Frameworkbefunde stammen aus offizieller Dokumentation,
abgerufen am 12. November 2026. Sie sind eine repräsentative Auswahl,
kein vollständiger Marktvergleich oder praktischer Bibliotheksbenchmark.
Die Empfehlungen und der Widgetkit-Vorschlag sind eigene Ableitungen;
die vorgeschlagene Erweiterung ist nicht implementiert.

## Begriffe und Ebenen [layout-ebenen]

```yaml block-meta
updated: "2026-11-12 07"
status: active
relations:
  - target: "README.md#architecture-index"
    type: parent
    reason: Navigation zum Frameworkvergleich und Widgetkit-Vorschlag
```

| Begriff | Verantwortung | Nicht automatisch enthalten |
|---|---|---|
| Responsives Layout | Größen, Umbruch und Anordnung bei verfügbarem Platz | Benannte, gespeicherte Benutzerlayouts |
| Layout-Engine | Grid, Splits, Tabs, Docking und deren Interaktion | Fachlicher Workflow, Benutzerrechte |
| Preset / Perspektive | Benannte Anfangsanordnung für einen Zweck | Momentaufnahme aller offenen Inhalte |
| Layout-Snapshot | Konkreter gespeicherter Darstellungszustand | Versionierter Produktdefault |
| Workspace / Sitzung | Kontext, offene Ressourcen und gegebenenfalls Layout und Wiederaufnahmezustand | Einheitliche Bedeutung über Produkte hinweg |
| Workflow-Policy | Erlaubte Aktionen, Pflichtbereiche und Übergänge | Layoutberechnung und Rendering |

Ein Preset kann beispielsweise „Vergleichen“ heißen und zwei
Dokumentbereiche vorsehen. Der Snapshot hält ihre Größen und den aktiven
Tab fest. Welche Dokumente verglichen werden und ob eine Übernahme erlaubt
ist, bestimmt die Fachanwendung. Ein ausgeblendetes Werkzeug ist weder
beendet noch automatisch von seinen Berechtigungen entbunden.

## Muster großer Frameworks [framework-vergleich]

```yaml block-meta
updated: "2026-11-12 07"
status: active
relations: []
```

| System | Belegtes Muster | Grenze / Konsequenz |
|---|---|---|
| Bootstrap 5.3 | Responsive Flexbox-Container, Zeilen und zwölf Spalten; sechs Breakpoints | Das dokumentierte Grid ist Anordnungsmechanik, kein Workspace-Speicher. |
| MUI Grid | Responsive Flexbox-Spalten mit Breakpointwerten; für Row-Spanning wird CSS Grid empfohlen | Auch ein großes Komponentenframework ersetzt damit keinen Presetkatalog oder Dockingmanager. |
| Qt 6 QMainWindow | Dock-/Toolbar-Anordnung über `saveState(version)` und `restoreState`; eindeutige `objectName`-Identitäten | Speicherung und benannte Auswahl sind zusätzliche Anwendungsaufgaben; Fenstergeometrie ist separat. |
| Eclipse Workbench | Perspektive definiert anfängliche Views, Anordnung und aufgabenbezogene Aktionen; Editoren werden zwischen Perspektiven geteilt | Perspektive ist mehr als ein Spaltenraster, aber kein neuer fachlicher Datenbestand. |
| VS Code | Verschiebbare Views, Sidebars und Editorgruppen; Positionen bleiben über Sitzungen erhalten; Reset vorhanden | Profiles bündeln zusätzlich Einstellungen und Erweiterungen: kein Synonym für einen reinen Layout-Snapshot. |
| JupyterLab | Benannte Workspaces mit UI-Zustand, Serverpersistenz, Klonen, Reset sowie Import/Export | Workspace kann Ressourcen und Pluginzustand umfassen; größerer Vertrag als ein Layoutpreset. |
| Golden Layout 2 | Docking mit Komponentenregistrierung und serialisierbarer Konfiguration; `saveLayout()` liefert aufgelöste Konfiguration | Backend, Freigaben und benannte Presets sind Hostaufgaben; Framework-Lifecycle muss gesondert integriert werden. |

### Responsive Frameworks

[Bootstrap Grid](https://getbootstrap.com/docs/5.3/layout/grid/) und
[MUI Grid](https://mui.com/material-ui/react-grid/) zeigen den kleinen,
deklarativen Ansatz: Layoutparameter beschreiben verfügbare Breite und
Verteilung, nicht den Arbeitsablauf. MUI Grid verwendet trotz seines Namens
Flexbox, nicht CSS Grid. „Grid“ ist deshalb kein präziser Vertrag für
beliebige zweidimensionale oder verschachtelte Anordnungen.

Für wenige festgelegte Arbeitsansichten reichen CSS Grid, Flexbox und
benannte Klassen häufig aus. Persistenz ist dadurch weder ausgeschlossen
noch bereits gelöst.

### Desktop- und IDE-Muster

[Qt QMainWindow](https://doc.qt.io/qt-6/qmainwindow.html#saveState)
speichert Dock-/Toolbarzustand getrennt von `saveGeometry()`. Die übergebene
Version wird beim Wiederherstellen verglichen; eine Abweichung führt zu
`false` und unverändertem Zustand, nicht zu einer automatischen Migration.
Die API verlangt eindeutige `objectName`-Werte. Ein Name wie „Recherche“
und dessen Auswahloberfläche sind eine zusätzliche Ebene über diesem Blob.

[Eclipse Perspectives](https://help.eclipse.org/latest/topic/org.eclipse.platform.doc.user/concepts/concepts-4.htm)
verbinden eine anfängliche View-Anordnung mit einer Aufgabe, etwa Entwicklung
oder Debugging. Benutzer können eigene Perspektiven speichern; die
Editoren werden zwischen Perspektiven eines Fensters geteilt. Das ist
ein besonders passendes Vorbild für Workflowlayouts ohne Inhaltsduplikation.

[VS Code Custom Layout](https://code.visualstudio.com/docs/configure/custom-layout)
bietet das Verschieben von Views, gespeicherte Positionen und
`View: Reset View Locations`.
[Profiles](https://code.visualstudio.com/docs/configure/profiles) bündeln
darüber hinaus Anpassungen wie Einstellungen und Erweiterungen.
Daraus folgt keine pauschale Zusage, dass ein exportiertes Profil jeden
flüchtigen Editor-/Sitzungszustand als Layout reproduziert.

### Web-Workspaces und Docking

[JupyterLab Workspaces](https://jupyterlab.readthedocs.io/en/stable/user/workspaces.html)
speichern UI-Zustand serverseitig. Ihre JSON-Dateien enthalten `data` und
`metadata`; Plugins und der Layout-Restorer tragen zur Wiederaufnahme bei.
Darunter ist [Lumino DockPanel](https://lumino.readthedocs.io/en/latest/api/classes/widgets.DockPanel-1.html)
ein flexibler Dockingbaustein. Eine Dockingfläche und ein wiederaufnehmbarer
Workspace sind damit unterschiedliche Integrationsschichten.

[Golden Layout 2](https://golden-layout.github.io/golden-layout/version-2/)
unterscheidet Eingabekonfiguration und vollständig aufgelöste Konfiguration.
Für Persistenz empfiehlt die Dokumentation `LayoutManager.saveLayout()`
und beim Laden `LayoutConfig.fromResolved()`. Komponentenregistrierung
ist von Layoutverwaltung getrennt. Version 2 hat die frühere direkte
React-Unterstützung entfernt und verweist dafür auf FlexLayout.
Eine beliebige Dockingbibliothek lässt sich daher nicht allein aufgrund
ihres JSON-Formats zustandsneutral in jedes UI-Framework einsetzen.

## Abgeleitete Best Practices [layout-best-practices]

```yaml block-meta
updated: "2026-11-12 15"
status: active
relations: []
```

Die folgenden Punkte sind Empfehlungen, keine gemeinsame API-Zusage
aller verglichenen Systeme.

### Definition, Auflösung und Darstellung trennen

Ein Produkt liefert benannte, versionierte Defaults. Ein Resolver kombiniert
diese mit erlaubten Benutzerabweichungen, Fähigkeiten und Platzangebot.
Ein Renderer setzt das Ergebnis um. Die Fachanwendung bestimmt zulässige
Panels und Aktionen. Layoutdaten enthalten keine ausführbaren Bedingungen,
beliebigen Importpfade oder vom Client behaupteten Berechtigungen.

Eine sinnvolle Auflösungsreihenfolge ist Produktdefault → freigegebene
Teamvorgabe → persönliche Abweichung → flüchtige Anpassung.
Rechte und harte Mindestbedingungen werden danach weiterhin durchgesetzt.
Diese Reihenfolge ist eine Produktentscheidung, keine allgemeine Norm.

### Wenige Grundlayouts mit gemeinsamem CSS

Für ungefähr fünf grundsätzlich unterschiedliche Layouts empfiehlt sich
eine gemeinsame Basis plus je eine kleine Layoutdatei, statt einer Datei
für jede Kombination aus Layout und Workflowzustand:

| Ebene | Inhalt |
|---|---|
| Basis-CSS | Gemeinsame Gestaltung, Abstände, Panels, Formulare und Werkzeugleisten |
| Layout-CSS je Grundlayout | Benannte Grid-Bereiche, Spaltengewichte, Mindestgrößen und responsive Regeln |
| Zustandsregeln im Layout | Anpassungen für geöffnete Werkzeuge, verborgene Vorschau oder Navigation |
| Gemeinsame JavaScript-Steuerung | Presetauswahl und ausdrücklich definierte Workflowübergänge |

CSS Grid eignet sich für die großen zweidimensionalen Arbeitsbereiche,
Flexbox für Werkzeugleisten und Buttonreihen. Ein Attribut wie
`data-layout="vergleich"` wählt das Grundlayout; eine unabhängige Klasse
wie `ai-open` beschreibt den momentanen Zustand. CSS Custom Properties
können gemeinsame Größen und veränderliche Spaltengewichte ausdrücken.
Ein Workflowübergang setzt die passenden Zustände, kopiert aber weder
Editorlogik noch Templates und löst durch den Layoutwechsel allein keine
fachlichen Aktionen aus.

Die wenigen Layoutdateien können gemeinsam geladen oder zu einem Stylesheet
gebündelt werden. Eindeutig auf das jeweilige Layout begrenzte Selektoren
aktivieren dessen Regeln; ein dynamischer Austausch des Stylesheet-Links
ist dafür nicht nötig. Das vermeidet zusätzliche Ladephasen beim Wechsel.
Gemeinsame Komponentenregeln bleiben in der Basis, damit die Varianten
nicht durch Kopien auseinanderlaufen.

Der schrittweise Einstieg ist: vorhandene Gestaltung von Anordnungsregeln
trennen, bestehende Ansichten als erste benannte Presets abbilden und
weitere Grundlayouts ergänzen. DOM-Instanzen und fachlicher Zustand bleiben
gemeinsam. Bei diesem Umfang sind etwa fünf kleine Layoutdateien plus
Basis plausibel; zwölf bis fünfzehn Dateien sind keine notwendige Folge der
Workflowkombinationen. Entscheidend ist die Zahl wirklich unterschiedlicher
Anordnungen, nicht die Zahl aller Zustandskombinationen.

### Identität und Zustand erhalten

Widgettyp, konkrete Instanz, Layoutknoten und Preset brauchen unterscheidbare
Identitäten. Zwei Ansichten desselben Widgettyps dürfen getrennte Instanzen
sein. Label und Listenposition eignen sich nicht als stabile IDs.

Layoutwechsel dürfen Textpuffer, Undo-Verlauf, Auswahl, Fokus und laufende
Operationen nicht beiläufig zurücksetzen. In React hängt Zustand von Typ
und Position im Renderbaum ab; ein gleicher `key` ist keine globale
Zustandsgarantie über Elternwechsel hinweg
([React: Preserving and Resetting State](https://react.dev/learn/preserving-and-resetting-state)).
Möglich sind stabile Mountpunkte oder explizit außerhalb der Panels
verwalteter Zustand. Wiederherstellung benötigt einen eigenen Vertrag,
nicht nur das erneute Laden der Layoutkonfiguration.

### Responsivität und Zugänglichkeit einplanen

Semantische Bereiche, relative Gewichte und Mindestgrößen sind portabler
als ein gespeichertes Desktop-Pixelbild. Bei kleinen Flächen darf ein
Renderer Splits stapeln oder Nebenbereiche zugänglich einklappen.
Diese temporäre Projektion soll den gespeicherten Desktopzustand nicht
ungefragt überschreiben.

[Container Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Containment/Container_queries)
eignen sich für Komponenten, deren Platz nicht allein vom Viewport abhängt.
CSS-Umordnung verändert nicht automatisch Lese- und Fokusreihenfolge
([MDN Grid Accessibility](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Grid_layout/Accessibility)).
Ein Preset muss deshalb auch eine schlüssige Tastaturbedienung besitzen.

Bei veränderbaren Splittern sind Tastaturaktionen, zugänglicher Name,
Orientierung und Wertebereich zu berücksichtigen. Das
[WAI-ARIA Window Splitter Pattern](https://www.w3.org/WAI/ARIA/apg/patterns/windowsplitter/)
beschreibt dazu `separator` und Pfeiltasten; die Seite kennzeichnet ihre
Review-/Beispielarbeit selbst als noch nicht abgeschlossen. Sie ersetzt
keine reale Prüfung mit Tastatur und assistiven Technologien.

### Speicherung als eigenen Vertrag behandeln

Schema-Version, Preset-Version und Speicherrevision lösen verschiedene
Probleme: Datenformat, Entwicklung des Defaults und konkurrierende Saves.
Eine atomare Transaktion verhindert allein kein verlorenes Update aus
zwei Browserfenstern. Dafür braucht es beispielsweise eine erwartete
Revision mit sichtbarem Konflikt statt stiller Überschreibung.

„Kein persönlicher Zustand“, „absichtlich leeres Layout“ und „ungültiger
Zustand“ bleiben unterscheidbar. Reset entfernt die persönliche Abweichung;
„Als neues Preset speichern“ legt dagegen eine neue benannte Vorgabe an.
Beide Aktionen dürfen weder Dokumente noch fachliche Eingaben löschen.

Imports werden auf erlaubte IDs, Knotenarten, Größen, Tiefe und Anzahl
begrenzt; entfernte Panels und Migrationen werden sichtbar erklärt.
Bei inkompatiblen Daten bleibt der alte Snapshot für Wiederherstellung
erhalten. Layoutdaten können Ressourcenpfade enthalten und benötigen
entsprechende Export- und Freigabegrenzen.

Qt warnt ausdrücklich vor `restoreState()` mit Daten unbekannter oder
nicht vertrauenswürdiger Herkunft
([Qt Security Considerations](https://doc.qt.io/qt-6/qmainwindow.html#security-considerations)).
Serialisierbar bedeutet weder sicher importierbar noch engineübergreifend
kompatibel.

## Widgetkit: Bestand und Erweiterung [widgetkit-erweiterung]

```yaml block-meta
updated: "2026-11-12 07"
status: active
relations:
  - target: ../../../dev_flow/ki-knowledge-next/doc/architecture/ui/EDITOR-UND-KI.md#layout-presets
    type: reference
    reason: Produktspezifische Anwendung auf den Next-Editor und dessen Workflow
```

### Vorhandener Anschluss

Der lokale Quellstand wurde am 12. November 2026 geprüft:
Widgetkit HEAD `15e50c6`, Widgetkit-Django HEAD `e4c6342`.
Diese Angabe bezeichnet den untersuchten Stand, keine Zusage über künftige
Releases.

| Paket / Host | Bereits vorhanden | Für generische Presets zusätzlich nötig |
|---|---|---|
| [Widgetkit](../../../dev_flow/widgetkit/README.md) | Frameworkneutrale Widgetmetadaten, Payload-/Adaptervertrag, Jinja-Rendering | Presetdefinition, Layoutauflösung und expliziter Lifecyclevertrag |
| [Widgetkit-Django](../../../dev_flow/widgetkit-django/docs/host-integration.md) | Registry, Platzierungen, LayoutStore, Builderaktionen, fehlend/leer unterscheidbar | Benannter/versionierter Katalog, Revisionskonflikte, gegebenenfalls Gruppen |
| [Legacy-Host](../../../dev_flow/ki-knowledge/doc/architecture/DJANGO-WAGTAIL-WIDGETKIT.md#widgetkit-integration) | Hostmodelle, Bereich-/Nutzer-/Domänenscope, persönliche/gemeinsame Defaults | Keine automatische Vorlage für beliebige Editorzustände |

Die Verträge sind noch nicht deckungsgleich: `WidgetSpec` im Core enthält
nicht dieselben Felder wie das Django-Protokoll `WidgetMetadata`
(`min_w`, `resizable`). Der bestehende
[LayoutStore](../../../dev_flow/widgetkit-django/widgetkit_django/layout_store.py)
hat keinen Parameter für eine erwartete Speicherrevision.
Die [Grid-CSS](../../../dev_flow/widgetkit-django/widgetkit_django/static/widgetkit_django/css/layout-grid.css)
nutzt Breiten-Spans; gespeicherte `x`, `y`, `h` ergeben damit noch keine
frei positionierte Dockingoberfläche.

### Empfohlene Paketgrenze

**Widgetkit lässt sich sinnvoll erweitern, sollte aber nicht automatisch
zu einem universellen Dockingframework werden.**

| Schicht | Mögliche Verantwortung |
|---|---|
| Widgetkit Core | Neutrale Preset-/Instanzdefinition, Schema und Validierung, deterministische Auflösung; keine Django-Modelle und kein Browserzwang |
| Widgetkit-Django | Optionaler Persistenz-/Katalogadapter, erwartete Revision und Konfliktergebnis; Integration in Hostrechte und CSRF |
| Clientadapter | CSS-Grid-/Split-/Docking-Rendering, Resize/Fokus, barrierefreie Bedienung, Lifecycleereignisse |
| Hostanwendung | Workflow, Pflichtpanels, verfügbare Fähigkeiten, fachliche Zustände, Nutzer-/Teamscope und Freigaben |

Der Core sollte nur Mechanik aufnehmen, die tatsächlich mindestens zwei
Hosts gemeinsam benötigen. Eine CSS-Grid-Implementierung muss kein Docking
unterstützen; ein Dockingadapter darf zusätzliche Fähigkeiten anbieten.
Nicht unterstützte Knoten werden ausdrücklich abgelehnt oder über eine
deklarierte Ersatzdarstellung behandelt, nicht still ignoriert.

### Kleiner möglicher Datenvertrag

Folgende Felder sind eine Entwurfsskizze, keine bestehende Widgetkit-API:

| Objekt | Wesentliche Felder / Bedeutung |
|---|---|
| `PresetDefinition` | `preset_id`, `preset_version`, Label, benötigte Rendererfähigkeiten, Standardlayout |
| `PanelInstance` | `instance_id`, `widget_type`, validierte Darstellungsoptionen; fachlicher Zustand separat |
| `LayoutDefinition` | `schema_version`, semantische Bereiche oder begrenzter Baum aus Panel, Split und Tabs |
| `LayoutOverride` | Bezug auf Preset und Version, erlaubte Abweichungen, eindeutiger Scope |
| `StoredLayout` | Aufgelöster beziehungsweise überschreibender Zustand nach festgelegter Speicherstrategie, `revision` |
| `ResolutionResult` | Effektives Layout sowie sichtbare Diagnosen für Migrationen, fehlende Panels und Einschränkungen |

Nicht von Beginn an beide Speichermodelle unterstützen: Ein vollständiger
Snapshot ist einfach wiederherzustellen, übernimmt aber neue Defaults
nicht von selbst. Ein Override kann Änderungen am Default mitnehmen,
braucht dafür klare Merge- und Migrationsregeln. Für wenige kuratierte
Presets reichen erlaubte Abweichungen wie Sichtbarkeit und Spaltengewichte.
Ein beliebiger JSON-Patch-Vertrag wäre zunächst unnötig komplex.

Ein optionaler erweiterter Store kann den bestehenden Vertrag adaptieren,
ohne alte Dashboardhosts sofort umzubauen. Neue Zusagen wie konkurrierendes
Speichern dürfen dabei nicht nur im Adapter behauptet werden: Sie brauchen
entsprechende Backendunterstützung. Mehrfachinstanzen und Topologiebäume
lassen sich nicht verlustfrei als heutige eindeutige Widgetplatzierungen
ausgeben; dafür ist eine explizite spätere Schemaerweiterung nötig.

### Einführung und Entscheidungstore

1. Benannte Presets mit stabilen Panels in zwei realen Hosts erproben.
   Fachzustand und Layout strikt getrennt halten.
2. Gemeinsamen kleinen Vertrag extrahieren: Katalog, Identitäten,
   erlaubte Abweichungen und deterministische Auflösung.
3. Persistenz bei Bedarf ergänzen: Reset, Migration, persönliche/gemeinsame
   Scopes, Rechte und Revisionskonflikte.
4. Erst bei belegtem Bedarf Split-/Tabtopologie und einen austauschbaren
   Dockingadapter aufnehmen.

Vor Paketarbeit offen zu entscheiden sind Snapshot oder Override,
Mehrfachinstanzen, gemeinsame Freigaben, Geräte-/Arbeitsflächenscope und
der tatsächlich benötigte Umfang von Docking. Das sind Produkt- und
Vertragsentscheidungen, keine durch ein CSS-Framework beantworteten Fragen.

## Abnahme und Auswahlhilfe [abnahme-und-auswahl]

```yaml block-meta
updated: "2026-11-12 07"
status: active
relations: []
```

| Bedarf | Kleinste passende Lösung |
|---|---|
| Zwei bis fünf feste Anordnungen | Benannte CSS-Layouts mit getrenntem Zustand |
| Persönliche Reihenfolge und Breite von Dashboardkarten | Registry und Platzierungsstore; vorhandenes Widgetkit-Django prüfen |
| Verschachtelte Splits, Tabs und frei andockbare Werkzeuge | Dedizierte Engine plus Lifecycle-/Persistenzadapter |
| Wiederaufnahme offener Ressourcen und Pluginzustände | Eigener Workspacevertrag oberhalb des Layouts |

Eine belastbare Abnahme umfasst Layoutwechsel mit ungespeicherten Eingaben,
Undo und laufenden Operationen; Tastatur/Fokus und schmale Container;
fehlende/entfernte Panels; alte und unbekannte Schemata; Reset gegenüber
explizit leer; unberechtigte Freigaben; zwei konkurrierende Saves.
Bei Importen kommen Größen-/Tiefenlimits und Datenfreigaben hinzu.

Statische Strukturtests können Auflösung und Validierung prüfen.
Zustandserhalt, Resize und Zugänglichkeit benötigen zusätzlich Tests im
tatsächlichen Host und Browser. Die Wahl der Engine folgt diesen
Anforderungen, nicht allein einer eindrucksvollen Drag-and-drop-Demo.
