---
title: AI Agent Intelligence Architecture
project: dev_doc
scope: architecture
purpose: Conceptual model and implementation proposal for cognitive and generative agents
created: 2026-11-05 11
agent_project: ki-ecosystem
status: draft
updated: "2026-11-12 10"
update_reason: Move into the AI agents topic folder and link the topic index
semantic_blocks:
- agent-intelligence-levels
- hybrid-layer-model
- behavior-feedback-loop
- implementation-roadmap
relations:
  - target: README.md
    type: parent
    reason: AI agents topic index
---
# Architektur-Konzept: Kognitive & Lernende Agenten-Architekturen

Dieses Dokument beschreibt die theoretischen Grundlagen von Agenten-Intelligenzen in Simulationen, das Zusammenspiel klassischer Algorithmen mit modernen KI-Modellen (LLMs, neuronale Netze) und skizziert einen konkreten Umsetzungsplan für die IA3 SimWorld.

---

<a id="1-die-grade-von-agenten-intelligenz-theoretische-einordnung"></a>

## 1. Die Grade von Agenten-Intelligenz (Theoretische Einordnung) [agent-intelligence-levels]

```yaml block-meta
updated: "2026-11-12 10"
status: draft
relations:
  - target: "README.md#documents"
    type: reference
    reason: Topic index distinguishes this conceptual model from the architecture review
  - target: "../../../dev_flow/kicli-code-assist/doc/architecture/AGENT_COORDINATION_ARCHITECTURE.md#references"
    type: reference
    reason: Assist architecture notes cite this theory as background
  - target: "../../../dev_flow/kicli-code-assist/doc/backend/layer-3-autonomous-agent/AGENT_COORDINATION_ARCHITECTURE.md#references"
    type: reference
    reason: Layer 3 architecture notes cite this theory as background
  - target: "../copilot-memory/COPILOT_MEMORY_MAP.md#stored-memories-persistent-across"
    type: reference
    reason: The memory map points readers to this agent-intelligence model
```

In der KI- und Simulationsforschung werden Agenten anhand ihrer kognitiven Fähigkeiten in fünf Entwicklungsstufen eingeteilt:

```text
Stufe 1: Einfach-Reaktiv (Reflexe)
  │   └── Stimulus-Response ohne Gedächtnis (z. B. Kollisionsvermeidung, Boids).
  ▼
Stufe 2: Modellbasiert-Reaktiv (Zustandsautomaten)
  │   └── Interner Zustand + Historie. FSMs (Finite State Machines) und Behavior Trees (Verhaltenbäume).
  │       [Aktueller Stand von IA3 SimWorld: Mesa-Routinen steuern feste Tageszeiten].
  ▼
Stufe 3: Ziel- und Nutzenorientiert (BDI & GOAP)
  │   └── Belief-Desire-Intention (BDI) & Goal-Oriented Action Planning (GOAP). Agenten planen
  │       Ketten von Aktionen, um Bedürfnisse (Nahrung, Energie) autonom zu optimieren.
  ▼
Stufe 4: Kognitiv & Generativ (LLM-basierte Agenten)
  │   └── Generative Agents (Stanford-Modell). Nutzen LLMs als Gehirn, besitzen ein Vektor-basiertes
  │       Erinnerungsgedächtnis, reflektieren über Erlebnisse und bilden soziale Pläne.
  ▼
Stufe 5: Autonom Lernend (Reinforcement Learning - RL)
      └── Agenten lernen Strategien durch Belohnung/Bestrafung (PPO, Q-Learning).
          Ermöglicht das Entstehen völlig neuartiger, unvorhergesehener Verhaltensweisen.
```

---

<a id="2-das-hybride-schichtenmodell-kopplung-von-simulation--ki"></a>

## 2. Das hybride Schichtenmodell (Kopplung von Simulation & KI) [hybrid-layer-model]

Ein reiner LLM-Betrieb für jeden einzelnen Bewegungsschritt bei Hunderten von Agenten ist aus Performance- und Latenzgründen unmöglich. Die Lösung ist eine **hybride, dreischichtige Architektur**:

```text
┌────────────────────────────────────────────────────────────────────────┐
│ 1. Generative & Reflexive Schicht (LLM / GNN - Asynchron)             │
│    - Reflektiert alle X Ticks über das Tagebuch (Memory Stream).       │
│    - Bildet Pläne, verhandelt soziale Verträge, schließt Allianzen.    │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   ▼ (Überschreibt Ziele / Missionen)
┌────────────────────────────────────────────────────────────────────────┐
│ 2. Kognitive & Taktische Schicht (Mesa / Python - Synchron)            │
│    - Führt die aktuellen High-Level-Missionsziele aus (z. B. "Bauen").  │
│    - Berechnet Energie, Hunger und soziale Interaktions-Wahrscheinlichkeit.│
└──────────────────────────────────┬─────────────────────────────────────┘
                                   ▼ (Generiert Bewegungswünsche)
┌────────────────────────────────────────────────────────────────────────┐
│ 3. Physische & Reaktive Schicht (Go Simulator - Tick-Schleife)         │
│    - Verwaltet die Spielwelt, Kollisionen und tickgenaue Pfade (A*).   │
│    - Garantiert unumstößliche physikalische Gesetze der Simulation.     │
└────────────────────────────────────────────────────────────────────────┘
```

