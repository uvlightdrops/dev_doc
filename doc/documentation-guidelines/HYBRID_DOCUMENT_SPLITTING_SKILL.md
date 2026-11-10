---
title: Hybrid Document Splitting Skill — Detailed Reference
project: cross-project
agent_project: kicli-code-assist
scope: documentation-patterns
purpose: Complete reference guide for splitting hybrid docs that mix backend and frontend concerns
created: 2026-11-07 14
updated: 2026-11-10 23
update_reason: Validate YAML metadata and align semantic blocks with section headings
status: active
confidence_level: medium
target_repositories:
- kicli-code-assist
semantic_blocks:
- overview
- process-steps
- checklist
- key-guidelines
- confidence-limitations
- related-patterns
- examples
---

# Hybrid Document Splitting Skill — Detailed Reference Guide

**Framework Integration:** `.github/skills/hybrid-doc-splitting/SKILL.md`  
**Purpose:** Complete step-by-step guide (read in Typora)

<a id="overview"></a>

## Overview [overview]

This skill handles documents that mistakenly mix backend and frontend (usually TUI) concerns. The goal: separate concerns, maintain bidirectional links, keep both docs focused.

**When to use:**
- A backend doc mentions "TUI will display X"
- A doc has "Integration with TUI" sections
- Backend and frontend implementation details are intertwined
- One doc tries to serve two audiences (backend engineers + UI developers)

<a id="process-steps"></a>

## Process Steps [process-steps]

### 1. Identify Hybrid Content

**Read the entire doc** and categorize sections:

```
BACKEND SECTIONS (keep in original doc):
  - API endpoint spec
  - Request/response formats
  - Error handling at API level
  - Performance characteristics
  - Security validations
  - Database/cache interactions

FRONTEND SECTIONS (move to new doc):
  - "Integration with TUI" sections
  - UI widget/component discussion
  - User workflow description
  - Next steps specifically for frontend
  - Display/rendering implementation
  - Keyboard interaction
```

### 2. Create Split Plan

Before editing, document what goes where:

**Original Doc (Backend):**
- Keep: Sections X, Y, Z
- Remove: Sections A, B, C (move to frontend doc)
- Update YAML: `update_reason: "Split from hybrid doc; removed [X/Y/Z], moved to [NEW_DOC]"`

**New Doc (Frontend):**
- File: `doc/frontend-[type]/[DESCRIPTIVE_NAME].md`
- Title: Frontend-focused title (no "PHASE" prefix)
- Content: All frontend sections from original doc
- Relations: `depends-on: ../backend/.../ORIGINAL_DOC.md`

### 3. Edit Original Doc

Apply these steps **in order**:

#### Step 3a: Update YAML Header

```yaml
# BEFORE
updated: 2026-11-07T10:00:00+01:00
update_reason: "Added YAML header for semantic organization"

# AFTER
updated: 2026-11-07T14:30:00+01:00
update_reason: "Split from hybrid doc; removed TUI integration content, moved to frontend-tui/[NEW_DOC].md"
```

#### Step 3b: Update Relations

Add relation to new doc:

```yaml
relations:
  - target: ../../../frontend-tui/ENDPOINT_CONSUMPTION_TUI.md
    type: reference
    reason: "TUI implementation of this backend endpoint"
```

#### Step 3c: Shorten Title

Remove "PHASE" prefix:

```
# BEFORE
# Phase I2.1: Backend Integration Complete

# AFTER  
# REST Endpoint Implementation - Project Analysis
```

#### Step 3d: Remove Frontend Sections

Delete all TUI-specific content:
- "Integration with TUI" sections
- UI-focused examples
- Frontend-specific next steps
- Replace with short reference to new doc:

```markdown
## Integration with TUI

See `../../../frontend-tui/ENDPOINT_CONSUMPTION_TUI.md` for TUI implementation.
```

### 4. Create New Frontend Doc

Create file: `doc/frontend-[type]/[NAME].md`

**Template:**

```yaml
---
title: [FRONTEND-FOCUSED TITLE]
project: kicli-code-assist
agent_project: kicli-code-assist
scope: frontend-[type]
purpose: Document how [FRONTEND] consumes [BACKEND]
created: [TODAY]T[TIME]:00+01:00
updated: [TODAY]T[TIME]:00+01:00
update_reason: "Extracted from backend [ORIGINAL_DOC]; TUI-specific implementation"
status: [active|draft]
relations:
  - target: ../backend/layer-2-workflow-orchestration/[ORIGINAL_DOC].md
    type: depends-on
    reason: "Consumes [BACKEND_FEATURE] endpoint/API"
  - target: README.md
    type: parent
    reason: "TUI documentation hub"
---

# [FRONTEND-FOCUSED TITLE]

**Status**: 🔨 IN DEVELOPMENT or ✅ COMPLETE

## Integration Points

[Content from "Integration with TUI" section]

## Implementation Details

[UI-specific content]

## Next Steps

[Frontend next steps from original doc]

---

**Backend spec**: `../backend/layer-2-workflow-orchestration/[ORIGINAL_DOC].md`
```

