---
title: Documentation Guidelines
scope: How to organize and maintain documentation
purpose: Central location for doc standards across all projects
status: active
created: 2026-11-05T23:35:00+01:00
updated: 2026-11-05T23:35:00+01:00
---

# 📖 Documentation Guidelines

This folder contains standards, checklists, and best practices for organizing and maintaining documentation across the KI ecosystem.

## 📋 Files in This Folder

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

## ✅ Quick Checklist: Creating New Docs

- [ ] Use YAML header (copy from MAINTENANCE_STANDARDS.md)
- [ ] Clear, descriptive filename
- [ ] Appropriate folder location (cross-project or project-specific?)
- [ ] Cross-reference related docs
- [ ] Pass quality checklist (QUALITY_QUERY.md)
- [ ] Update navigation README in parent folder
- [ ] Update INDEX.md if applicable

## ✅ Quick Checklist: Archiving Docs

- [ ] Update YAML header: `status: archived`
- [ ] Add `update_reason: Superseded by <doc>`
- [ ] Move to `archive/` folder with context
- [ ] Document why in archive README
- [ ] Update navigation in parent folder

---

**Last Updated**: 2026-11-05  
**Status**: ✅ Active & Maintained
