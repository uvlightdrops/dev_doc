# Documentation Organization Guide

**Status:** Active Standard  
**Version:** 1.0  
**Last Updated:** 2026-10-19  
**Scope:** Cross-project organization for ~/dev_flow ecosystem

---

## Overview

This guide provides a framework for organizing documentation across the KI Ecosystem projects in ~/dev_flow. The goal is to:

1. **Separate generic knowledge** (best practices, patterns, architecture concepts) into ~/dev_doc
2. **Keep project-specific implementation** in each project's docs/ folder
3. **Split admin-friendly quick guides** from deep technical research
4. **Enable discoverability** through consistent folder structure

---

## 1. Documentation Location Strategy

### ~/dev_doc/doc (Shared, Cross-Project)

**Purpose:** Generic, reusable knowledge applicable to multiple projects or the entire ecosystem

Use `~/dev_doc/doc/` as the knowledge-content root and Wissenswiki
documentation source. Keep `agent-instructions/`, `skills/`, `scripts/` and
`.github/` at repository level so technical entry points remain stable.

**Examples:**
- `BEST_PRACTICES_*.md` - General patterns (AI architecture, testing, CLI design, etc.)
- `ARCHITECTURE_PATTERNS.md` - System design principles used across projects
- `ADMIN_QUICKSTART_*.md` - Setup/maintenance for non-expert operators
- `STANDARDS_*.md` - Coding standards, naming conventions, etc.
- `DEPLOYMENT_REFERENCE.md` - General deployment workflow (applies to all projects)

**Characteristics:**
- Minimal code snippets (links to projects instead)
- No project-specific file paths
- Usable as-is by any project in the ecosystem
- Version-controlled in ~/dev_doc (git), not in individual projects

### ~/dev_flow/{project}/docs (Project-Specific)

**Purpose:** Implementation details, setup, usage, architecture of THIS project

**Examples:**
- Feature documentation (user guides for specific features)
- Project-specific architecture decisions
- Integration guides with other projects
- Troubleshooting for this project's issues
- CLI command reference
- Configuration schema explanation

**Characteristics:**
- Project-specific file paths and examples
- Implementation patterns used in THIS project
- How this project fits into the ecosystem
- Can reference ~/dev_doc for general principles

---

## 2. Document Categorization Within Projects

### Each project's docs/ should have subfolders:

```
{project}/docs/
├── README.md                          # Project overview (keep top-level)
├── INDEX.md                           # Navigation hub for all docs
├── architecture/                      # System design & structure
│   ├── ARCHITECTURE.md                # Overall architecture
│   ├── DATA_MODEL.md                  # Data structures
│   ├── API_REFERENCE.md               # Public APIs
│   └── DESIGN_DECISIONS.md            # Why we chose X over Y
├── features/                          # Feature documentation
│   ├── FEATURE_A.md                   # How to use/implement feature A
│   ├── FEATURE_B.md                   # How to use/implement feature B
│   └── WORKFLOW_*.md                  # End-to-end user workflows
├── guides/                            # How-to & operational guides
│   ├── INSTALLATION.md                # How to install/setup
│   ├── CONFIGURATION.md               # Configuration options
│   ├── ADMIN_GUIDE.md                 # Admin operations (non-expert friendly)
│   ├── TROUBLESHOOTING.md             # Common issues & solutions
│   └── DEPLOYMENT.md                  # How to deploy this project
├── refactorings/                      # Refactoring & tech debt tracking
│   ├── REFACTORING_*.md               # What was changed & why
│   └── MIGRATION_GUIDE.md             # How to migrate from old → new
├── tracking/                          # Phase, status, session notes (keep chronological)
│   ├── PHASE_*.md                     # Phase completion summaries
│   ├── STATUS_*.md                    # Status snapshots
│   └── SESSION_*.md                   # Session notes (can be ephemeral)
└── integration/                       # Integration with other projects
    ├── KI_CORE_INTEGRATION.md         # How this project uses ki-core
    ├── YAML_CFG_INTEGRATION.md        # How this project uses yaml-cfg-wizard
    └── API_COMPATIBILITY.md           # API compatibility notes
```

