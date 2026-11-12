---
name: semantic-knowledge-organization
description: Apply semantic knowledge organization to documentation using YAML headers, semantic blocks with unique IDs, and cross-document relations. Use when creating or upgrading documentation that needs machine-readable structure and human navigation aids.
license: MIT
allowed-tools: []
---

# Semantic Knowledge Organization Skill

## Overview

This skill implements a machine-readable documentation system that enables both humans and tools to understand doc structure and interdependencies.

## Core Components

### 1. YAML Header Metadata
Every doc must have:
```yaml
---
title: [Short title - no PHASE prefix]
project: kicli-code-assist
agent_project: kicli-code-assist
scope: [backend-layer-1 | frontend-tui | features | etc.]
purpose: [Clear 1-2 sentence purpose]
created: [ISO 8601 date from git log]
updated: [Today's date]
update_reason: [Why updated today]
status: [active | draft | archived]
relations:
  - target: [relative/path/to/doc.md]
    type: [depends-on | reference | parent | child]
    reason: [Why related]
---
```

### 2. Semantic Blocks
Each major section is a semantic block:
- H2 heading with `[block-id]` tag: `## My Section [my-section-id]`
- Block ID: kebab-case, ≤38 chars
- 4 typical blocks per doc: architecture|implementation|operations|reference

Each new or deliberately revised block must have its own `updated`,
`status` and `relations` in a visible `yaml block-meta` fenced code block
immediately below its H2 heading. Do not duplicate them in document
frontmatter or hide them in HTML comments. The ID stays in the heading;
an optional header ID index is not a metadata store. `updated` is a
quoted `YYYY-MM-DD HH` string; status is `draft|active|archived`.
Relations use `target`, `type`, `reason`, or `[]` when absent. Use relative
`file.md#block-id` targets (or `#block-id` within the same file), and verify
reciprocal block relations. Do not inherit document values, duplicate every
block relation at document level, or update untouched blocks' timestamps.
Move legacy header attributes to the section when upgrading, without
duplicating them; missing local values remain missing, not inferred.

The authoritative contract is
`dev_doc/doc/documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md#block-metadaten`.

### 3. Cross-Document Relations
Links use relative paths and semantic types:
- `depends-on`: Requires this doc to be understood first
- `reference`: Related doc for context
- `parent`: This doc's hub/README
- `child`: Detailed extension of this concept

## When to Use

- Creating new documentation structure
- Upgrading existing docs to be machine-readable
- Establishing connections between multiple docs
- Preparing for doc generation/analysis tools

## Full Details

See: `dev_doc/doc/documentation-guidelines/SEMANTIC_KNOWLEDGE_ORGANIZATION.md`
