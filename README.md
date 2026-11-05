# 🌍 Cross-Project Developer Documentation

**Location**: `/home/flow/dev_doc/`  
**Purpose**: Global standards, architecture patterns, and best practices shared across all KI projects

---

## 📖 Quick Navigation

### 🎯 **Core Standards** (Read These First)
- **[DOCUMENTATION_ORGANIZATION_GUIDE.md](DOCUMENTATION_ORGANIZATION_GUIDE.md)** — How to structure project docs
  - Generic patterns, admin guides → `~/dev_doc`
  - Project-specific → `project/docs`
  - Architecture, features, guides, refactorings, tracking subfolders
  - **→ Essential for doc maintainers**

- **[COPILOT_MEMORY_MAP.md](COPILOT_MEMORY_MAP.md)** — Copilot session memory management
  - User vs. repository memory scope
  - When to store, vote, downvote
  - Memory lifecycle and best practices
  - **→ For session coordination**

- **[COPILOT_SESSION_INDEX.md](COPILOT_SESSION_INDEX.md)** — Checkpoint tracking
  - Session history and continuity
  - How checkpoints preserve work
  - Session metadata and artifacts
  - **→ For project continuity**

### 🛠️ **Operations & Administration** (Daily Use)
- **[yaml_config_administration.md](yaml_config_administration.md)** — Config system deep dive
  - YAML structure and patterns
  - Validation, merging, multi-environment
  - Secrets management best practices
  - Schema-driven configuration
  - **→ Config experts read this**

- **[security_guidelines.md](security_guidelines.md)** — Security best practices
  - Secrets management (creds.yaml, env vars)
  - Code review for vulnerabilities
  - Deployment security considerations
  - Sensitive data handling
  - **→ Before committing code**

- **[shell_execution_patterns.md](shell_execution_patterns.md)** — Shell scripting standards
  - Error handling and exit codes
  - Process management
  - Logging and debugging
  - **→ For script development**

### 🏗️ **Architecture & Design** (Learning)
- **[ai_agent_intelligence_architecture.md](ai_agent_intelligence_architecture.md)** — AI agent theory
  - 4-stage implementation plan
  - Cognitive and generative AI patterns
  - IA3 SimWorld agent architecture
  - **→ For agent system design**

- **[ai_agent_architecture_review.md](ai_agent_architecture_review.md)** — Implementation review
  - Architecture patterns for agents
  - Reasoning and autonomy systems
  - **→ For architecture decisions**

- **[MULTI_REPO_ORCHESTRATION_STUDY.md](MULTI_REPO_ORCHESTRATION_STUDY.md)** — Multi-repo coordination
  - Dependency management across 6 repos
  - Version alignment strategies
  - Release coordination patterns
  - **→ For ecosystem maintenance**

### 💻 **Development Practices** (Coding)
- **[script_organization_standards.md](script_organization_standards.md)** — Script standards
  - Python and shell script organization
  - Module structure and imports
  - Naming conventions
  - **→ Before writing scripts**

- **[textual_tui_best_practices.md](textual_tui_best_practices.md)** — TUI development
  - Textual framework patterns
  - Widget composition
  - Event handling
  - **→ For CLI UI development**

### 📂 **Subdirectories** (Organized by Area)

#### `architecture/` — System design patterns
- Multi-layer architecture documentation
- Integration patterns
- Component relationships
- Read for: Design decisions, system overview

#### `cli-management/` — CLI command patterns
- Shared command structure across apps
- Delegation patterns (ki-core → app-specific CLI)
- Echo function abstraction
- **Key**: `CLI_ARCHITECTURE_ROADMAP.md` - Phases and strategy

#### `server-mngm/` — Server management
- Service control patterns
- Process orchestration
- Health checks and monitoring
- Deployment management

---

## 🔗 Relationship with `/home/flow/dev_flow/`

### **This Folder** (`~/dev_doc/`)
- ✅ Cross-project patterns and standards
- ✅ Best practices and guidelines
- ✅ Theoretical documentation
- ✅ Maintained long-term
- ✅ Language/framework-agnostic where possible

### **That Folder** (`~/dev_flow/`)
- ✅ Session-specific reports (deployment, audits)
- ✅ Current status and recent changes
- ✅ Project metadata and coordination
- ✅ Kept current for 2-4 weeks, then archived
- ✅ Links to per-project `doc/` folders

### **Per-Project Folders** (`project/docs/`)
- ✅ Implementation details
- ✅ Architecture for that specific project
- ✅ Feature documentation
- ✅ Project-specific guides
- See: `/home/flow/dev_flow/kicli-code-assist/doc/deployment/`

---