---

<a id="3-der-feedback-loop-vom-verhalten-zurück-ins-netz-und-den-code"></a>

## 3. Der Feedback-Loop (Vom Verhalten zurück ins Netz und den Code) [behavior-feedback-loop]

Das ultimative Ziel ist eine adaptive Simulation, in der Agenten durch Interaktion lernen und dieses gelernte Verhalten in das System zurückfließt:

```text
┌──────────────┐      Ereignisprotokoll      ┌──────────────────────┐
│ Simulation   ├────────────────────────────>│ Memory Stream        │
│ (IA3 World)  │   (Tagebuch als Vektoren)   │ (Datenbank / Graph)  │
└──────▲───────┘                             └──────────┬───────────┘
       │                                                │
       │ Generierter Code /                             │ Abstraktion &
       │ Neuronale Gewichte                             │ Reflexion (LLM)
       │                                                ▼
┌──────┴───────────────┐  Behavioral Cloning  ┌──────────────────────┐
│ Evolutionärer Code-  │<─────────────────────┤ Emergenz-Erkennung   │
│ Generator / RL-Netz  │  (Imitation Learning)│ (Neues Verhalten)    │
└──────────────────────┘                      └──────────────────────┘
```

### Technische Umsetzung des Loops:
1. **Ereignis-Abstraktion (Memory Stream):** Jede Interaktion (z. B. „Scout teilt Beerenfundort mit Tracker“) wird als semantischer Vektor (Embedding) abgespeichert.
2. **Reflexion (LLM):** Ein asynchroner LLM-Thread scannt das Gedächtnis des Agenten: *„Ich habe wenig Energie, aber mein Nachbar hat viel Weizen geerntet. Ich sollte ihm anbieten, seine Gehöfte zu reparieren, wenn er mir Essen gibt.“*
3. **Emergenz & Behavioral Cloning (Neuronales Netz):** Das LLM entscheidet sich für eine neue Interaktionsform (z. B. Tauschhandel). Diese erfolgreichen Sequenzen werden gesammelt. Ein kompaktes, lokales neuronales Netz (Policy Network) wird mit diesen Interaktionen trainiert (Behavioral Cloning), um das LLM langfristig für Routineentscheidungen zu ersetzen.
4. **Code-Synthese (Echtzeit-Code-Injektion):** Das LLM generiert direkt neue Python-Mesa-Klassen oder Verhaltensregeln, kompiliert diese zur Laufzeit und injiziert sie als neue Zustandsknoten in den laufenden Simulator.

---

<a id="4-konkreter-fahrplan-zur-umsetzung-in-ia3-simworld"></a>

## 4. Konkreter Fahrplan zur Umsetzung in IA3 SimWorld [implementation-roadmap]

Hier ist ein pragmatischer 4-Stufen-Plan, um diese Intelligenzstufen schrittweise in unserer existierenden Architektur zu verankern:

### Stufe A: Der asynchrone Memory Stream & Tagebuch (Kurzfristig)
- **Konzept:** Jeder Mesa-Agent erhält ein In-Memory-Tagebuch (`List[str]`), das lokal persistiert wird.
- **Implementierung:** 
  - Bei wichtigen Ereignissen (Arbeit beendet, Hunger erlitten, Kooperation geschlossen) schreibt der Agent einen Tagebucheintrag.
  - Ein asynchroner Hintergrundprozess im Python `agent-service` nutzt ein lokales Mini-Embedding-Modell (z. B. `sentence-transformers`), um die Einträge als Vektoren abzuspeichern.

### Stufe B: Das asynchrone LLM-Gehirn ("The Generative Brain")
- **Konzept:** Integration einer LLM-Schnittstelle (z. B. via Ollama lokal oder OpenAI API).
- **Implementierung:**
  - Sobald zwei Agenten kollidieren oder am Marktplatz sozialisieren, wird ein LLM-Prompt gestartet.
  - Das LLM erhält die Tagebücher beider Agenten und generiert einen natürlichen Dialog sowie eine veränderte Beziehungsmatrix (Interessen-Injektion, neue Kooperations-Missionsziele).

### Stufe C: Nutzen-Planung (GOAP / Utility Layer)
- **Konzept:** Ersetzen der starren Mesa-Stundenroutinen durch ein dynamisches Utility-Modell.
- **Implementierung:**
  - Der Agent wählt seine Aktion basierend auf der am schnellsten sinkenden Bedürfniskurve (Energie, Hunger, Sozialisierung, Müllentsorgung).
  - Jede Aktion hat Kosten (Stamina) und einen Nutzen. Die Mesa-Rolle (Scout, Builder etc.) gewichtet diese Nutzen unterschiedlich.

### Stufe D: Evolutionärer Feedback-Loop & RL
- **Konzept:** Trainieren eines neuronalen Netzes auf Basis von LLM-Entscheidungen.
- **Implementierung:**
  - Die im Stufe-B-Betrieb erzeugten Verhaltensentscheidungen werden als Trainingspaare `(Zustand, Aktion)` gespeichert.
  - Ein kleines PyTorch-Modell lernt diese Aktionen nachzuahmen. Nach genügend Ticks übernimmt das neuronale Netz die Steuerung, wodurch die teuren LLM-Aufrufe drastisch reduziert werden.
