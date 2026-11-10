---
name: hybrid-doc-splitting
description: Split documentation that mixes backend and frontend (TUI) concerns into separate, focused docs with bidirectional links. Use when a single doc contains both backend API/system design and frontend UI/workflow implementation details.
license: MIT
allowed-tools: []
---

# Hybrid Document Splitting Skill

## Overview

This skill handles documentation that mistakenly mixes backend and frontend (usually TUI) concerns. The goal: separate concerns, maintain bidirectional links, keep both docs focused and coherent.

## When to Use This Skill

Use this skill when:
- A backend doc mentions "TUI will display X"
- A doc has "Integration with TUI" sections
- Backend and frontend implementation details are intertwined in one file
- One doc tries to serve two audiences (backend engineers + UI developers)
- A doc describes both "what the endpoint does" AND "how the TUI should consume it"

## Process Overview

The skill involves these core steps:

1. **Identify Hybrid Content** — Read the doc and categorize sections into Backend (API, performance, security) vs. Frontend (UI, workflows, rendering)

2. **Create Split Plan** — Before editing, decide what stays (original doc) and what moves (new frontend doc)

3. **Edit Original Doc** — Update YAML header (update_reason, timestamp), shorten title (remove "PHASE"), remove frontend sections, add reference to new doc

4. **Create New Frontend Doc** — New file in `doc/frontend-[type]/` with UI-focused content, proper YAML with relations pointing back to backend

5. **Halt and Report** — After each source+target pair, stop and report progress to user for approval

6. **Verify Both Docs** — Read both docs to ensure each is coherent and focused

## Key Guidelines

**Titles:**
- Remove "PHASE_I2_1_" prefixes
- Keep descriptive but short
- Example: `REST Endpoint Implementation - Project Analysis`

**Update Reason:**
- Must explain the split
- Example: `"Split from hybrid doc; removed TUI integration content, moved to frontend-tui/ENDPOINT_CONSUMPTION_TUI.md"`

**Content Separation:**
- Backend keeps: API specs, request/response, security, performance, error handling at API level
- Frontend gets: UI workflows, display logic, widget implementation, user interactions

**Bidirectional Links:**
- Backend doc: points TO frontend doc (type: reference)
- Frontend doc: points BACK TO backend (type: depends-on)
- Both include relations to parent README

## Example

**Before:** `doc/backend/layer-2-workflow-orchestration/PHASE_I2_1_ENDPOINT_INTEGRATION.md`
- Contains both endpoint spec AND "how TUI will display this"
- Serves two audiences poorly

**After (Backend):** Same file, refined
- Only endpoint spec, performance, error handling
- Title shortened: `REST Endpoint Implementation - Project Analysis`
- Added reference to new TUI doc

**After (Frontend):** New file `doc/frontend-tui/ENDPOINT_CONSUMPTION_TUI.md`
- Contains "Integration with TUI" content
- UI widget implementation
- Frontend next steps
- Relations back to backend doc

---

## Detailed Process

For step-by-step instructions, see the full guide at:
`dev_doc/doc/documentation-guidelines/HYBRID_DOCUMENT_SPLITTING_SKILL.md`

This document includes:
- Complete process steps with YAML templates
- Full checklist
- Confidence levels and limitations
- Multiple examples with before/after