## 📊 Documentation Organization Levels

```
Level 1: Generic Cross-Project Patterns
   Location: ~/dev_doc/
   Examples: yaml_config_administration.md, security_guidelines.md
   Audience: All developers across projects
   Lifespan: Long-term, evergreen

Level 2: Project-Specific Implementation
   Location: project/docs/ (e.g., kicli-code-assist/doc/)
   Examples: ARCHITECTURE.md, deployment/IMPLEMENTATION.md
   Audience: Project contributors
   Lifespan: Per-release cycle

Level 3: Session-Specific Metadata
   Location: ~/dev_flow/
   Examples: DEPLOYMENT_SESSION_SUMMARY.md, ECOSYSTEM_AUDIT_REPORT.md
   Audience: Team leads, reviewers
   Lifespan: 2-4 weeks, then archive

Level 4: Deprecated / Archived
   Location: ~/dev_flow/_archive/
   Examples: Old audits, superseded status reports
   Audience: Historical reference only
   Lifespan: Indefinite (backup)
```

---

## 🎯 Key Architectural Decisions

### **Config System** (8-Layer Merge)
From: `yaml_config_administration.md`
```
Priority (highest to lowest):
  1. creds.yaml (local, .gitignored)
  2. Environment variables (KI_CFG_*)
  3. Runtime configuration
  4. Active stage (KI_STAGE)
  5. Active profile
  6. ki.yaml
  7. config/defaults/
  8. Schema defaults
```
**Implemented in**: All 6 repos, ki-core/src/ki_core/config.py

### **CLI Architecture** (Delegation Pattern)
From: `cli-management/CLI_ARCHITECTURE_ROADMAP.md`
```
Dependency flow:
  Apps (kicli-assist, ki-knowledge) 
    → ki_core.commands (shared implementations)
    → yaml-cfg-wizard (generic YAML tool)
    
Pattern: Apps provide echo_fn parameter (cli-agnostic)
Goal: Prevent circular dependencies, maximize reuse
```
**Implemented in**: ki-core/src/ki_core/commands/

### **Multi-Repo Coordination** (Version Alignment)
From: `MULTI_REPO_ORCHESTRATION_STUDY.md`
- Shared `pyproject.toml` version reference
- Semantic versioning (major.minor.patch)
- Release coordination via git tags
- Changelog per major version

---

## ✅ Checklist for Documentation Maintainers

### Creating New Cross-Project Docs
- [ ] Place in `~/dev_doc/` (not in project repos)
- [ ] Use clear, descriptive filename
- [ ] Link from this README.md
- [ ] Cross-reference related docs
- [ ] Include "Last Updated" date
- [ ] Keep format consistent with existing docs

### Updating Existing Docs
- [ ] Verify information is still accurate
- [ ] Update "Last Updated" date
- [ ] Fix any broken cross-links
- [ ] Archive if superseded (move to `archive/`)

### Creating Session Documentation
- [ ] Place in `~/dev_flow/` (temporary)
- [ ] Clear filename with date
- [ ] Link to relevant project docs
- [ ] Include next actions for reader
- [ ] Archive after 4 weeks if superseded

### Archiving Old Documentation
- [ ] Move to `~/dev_flow/_archive/`
- [ ] Preserve original filename + context
- [ ] Note superseding document (if any)
- [ ] Keep indefinitely for reference

---

## 📚 Quick Reference by Role

### **For DevOps/SRE**
1. [yaml_config_administration.md](yaml_config_administration.md) — Config systems
2. [security_guidelines.md](security_guidelines.md) — Security practices
3. `~/dev_flow/QUICK_REFERENCE.md` — Daily operations
4. Individual repo `doc/deployment/` — Per-project ops

### **For Backend Developers**
1. [script_organization_standards.md](script_organization_standards.md) — Code structure
2. [security_guidelines.md](security_guidelines.md) — Secure coding
3. [yaml_config_administration.md](yaml_config_administration.md) — Config handling
4. Project `doc/ARCHITECTURE.md` — Project-specific

### **For Frontend Developers**
1. [textual_tui_best_practices.md](textual_tui_best_practices.md) — UI patterns
2. `cli-management/` — Command structure
3. Project `doc/ARCHITECTURE.md` — UI design
4. [security_guidelines.md](security_guidelines.md) — XSS/CSRF prevention

### **For AI/ML Engineers**
1. [ai_agent_intelligence_architecture.md](ai_agent_intelligence_architecture.md) — Agent theory
2. [ai_agent_architecture_review.md](ai_agent_architecture_review.md) — Implementation
3. Project `doc/AGENTS.md` — IA3 SimWorld specifics

