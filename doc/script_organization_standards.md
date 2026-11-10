---
project: dev_doc
scope: guides
purpose: Shared organization of development and production scripts
created: 2026-11-05 11
agent_project: ki-ecosystem
status: active
semantic_blocks:
- core-philosophy
- directory-structure
- script-classes
- ai-assisted-development
updated: 2026-11-10 23
update_reason: Validate YAML metadata and align semantic blocks with section headings
---
# Best Practice: Standardisierte Skript-Anordnung & Logik (Dev vs. Prod)

Dieses Dokument beschreibt eine bewährte, moderne Architektur zur Organisation von Betriebs- und Entwicklungsskripten in Softwareprojekten. Diese Struktur ermöglicht eine nahtlose Koexistenz von direktem lokalem Betrieb (Non-Container/Non-K8s) und produktionsnahem Betrieb (Kubernetes/Minikube) bei maximalem Bedienkomfort.

---

<a id="1-die-kernphilosophie"></a>

## 1. Die Kernphilosophie [core-philosophy]

Moderne Softwareprojekte erfordern oft zwei Arten der lokalen Ausführung:
1. **Direct Local (Dev):** Ausführung direkt auf dem Host (z. B. via `go run`, `python main.py`, `npm run dev`) für schnelles Debugging und minimale Overhead-Zeiten.
2. **Containerized / Kubernetes (Prod-like):** Ausführung in einer lokalen Kubernetes-Instanz (z. B. Minikube) zur Validierung von Netzwerken, Umgebungsvariablen, Helm-Charts und Berechtigungen.

### Die goldene Regel der Skript-Architektur:
Beide Welten teilen sich dieselben logischen Befehlsnamen, sind jedoch strikt in getrennten Ordnern organisiert:
- `scripts/dev/` $\rightarrow$ Steuert den direkten, lokalen Betrieb.
- `scripts/prod/` $\rightarrow$ Steuert den containerisierten, Kubernetes-basierten Betrieb.

---

<a id="2-strukturierte-verzeichnisübersicht"></a>

## 2. Strukturierte Verzeichnisübersicht [directory-structure]

```text
scripts/
├── dev/                     # DOCKER-FREE / DIRECT HOST RUNS
│   ├── start.sh             # Startet alle Host-Dienste im Hintergrund (.tmp/*.log)
│   ├── stop.sh              # Beendet alle Hintergrundprozesse sauber via Port-PID
│   ├── diagnose.sh          # Prüft lokale Ports, Health-Endpunkte & API-Snapshots
│   ├── status.sh            # Kurzer Alias/Wrapper für diagnose.sh
│   ├── common-helpers.sh    # Gemeinsam genutzte Bash-Funktionen (Ports, Curl)
│   └── start-local.sh       # Abwärtskompatibler Wrapper (ruft start.sh auf)
│
└── prod/                    # KUBERNETES / MINIKUBE RUNS (Production-like)
    ├── start.sh             # Startet Minikube, baut Images, deployt Helm, port-forwards
    ├── stop.sh              # Deinstalliert Helm-Charts & beendet Port-Forward-PIDs
    ├── diagnose.sh          # Tiefe K8s-Diagnose (Pods, Services, Tunnels, Snapshots)
    ├── status.sh            # Kurzer Alias/Wrapper für diagnose.sh
    ├── update.sh            # Rebuildet geänderten Service im Minikube & Rollout-Restart
    ├── port-forward-k8s.sh  # Tunnel-Skript für Ingress-Ports (läuft im Hintergrund)
    └── port-forward-status.sh # Zeigt aktive Tunnel und deren Prozess-IDs (PIDs)
```

---

<a id="3-die-logik-der-skript-klassen"></a>

## 3. Die Logik der Skript-Klassen [script-classes]

### A. `start.sh` (Das Setup-Wunder)
- **Dev-Logik:** Prüft, ob benötigte CLI-Compiler (Go, Node, Python) installiert sind. Sieht nach, ob Ports bereits belegt sind (um Abstürze zu verhindern). Startet Prozesse entkoppelt via `nohup` im Hintergrund und leitet Logs nach `.tmp/*.log` um. Verifiziert am Ende aktiv die Health-Endpunkte, bevor sie Erfolg meldet.
- **Prod-Logik:** Überprüft und startet bei Bedarf die Minikube-Node. Scannt und baut *nur fehlende* oder geänderte Docker-Images (`minikube image build`), um Zeit zu sparen. Erstellt K8s-Namespaces, wendet Helm-Upgrades an, wartet aktiv auf den erfolgreichen Rollout aller Pods (`kubectl rollout status`) und startet automatisch den Port-Forwarder im Hintergrund.

### B. `stop.sh` (Das saubere Aufräumen)
- **Dev-Logik:** Sucht gezielt nach den PIDs der genutzten Ports (z. B. `13000`, `18080`), filtert Prozesse, die dem aktuellen Systembenutzer gehören, und beendet diese sauber (`kill`).
- **Prod-Logik:** Liest die im Hintergrund gespeicherte Port-Forwarder-PID aus `.tmp/port-forward.pid` aus, beendet die Weiterleitung und deinstalliert die Helm-Releases (`helm uninstall`) in den Namespaces.

### C. `diagnose.sh` (Die objektive Wahrheit)
- **Dev-Logik:** Zeigt an, welche PIDs auf welchen Entwicklungs-Ports lauschen. Führt Health-Checks durch und gibt ein kurzes JSON-State-Snippet aus, um zu zeigen, dass Daten fließen.
- **Prod-Logik:** Zeigt den Minikube-Status, listet K8s-Pods und Services übersichtlich auf, prüft die Port-Forwarding-Tunnel und fragt die weitergeleiteten API-Endpunkte ab. Gibt am Ende konkrete Handlungsempfehlungen.

### D. `update.sh` (Hot-Reloading im Cluster)
Ermöglicht schnelles Weiterentwickeln im K8s-Betrieb ohne kompletten Neustart. Nimmt den Servicenamen als Argument (z. B. `web-client`):
1. Baut das geänderte Image direkt im Minikube-Daemon.
2. Löst einen `rollout restart` auf das entsprechende Deployment in allen Namespaces aus.
3. Wartet auf die Bestätigung der K8s-Readiness.

---

<a id="4-best-practices-für-ki-gestützte-entwicklung"></a>

## 4. Best Practices für KI-gestützte Entwicklung [ai-assisted-development]

Um dieses Skriptmodell mit KI-Unterstützung auf andere Projekte zu übertragen, gelten folgende Leitlinien:

1. **Einheitliche Namensgebung:** Verwende immer `start.sh`, `stop.sh` und `diagnose.sh` als primäre Einstiegspunkte.
2. **Abwärtskompatibilität durch Wrapper:** Wenn alte Skripte (wie `start-local.sh`) existieren, ersetze sie durch schlanke `exec "$(dirname "$0")/start.sh" "$@"` Wrapper, statt Redundanzen zu erzeugen.
3. **Ecosystem-Helper integrieren:** Biete im `Makefile` einfache Verknüpfungen (z. B. `make dev-start` oder `make prod-update-web`), die die Skripte kapseln. Dadurch bleibt die Bedienung extrem einheitlich.
4. **Zustandslosigkeit & Isolation:** Nutze temporäre Ordner wie `.tmp/` für Log-Dateien und PID-Tracker, die von `.gitignore` ausgeschlossen sind.
