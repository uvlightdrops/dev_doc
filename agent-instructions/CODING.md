---
title: "Shared Coding Workflow"
project: KI-Ecosystem
agent_project: dev_doc
scope: agent-instructions
purpose: "Sets project-agnostic expectations for repository exploration, surgical implementation, and validation."
created: "2026-11-10T06:52:00+01:00"
updated: "2026-11-10T06:52:00+01:00"
update_reason: "Established shared coding instructions"
status: active
semantic_blocks:
  - repository-context
  - implementation
  - validation
relations:
  - target: "README.md"
    type: parent
    reason: "Shared agent instructions index"
---

# Shared Coding Workflow

## Repository context [repository-context]

Read applicable repository instructions and inspect the relevant code before
changing it. Search for existing patterns and tests to understand expected
behavior. Preserve unrelated changes in the worktree; do not assume the
repository is clean.

## Implementation [implementation]

Make the smallest complete change that addresses the request. Reuse existing
types, helpers, conventions, and dependencies. Keep component responsibilities
clear, surface errors through project-standard mechanisms, and avoid unrelated
refactoring. Do not commit unless explicitly asked.

## Validation [validation]

Run the smallest existing tests, checks, or build that directly covers the
change. Add or update tests when behavior changes. Report checks that could
not be run and any remaining uncertainty plainly.
