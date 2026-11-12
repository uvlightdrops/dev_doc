---
project: dev_doc
agent_project: ki-ecosystem
scope: navigation
purpose: Knowledge-content hub and documentation source for Wissenswiki
created: 2026-11-10 20
updated: "2026-11-12 10"
status: active
semantic_blocks:
- knowledge-folders
- technical-guides
- domain-boundary
relations:
- target: ../README.md
  type: parent
  reason: Repository entry point and technical tooling
- target: INDEX.md
  type: child
  reason: Detailed topic and audience index
update_reason: Group AI-agent documents and link architecture navigation with layout research
---

# Shared knowledge

This folder contains the shared documentation content. The
[detailed index](INDEX.md) explains individual topics and their intended
audiences; existing document status and historical notes remain authoritative.

## Knowledge folders [knowledge-folders]

```yaml block-meta
updated: "2026-11-12 10"
status: active
relations:
  - target: "INDEX.md#how-files-are-organized"
    type: reference
    reason: Detailed index of the knowledge folders by purpose and audience
  - target: "../README.md#repository-layout"
    type: reference
    reason: Repository-level map includes the shared topic folder
  - target: "ai-agents/README.md#documents"
    type: reference
    reason: Index of the AI-agent documents in this topic folder
  - target: "architecture/README.md#architecture-index"
    type: child
    reason: Shared architecture topics including layout and workspace research
```

| Folder | Purpose |
|---|---|
| [architecture/](architecture/README.md) | Shared workbench, runtime-settings and layout/workspace architecture |
| [cli-management/](cli-management/README.md) | CLI architecture references and archived designs |
| [copilot-memory/](copilot-memory/README.md) | Memory, session continuity and documentation workflow |
| [documentation-guidelines/](documentation-guidelines/README.md) | Organization, metadata, semantic blocks, relations and visualization |
| [ai-agents/](ai-agents/README.md) | Shared AI-agent architecture reviews and conceptual models |
| [server-mngm/](server-mngm/SERVER_MANAGEMENT_README.md) | Historical server-management proposals; not evidence of implemented behavior |
| [wissenswiki/](wissenswiki/KONZEPT.md) | Wiki concept and [usage guidance](wissenswiki/BEDIENUNG.md) |

## Technical guides [technical-guides]

```yaml block-meta
updated: "2026-11-12 10"
status: active
relations:
  - target: "INDEX.md#technical-guides"
    type: reference
    reason: Detailed index of the technical guides and their audiences
```

| Document | Purpose |
|---|---|
| [Deployment standards](DEPLOYMENT_STANDARDS.md) | Shared deployment conventions |
| [Multi-repository orchestration](MULTI_REPO_ORCHESTRATION_STUDY.md) | Dependency and release coordination |
| [Service orchestration](SERVICE_ORCHESTRATION_PATTERN.md) | stackctl and supervision patterns |
| [TUI strategy](TUI_STRATEGY_CLARIFICATION.md) | Multi-frontend architecture |
| [Script organization](script_organization_standards.md) | Python and shell code organization |
| [Security guidelines](security_guidelines.md) | Secrets, secure coding and deployment guidance |
| [Shell execution](shell_execution_patterns.md) | Shell execution and process-handling patterns |
| [Textual practices](textual_tui_best_practices.md) | Terminal UI development |
| [YAML administration](yaml_config_administration.md) | Layered configuration and administration |

## Wissenswiki boundary [domain-boundary]

```yaml block-meta
updated: "2026-11-11 05"
status: active
relations:
  - target: "INDEX.md#content-boundary"
    type: reference
    reason: Matching description of the documentation-domain boundary
```

Use `~/dev_doc/doc` as the documentation-domain source to include these files
and their subfolders without importing the technical skill collection.
Keep writable wiki relations separate from documentation sources.

[Agent instructions](../agent-instructions/README.md), [skills](../skills/README.md)
and scripts stay at repository level. Their entry-point paths are intentionally
stable; moving content does not grant filesystem access to an agent.
