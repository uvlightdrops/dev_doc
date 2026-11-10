---
name: documentation-coherence-check
description: Verify that split or related documentation remains internally coherent, with no contradictions or redundant content. Use after splitting docs, reorganizing sections, or when docs have bidirectional links.
license: MIT
allowed-tools: []
---

# Documentation Coherence Check Skill

## Overview

After splitting a document (e.g., backend + frontend), verify that both resulting docs are:
- **Internally coherent** (no self-contradictions)
- **Mutually coherent** (not repeating each other)
- **Clearly focused** (each doc has one clear purpose)
- **Properly linked** (bidirectional relations are consistent)

## Coherence Checklist

### Step 1: Read Both Docs End-to-End
- [ ] Read original/source doc completely
- [ ] Read new/target doc completely
- [ ] Note any sections that feel "off" or confusing

### Step 2: Check for Redundancy
```
Questions to ask:
- Is the same concept explained in both docs?
- Do both docs claim responsibility for the same task?
- Are there duplicate sections?
- Could content be combined?
```

**Fix:** Remove redundancy. Keep one explanation in most-relevant doc. Link to it from the other.

Example:
```
❌ WRONG (both docs explain "What is an Endpoint"):
Backend doc: "An endpoint is a URL that accepts requests..."
TUI doc: "An endpoint is a URL that accepts requests..."

✅ CORRECT:
Backend doc: "## Endpoint Specification [endpoint-spec]
An endpoint is a URL that accepts requests..."

TUI doc: "## Consuming Endpoints
See endpoint specification in `../backend/ENDPOINT_SPEC.md` 
for API contract details.
```

### Step 3: Verify Logical Flow
In each doc:
- [ ] Does section order make sense?
- [ ] Does each section lead naturally to the next?
- [ ] Are prerequisites introduced before dependencies?

Example — Backend doc order:
1. Overview
2. Request format
3. Response format
4. Error handling
5. Performance

Example — TUI doc order:
1. Understanding the endpoint (link to backend)
2. How to call it
3. Parsing responses
4. Error handling from TUI side
5. Display implementation

### Step 4: Check Bidirectional Relations
```
Backend doc says:
  relations:
    - target: ../frontend-tui/ENDPOINT_CONSUMPTION_TUI.md
      type: reference
      reason: "TUI implementation"

TUI doc says:
  relations:
    - target: ../backend/ENDPOINT_SPEC.md
      type: depends-on
      reason: "Implements this API"
```

**Verify:**
- Types are semantically correct (depends-on ↔ reference)
- Both sides mention the relationship
- Reasons explain the connection

### Step 5: Audience Alignment
Ask for each doc:
- [ ] **Backend doc:** Is this written for backend engineers?
  - ✅ Contains: API specs, performance, error codes, security
  - ❌ Doesn't contain: UI widget names, CSS classes, keyboard shortcuts
  
- [ ] **TUI doc:** Is this written for frontend/UI developers?
  - ✅ Contains: UI workflows, widget layout, user interaction
  - ❌ Doesn't contain: Backend database schema, API internals, backend performance tuning

### Step 6: YAML Header Consistency
Both docs should have proper headers:
```yaml
---
title: [Different titles for different purposes]
scope: [backend-layer-2 vs. frontend-tui]
purpose: [Each has distinct purpose]
created: [Same or different - depends on origin]
updated: [Both updated today if split happened today]
relations: [Each mentions the other]
---
```

### Step 7: Test the Links
```bash
# Verify target paths exist
ls -la ../backend/layer-2-workflow-orchestration/ENDPOINT_SPEC.md
ls -la ../frontend-tui/ENDPOINT_CONSUMPTION_TUI.md

# Verify no typos in relation targets
grep "target:" ENDPOINT_SPEC.md
grep "target:" ENDPOINT_CONSUMPTION_TUI.md
```

## Red Flags (Things That Mean Docs Are NOT Coherent)

🚩 Same concept explained differently in both docs  
🚩 Contradictory information  
🚩 One doc says "Backend does X", other says "Frontend does X"  
🚩 Missing or one-directional links  
🚩 Sections that don't belong (e.g., UI code in backend doc)  
🚩 Unclear which doc is the "source of truth"  
🚩 Readers need to read both docs to understand one task  

## Green Flags (Signs Docs ARE Coherent)

✅ Each doc has single, clear purpose  
✅ No content duplication  
✅ Reading one doc doesn't contradict reading the other  
✅ Bidirectional links are present and correct  
✅ Audience expectations met (backend doc for engineers, TUI doc for UI devs)  
✅ Logical flow within each doc  
✅ Proper YAML headers with relations  

## Full Details

See: `dev_doc/doc/documentation-guidelines/DOCUMENTATION_ORGANIZATION_GUIDE.md`
