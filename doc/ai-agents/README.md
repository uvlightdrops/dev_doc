---
title: AI Agents
project: dev_doc
agent_project: ki-ecosystem
scope: navigation
purpose: Index for shared AI-agent architecture reviews and conceptual models
created: "2026-11-12 10"
updated: "2026-11-12 10"
update_reason: Create a focused topic index for shared AI-agent documents
status: active
semantic_blocks:
  - documents
relations:
  - target: AI_AGENT_ARCHITECTURE_REVIEW.md
    type: child
    reason: Historical review of AI architecture and integration patterns
  - target: AI_AGENT_INTELLIGENCE_ARCHITECTURE.md
    type: child
    reason: Conceptual model and proposed intelligence roadmap for agents
---

# AI Agents

These shared documents cover complementary concerns and are not implementation
status reports. The architecture review is a dated assessment of
kicli-code-assist; the intelligence architecture is a conceptual proposal
using IA3 SimWorld as an example.

## Documents [documents]

```yaml block-meta
updated: "2026-11-12 10"
status: active
relations:
  - target: "AI_AGENT_ARCHITECTURE_REVIEW.md#executive-summary"
    type: reference
    reason: Entry point to the dated review of AI architecture and safety patterns
  - target: "AI_AGENT_INTELLIGENCE_ARCHITECTURE.md#agent-intelligence-levels"
    type: reference
    reason: Entry point to the conceptual model of agent intelligence
  - target: "../README.md#knowledge-folders"
    type: reference
    reason: Shared knowledge hub lists this topic folder
  - target: "../INDEX.md#how-files-are-organized"
    type: reference
    reason: Detailed file index groups the AI-agent topic
  - target: "../INDEX.md#technical-guides"
    type: reference
    reason: Detailed index links readers to this topic folder
  - target: "../INDEX.md#search-tips"
    type: reference
    reason: Search guidance directs readers to this topic index
  - target: "../documentation-guidelines/DOCUMENTATION_STRUCTURE_MAP.md#navigation-decision-tree"
    type: reference
    reason: Navigation decision map points to the AI-agent topic
```

| Document | Focus |
|---|---|
| [AI agent architecture review](AI_AGENT_ARCHITECTURE_REVIEW.md) | Historical review of architecture, safety, LLM providers and context management |
| [AI agent intelligence architecture](AI_AGENT_INTELLIGENCE_ARCHITECTURE.md) | Agent-intelligence concepts, hybrid simulation layers, feedback and a proposed roadmap |

The review's implementation observations are time-bound and should be checked
against the current project. The intelligence roadmap is a proposal, not
evidence that its stages have been implemented.