---

## 3. Splitting Generic vs. Project-Specific Content

### Pattern: Best Practices Document

**Generic content** → ~/dev_doc/doc/BEST_PRACTICES_AI_ARCHITECTURE.md
```markdown
# Best Practices: AI Architecture

## Pattern 1: Provider Chain Pattern
- Use for: Multiple LLM providers with fallback
- Principle: Always have a primary + fallback
- Trade-offs: Complexity vs. reliability
- Languages: Language-agnostic

## Pattern 2: Request Logging
- Log: Provider, URL, system prompt, payload
- Why: Debugging & transparency
- Security: Filter secrets before logging
```

**Project-specific implementation** → kicli-code-assist/docs/architecture/AI_INTEGRATION.md
```markdown
# AI Integration in kicli-code-assist

## Our Implementation
We use the Provider Chain Pattern (see ~/dev_doc/doc/BEST_PRACTICES_AI_ARCHITECTURE.md).

### Configuration
- OpenAI as primary provider
- Ollama localhost:11434 as fallback
- See: llm_integration.py:50-100

### Request Logging
All requests logged to kicli-llm.log with full transparency.
(See: logging_integration.py)
```

### Pattern: Admin Guide Splitting

**Quick Admin Guide** → ~/dev_doc/doc/ADMIN_QUICKSTART_DEPLOYMENT.md
```markdown
# Quick Admin Guide: Deployment

1. SSH into production host
2. cd /opt/myapp
3. git pull origin main
4. systemctl restart myapp
5. Verify: curl http://localhost:8080/health

For troubleshooting, see DEPLOYMENT_REFERENCE.md
```

**Deep Technical Reference** → project/docs/guides/DEPLOYMENT.md
```markdown
# Deployment Reference

## Architecture
- Uses Helm charts (see charts/ directory)
- Container registry: Harbor
- Orchestration: Kubernetes

## Deployment Process
1. Build: make build
2. Push: skopeo copy ... (see Makefile)
3. Deploy: helm upgrade myapp ./charts
4. Rollback: helm rollback myapp [revision]

## Troubleshooting
- Port conflicts: Check netstat
- Image pull failures: Check Harbor credentials
```

---

## 4. Extraction Examples

### Example 1: AI_ARCHITECTURE_BEST_PRACTICES_REVIEW.md

**Current state:** In kicli-code-assist/docs/ (mixed generic + specific)

**Split into:**

1. **~/dev_doc/doc/BEST_PRACTICES_AI_ARCHITECTURE.md** (Generic)
   - Provider Chain pattern
   - Async/await patterns for LLM calls
   - Error handling principles
   - Testing strategies for AI modules

2. **kicli-code-assist/docs/architecture/AI_INTEGRATION.md** (Project-specific)
   - How we implement the pattern
   - File references: llm_integration.py, AsyncLLMIntegration
   - Our specific error handling (LLMError, RecoveryHints)
   - Our specific logging (kicli-llm.log)

### Example 2: CONFIG_VALIDATION_GUIDE.md (Exists in both ki-core and kicli-code-assist)

**Current state:** Duplicated across projects

**Unified into:**

1. **~/dev_doc/doc/CONFIG_VALIDATION_GUIDE.md** (Generic)
   - JSON Schema validation
   - Layered config resolution
   - Precedence order
   - Error reporting strategy

2. **ki-core/docs/architecture/CONFIG_SYSTEM.md** (ki-core specific)
   - Our ConfigManager implementation
   - Schema file structure
   - How to extend the schema

3. **kicli-code-assist/docs/guides/CONFIGURATION.md** (App-specific)
   - How to configure kicli-code-assist
   - Supported settings
   - Example ki.yaml files

### Example 3: CLI_ARCHITECTURE_ROADMAP.md (In 4 projects!)

**Current state:** Copied/duplicated in: ia3simworld, ki-core, ki-ecosystem, ki-knowledge, yaml_cfg_wizard

**Unified approach:**

1. **~/dev_doc/doc/CLI_ARCHITECTURE_STANDARD.md** (Generic)
   - Click/Typer patterns
   - Command hierarchy design
   - Testing CLI commands

