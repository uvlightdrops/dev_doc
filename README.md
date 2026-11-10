---
project: dev_doc
agent_project: ki-ecosystem
scope: navigation
purpose: Entry point for shared documentation and agent tooling
created: null
updated: "2026-11-10 20"
update_reason: Separate documentation content from technical entry points
status: active
semantic_blocks: [repository-layout, starting-points]
relations:
  - target: doc/README.md
    type: child
    reason: Shared knowledge and Wissenswiki documentation domain
  - target: agent-instructions/README.md
    type: reference
    reason: Cross-project agent instructions
---

# Cross-project developer documentation

Shared standards, architecture patterns and administration guides for the KI
ecosystem live in [`doc/`](doc/README.md). Project-specific implementation
documentation stays in the owning project's documentation folder.

## Repository layout [repository-layout]

```text
dev_doc/
├── README.md
├── doc/                         # Knowledge content; Wissenswiki domain root
│   ├── README.md
│   ├── INDEX.md
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

For Wissenswiki, select `~/dev_doc/doc` as the documentation source. Keep
additional relation data outside this source tree. Technical instructions and
skills deliberately remain outside the knowledge domain; project symlinks to
`dev_doc/skills` do not change.

## Starting points [starting-points]

| Audience | Start here |
|---|---|
| Knowledge readers | [Documentation hub](doc/README.md), [detailed index](doc/INDEX.md) |
| Documentation maintainers | [Documentation standards](doc/documentation-guidelines/README.md) |
| Developers and operators | [Technical guides](doc/README.md#technical-guides-technical-guides) |
| Copilot users | [Memory guidance](doc/copilot-memory/README.md) |
| Agents | [Shared instructions](agent-instructions/README.md), [skill registry](skills/README.md) |

Historical proposals stay in their documented archive/reference sections.
Session reports and product implementation details belong to the relevant
workspace or project, not to this shared standards collection.
