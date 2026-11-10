# KI-Ökosystem: gemeinsame Textwerkstatt statt gemeinsames Monolith-Frontend

**Status: Architekturentwurf zur Diskussion, nicht implementierter Standard.**
Ausgangspunkt sind die unterschiedlichen Stärken von ki-knowledge
(Django/Wagtail, Wissensprozesse) und kicli-code-assist
(React/TypeScript, Code-/Agenteninteraktion). Dieses Dokument formuliert
projektübergreifende Muster; die konkrete Knowledge-Werkstatt steht im
[Projektkonzept](../../dev_flow/ki-knowledge/doc/architecture/knowledge-workbench-concept.md).

Alle Dokumente dieses Entwurfs sind Markdown. Der
[Editor-Wireframe](../../../dev_flow/ki-knowledge/doc/features/knowledge-workbench-design.md)
ist ebenfalls eine Markdown-Datei, kein Prototyp mit vorgetäuschter Speicherung.
Die Links zu Projekten setzen das bekannte Nachbarverzeichnislayout voraus.

## 1. Gemeinsames Produktgefühl verlangt keinen identischen Backend-Stack

Ein Ökosystem kann einheitlich wirken, obwohl seine Anwendungen verschiedene
Serverframeworks verwenden. Gemeinsam sein sollten zunächst:

- verständliche Begriffe für Dokument, Revision, Auswahl, Vorschlag und Kontext;
- Bedienmechanik für Öffnen, Bearbeiten, Vergleichen und Annehmen;
- konsistente Fähigkeiten/Rechte und sichtbare Fehlerzustände;
- kompatible, versionierte Datenverträge und nachvollziehbare Herkunft.

Nicht zwingend gemeinsam sein müssen ORM, Jobverwaltung, Quellenimport oder
das gesamte Rendering. Ein Projektdateibaum ist kein Wissensregister;
ein Agentenauftrag ist keine redaktionelle Veröffentlichung.

Die Ebene „gemeinsam“ sollte zuerst die Schnittstelle betreffen, nicht die
Zusammenlegung aller Datenbanken und Server.

## 2. Drei plausible Entwicklungswege

| Weg | Nutzen | Kosten / Grenze | Einschätzung |
|---|---|---|---|
| Getrennte Anwendungen, kompatible Verträge | Schnell nutzbar, geringe Kopplung | Teilweise unterschiedliche Navigation | Ausgangspunkt |
| Gemeinsame React-Bausteine in bestehenden Hosts | Gleiches Editor-/Reviewgefühl, Backends bleiben fachlich passend | Build-, Paket- und Vertragsdisziplin nötig | Empfohlener nächster Entwicklungsschritt |
| Gemeinsame React-Shell über mehreren APIs | Durchgehende Navigation und Arbeitsbereiche | Identität, Routing, API-Gateway, Releases und Betrieb deutlich anspruchsvoller | Langfristige Option nach Produktbewährung |

Eine komplette Backendmigration ist kein vierter notwendiger Schritt.
Sie müsste unabhängig durch Wartungs- und Produktnutzen gerechtfertigt werden.
Ebenso sind Federation/Microfrontends kein Selbstzweck für wenige Anwendungen.

## 3. Empfohlene Struktur

```text
                    Gemeinsame Bedien- und Datenverträge
                       /                         \
             React-Editor/Reviewfläche       JSON-Schemas
                    /        \                  /      \
           Knowledge-Host    Assist-Host   Python       TypeScript
                 |               |
         Django-Service-Layer    Projekt-/Agentendienste
                 |               |
         Wissens-/Redaktions-    Projektdateien, Werkzeuge,
         und Authoring-Daten     Agentenprozesse

              Python-ki-core: Konfiguration, Provider, Basistypen
              stackctl: unabhängige Prozess-/Deployment-Lebenszyklen
```

React-Komponenten kennen keine Django-Modelle, Flask-Routen oder absoluten
Dateipfade. Hosts geben Daten, Berechtigungsfähigkeiten und Aktionen hinein.
Server vertrauen nie auf vom Client gemeldete Fähigkeiten.

Gemeinsames Starten/Stoppen durch stackctl ist davon unabhängig. Eine
Editorbibliothek sollte keinen eigenen Prozessmanager oder Konfigurationsstack
mitbringen.

## 4. Mögliche Pakete, aber erst nach einer bewährten Schnittstelle

Arbeitsnamen, ausdrücklich keine bereits beschlossenen neuen Repositories:

