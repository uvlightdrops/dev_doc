---
title: Documentation Visualization & Relation Graphs
project: kicli-code-assist
agent_project: ki-knowledge
scope: Documentation Tools / Meta
purpose: Script to visualize semantic doc relations as GraphViz graphs
created: 2026-11-07 12
updated: 2026-11-10 23
update_reason: Validate YAML metadata and align semantic blocks with section headings
status: active
semantic_blocks:
- id: overview
- id: usage
- id: graph-generation
  type: implementation
  status: stable
  version: 1.0
- id: how-it-works
- id: features
- id: next-steps
---

# Documentation Visualization & Graphs

<a id="overview"></a>

## Overview [overview]

Script: `scripts/generate_doc_graphs.py`

Automatically generates GraphViz visualizations from semantic doc relations:
- **Folder-level graphs**: Per-directory visualization (e.g., backend/layer-2)
- **Master graph**: All docs across entire doc/ tree
- **README generation**: Markdown tables from doc metadata

<a id="usage"></a>

## Usage [usage]

### Generate Folder Graphs

```bash
python3 scripts/generate_doc_graphs.py --folder-only
```

Output: `doc/.graphs/backend_layer-2-workflow-orchestration.svg` etc.

### Generate Master Graph

```bash
python3 scripts/generate_doc_graphs.py --master-only
```

Output: `doc/.graphs/master.svg` – shows entire doc network

### Generate Both + README Sections

```bash
python3 scripts/generate_doc_graphs.py --readme
```

## Graph Visualization [graph-generation]

Each relation has a **color + label**:

| Type | Color | Meaning |
|------|-------|---------|
| `depends-on` | 🔴 Red | A requires B to understand context |
| `part-of` | 🔵 Blue | A is a section of B |
| `composed-of` | 🟢 Green | A is made up of multiple B's |
| `implements` | 🟠 Orange | A implements design from B |
| `extends` | 🟣 Purple | A builds on/extends B |
| `references` | ⚫ Gray | A mentions B but isn't tightly coupled |
| `deprecates` | 🔴 Dark Red | A replaces/supersedes B |

### Example: Layer 2 Workflow Orchestration

```
digraph "Doc Graph" {
  rankdir=LR;
  
  "PHASE_I2_PROJECTANALYZER_COMPLETE" -> "PHASE_I2_2_TUI_INTEGRATION_COMPLETE" 
    [label="depends-on", color="#e74c3c"];
    
  "PHASE_I2_1_ENDPOINT_INTEGRATION" -> "PHASE_I2_PROJECTANALYZER_COMPLETE" 
    [label="depends-on", color="#e74c3c"];
```

**Visual**:
```
ProjectAnalyzer (core)
  ├── depends-on → TUI Integration
  └── depends-on ← Endpoint Integration
```

<a id="how-it-works"></a>

## How It Works [how-it-works]

1. **Scan**: Find all `.md` files in `doc/`
2. **Parse**: Extract YAML header + `relations` field
3. **Build Graph**: Create nodes + edges from relations
4. **Filter**: Per-folder subgraphs only include internal relations
5. **Render**: GraphViz dot → SVG

<a id="features"></a>

## Features [features]

- **Automatic folder grouping**: Subgraphs per directory
- **Color-coded edges**: See relation types at a glance
- **Scalable**: Works with 10 or 1000 docs
- **Machine-readable**: DOT files as git artifacts
- **README generation**: Auto-markdown tables

<a id="next-steps"></a>

## Next Steps [next-steps]

- [ ] Add cycle detection (circular relation warnings)
- [ ] Add orphan detection (docs with zero in/out edges)
- [ ] Generate HTML report with clickable links
- [ ] Integrate into CI/CD (warn on broken relations)
- [ ] Add layer-level aggregation (show backend/ as single super-node)