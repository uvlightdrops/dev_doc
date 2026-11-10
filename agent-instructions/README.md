---
title: "Shared Agent Instructions"
project: KI-Ecosystem
agent_project: dev_doc
scope: agent-instructions
purpose: "Central index for reusable instructions applied by Copilot and other coding agents across KI ecosystem repositories."
created: "2026-11-10T06:52:00+01:00"
updated: "2026-11-10T06:52:00+01:00"
update_reason: "Initial shared instructions structure"
status: active
semantic_blocks:
  - instruction-scope
  - instruction-index
  - adding-guidance
  - instruction-precedence
relations:
  - target: "../.github/copilot-instructions.md"
    type: reference
    reason: "Repository-wide instructions for the central documentation repository"
  - target: "WORKSPACE_ACCESS.md"
    type: child
    reason: "Shared workspace access information"
  - target: "DOCUMENTATION.md"
    type: child
    reason: "Reusable documentation workflow guidance"
  - target: "CODING.md"
    type: child
    reason: "Reusable coding workflow guidance"
---

# Shared Agent Instructions

## Scope [instruction-scope]

This folder contains reusable instructions for work across the KI ecosystem.
It supplements each repository's own `AGENTS.md` and
`.github/copilot-instructions.md`; it does not replace project-specific
architecture, setup, or implementation guidance.

The central repository also has its own instructions in
[`.github/copilot-instructions.md`](../.github/copilot-instructions.md).
Project instruction files should explicitly direct agents to this index and
the relevant topic files. A link without an instruction to read it is not
sufficient.

## Instruction index [instruction-index]

| File | Status | Purpose | Semantic blocks |
|---|---|---|---|
| [WORKSPACE_ACCESS.md](WORKSPACE_ACCESS.md) | active | Shared workspace access and scope | accessible-workspaces, usage-boundary |
| [DOCUMENTATION.md](DOCUMENTATION.md) | active | Cross-project documentation workflow and organization | placement, workflow, structure |
| [CODING.md](CODING.md) | active | Cross-project coding workflow and change discipline | repository-context, implementation, validation |

## Adding guidance [adding-guidance]

Add reusable guidance to the closest existing topic file. Create another
uppercase-named Markdown file in this folder only when a distinct topic needs
its own durable instructions; then add it to this index and update the
relevant central and project entry points.

Keep general rules here and implementation details in the owning project.
Prefer a focused instruction over repeating a full standard that already has
an authoritative document or skill.

## Instruction precedence [instruction-precedence]

Follow applicable platform and safety requirements first, then the
repository-specific instructions and the relevant shared guidance. If they
conflict, surface the conflict rather than silently overriding a more specific
project rule.
