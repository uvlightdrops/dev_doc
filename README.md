# 🌍 Cross-Project Developer Documentation

**Location**: `/home/flow/dev_doc/`  
**Purpose**: Shared standards, architecture patterns, and best practices across all KI ecosystem projects  
**Status**: ✅ Active & Maintained

---

## 🎯 What Is This?

This folder contains **evergreen documentation** that applies across multiple projects:

- ✅ Architecture patterns and design decisions
- ✅ Development standards (code organization, shell scripts, etc.)
- ✅ Operational guidelines (config, security, deployment)
- ✅ Development tools guidance (Copilot, TUI frameworks)
- ✅ Multi-project coordination (orchestration, version alignment)

**Not** included here:
- ❌ Project-specific implementation details (see `~/dev_flow/project/doc/`)
- ❌ Session reports (see `~/dev_flow/REPORTS/`)
- ❌ Archived patterns (see `~/dev_doc/archive/`)

---

## 🚀 Quick Start: What Do You Need?

### Documentation Maintainers
→ **Start here:** [`documentation-guidelines/`](documentation-guidelines/)  
- How to organize project docs, YAML headers, archival policy

### Copilot / AI-Assisted Sessions
→ **Start here:** [`copilot-memory/`](copilot-memory/)  
- Session memory best practices, checkpoints, scope

### Developers (Code & Deployment)
→ **Start here:** [Quick Reference by Role](#-quick-reference-by-role) below

---

## 📂 Folder Structure

```
/home/flow/dev_doc/
├── README.md                            ← Overview (you are here)
├── INDEX.md                             ← Complete file listing with descriptions
│
├── documentation-guidelines/            ← Doc standards & best practices
│   ├── ORGANIZATION_GUIDE.md           ← Project folder structure
│   ├── MAINTENANCE_STANDARDS.md         ← YAML headers, archival
│   ├── QUALITY_QUERY.md                ← Quality checklist
│   └── STRUCTURE_MAP.md                ← Knowledge organization
│
├── copilot-memory/                      ← AI session management
│   ├── MEMORY_MAP.md                  ← Memory scope & lifecycle
│   ├── SESSION_INDEX.md               ← Checkpoint tracking
│   └── DOCUMENTATION_BEHAVIOR.md      ← Session documentation patterns
│
├── architecture/                        ← System design patterns
├── cli-management/                      ← CLI architecture & delegation
├── server-mngm/                         ← Server orchestration (historical)
│
└── Root-level technical guides:
    ├── yaml_config_administration.md   ← Config systems (8-layer merge)
    ├── security_guidelines.md          ← Security practices
    ├── shell_execution_patterns.md     ← Shell scripting standards
    ├── script_organization_standards.md ← Code organization
    ├── textual_tui_best_practices.md   ← TUI development (Textual)
    ├── ai_agent_intelligence_architecture.md ← Agent theory (4-stage)
    ├── ai_agent_architecture_review.md  ← Implementation patterns
    ├── MULTI_REPO_ORCHESTRATION_STUDY.md ← Multi-repo coordination
    ├── SERVICE_ORCHESTRATION_PATTERN.md ← stackctl + Layer 1 design
    └── TUI_STRATEGY_CLARIFICATION.md   ← TUI 2.0 multi-target approach
```

---

## 🎯 Quick Reference by Role

| Role | Start With | Next | Then |
|------|-----------|------|------|
| **Doc Maintainer** | `documentation-guidelines/ORGANIZATION_GUIDE.md` | [`INDEX.md`](INDEX.md) | Create/update docs |
| **Backend Dev** | `script_organization_standards.md` | `security_guidelines.md` | Project `doc/` |
| **DevOps/SRE** | `yaml_config_administration.md` | `SERVICE_ORCHESTRATION_PATTERN.md` | Project deployment/ |
| **Frontend/TUI** | `textual_tui_best_practices.md` | `TUI_STRATEGY_CLARIFICATION.md` | Project frontend-*/ |
| **AI/Agent Eng** | `ai_agent_intelligence_architecture.md` | `ai_agent_architecture_review.md` | Project AGENTS.md |
| **Tech Lead** | `documentation-guidelines/` | `MULTI_REPO_ORCHESTRATION_STUDY.md` | Session reports |
| **Copilot User** | `copilot-memory/MEMORY_MAP.md` | `copilot-memory/SESSION_INDEX.md` | Work with AI |

---

## 🔗 Documentation Levels

### **Level 1: Global Standards** (this folder)
- Cross-project patterns & best practices
- Long-term, evergreen documentation
- Applies to all 6 ecosystem projects

### **Level 2: Project Implementation** (`~/dev_flow/project/doc/`)
- Project-specific architecture
- Feature documentation
- Deployment procedures

### **Level 3: Session Reports** (`~/dev_flow/REPORTS/`)
- Time-bound coordination
- Status updates (archived after 4 weeks)
- Links to relevant docs

### **Level 4: Archive** (`~/dev_flow/_archive/`)
- Historical reference
- Superseded documentation
- Kept indefinitely

---

## ✅ For Next Steps

See [`INDEX.md`](INDEX.md) for a complete listing of all files with descriptions.

**Last Updated**: 2026-11-05  
**Status**: ✅ Current & Evergreen
