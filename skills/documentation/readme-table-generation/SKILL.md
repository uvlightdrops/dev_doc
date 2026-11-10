---
name: readme-table-generation
description: Generate semantic README tables that describe folder contents with status, purpose, and block IDs. Use when creating or upgrading folder READMEs to help humans and tools navigate documentation.
license: MIT
allowed-tools: []
---

# README Table Generation Skill

## Overview

Each documentation folder (backend/layer-1, frontend-tui, features, etc.) needs a README with a structured table showing all docs in that folder.

## Table Format

```markdown
| File | Status | Purpose | Semantic Blocks |
|------|--------|---------|-----------------|
| ARCHITECTURE.md | active | Defines system design patterns | architecture-overview, design-decisions, reference-implementation |
| IMPLEMENTATION.md | active | Step-by-step build guide | quick-start, code-structure, testing-strategy |
| TROUBLESHOOTING.md | active | Common issues & solutions | known-issues, faq, debug-process |
```

### Column Meanings

- **File:** Filename (clickable link to doc)
- **Status:** `active` | `draft` | `archived`
- **Purpose:** 1-line summary of doc's purpose
- **Semantic Blocks:** Comma-separated list of block IDs in that doc (no code tags)

## Table Size Guidelines

**Readability concern:** Markdown viewers struggle with wide tables.

If table gets wide:
- **Option A:** Put purpose in a separate line above the table
- **Option B:** Create multiple smaller tables per category
- **Option C:** Shorten block-ID display (first 3-4 blocks only, truncate with ...)

## Structure for Folder README

```markdown
---
title: [Folder] Documentation Hub
project: kicli-code-assist
scope: [backend-layer-1 | frontend-tui | etc.]
purpose: Central reference for [folder] documentation
created: [git date]
updated: [today]
status: active
relations:
  - target: ../README.md
    type: parent
  - target: doc/specifications/README.md
    type: reference
    reason: "Specifications that apply across layers"
---

# [Folder] Documentation

**Purpose:** [2-3 sentence overview of this folder's scope]

## Documentation Index

| File | Status | Purpose | Blocks |
|------|--------|---------|--------|
| ... | ... | ... | ... |

## Navigation

- **For beginners:** Start with ARCHITECTURE.md
- **For implementation:** See IMPLEMENTATION.md
- **For troubleshooting:** Check TROUBLESHOOTING.md

## Cross-References

- Related: ../layer-2/README.md
- Specs: ../specifications/README.md
```

## Block ID Abbreviations

If list is long, abbreviate:

```
Full:   architecture-overview, quick-start-guide, workflow-execution, best-practices-reference
Short:  arch-overview, quick-start, workflow, best-practices
```

Keep meaning clear — don't over-abbreviate.

## Full Details

See: `dev_doc/doc/documentation-guidelines/DOCUMENTATION_ORGANIZATION_GUIDE.md`