2. **ki-core/docs/architecture/CLI_PATTERN.md** (ki-core pattern)
   - Our shared command pattern
   - echo_fn parameter for CLI-agnosticity
   - Provider delegation model

3. **{each-app}/docs/architecture/CLI.md** (App-specific)
   - How this app implements the pattern
   - Available commands
   - Integration with ki-core.commands

---

## 5. Admin-Friendly Quick Start Extraction

### Pattern: Separate complexity levels

**For Non-Admins** → ~/dev_doc/ADMIN_QUICKSTART_*.md
```markdown
# Quick Start: Setting Up a New App

1. Clone the repo
2. `pip install -e .`
3. `python app.py`
4. Done!

If something breaks, check ADMIN_GUIDE_TROUBLESHOOTING.md
```

**For Experts** → project/docs/guides/INSTALLATION.md
```markdown
# Installation & Setup

## Prerequisites
- Python 3.10+
- PostgreSQL 14+
- Redis 6.0+

## Virtual Environment
export VENV=.venv
python3 -m venv $VENV
source $VENV/bin/activate

## Dependencies
See pyproject.toml for:
- Core dependencies
- Dev dependencies (pytest, black, etc.)
- Optional dependencies (dev-mode extras)

## Configuration
See CONFIG_SCHEMA.md for all options.

## Development Mode
make dev-setup && make dev-up
```

---

## 6. Cross-Project Duplicates: Strategy

### When you find the same doc in multiple projects:

1. **Identify the generic core** → Move to ~/dev_doc
2. **Keep project-specific adaptations** → Keep in project/docs
3. **Link between them** → Use cross-references

### Example: GETTING_STARTED.md exists in multiple projects

**In ~/dev_doc/doc/GETTING_STARTED_ECOSYSTEM.md:**
```markdown
# Getting Started: KI Ecosystem

Choose your project:
- **kicli-code-assist** - AI code assistant TUI
- **ki-core** - Shared configuration & LLM infrastructure
- **yaml_cfg_wizard** - Generic YAML config tooling
- **ki-knowledge** - Knowledge management system

→ See individual project README.md for project-specific setup
```

**In each project/docs/guides/INSTALLATION.md:**
```markdown
# Installation: {Project Name}

For general ecosystem info, see ~/dev_doc/doc/GETTING_STARTED_ECOSYSTEM.md

## This Project Specifics
...
```

---

## 7. File Naming Conventions

**Generic (~/dev_doc):**
- `BEST_PRACTICES_*.md` - Reusable patterns
- `ADMIN_QUICKSTART_*.md` - Non-expert friendly
- `ADMIN_GUIDE_*.md` - Operator reference
- `STANDARDS_*.md` - Coding standards
- `ARCHITECTURE_*.md` - Design patterns

**Project-specific (project/docs/):**
- `INDEX.md` - Navigation hub
- `README.md` - Project overview
- `FEATURE_*.md` - Feature documentation
- `INTEGRATION_*.md` - Integration with other projects
- `CONFIGURATION.md` - Config reference
- `API_REFERENCE.md` - Public API docs
- `TROUBLESHOOTING.md` - Common issues
- `PHASE_*.md` - Phase summaries (keep in tracking/)

---

## 8. Cross-References Format

**Link to ~/dev_doc from project:**
```markdown
For general patterns, see ~/dev_doc/doc/BEST_PRACTICES_AI_ARCHITECTURE.md

For our implementation, see architecture/AI_INTEGRATION.md (this project)
```

**Link to project from ~/dev_doc:**
```markdown
See kicli-code-assist/docs/architecture/AI_INTEGRATION.md for a reference implementation.
```

---

## 9. Quick Reference: Where Does This Doc Go?

```
Is it reusable across multiple projects?
├─ YES → ~/dev_doc/BEST_PRACTICES_*.md or ADMIN_*.md
└─ NO
   └─ Is it step-by-step instructions for non-experts?
      ├─ YES → project/docs/guides/
      └─ NO → project/docs/{architecture|features|integration}/
```

---

**Quick Decision Tree:** Use when organizing new documentation
