---
title: Documentation Guidelines
scope: How to organize and maintain documentation
purpose: Central location for doc standards across all projects
status: active
created: 2026-11-05 23
updated: 2026-11-10 23
update_reason: Validate YAML metadata and align semantic blocks with section headings
project: dev_doc
agent_project: ki-ecosystem
semantic_blocks:
- files-in-this-folder
- quick-checklist-creating-new-docs
- quick-checklist-archiving-docs
---

# 📖 Documentation Guidelines

This folder contains standards, checklists, and best practices for organizing and maintaining documentation across the KI ecosystem.

<a id="-files-in-this-folder"></a>

## 📋 Files in This Folder [files-in-this-folder]

### `DOCUMENTATION_ORGANIZATION_GUIDE.md`
**How to structure project documentation**
- Folder organization (architecture/, features/, guides/, deployment/, tracking/)
- When to place docs in `~/dev_doc/` vs. project `/doc/`
- Cross-reference best practices
- **→ Read this first** when setting up new project docs

### `DOCUMENTATION_MAINTENANCE_STANDARDS.md`
**YAML header standards, archival policy, versioning**
- Required metadata (title, status, created, updated, etc.)
- Status values (active, archived, draft, deprecated)
- Timestamp format (ISO 8601+01:00, year 2026)
- Archive vs. delete policy
- **→ Use as template** when creating new docs

### `DOCUMENTATION_QUALITY_QUERY.md`
**Quality checklist for documentation**
- Accuracy, clarity, completeness
- Link verification
- Code examples validation
- **→ Use before publishing** docs

### `DOCUMENTATION_STRUCTURE_MAP.md`
**Visual knowledge organization**
- How knowledge flows between doc levels
- Folder relationships
- Dependency visualization

### `SEMANTIC_KNOWLEDGE_ORGANIZATION.md`
**Semantic knowledge framework for documentation**
- Problem statement (orphaned docs, opacity, brittleness)
- 4-phase implementation plan (metadata → relations → automation → ki-knowledge)
- Connection to ki-knowledge ecosystem
- **→ Read to understand the vision** for machine-readable docs

### `AGENTIC_DOCUMENTATION_PATTERNS.md`
**Best practices for agent-human collaborative documentation**
- 8 patterns for agent-authored doc updates
- Signed blocks, locked sections, proposals, versioning strategies
- Conflict resolution & trust scoring
- **→ Use when implementing agentic documentation changes**

### `DOCUMENTATION_VISUALIZATION.md`
**Visualizing doc relations as graphs**
- How to generate folder-level + master graphs
- Color-coded edge types (depends-on, part-of, composed-of, extends, etc.)
- Tool usage: `python3 scripts/generate_doc_graphs.py`
- **→ Use to understand cross-doc dependencies visually**

<a id="-quick-checklist-creating-new-docs"></a>

## ✅ Quick Checklist: Creating New Docs [quick-checklist-creating-new-docs]

- [ ] Use YAML header (copy from MAINTENANCE_STANDARDS.md)
- [ ] Clear, descriptive filename
- [ ] Appropriate folder location (cross-project or project-specific?)
- [ ] Cross-reference related docs
- [ ] Pass quality checklist (QUALITY_QUERY.md)
- [ ] Update navigation README in parent folder
- [ ] Update INDEX.md if applicable

<a id="-quick-checklist-archiving-docs"></a>

## ✅ Quick Checklist: Archiving Docs [quick-checklist-archiving-docs]

- [ ] Update YAML header: `status: archived`
- [ ] Add `update_reason: Superseded by <doc>`
- [ ] Move to `archive/` folder with context
- [ ] Document why in archive README
- [ ] Update navigation in parent folder

---