---
name: documentation-maintenance-standards
description: Apply documentation maintenance standards including timestamp management, header validation, relation verification, and consistency checks. Use to ensure all docs follow project standards and stay coherent over time.
license: MIT
allowed-tools: []
---

# Documentation Maintenance Standards Skill

## Overview

Standards and checklists for maintaining documentation quality, consistency, and accuracy across a project.

## Core Standards

### 1. YAML Header Validation
Every doc must have complete headers:
- ✅ title (required)
  - No PHASE prefix **except in `doc/phases/` folder** (context-appropriate there)
- ✅ project (required)
- ✅ scope (required, from approved list)
- ✅ purpose (required, 1-2 sentences)
- ✅ created (required, ISO 8601 from git log)
- ✅ updated (required, today's date)
- ✅ update_reason (required, explain what changed)
- ✅ status (required: active|draft|archived)
- ✅ semantic_blocks (if doc has H2 headings)
- ✅ relations (if doc references/depends-on others)

### 2. Timestamp Handling
- `created`: When doc was first committed (from git log, never changes)
- `updated`: When doc was last modified (today's date when editing)
- Format: ISO 8601 with timezone (+01:00 or +02:00 per season)
- Consistency: All timestamps in same format across project

### 3. Relation Verification
After moving/renaming docs:
- Update all relations in source docs
- Update all relations in target docs (add relation back)
- Use relative paths (../../../folder/file.md)
- Verify paths actually exist
- Check: type matches (depends-on ↔ reference)

### 4. Cross-Reference Link Maintenance
When editing doc paths:
- Find all docs that reference the old path
- Update every reference to new path
- Use grep: `grep -r "old/path" doc/`
- Verify no broken links remain

### 5. Title & Filename Coherence
- Title and filename must **meaningfully reflect** document content
- Document filenames must use uppercase letters; keep the `.md` extension lowercase.
- Filename should be **recognizable from title** (not necessarily exact slug match)
  - ✅ Title: `Secrets Management Strategy` → Filename: `SECRETS_MANAGEMENT`
  - ✅ Title: `TUI Endpoint Consumption (Phase I1)` → Filename: `I1_ENDPOINT_CONSUMPTION_TUI`
  - ❌ Title: `Deployment Status Report` → Filename: `config.md` (unrelated)
- Avoid generic filenames: be specific
- Check: Does title clearly describe what the doc contains?
- Check: Does filename clearly relate to title content?
- Check: Would someone find this doc by searching the title?

### 6. Consistency Checks
Markdown headings at level 2 or higher (`##`, `###`, etc.) must not contain emojis.

Before committing:
- [ ] Title & filename match document content
- [ ] All H2 headings have `[block-id]` tags
- [ ] No duplicate block IDs within doc
- [ ] Timestamps match format (±timezone)
- [ ] All relations have target, type, reason
- [ ] No absolute paths (/home/flow/...)
- [ ] Status values are valid (active|draft|archived)
- [ ] Scopes match project conventions

## Maintenance Checklist

Before any doc commit:
```
YAML Headers:
  [ ] All 10 required fields present
  [ ] Title: no PHASE prefix (except in doc/phases/ folder)
  [ ] Created from git, updated = today
  [ ] Status is valid

Title & Filename:
  [ ] Title clearly describes content (not generic)
  [ ] Document filename uses uppercase letters (with lowercase `.md` extension)
  [ ] Filename meaningfully reflects title (recognizable, not exact slug)
  [ ] Neither is vague or unrelated to content
  [ ] Someone could find doc by searching title or filename

Semantic Blocks:
  [ ] H2 headings have [block-id] tags
  [ ] Block IDs are unique in doc
  [ ] Block IDs are kebab-case ≤38 chars

Relations:
  [ ] All relations have target, type, reason
  [ ] Targets use relative paths
  [ ] Paths exist (no broken links)
  [ ] Bidirectional links are consistent

Cross-References:
  [ ] No absolute paths
  [ ] No dead links
  [ ] Links updated after file moves
  [ ] Grep verified no orphaned refs

Consistency:
  [ ] Timestamps format consistent
  [ ] H2-and-higher Markdown headings contain no emojis
  [ ] No duplicate content across docs
  [ ] Related docs reference each other
  [ ] Scopes appropriate for folder location
```

## Full Details

See: `dev_doc/documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md`
