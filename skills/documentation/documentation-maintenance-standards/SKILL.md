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

### 6. Block Content, Placement and Documentation Status
- A block develops the subject named by its heading. Write direct subject
  matter, not a retelling of the conversation, agent reasoning or work process.
  Keep that reflection in the dialogue; keep change provenance in metadata
  and Git rather than narrative additions to every block.
- Subject documentation describes the intended, relevant behavior, not the
  absence of defects or inappropriate elements. Do not turn routine cleanup
  into a permanent feature statement (for example, "no prototype banner").
  Keep resolved defects and removed mistakes in TODO/DONE, change metadata
  or Git. Document negative statements only when they clarify a meaningful
  boundary, guarantee, exception or compatibility constraint.
- `features/` describes implemented capabilities, their operation and actual
  limitations only. A draft feature document may describe existing functionality;
  it must not present an unimplemented capability as a feature.
- `architecture/` contains the associated considerations, models, alternatives
  and rationale. Link the relevant feature and architecture blocks reciprocally
  instead of mixing or duplicating their contents.
- `status` describes the documentation, not implementation progress:
  `draft` means incomplete or not yet sufficiently clarified documentation;
  `active` means sufficiently complete and maintained for its stated scope;
  `archived` means historical and no longer maintained. State specific gaps
  when needed; do not invent completeness percentages. Apply this meaning
  separately to document and block status.
- Future ideas remain welcome, but their permanent location is intentionally
  undecided. Keep them distinguishable from implemented features; do not
  introduce a folder, workflow or roadmap by assumption.
- If a project maintains a requirements index, read its current usage rules
  and entries before acting. Simple fitting items may be implemented when
  that index explicitly authorizes autonomous work; complex topics first
  receive a linked architecture concept in the relevant block's relations.
  Preserve user additions, distinguish open from implemented items, and
  update the outcome without turning the index into a process manual.

### 7. Consistency Checks
New or deliberately revised semantic blocks require an ID in the H2
heading and their own visible `yaml block-meta` fence immediately below
with `updated`, `status` and `relations`. Do not duplicate local attributes
in document frontmatter or hide them in HTML comments. Use quoted `YYYY-MM-DD HH`
timestamps, `draft|active|archived` status, and relation lists containing
`target`, `type`, `reason` (or `[]`). Document metadata remains separate.
Verify relative `file.md#block-id` / `#block-id` targets and reciprocal
block relations. Do not refresh untouched blocks or infer missing values.
Legacy header ID indexes may remain; move header attributes to their
section when upgrading, without duplication.
For block metadata, this contract takes precedence over the older
document timestamp examples above; see
`dev_doc/doc/documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md#block-metadaten`.

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
  [ ] Revised sections have visible local updated, status, relations
  [ ] No local attributes duplicated in document frontmatter
  [ ] Block timestamps change only for changed blocks
  [ ] Block targets and reciprocal relations are valid

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
  [ ] Each block develops its heading's subject, without dialogue/process narration
  [ ] No permanent statements about removed defects or self-evident expected behavior
  [ ] features/ contains implemented behavior only; architecture rationale is linked
  [ ] Status reflects documentation completeness, not implementation progress
  [ ] Future ideas are distinguishable without imposing a new organization
  [ ] Timestamps format consistent
  [ ] H2-and-higher Markdown headings contain no emojis
  [ ] No duplicate content across docs
  [ ] Related docs reference each other
  [ ] Scopes appropriate for folder location
```

## Full Details

See: `dev_doc/doc/documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md`
