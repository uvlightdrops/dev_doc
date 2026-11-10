---
title: "Shared Workspace Access"
project: KI-Ecosystem
agent_project: dev_doc
scope: agent-instructions
purpose: "Records the shared development and documentation workspaces available for ecosystem tasks and their intended use."
created: "2026-11-10T06:52:00+01:00"
updated: "2026-11-10T06:52:00+01:00"
update_reason: "Documented shared workspace access for agents"
status: active
semantic_blocks:
  - accessible-workspaces
  - usage-boundary
relations:
  - target: "README.md"
    type: parent
    reason: "Shared agent instructions index"
---

# Shared Workspace Access

## Accessible workspaces [accessible-workspaces]

For tasks in this environment, you have access to `~/dev_flow/` and its
subdirectories, as well as `~/dev_doc/`. Use these workspaces to inspect
related projects, shared standards, and documentation when they are relevant
to the task.

## Use and boundaries [usage-boundary]

Access does not imply that every neighboring project should be changed.
Preserve unrelated worktree changes and edit another repository only when the
task calls for it. Keep project-specific implementation documentation with
its project; put reusable standards and patterns in `~/dev_doc/`.
