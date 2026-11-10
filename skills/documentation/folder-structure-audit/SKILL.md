---
name: folder-structure-audit
description: Audit documentation folders to identify missing docs, misplaced files, orphaned sections, and structural inconsistencies. Use when reviewing a folder's completeness or detecting documentation gaps.
license: MIT
allowed-tools: []
---

# Folder Structure Audit Skill

## Overview

Systematically review a documentation folder to ensure it's complete, well-organized, and follows project standards.

## Audit Checklist

### 1. File Inventory
- [ ] List all .md files in folder
- [ ] Check each file has proper YAML header
- [ ] Verify no duplicate filenames
- [ ] Identify any orphaned files (not in README table)

### 2. Scope Alignment
For each file, ask:
- [ ] Does the `scope:` in YAML match the folder it's in?
- [ ] Is file in right folder, or should it be elsewhere?
- [ ] Examples: UI_TABS.md in frontend-web (TUI feature) → should move to frontend-tui

### 3. Status Verification
- [ ] Archived docs: Are they truly in `doc/archive/` or still in active folder?
- [ ] Draft docs: Should they be published or removed?
- [ ] Active docs: Are they truly maintained?

### 4. Missing Documentation
Common gaps to check for:

**Backend Layers:**
- [ ] Layer 1: Do we have LLM specs, error handling, context compression?
- [ ] Layer 2: Do we have request/response lifecycle, agent coordination, state management?
- [ ] Layer 3: Do we have decision framework, learning mechanisms?

**Frontend:**
- [ ] Architecture doc (high-level design)
- [ ] Component reference (widget library)
- [ ] Widget/panel descriptions (features)
- [ ] Testing guide (how to test)

**Features:**
- [ ] Each major feature has a doc
- [ ] User workflows documented
- [ ] Integration points clear

### 5. Cross-Reference Check
- [ ] Do docs reference each other appropriately?
- [ ] Are relations bidirectional (A→B, B→A)?
- [ ] No broken links?

```bash
# Check for dead relative paths
grep -r "target:" doc/folder/*.md | grep -v "^\.\."
```

### 6. Semantic Structure
- [ ] All H2 headings have `[block-id]` tags?
- [ ] Block IDs are unique within each doc?
- [ ] Block IDs follow kebab-case format?

### 7. README Quality
- [ ] README exists in folder?
- [ ] README has proper YAML header?
- [ ] README has table of contents (all files listed)?
- [ ] README has navigation hints (where to start)?
- [ ] README has cross-references to related folders?

## Audit Output Format

Create a summary report:

```
## Folder Audit: doc/backend/layer-2-workflow-orchestration

**Status:** 7/10 (Good, some gaps)

### Files Inventory
- ✅ AGENT_ROUTE_SPEC.md (active)
- ✅ SESSION_MANAGEMENT_STATE.md (active)
- ⚠️ PHASE_I2_1_ENDPOINT_INTEGRATION.md (title too long, has PHASE prefix)
- ❌ REQUEST_RESPONSE_LIFECYCLE.md (TBD - missing)

### Scope Issues
- 🔴 PHASE_I2_2_TUI_INTEGRATION_COMPLETE.md: Marked backend but has TUI content → should move to frontend-tui

### Missing Docs (High Priority)
- REQUEST_RESPONSE_LIFECYCLE.md (lifecycle documentation)
- MULTI_AGENT_COORDINATION.md (agent orchestration)
- STATE_PERSISTENCE_STRATEGY.md (data storage)

### Cross-Reference Issues
- ✅ All relations point to valid paths
- ⚠️ PHASE_I2_1 relations outdated (references old PHASE_I2_2 location)

### Recommendations
1. Split PHASE_I2_2 (move TUI content to frontend-tui)
2. Create 3 missing TBD docs
3. Rename docs (remove PHASE prefix)
4. Update relations in PHASE_I2_1
```

## Full Details

See: `dev_doc/doc/documentation-guidelines/DOCUMENTATION_ORGANIZATION_GUIDE.md`
