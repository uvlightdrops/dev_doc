# Gemeinsame Live-Konfiguration der Python-Webanwendungen

Stand: 2026-10-29. Geltungsbereich: ki-core, widgetkit-django,
ki-knowledge und kicli-code-assist. Keine pauschale Hot-Reload-Zusage
für andere Anwendungen oder Go-Dienste.

## Verantwortlichkeiten

| Schicht | Aufgabe |
| --- | --- |
| yaml-cfg-wizard | YAML-/Schemawerkzeuge und atomarer, revisionsgeschützter Editor |
| ki-core | Verbindliche Layer-Auflösung und frameworkneutraler `RuntimeSettings`-Dienst |
| widgetkit-django | Wiederverwendbare autorisierte Django-Oberfläche, keine projektspezifischen Konfigurationsfelder |
| ki-knowledge | Projektschema, Eigentümer-/Neustartgrenzen und Navigationsanbindung |
| kicli-code-assist | Dasselbe Kernmodell über Flask/React, ohne Django-Abhängigkeit |

Es gibt keinen zweiten GUI-spezifischen Konfigurationsspeicher.
Änderungen liegen im host-lokalen Runtime-Layer. Projektkonfiguration und
Credentials bleiben pro Projekt getrennt; gemeinsame Pakete teilen keine
Geheimnisse zwischen Anwendungen.

## Invarianten

- Felder werden aus dem zusammengeführten Schema registriert. Kein
  requestgesteuerter Dateipfad, kein unbeschränktes Roh-YAML-Schreiben.
- Snapshot und Änderung verwenden dieselbe Schichtenauflösung wie die
  Anwendung; das Entfernen eines Runtime-Overrides stellt Vererbung wieder her.
- Die Oberfläche unterscheidet gespeicherten Override und aufgelösten Wert.
  Environment/Credentials können eine Runtime-Änderung weiterhin überdecken.
- Vor dem Schreiben: Typ-/Schemavalidierung, Revisionsvergleich und
  Dateisperre; Austausch der Zieldatei atomar. Ein alter Browser darf
  neuere Änderungen nicht still verlieren lassen.
- Geheimnisse gehören weder in HTML/JSON-Snapshots noch in Fehlermeldungen,
  Formular-Defaults, Logs oder Versionskontrolle.
- Schema-definierte Zugangsdaten sind getrennt write-only editierbar, mit
  eigener Credential-Revision, explizitem Entfernen und atomarem `0600`-Schreiben
  in die projekteigene `creds.yaml`. Externe Credentials werden nicht still
  übernommen; DB-eigene Secrets bleiben beim jeweiligen Eigentümer.
- Anwendungsweit wirkende Änderungen benötigen entsprechend starke
  Autorisierung und CSRF-/Same-Origin-Schutz, nicht nur einen versteckten Link.
- DB-eigene Einstellungen werden nicht durch wirkungslose YAML-Duplikate
  administriert. Ein Host kennzeichnet solche Felder und verlinkt den Eigentümer.

## Aktivierung ist eine eigene Eigenschaft

Ein erfolgreicher Dateischreibvorgang bedeutet nicht, dass alle alten Objekte
ihre Konfiguration geändert haben. Die Anzeige nennt deshalb pro Feld die
Aktivierungsgrenze: nächster Client/Request oder Neustart.

LLM-Client-Erzeugung gehört in `ki_core.chat`. Anwendungen adaptieren ihre
aufgelöste Konfiguration; sie implementieren weder Provider-Fallbacks noch
einen zweiten Credentials-/Timeoutpfad. Laufende Requests behalten ihren
Client. Explizite Providerfehler dürfen keinen stillen Wechsel auf Mock
oder einen anderen Anbieter bewirken.

Django-Bootstrap, Ports, Datenbankverbindungen, Storage-Wechsel und
Embedding-Reindexierung sind keine gewöhnlichen Live-Formularänderungen.
Ein Konfigurationseditor soll dafür keine unsicheren globalen
Settings-Mutationen während laufender Requests vornehmen.

## Übertragbarkeit

Weitere Python-Anwendungen können denselben `RuntimeSettings`-Dienst
verwenden. Host-Authentifizierung, Projektroot und DB-/Neustartgrenzen
müssen ausdrücklich angebunden werden. Ein installiertes Paket allein
beweist noch keine Integration oder Hot-Reload-Fähigkeit.

Die Projektdokumentation enthält konkrete Bedienung und datierte
Prüfergebnisse; dieser Text beschreibt die gemeinsamen Mechanismen.
