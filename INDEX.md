---
title: Dev_doc File Index
project: Cross-Project
scope: Navigation & Reference
status: active
created: 2026-11-05T23:30:00+01:00
updated: 2026-11-05T23:30:00+01:00
update_reason: Complete reorganization of dev_doc folder structure
---

# 📑 Complete File Index

**Quick Navigation:** [Documentation Guidelines](#-documentation-guidelines) | [Copilot Memory](#-copilot-memory) | [Architecture](#-architecture) | [Technical Guides](#-technical-guides)

---

## 📋 Documentation Guidelines

These files define how to organize, maintain, and evolve documentation across the ecosystem.

### `documentation-guidelines/DOCUMENTATION_ORGANIZATION_GUIDE.md`
- **Purpose:** Folder structure and organization standards for project documentation
- **Audience:** Documentation maintainers, tech leads
- **Key Topics:** 
  - Generic patterns → `~/dev_doc/`
  - Project-specific → `project/doc/`
  - Subfolder organization (architecture/, features/, guides/, deployment/, tracking/)
  - Cross-reference best practices
- **When to Read:** Starting a new project doc structure or reorganizing existing docs

### `documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md`
- **Purpose:** YAML header standards, archival policy, versioning
- **Audience:** Documentation maintainers, Copilot
- **Key Topics:**
  - Required YAML metadata (title, status, created, updated, cross_references)
  - Status values (active, archived, draft, deprecated)
  - Timestamp format (ISO 8601+01:00, year 2026)
  - Archive vs. delete policy
- **When to Read:** Before creating new docs or modifying headers

### `documentation-guidelines/DOCUMENTATION_QUALITY_QUERY.md`
- **Purpose:** Quality checklist for documentation
- **Audience:** Doc reviewers, maintainers
- **Key Topics:**
  - Accuracy, clarity, completeness
  - Links verification
  - Code examples validation
  - Audience appropriateness
- **When to Read:** When reviewing or auditing documentation

### `documentation-guidelines/DOCUMENTATION_STRUCTURE_MAP.md`
- **Purpose:** Visual maps showing knowledge organization across projects
- **Audience:** Architects, documentation planners
- **Key Topics:**
  - How knowledge flows between levels
  - Relationships between doc folders
  - Dependency visualization
- **When to Read:** Understanding overall documentation structure

---

## 🧠 Copilot Memory

These files manage AI session memory, checkpoint tracking, and documentation behavior.

### `copilot-memory/COPILOT_MEMORY_MAP.md`
- **Purpose:** How to use Copilot session memory (store, vote, management)
- **Audience:** Copilot users, session coordinators
- **Key Topics:**
  - User vs. repository memory scope
  - When to store/upvote/downvote
  - Memory lifecycle
  - Best practices for durability
- **When to Read:** Before working with Copilot memory in sessions

### `copilot-memory/COPILOT_SESSION_INDEX.md`
- **Purpose:** Checkpoint tracking and session history
- **Audience:** Project coordinators, tech leads
- **Key Topics:**
  - Session metadata
  - Checkpoint archival
  - Continuity across sessions
  - Artifact management
- **When to Read:** Finding prior work or understanding session flow

### `copilot-memory/COPILOT_DOCUMENTATION_BEHAVIOR.md`
- **Purpose:** How Copilot should document and maintain docs during sessions
- **Audience:** Copilot, documentation-focused sessions
- **Key Topics:**
  - Session documentation patterns
  - Checkpoint content guidelines
  - Archival procedures
- **When to Read:** Setting up documentation-focused sessions

---

## 🏗️ Architecture

System design patterns, multi-layer architecture, and integration patterns.

### `architecture/` (Folder)
- **Purpose:** Multi-layer architecture documentation and design patterns
- **Contents:** Integration patterns, component relationships, system topology
- **Audience:** Architects, system designers
- **Key Topics:**
  - Layer separation (orchestration, service, application)
  - Integration points
  - Data flow between layers
  - Scalability patterns

---

## 💻 CLI Management

CLI architecture patterns and delegation strategies.

### `cli-management/CLI_ARCHITECTURE_ROADMAP.md`
- **Purpose:** CLI architecture phases and delegation pattern
- **Audience:** Backend developers, CLI implementers
- **Key Topics:**
  - Phase 0-3 implementation plan
  - Shared command library in ki-core
  - Delegation pattern (apps → ki_core.commands → yaml-cfg-wizard)
  - Echo function abstraction (cli-agnostic)
- **When to Read:** Implementing CLI commands or adding new command categories

### `cli-management/IMPLEMENTATION_STATUS.md`
- **Purpose:** Current progress on CLI architecture implementation
- **Audience:** CLI team leads
- **Key Topics:**
  - Phase completion status
  - Remaining tasks
  - Known blockers
- **When to Read:** Checking implementation progress

---

## 🖥️ Server Management (Historical)

Historical server management design documents (mostly superseded by stackctl).

### `server-mngm/SERVER_MANAGEMENT_README.md`
- **Purpose:** Executive summary of unified server management system design
- **Status:** Historical proposal (Oct 28, 2026) — not implemented
- **Audience:** Reference only
- **Key Topics:**
  - Original design goals (ghost process elimination)
  - 6-phase implementation roadmap
  - Architecture Options A, B, C
- **Note:** See `SERVICE_ORCHESTRATION_PATTERN.md` for current approach

### `server-mngm/SERVER_MANAGEMENT_DESIGN.md`
- **Purpose:** Detailed architecture analysis and options
- **Status:** Historical

### `server-mngm/SERVER_MANAGEMENT_DECISIONS.md`
- **Purpose:** Architectural decision rationale
- **Status:** Historical

### `server-mngm/SERVER_MANAGEMENT_FLOWS.md`
- **Purpose:** Data flow diagrams and state transitions
- **Status:** Historical

### `server-mngm/SERVER_MANAGEMENT_IMPLEMENTATION.md`
- **Purpose:** Implementation guide and code sketches
- **Status:** Historical

---

## 📖 Technical Guides

Root-level technical documentation on specific domains.

### `yaml_config_administration.md`
- **Purpose:** YAML configuration system deep dive
- **Audience:** Config admins, DevOps, backend developers
- **Key Topics:**
  - 8-layer configuration merge (creds → env → runtime → stage → profile → ki.yaml → defaults → schema)
  - Validation and schema-driven config
  - Multi-environment setup
  - Secrets management best practices
- **Implemented In:** All 6 ecosystem projects (ki-core/src/ki_core/config.py)
- **When to Read:** Setting up app configuration or understanding config precedence

### `security_guidelines.md`
- **Purpose:** Security best practices across ecosystem
- **Audience:** All developers before committing code
- **Key Topics:**
  - Secrets management (creds.yaml, env vars)
  - Secure coding practices
  - Code review for vulnerabilities
  - Deployment security
  - Sensitive data handling
- **When to Read:** Before committing, writing security-sensitive code, or deploying

### `shell_execution_patterns.md`
- **Purpose:** Shell scripting standards and patterns
- **Audience:** Script developers, DevOps
- **Key Topics:**
  - Error handling and exit codes
  - Process management
  - Logging and debugging
  - Signal handling
  - Resource cleanup
- **When to Read:** Writing shell scripts or debugging automation

### `script_organization_standards.md`
- **Purpose:** Code organization standards (Python and shell)
- **Audience:** All developers
- **Key Topics:**
  - Module structure and imports
  - Naming conventions
  - Function organization
  - Docstring standards
  - Testing patterns
- **When to Read:** Starting new scripts or refactoring code organization

### `textual_tui_best_practices.md`
- **Purpose:** Best practices for Terminal User Interfaces (Textual framework)
- **Audience:** Frontend/TUI developers
- **Key Topics:**
  - Widget composition and event handling
  - Layout patterns
  - Reactive programming in TUIs
  - Performance optimization
  - Accessibility in terminal UIs
- **When to Read:** Building TUI applications or improving TUI code quality

### `ai_agent_intelligence_architecture.md`
- **Purpose:** AI agent theory and 4-stage implementation plan
- **Audience:** AI/ML engineers, agent system designers
- **Key Topics:**
  - Cognitive AI vs. Generative AI
  - 4-stage implementation (Foundation → Reasoning → Autonomy → Optimization)
  - IA3 SimWorld agent architecture
  - Integration with reasoning tracers
- **When to Read:** Designing agent systems or understanding AI architecture decisions

### `ai_agent_architecture_review.md`
- **Purpose:** Review of agent architecture implementation patterns
- **Audience:** Architecture reviewers, agent developers
- **Key Topics:**
  - Architecture pattern analysis
  - Reasoning and autonomy systems
  - Multi-agent coordination
  - Performance characteristics
- **When to Read:** Evaluating or refining agent implementations

### `MULTI_REPO_ORCHESTRATION_STUDY.md`
- **Purpose:** Multi-project coordination and dependency management
- **Audience:** Tech leads, ecosystem maintainers
- **Key Topics:**
  - 6-project dependency graph
  - Version alignment strategies
  - Release coordination patterns
  - Shared dependency management
- **When to Read:** Setting up multi-project builds, releases, or version management

### `SERVICE_ORCHESTRATION_PATTERN.md`
- **Purpose:** Service orchestration using stackctl + optional Layer 1 supervision
- **Audience:** DevOps, deployment architects
- **Key Topics:**
  - Layer 2 (stackctl): Full-stack orchestration
  - Layer 1 (optional): Single-service supervision
  - Configuration best practices (stack.yaml, ki.yaml)
  - Multi-target deployments (local, minikube, K8s)
  - Service dependencies and health checks
- **Status:** Active (Nov 5, 2026) — synthesized from ki-knowledge production + kicli-code-assist design
- **When to Read:** Designing deployment strategies or setting up orchestration

### `TUI_STRATEGY_CLARIFICATION.md`
- **Purpose:** TUI 2.0 multi-target strategy and architecture decisions
- **Audience:** Frontend architects, TUI developers
- **Key Topics:**
  - Multi-target approach (WebUI + TUI + future GTK)
  - Abstract Frontend Specification pattern
  - Phase boundaries (Scaffold → Integration → Intelligence → Performance)
  - Relationship between TUI and WebUI
- **When to Read:** Planning new frontend implementations or understanding TUI strategy

---

## 🗂️ How Files Are Organized

### By Purpose

| Purpose | Files |
|---------|-------|
| **Guidelines** | All files in `documentation-guidelines/` |
| **Memory Management** | All files in `copilot-memory/` |
| **Configuration** | `yaml_config_administration.md` |
| **Security** | `security_guidelines.md` |
| **Scripting** | `shell_execution_patterns.md`, `script_organization_standards.md` |
| **Frontend** | `textual_tui_best_practices.md`, `TUI_STRATEGY_CLARIFICATION.md` |
| **AI/Agents** | `ai_agent_*.md` |
| **Deployment** | `SERVICE_ORCHESTRATION_PATTERN.md`, `MULTI_REPO_ORCHESTRATION_STUDY.md` |
| **CLI** | Files in `cli-management/` |
| **Architecture** | Files in `architecture/` |
| **Historical** | Files in `server-mngm/` |

### By Audience

| Audience | Key Files |
|----------|-----------|
| **Doc Maintainers** | `documentation-guidelines/ORGANIZATION_GUIDE.md`, `MAINTENANCE_STANDARDS.md` |
| **Backend Devs** | `script_organization_standards.md`, `security_guidelines.md`, `yaml_config_administration.md` |
| **Frontend/TUI Devs** | `textual_tui_best_practices.md`, `TUI_STRATEGY_CLARIFICATION.md` |
| **DevOps/SRE** | `yaml_config_administration.md`, `SERVICE_ORCHESTRATION_PATTERN.md`, `security_guidelines.md` |
| **AI/ML Engineers** | `ai_agent_intelligence_architecture.md`, `ai_agent_architecture_review.md` |
| **Tech Leads** | All guidelines + `MULTI_REPO_ORCHESTRATION_STUDY.md` |
| **Copilot Users** | `copilot-memory/` folder |
| **Architects** | `architecture/`, `SERVICE_ORCHESTRATION_PATTERN.md`, `MULTI_REPO_ORCHESTRATION_STUDY.md` |

### By Status

| Status | Files |
|--------|-------|
| **Active** | All files except `server-mngm/` |
| **Historical (Reference Only)** | All files in `server-mngm/` (Oct 28 design, not implemented) |

---

## 🔍 Search Tips

**"Where do I find...?"**

| Question | File(s) |
|----------|---------|
| How to structure project docs? | `documentation-guidelines/ORGANIZATION_GUIDE.md` |
| What should doc headers contain? | `documentation-guidelines/MAINTENANCE_STANDARDS.md` |
| How does Copilot memory work? | `copilot-memory/MEMORY_MAP.md` |
| How to track sessions/checkpoints? | `copilot-memory/SESSION_INDEX.md` |
| How config precedence works? | `yaml_config_administration.md` |
| What are security best practices? | `security_guidelines.md` |
| How to write shell scripts? | `shell_execution_patterns.md` |
| How to organize Python code? | `script_organization_standards.md` |
| How to build a TUI? | `textual_tui_best_practices.md` |
| How AI agents work? | `ai_agent_intelligence_architecture.md` |
| How to deploy services? | `SERVICE_ORCHESTRATION_PATTERN.md` |
| How to coordinate multiple repos? | `MULTI_REPO_ORCHESTRATION_STUDY.md` |
| How should CLI commands work? | `cli-management/CLI_ARCHITECTURE_ROADMAP.md` |

---

## 📊 File Statistics

- **Total Files:** 20 (5 in subfolders, 15 root-level)
- **Total Folders:** 4 (`documentation-guidelines/`, `copilot-memory/`, `architecture/`, `cli-management/`, `server-mngm/`)
- **Active Files:** 15 (Nov 2026)
- **Historical Files:** 5 (`server-mngm/`)

---

## ✅ Maintenance Checklist

### For New Contributors
- [ ] Read `documentation-guidelines/ORGANIZATION_GUIDE.md`
- [ ] Understand folder structure from this INDEX
- [ ] Pick role from README and find your start file

### For Documentation Updates
- [ ] Update relevant file(s)
- [ ] Update YAML header (esp. `updated` and `update_reason`)
- [ ] Update this INDEX if adding new files
- [ ] Link new docs from README

### For Archival
- [ ] Update status to `archived` in YAML header
- [ ] Move to `~/dev_doc/archive/` if superseded
- [ ] Document rationale in archive README

---

**Last Updated**: 2026-11-05  
**Total Size**: ~24 KB (index + all files)  
**Status**: ✅ Complete & Current
