---
title: "Shared Documentation Workflow"
project: KI-Ecosystem
agent_project: dev_doc
scope: agent-instructions
purpose: "Defines the cross-project workflow for locating, writing, and maintaining documentation without duplicating specialized standards."
created: "2026-11-10T06:52:00+01:00"
updated: "2026-11-10 20"
update_reason: "Relocate shared documentation into doc/ and update references"
status: active
semantic_blocks:
  - placement
  - workflow
  - structure
relations:
  - target: "README.md"
    type: parent
    reason: "Shared agent instructions index"
  - target: "../doc/documentation-guidelines/DOCUMENTATION_ORGANIZATION_GUIDE.md"
    type: reference
    reason: "Authoritative cross-project documentation placement and structure guide"
  - target: "../doc/documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md"
    type: reference
    reason: "Authoritative metadata and maintenance standards"
---

# Shared Documentation Workflow

## Document placement [placement]

Put reusable standards, patterns, and administration guidance in `~/dev_doc/doc/`.
This is also the Wissenswiki documentation-domain root. Keep technical
`agent-instructions/`, `skills/`, `scripts/` and `.github/` entry points at the
repository root.
Keep product concepts, feature requirements, project-specific architecture,
and implementation instructions in the owning repository's documentation
folder. Link related documents instead of copying their content.

Use the
[Documentation Organization Guide](../doc/documentation-guidelines/DOCUMENTATION_ORGANIZATION_GUIDE.md)
as the authoritative source for placement and folder structure.

## Documentation workflow [workflow]

Before editing, inspect the repository's instructions, the target documents,
and the worktree state. For documentation tasks, review the descriptions of
all documentation skills and load the applicable skill instructions before
making changes. Preserve unrelated work and keep edits focused.

After editing, check links, headings, metadata, naming, and consistency
affected by the change. Validate documentation with the smallest applicable
check; do not commit unless explicitly asked.

## Document structure [structure]

Follow the
[Documentation Maintenance Standards](../doc/documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md)
for headers, timestamps, semantic block identifiers, relations, and status.
Use uppercase filenames with a lowercase `.md` extension for new documents.
Keep current behavior distinct from proposals and unresolved decisions. When a
folder contains multiple documents, maintain a navigable README index.
