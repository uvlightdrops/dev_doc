---
project: dev_doc
agent_project: ki-ecosystem
scope: navigation
purpose: Knowledge-content hub and documentation source for Wissenswiki
created: "2026-11-10 20"
updated: "2026-11-10 20"
status: active
semantic_blocks: [knowledge-folders, technical-guides, domain-boundary]
relations:
  - target: ../README.md
    type: parent
    reason: Repository entry point and technical tooling
  - target: INDEX.md
    type: child
    reason: Detailed topic and audience index
---

# Shared knowledge

This folder contains the shared documentation content. The
[detailed index](INDEX.md) explains individual topics and their intended
audiences; existing document status and historical notes remain authoritative.

## Knowledge folders [knowledge-folders]

| Folder | Purpose |
|---|---|
| [architecture/](architecture/) | Shared workbench and runtime-settings architecture |
| [cli-management/](cli-management/README.md) | CLI architecture references and archived designs |
| [copilot-memory/](copilot-memory/README.md) | Memory, session continuity and documentation workflow |
| [documentation-guidelines/](documentation-guidelines/README.md) | Organization, metadata, semantic blocks, relations and visualization |
| [server-mngm/](server-mngm/SERVER_MANAGEMENT_README.md) | Historical server-management proposals; not evidence of implemented behavior |
| [wissenswiki/](wissenswiki/KONZEPT.md) | Wiki concept and [usage guidance](wissenswiki/BEDIENUNG.md) |

## Technical guides [technical-guides]

| Document | Purpose |
|---|---|
| [Deployment standards](DEPLOYMENT_STANDARDS.md) | Shared deployment conventions |
| [Multi-repository orchestration](MULTI_REPO_ORCHESTRATION_STUDY.md) | Dependency and release coordination |
| [Service orchestration](SERVICE_ORCHESTRATION_PATTERN.md) | stackctl and supervision patterns |
| [TUI strategy](TUI_STRATEGY_CLARIFICATION.md) | Multi-frontend architecture |
| [Agent architecture review](ai_agent_architecture_review.md) | Implementation-pattern analysis |
| [Agent intelligence](ai_agent_intelligence_architecture.md) | Cognitive/generative agent concepts |
| [Script organization](script_organization_standards.md) | Python and shell code organization |
| [Security guidelines](security_guidelines.md) | Secrets, secure coding and deployment guidance |
| [Shell execution](shell_execution_patterns.md) | Shell execution and process-handling patterns |
| [Textual practices](textual_tui_best_practices.md) | Terminal UI development |
| [YAML administration](yaml_config_administration.md) | Layered configuration and administration |

## Wissenswiki boundary [domain-boundary]

Use `~/dev_doc/doc` as the documentation-domain source to include these files
and their subfolders without importing the technical skill collection.
Keep writable wiki relations separate from documentation sources.

[Agent instructions](../agent-instructions/README.md), [skills](../skills/README.md)
and scripts stay at repository level. Their entry-point paths are intentionally
stable; moving content does not grant filesystem access to an agent.
