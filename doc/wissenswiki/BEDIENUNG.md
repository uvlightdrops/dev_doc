---
project: dev_doc
agent_project: ki-knowledge
scope: guide
created: null
updated: "2026-11-10 20"
update_reason: "Relocate shared documentation into doc/ and update references"
status: draft
semantic_blocks:
  - start
  - blocks
  - network
  - settings
  - storage
  - bridge
relations:
  - target: KONZEPT.md
    type: references
    reason: "Konzept, technische Grenzen und offene Ideen der Anwendung"
---

# Wissenswiki: Bedienung

Diese Anleitung beschreibt den derzeitigen Stand des lokalen Wissenswiki in
ki-knowledge. Der [Konzeptentwurf](KONZEPT.md) unterscheidet bestehende
Funktionen von Ideen.

## Start [start]

Im Projektverzeichnis von ki-knowledge:

```bash
mkdir -p ~/dev_wiki ~/dev_wiki_rel
./start-wiki
```

`http://127.0.0.1:8765/workspace/` öffnet den Arbeitsraum, über
**Wissensnetz** die animierte Ansicht. Mit `Strg+C` im Terminal beenden.
Beispiele für zusätzliche Optionen:

```bash
./start-wiki --host 127.0.0.1
./start-wiki --domain Weitere /pfad/zur/dokumentation /pfad/zu/beziehungen
```

Der Server bindet standardmäßig an alle IPv4-Schnittstellen. Aufruf im
vertrauenswürdigen lokalen Netz: `http://<IPv4-Adresse>:8765/workspace/`.
**Es gibt weder Anmeldung noch HTTPS:** Alle Geräte, die den Server erreichen,
können lesen und schreiben. Für ausschließlich lokalen Zugriff `--host
127.0.0.1` verwenden.

## Blöcke bearbeiten [blocks]

Im Arbeitsraum lassen sich links und rechts eigene Blöcke anlegen oder über
die durchsuchbare Seitenleiste öffnen. Titel sind optional. Zwischen den
Blöcken können gerichtete Beziehungen angelegt und geordnete Bestandteile
hinzugefügt werden. Sind Dateien zwischenzeitlich geändert worden, meldet
die Anwendung beim Speichern einen Konflikt.

## Wissensnetz [network]

Die Domänenlinks im Kopf wechseln zwischen eigenen Wiki-Blöcken und der
lesenden Dokumentation. Suche und Seitenwahl begrenzen die gleichzeitig
animierten Blasen auf 60; ausgewählte Blasen bleiben beim Seitenwechsel
sichtbar. Anklicken wählt Ausgang, dann Ziel; beide werden vergrößert und
links farbig umrahmt angezeigt. Eine dritte Auswahl beginnt von vorn.

Unter **Farben und Richtung** (zwischen Ausgang und Ziel) einen Typ auswählen
und mit **Beziehung anlegen** speichern. **Auswahl löschen** hebt die Auswahl
auf. Das einklappbare Widget **Blockattribute** unter dem Netz zeigt über die
ganze Breite die Attribute der zuletzt ausgewählten
Blase; `dokument_metadaten` stammen vom Dokument und gelten nicht als
eigenständig erfasste Attribute des Abschnitts. In `/workspace/graph-header/`
zeigt das Widget Ausgang und Ziel nebeneinander; links stehen die
Attributnamen ohne vorangestellte Gruppe (`dokument_metadaten.status` →
`status`); zu lange Namen werden gekürzt. Der vollständige Name erscheint beim Überfahren.

Dokumente und H2-Abschnitte sind sichtbar. Abschnitte ohne stabile Block-ID
können nicht als Ausgang oder Ziel einer neuen Beziehung gewählt werden.
Ungültige Dokumente oder nicht auflösbare Ziele werden angezeigt.

## Ansichtseinstellungen [settings]

Unter **Ansichtseinstellungen** (Variante `/workspace/graph-header/`: als
zweizeilige Filterleiste im Kopf, mit denselben gespeicherten Einstellungen;
dort ohne Ordnerlegende, Treffer als `x/y` und Zurücksetzen als **reset**):

- **Blasenfarbe:** einheitlich oder nach dem relativen Dokumentordner. Die
  Legende zeigt die Farben der aktuell sichtbaren Ordner; eigene Wiki-Blöcke
  haben keine Dokumentordnerzuordnung.
- **Ordner und Datei:** nur Blöcke aus genau einem Dokumentordner anzeigen
  (ohne Unterordner). Erst nach der Ordnerwahl listet das zweite Dropdown die
  Markdown-Dateien dieses Ordners zur weiteren Einschränkung.