| Kandidat | Verantwortlich für | Nicht verantwortlich für |
|---|---|---|
| `ki-document-contracts` | Versionierte Schemas: Dokumentreferenz, Revision, Auswahl, Vorschlag, Fehler | Datenbank, Rechteentscheidung, Dateizugriff |
| `ki-workbench-ui` | React-Tabs, Editoradapter, Auswahl, Dirty-State, Diff-/Reviewpanel | Knowledge-Retrieval, Projektdateibrowser, globale Anmeldung |
| Hostadapter je Anwendung | Authentifizierte API, Speicherung, Objektrechte, Kontextbereitstellung | Kopie der gemeinsamen UI |
| Bestehendes Python-ki-core | Konfiguration, Providerabstraktion, gemeinsame Python-Basis | React-Komponenten und app-spezifische Wissenslogik |

Ein Schemaartefakt kann Python-Validierung und TypeScript-Typen speisen.
Generierte Typen ersetzen keine Laufzeitvalidierung untrusted Eingaben.
Zunächst genügt eine dokumentierte Schnittstelle im ersten Host. Ein neues
Paket braucht zwei echte Verbraucher, einen kleinen stabilen Vertrag,
Lizenzprüfung, Releaseverantwortung und Kompatibilitätstests.

Kein Repository sollte auf einen willkürlichen privaten Quellpfad eines anderen
zugreifen. Python- und npm-Paketierung bleiben ihren jeweiligen Werkzeugen
überlassen; ein gemeinsames Namensschema macht sie nicht zu einem Paket.

## 5. Was sich aus Assist wirklich wiederverwenden lässt

Der untersuchte Assist-Editor arbeitet mit React, TypeScript, Vite und Monaco.
Tabs und Diffansicht besitzen bereits gut erkennbare präsentationsnahe
Schnittstellen. Das ist wertvolle konkrete Vorarbeit.

Die gesamte App ist dagegen kein universelles Editor-SDK. Projektdateipfade,
Agentenpanels, Socket-Verbindungen und lokale Serverannahmen sind Hostlogik.
Das untersuchte Speichern ist außerdem nicht als durchgehend funktionierender
Client-/Serververtrag bestätigt. Vor einer Extraktion müssen gespeicherter
Basisstand und aktueller Puffer ausdrücklich getrennt werden.

Eine gemeinsame Editoroberfläche braucht sinngemäß:

```text
input:
  document reference, immutable base revision, current buffer
  annotations, capabilities, pending proposals

events:
  selectionChanged, bufferChanged, saveRequested
  annotationRequested, proposalAccepted, proposalRejected

host results:
  confirmed revision/version OR typed error/conflict
```

Ein `saveRequested`-Event ist keine Speicherbestätigung. Nur der Host darf den
erfolgreich bestätigten Serverstand zurückmelden. Navigation bei Dirty-State,
Konflikte und Providerfehler bleiben erstklassige Zustände.

widgetkit und widgetkit-django sind kein vorhandenes React-Designsystem.
Ihre Registry-/Layout-/Hostadapter-Ideen können nützlich bleiben, ohne sie
für diese Werkstatt durch eine völlig andere Bibliothek zu ersetzen.

## 6. Editorvergleich und belastbare Grenzen

| Engine | Dokumentmodell | Eignung für den Start | Was zusätzlich gebaut werden muss |
|---|---|---|---|
| Monaco | Textpuffer, Sprachen, Decorations | Gut bei bestehender Assist-Erfahrung und Markdown-Quelltext | Dauerhafte Annotationen, Vorschau, Host-Speichervertrag |
| CodeMirror 6 | Modularer Texteditor, Transactions, RangeSets | Sehr plausible Alternative für schlanke Textwerkstatt | Dasselbe fachliche Anker-/Revisionsmodell, eigene Integration |
| Tiptap / ProseMirror | Strukturbaum mit Nodes/Marks | Interessant für spätere Rich-Text-Autorenschaft | Markdown-Fidelity, Schemapflege, eigene Speicher-/Rechteintegration |
| Lexical | Strukturierter Editorzustand und erweiterbare Nodes | Alternative für bewusst gewähltes Rich-Text-Modell | Transformerabdeckung, Annotation-/Reviewprodukt |
| Milkdown | Markdown-orientierte Schicht auf ProseMirror | Interessant für Markdown-nahe WYSIWYG-Bedienung | Roundtripprüfung, Plugin-/Schema-/Hostintegration |

**Empfehlung:** Eine Quelltextengine zuerst. Monaco lokal erproben, weil
bereits benutzt; CodeMirror 6 wählen, wenn tatsächliche Tests bessere
Ergebnisse liefern. Nicht sofort eine zweite Engine für Notizen und eine dritte
für Quellen einführen. Rich-Text erst dann, wenn dessen Nutzen die zusätzliche
Serialisierung und Modellhoheit rechtfertigt.

