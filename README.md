---
project: dev_doc
agent_project: ki-ecosystem
scope: navigation
purpose: Entry point for shared documentation and agent tooling
created: "2026-11-05 11"
updated: "2026-11-12 10"
update_reason: Include the AI-agents topic folder in repository navigation
status: active
semantic_blocks: [repository-layout, starting-points, wissenswiki-source]
relations:
  - target: doc/README.md
    type: child
    reason: Shared knowledge and Wissenswiki documentation domain
  - target: agent-instructions/README.md
    type: reference
    reason: Cross-project agent instructions
  - target: skills/README.md
    type: reference
    reason: Central skill registry and project integration
---

# Shared documentation

Shared standards, architecture patterns and administration guides for the KI
ecosystem live in [`doc/`](doc/README.md). Project-specific implementation
documentation stays in the owning project's documentation folder.

## Repository layout [repository-layout]

```yaml block-meta
updated: "2026-11-12 10"
status: active
relations:
  - target: "doc/README.md#knowledge-folders"
    type: reference
    reason: Detailed knowledge-folder navigation
  - target: "doc/ai-agents/README.md#documents"
    type: reference
    reason: New topic index for shared AI-agent documentation
```

```text
dev_doc/
├── README.md
├── doc/                         # Knowledge content; Wissenswiki domain root
│   ├── README.md
│   ├── INDEX.md
│   ├── ai-agents/
│   ├── architecture/
│   ├── cli-management/
│   ├── copilot-memory/
│   ├── documentation-guidelines/
│   ├── server-mngm/
│   ├── wissenswiki/
│   └── *.md                     # Cross-project technical guides
├── agent-instructions/          # Stable instruction entry points
├── skills/                      # Shared agent skills; keep symlink targets stable
├── scripts/                     # Documentation tooling
└── .github/                     # Copilot instructions, prompts and skills link
```

## Starting points [starting-points]

| Audience | Start here |
|---|---|
| Knowledge readers | [Documentation hub](doc/README.md), [detailed index](doc/INDEX.md) |
| Documentation maintainers | [Documentation standards](doc/documentation-guidelines/README.md) |
| Developers and operators | [Technical guides](doc/README.md#technical-guides-technical-guides) |
| Copilot users | [Memory guidance](doc/copilot-memory/README.md) |
| Agents | [Repository instructions](.github/copilot-instructions.md), [shared instructions](agent-instructions/README.md), [skill registry](skills/README.md) |

Historical proposals stay in their documented archive/reference sections.
Session reports and product implementation details belong to the relevant
workspace or project, not to this shared standards collection.

## Wissenswiki source [wissenswiki-source]

Select `~/dev_doc/doc` as the documentation-domain source, not the repository
root. This includes shared knowledge and its subfolders without importing the
technical instructions, skills or local development environment.

Keep writable relation data in a separate directory outside the source tree.
Changing the domain source changes relative document paths; review existing
saved relations before switching an already configured domain. Moving files
does not automatically migrate those relations.

The technical entry points `agent-instructions/`, `skills/`, `scripts/` and
`.github/` stay at repository level. Project symlinks to `dev_doc/skills`
remain unchanged. These instructions describe intended workspace use; they
do not grant Copilot filesystem or network permissions.