### **For Tech Leads**
1. [DOCUMENTATION_ORGANIZATION_GUIDE.md](DOCUMENTATION_ORGANIZATION_GUIDE.md) — Doc standards
2. [MULTI_REPO_ORCHESTRATION_STUDY.md](MULTI_REPO_ORCHESTRATION_STUDY.md) — Coordination
3. [COPILOT_SESSION_INDEX.md](COPILOT_SESSION_INDEX.md) — Session tracking
4. `~/dev_flow/ECOSYSTEM_AUDIT_REPORT.md` — Current status

---

## 🔄 Knowledge Flow

```
Theoretical Foundation
  ↓
/home/flow/dev_doc/ (Global Standards)
  ↓
  ├→ Architecture patterns, best practices, guidelines
  ├→ Used by all 6 projects
  └→ Updated quarterly or when major patterns change
  
  ↓
Project Implementation
  ↓
/home/flow/dev_flow/project/doc/ (Project-Specific)
  ├→ Architecture for this project
  ├→ Feature documentation
  └→ Implementation details
  
  ↓
Session Tracking
  ↓
/home/flow/dev_flow/ROOT (Session Reports)
  ├→ Deployment summaries
  ├→ Audit results
  └→ Status updates (archived after 4 weeks)
```

---

## 📞 How to Navigate

**"Where do I find how to...?"**

| Question | Answer |
|----------|--------|
| ...structure my code? | `script_organization_standards.md` |
| ...configure the app? | `yaml_config_administration.md` + project `doc/deployment/` |
| ...deploy safely? | `security_guidelines.md` + project `doc/deployment/IMPLEMENTATION.md` |
| ...build a TUI? | `textual_tui_best_practices.md` |
| ...understand the architecture? | Project `doc/ARCHITECTURE.md` |
| ...run daily operations? | `~/dev_flow/QUICK_REFERENCE.md` |
| ...integrate with agents? | `ai_agent_intelligence_architecture.md` |
| ...organize this documentation? | `DOCUMENTATION_ORGANIZATION_GUIDE.md` |

---

## 🗂️ File Structure

```
/home/flow/dev_doc/
├── README.md                              ← You are here
├── DOCUMENTATION_ORGANIZATION_GUIDE.md    ← Doc standards (READ FIRST)
├── COPILOT_MEMORY_MAP.md                  ← Memory management
├── COPILOT_SESSION_INDEX.md               ← Session tracking
│
├── yaml_config_administration.md          ← Config deep dive
├── security_guidelines.md                 ← Security practices
├── shell_execution_patterns.md            ← Shell scripting
├── script_organization_standards.md       ← Code organization
├── textual_tui_best_practices.md          ← TUI development
│
├── ai_agent_intelligence_architecture.md  ← Agent theory (4-stage plan)
├── ai_agent_architecture_review.md        ← Implementation review
├── MULTI_REPO_ORCHESTRATION_STUDY.md      ← Multi-repo coordination
│
├── architecture/                          ← System design
│   └── (multi-layer patterns)
│
├── cli-management/                        ← CLI patterns
│   ├── CLI_ARCHITECTURE_ROADMAP.md        ← Phase plan (start here)
│   ├── IMPLEMENTATION_STATUS.md
│   └── (command structure docs)
│
└── server-mngm/                           ← Server management
    └── (service control, monitoring)
```

---

## 🚀 Getting Started

1. **New to this codebase?**
   - Start: [DOCUMENTATION_ORGANIZATION_GUIDE.md](DOCUMENTATION_ORGANIZATION_GUIDE.md)
   - Then: This README.md
   - Next: Go to specific `/home/flow/dev_flow/project/` for your project

2. **Working on deployment?**
   - Read: `~/dev_flow/DEPLOYMENT_SESSION_SUMMARY.md`
   - Check: `~/dev_flow/QUICK_REFERENCE.md`
   - Deep dive: [yaml_config_administration.md](yaml_config_administration.md)

3. **Building features?**
   - Standards: [script_organization_standards.md](script_organization_standards.md)
   - Security: [security_guidelines.md](security_guidelines.md)
   - Project-specific: See `project/doc/ARCHITECTURE.md`

4. **Setting up a new repo?**
   - Reference: [DOCUMENTATION_ORGANIZATION_GUIDE.md](DOCUMENTATION_ORGANIZATION_GUIDE.md)
   - Template: See existing project `doc/` structure
   - Add link: Update this README.md and `/home/flow/dev_flow/README.md`

---

**Last Updated**: 2026-10-31 08:57 UTC+1  
**Scope**: Cross-project standards and global documentation  
**Status**: ✅ Current & Evergreen