### 5. Update Backend Doc's Relations

Make sure original doc explicitly references new frontend doc:

```yaml
relations:
  - target: ../../../frontend-tui/ENDPOINT_CONSUMPTION_TUI.md
    type: reference
    reason: "TUI implementation of this endpoint"
```

### 6. Halt and Report

**After editing source and creating target**, stop and report briefly:

```
✅ **Backend-Doc gekürzt:**
- Updated YAML (update_reason, timestamp)
- Title shortened: [OLD] → [NEW]
- TUI sections removed
- Reference to new doc added

✅ **TUI-Doc erstellt:**
- [FRONTEND_DOC].md with content from "Integration with TUI"
- YAML header with correct relations
- Link back to backend doc
```

**This is the "halt and report" checkpoint.**

<a id="checklist"></a>

## Checklist [checklist]

- [ ] Read entire hybrid doc carefully
- [ ] Identify backend vs. frontend sections
- [ ] Plan split (what goes where)
- [ ] Update original doc YAML (update_reason, updated timestamp)
- [ ] Update original doc title (remove PHASE prefix)
- [ ] Update original doc relations (add reference to new doc)
- [ ] Remove frontend sections from original doc
- [ ] Create new frontend doc with proper template
- [ ] Add relations in new doc (back to backend + parent to README)
- [ ] Verify both docs are coherent (read them through)
- [ ] **Halt and report to user**
- [ ] Only continue after user approval

<a id="key-guidelines"></a>

## Key Guidelines [key-guidelines]

**Titles:**
- Remove "PHASE_I2_1_" prefix
- Keep descriptive, short
- Example: `REST Endpoint Implementation - Project Analysis`

**Update Reason:**
- Must explain the split
- Example: `"Split from hybrid doc; removed TUI integration content, moved to frontend-tui/ENDPOINT_CONSUMPTION_TUI.md"`

**Relations:**
- Backend doc: points TO frontend doc (reference)
- Frontend doc: points BACK TO backend (depends-on)
- Both include target relation to README (parent)

**Content:**
- Backend: API contracts, security, performance, error handling
- Frontend: UI workflows, widgets, rendering, user experience

<a id="confidence--limitations"></a>

## Confidence & Limitations [confidence-limitations]

**High confidence:**
- Identifying backend vs. TUI sections (clear separation)
- Updating YAML headers (mechanical)
- Creating bidirectional links (structured)

**Medium confidence:**
- Deciding "which section belongs where" (sometimes fuzzy)
- Tone/voice in new frontend doc (might need human adjustment)

**Always:**
- Halt after each source+target pair modification
- Let user review and approve before continuing
- Don't make assumptions about folder structure (verify it exists)
- Don't apply this skill to ALL docs at once (one doc pair per session)

<a id="related-patterns"></a>

## Related Patterns [related-patterns]

- See: `AGENTIC_DOCUMENTATION_PATTERNS.md` (change proposals, metadata)
- See: `SEMANTIC_KNOWLEDGE_ORGANIZATION.md` (YAML headers, relations format)
- See: kicli-code-assist project READMEs for folder structure

<a id="examples"></a>

## Examples [examples]

**Example 1: Backend API + TUI Consumer**
- Original: `doc/backend/layer-2-workflow-orchestration/PHASE_I2_1_ENDPOINT_INTEGRATION.md`
- Backend content stays: Endpoint spec, request/response, error handling
- TUI content extracted: "Integration with TUI" section + next steps
- New file: `doc/frontend-tui/ENDPOINT_CONSUMPTION_TUI.md`
- Relation: Backend → TUI (reference); TUI → Backend (depends-on)

**Example 2: Architecture + Implementation Split**
- Original: `doc/backend/PHASE_I2_ARCHITECTURE.md`
- Backend architecture stays: Design decisions, data flow
- Implementation details go: "How TUI will implement this"
- New file: `doc/frontend-tui/ARCHITECTURE_TUI_IMPLEMENTATION.md`
- Relation: Backend → TUI (reference); TUI → Backend (depends-on)

---

**Last verified**: 2026-11-07  
**Agent skill author**: Copilot (trained by @uvlightdrops)  
**Status**: Active, tested on PHASE_I2_1 split