### Markdown-Treue

Markdown-Quelltext ist für die ersten Etappen der führende Inhalt.
Ein Quelltexteditor vermeidet die automatische Umwandlung in einen Rich-Text-
Baum, garantiert aber nicht allein identische Bytes: Encoding, Zeilenenden und
Normalisierung gehören zum eigenen Speichervertrag.

Strukturierte Editoren parsen Markdown in ein Schema und serialisieren zurück.
Dabei können Whitespace, Referenzlinks, Kommentare, eingebettetes HTML,
Tabellen, Frontmatter und eigene Syntax verändert oder nicht dargestellt
werden. „Sieht gleich aus“ ist nicht „identischer Quelltext“.

Vor einer Rich-Text-Entscheidung daher echte Fixture-Sammlung und
Import-/Exportdiffs. Wenn künftig JSON/AST statt Markdown führen soll, ist das
eine eigene Datenmodellmigration mit bewusst normalisiertem Markdownexport,
kein harmloser Tausch des Widgets.

### Lizenz und kommerzielle Extras

Die betrachteten Kerne sind MIT-lizenziert; einzelne Erweiterungen und
Produkte müssen separat geprüft werden. Keine Gleichsetzung von Open Source,
kostenlosen Vorlagen, gehostetem Service und kommerziellen Features.

Wichtige Klarstellung: `@tiptap/markdown` existiert als MIT-Paket.
Markdownunterstützung ist deshalb **nicht generell hinter einer Bezahlschranke**.
Tiptap dokumentiert aber noch Einschränkungen seiner Markdown-Erweiterung,
unter anderem bei Kommentaren und Tabellenzellen.

Auch gemeinsames Editieren ist nicht grundsätzlich ein kostenpflichtiger
Dienst: Hocuspocus/Yjs sind von kommerziellen Collaboration-/History-/AI-
Produkten zu unterscheiden. Ob ein bestimmtes fertiges Kommentar-, History-
oder Tracked-Changes-Paket bezahlt werden muss, ist vor konkreter Auswahl zu
prüfen. Der Entwurf setzt keines dieser Produkte voraus und enthält bewusst
keine schnell veraltenden Preislisten.

### Keine unbelegten Technikversprechen

Bundlegrößen, Startzeiten, Mobile-Tauglichkeit und Barrierefreiheit sind hier
nicht vermessen. Vor Festlegung dieselben Zielgeräte, Dokumentgrößen und
Screenreaderabläufe prüfen. „Modular“ ist kein gemessener Performancewert.
Eigenes Hosting von Assets und gegebenenfalls Workern muss ohne CDN funktionieren.

## 7. Dauerhafte Textanker gehören der Anwendung

Keiner der verglichenen Editoren macht eine dekorierte Auswahl automatisch
zu einem dauerhaft korrekt verknüpften Wissensobjekt.

Ein tragfähiger Vertrag braucht:

```text
opaque document ID
immutable revision ID + content hash
named text representation + explicit offset unit
half-open source range
exact quote + prefix/suffix context
separate persistent annotation ID
explicit mapping/review status for other revisions
```

UTF-16-Editorpositionen, Unicode-Codepoints, sichtbare Grapheme und DOM-Positionen
sind nicht dasselbe. Der Engineadapter muss diese Unterschiede überbrücken.
In-Memory-Transaktionsmapping ist hilfreich, ersetzt aber keine gespeicherten,
revisionsbezogenen Zuordnungen über Reload und externe Änderungen hinweg.

Zitat-/Kontextselektoren orientieren sich am W3C Web Annotation Data Model.
Ein projektspezifisches JSON-Objekt mit ähnlichen Feldern ist nicht automatisch
vollständig standardkonform; Interoperabilität benötigt später einen expliziten
Import-/Exportadapter.

Wenn ein neues Dokument mehrere ähnliche Stellen enthält, gibt es keinen
zulässigen stillen „besten Treffer“. Historischen Bezug erhalten und neue
Zuordnung prüfen lassen. Eine falsche dauerhafte Relation ist gefährlicher
als eine sichtbar noch nicht zugeordnete Markierung.

## 8. KI-Gespräch und Vorschläge als gemeinsame Mechanik

Gemeinsame UI kann Kontextchips, Verlauf, Streamingzustand und Diff anzeigen.
Der Host entscheidet, welche Inhalte gelesen werden dürfen und wie sie
abgerufen werden. Knowledge verwendet Quellen/Retrieval; Assist verwendet
Projektdateien und Agentenkontext.