- **Beziehungsfilter:** Alle Blöcke oder nur einen vorhandenen Beziehungstyp
  anzeigen. Beim Typfilter bleiben nur passende Beziehungen und ihre beiden
  Endpunkte, soweit beide auch die anderen Filter erfüllen.
- **Attribut:** ein vorhandenes Attribut auswählen; der optionale Wertfilter
  sucht ohne Beachtung der Groß-/Kleinschreibung als Teiltext. Ohne Wert
  genügt das Vorhandensein des Attributs. Listen werden als JSON durchsucht.
- **Ansicht zurücksetzen:** Filter und Farbe zurücksetzen, auch die Textsuche.

Farbmodus sowie Ordner-, Datei-, Attribut- und Beziehungsfilter werden pro Domäne und Browser im
lokalen Browserspeicher abgelegt, nicht in den Dokumenten. Textsuche und
Seitenwahl werden nicht dauerhaft gespeichert. Andere Browser oder Adressen
haben eigene Einstellungen. Ein Speicherfehler wird angezeigt.

Die eigene Seite **Einstellungen** (`/workspace/settings/`, verlinkt in
`/workspace/graph-header/`) legt unter **Zeige folgende Attribute** per
Checkbox fest, welche Attribute im Vergleich von Ausgang und Ziel erscheinen.
Maßgeblich ist der Kurzname ohne Gruppe, z. B. `project` für
`dokument_metadaten.project`. Standardmäßig ausgeblendet sind `art`, `project`
und `agent_project`. Die Auswahl wird sofort und pro Domäne im Browser
gespeichert und gilt nach dem Neuladen der Netzansicht.

## Dateien und Grenzen [storage]

`~/dev_wiki` enthält eigene Blöcke als Markdown-Dateien. `doc/` bleibt die
lesende Dokumentationsquelle. Neue Beziehungen dieser Dokumentationsdomäne
stehen in `~/dev_wiki_rel/dokument-beziehungen.json`, nicht in `doc/`.
Weitere Domänen benötigen jeweils ein eigenes, bestehendes und getrenntes
Beziehungsverzeichnis. Geänderte Pfade oder Block-IDs können gespeicherte
Beziehungen unauflösbar machen.

Blasenpositionen werden nicht gespeichert. Es gibt noch keinen Chat, keine
KI-Textgenerierung und keine Versionsgeschichte im Wiki.

## Temporäre Brücke zu ki-knowledge [bridge]

Zum Ausprobieren arbeiten beide Werkzeuge vorübergehend auf den Daten des
jeweils anderen. Nichts wird kopiert; der Rückbau ist jederzeit möglich.

**Wiki-Tool auf Django-Domänen:** `./start-wiki --ki-domains` nimmt jede
Markdown-Domäne unter `~/dev_data/ki-knowledge/<domain>/md/` als lesende
Dokumentdomäne `ki:<domain>` auf. Verlinkte Unterordner (z. B. `anthro/sstk`)
erscheinen als eigene Domäne `ki:anthro/sstk`; Quellen, die schon als Wiki
oder Dokumentation angezeigt werden, entfallen. Die meisten Django-Dateien
haben keinen YAML-Header; sie werden tolerant gelesen und ihre H2-Abschnitte
erhalten abgeleitete IDs `auto-<titel>`. Diese bleiben nur stabil, solange die
Überschrift gleich bleibt. Neue Beziehungen liegen in
`~/dev_wiki_rel_ki/<domain>/dokument-beziehungen.json` (Schrägstrich als `--`);
die Quellen bleiben unverändert. Übersicht:
`.venv/bin/python -m ki_knowledge.wiki_bridge list`.

**Django-App auf Wiki-Blöcken:**
`.venv/bin/python -m ki_knowledge.wiki_bridge link-wiki` legt
`~/dev_data/ki-knowledge/dev-wiki/md/bloecke` als Symlink auf `~/dev_wiki` an.
In Django erscheint danach die Domäne `dev-wiki` mit dem Arbeitstitel
`bloecke` (eventuell erst nach einem Domänenscan). InfoSite-Erzeugung und
KI-Verfeinerung schreiben in den Ausgabeordner, nicht in `~/dev_wiki`. Die
Datei `.wissenswiki.lock` wird dort als Dokument mitgelistet; sie nicht
importieren. Wiki und Django nicht gleichzeitig dieselben Blöcke ändern lassen.

**Rückbau:** Option `--ki-domains` weglassen, `~/dev_wiki_rel_ki` bei Bedarf
löschen, den Symlink `~/dev_data/ki-knowledge/dev-wiki/md/bloecke` entfernen
und die Domäne `dev-wiki` in Django löschen. Gelöscht werden dabei nur der
Link und Django-Daten, nicht `~/dev_wiki` (`rmtree` folgt keinen Symlinks).