**Antwort** und **Änderungsvorschlag** sind unterschiedliche Ergebnisse.
Vorschläge referenzieren einen unveränderlichen Basisstand. Annehmen prüft
diesen Stand erneut, erzeugt eine Revision und bleibt nachvollziehbar.
Ein KI-Vorschlag erhält nicht allein durch seinen Ursprung Schreibrechte.

Es gibt keinen Grund, Revisionen und Konflikte zugunsten von CRDT zu überspringen.
Optimistische Nebenläufigkeit ist für den ersten Mehrbenutzerbetrieb ausreichend.
Echtzeit-Kollaboration verlangt später zusätzliche Rechte-, Schema-, Speicher-,
Präsenz- und Wiederanlaufkonzepte.

## 9. Authentifizierung: gemeinsame UI ist kein gemeinsames Vertrauen

Zuerst same-origin innerhalb jeder Anwendung bleiben. Ein geteiltes npm-Paket
sollte keine globale Tokenablage, keinen Benutzer-Service und keine
Cross-Origin-Freigabe erzwingen.

Eine gemeinsame Shell kann später zentralen Login, beispielsweise auf Basis
eines etablierten Identitätsprotokolls, bekommen. Dann müssen Audience,
Scopes, Domain-/Projektzuordnung, Ablauf und Abmeldung sauber definiert sein.
Benutzer-IDs zwischen Datenbanken einfach gleichzusetzen reicht nicht.

Browserdirektzugriffe auf rohe Dateisystem- oder Agentenwerkzeuge sind keine
neutrale Editor-API. Objektreferenzen, serverseitige erlaubte Aktionen und
kontrollierte Adapter begrenzen die Oberfläche.

## 10. Reihenfolge der Wiederverwendung

1. Eine vollständige manuelle Strecke im ersten Host liefern: Textstand,
   Markierung, Metadaten, Relation, Reload und Fehlerfälle.
2. Editorzustand und API-Vertrag anhand realer Nutzung bereinigen.
3. Kleine gemeinsame UI/Schemas mit einem zweiten Host tatsächlich verwenden.
4. Erst dann Pakete und Releases verantworten.
5. Einheitliche Shell nur bei erkennbarer Navigations-/Arbeitsraumfunktion
   gegenüber den getrennten Anwendungen rechtfertigen.

Kein neues Paket ohne zweiten Verbraucher. Kein neuer Server nur für eine
Auswahlaktion. Kein Backendwechsel nur weil ein Frontend React verwendet.
Ein gemeinsames Ökosystem wird durch klare Verträge größer, nicht durch
maximale gegenseitige Abhängigkeit.

## 11. Offizielle Quellen

Diese Quellen belegen Mechanismen und Lizenzgrenzen, nicht die erfolgreiche
Integration in ein konkretes Projekt:

- [Monaco-Repository und Lizenz](https://github.com/microsoft/monaco-editor).
- [CodeMirror-Systemhandbuch](https://codemirror.net/docs/guide/),
  [API: RangeSets und ChangeDesc](https://codemirror.net/docs/ref/),
  [Projekt und Lizenz](https://codemirror.net/).
- [ProseMirror: Schema, Transactions, Mapping](https://prosemirror.net/docs/guide/).
- [Tiptap: Open-Source-Editor](https://tiptap.dev/docs/editor/getting-started/overview).
- [Tiptap: Markdown und dokumentierte Grenzen](https://tiptap.dev/docs/editor/markdown).
- [Tiptap-Markdown-Paket: MIT-Lizenzdeklaration](https://github.com/ueberdosis/tiptap/blob/main/packages/markdown/package.json).
- [Tiptap Collaboration: Hocuspocus/Yjs und Produktgrenzen](https://tiptap.dev/docs/collaboration/getting-started/overview).
- [Lexical Markdown-Transformer](https://lexical.dev/docs/packages/lexical-markdown),
  [Lexical-Repository/Lizenz](https://github.com/facebook/lexical).
- [Milkdown](https://milkdown.dev/),
  [Repository/Lizenz](https://github.com/Milkdown/milkdown).
- [W3C Web Annotation Data Model](https://www.w3.org/TR/annotation-model/),
  insbesondere TextQuoteSelector und TextPositionSelector.

Versions- und Lizenzprüfung müssen vor einer konkreten Abhängigkeitsaufnahme
noch einmal an den ausgewählten Paketen erfolgen. Der Konzeptvergleich ist
keine Freigabe beliebiger künftiger Add-ons.
