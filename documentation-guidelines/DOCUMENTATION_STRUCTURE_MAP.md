# 📐 Documentation Structure Map

**Purpose**: Clear visual of what's where and why  
**Last Updated**: 2026-10-31 08:57 UTC+1

---

## Overview Diagram

```
User's Local Machine
│
├─ ~/dev_doc/                           Global Standards & Patterns (Long-term)
│  │
│  ├─ README.md                         ← Navigation index
│  ├─ DOCUMENTATION_ORGANIZATION_GUIDE.md
│  ├─ yaml_config_administration.md
│  ├─ security_guidelines.md
│  ├─ shell_execution_patterns.md
│  ├─ script_organization_standards.md
│  ├─ textual_tui_best_practices.md
│  │
│  ├─ ai_agent_*.md                     AI/Agent architecture
│  ├─ MULTI_REPO_ORCHESTRATION_STUDY.md
│  ├─ COPILOT_*.md                      Session management
│  │
│  ├─ architecture/                     Design patterns (subdirs)
│  ├─ cli-management/
│  └─ server-mngm/
│
└─ ~/dev_flow/                          Project Metadata & Sessions (Temporary)
   │
   ├─ README.md                         ← Navigation index
   ├─ DEPLOYMENT_SESSION_SUMMARY.md     ← CURRENT (2026-10-31)
   ├─ ECOSYSTEM_AUDIT_REPORT.md         ← CURRENT (comprehensive)
   ├─ QUICK_REFERENCE.md                ← CURRENT (cheat sheet)
   │
   ├─ PROJECTS_OVERVIEW.md              General info
   ├─ AUDIT_COMPLETION_SUMMARY.md       Latest findings
   ├─ AGENTS.md                         Simulation setup
   │
   └─ _archive/                         Old/Superseded (Keep for reference)
      ├─ CONFIG_SYSTEM_AUDIT.md         (2026-10-19 - old)
      ├─ DOCUMENTATION_AUDIT.md         (2026-10-19 - old)
      ├─ AUDIT_PHASE1_RESULTS.md        (2026-10-31 - incomplete)
      └─ ...
   
      └─ kicli-code-assist/            (Git repo - has own doc/)
         ├─ doc/
         │  ├─ deployment/              ← PER-PROJECT docs
         │  │  ├─ README.md
         │  │  ├─ ARCHITECTURE.md
         │  │  ├─ IMPLEMENTATION.md
         │  │  ├─ TROUBLESHOOTING.md
         │  │  └─ CONFIG_MERGE_REFERENCE.md
         │  └─ ...
         │
         └─ (other repos similarly)
             ki-core/doc/deployment/
             ki-knowledge/doc/deployment/
             yaml-cfg-wizard/doc/deployment/
             stackctl/doc/deployment/
             yaml-cfg-wizard-ui/doc/deployment/
```

---

## Content Distribution

### ~/dev_doc/ — What Goes Here?

**✅ DO Place Here:**
- Generic patterns used by multiple repos (config, CLI, agents)
- Best practices and standards
- Framework/language-agnostic guides
- Theoretical architecture
- Session memory and copilot coordination
- Long-lived documentation (updated yearly or less)

**❌ DON'T Place Here:**
- Session-specific reports
- "Status as of [date]" documents
- Deployment results
- Current project status (belongs in dev_flow/)
- Repo-specific implementation (belongs in project/doc/)

**Examples:**
- ✅ "How to structure YAML configs" → `yaml_config_administration.md`
- ✅ "CLI command delegation pattern" → `cli-management/CLI_ARCHITECTURE_ROADMAP.md`
- ❌ "Deployment completed on 2026-10-31" → `~/dev_flow/DEPLOYMENT_SESSION_SUMMARY.md`
- ❌ "kicli-code-assist config merge fix" → `kicli-code-assist/doc/deployment/`

### ~/dev_flow/ — What Goes Here?

**✅ DO Place Here:**
- Session-specific reports (deployments, audits, results)
- Current status snapshots
- Cross-repo coordination (as of [date])
- Action items and next steps
- Temporary documents (2-4 week lifespan)
- Links to per-project doc/ folders

**❌ DON'T Place Here:**
- Generic patterns → belongs in `~/dev_doc/`
- Long-lived documentation → belongs in `~/dev_doc/`
- Repo-specific details → belongs in `project/doc/`

**Lifecycle:**
- **Active (0-2 weeks)**: Root level, linked in README.md
- **Recent (2-4 weeks)**: Still in root, but referenced less
- **Archived (4+ weeks)**: Move to `_archive/` folder
- **Historical (6+ months)**: Archive folder is backup only

**Examples:**
- ✅ "Deployment standardization session (2026-10-31)" → root `.md` file
- ✅ "Ecosystem audit results" → root `.md` file
- ✅ "Quick reference for operations" → root `.md` file
- ❌ "How YAML configs work" → `~/dev_doc/yaml_config_administration.md`

### project/doc/ — What Goes Here?

**✅ DO Place Here:**
- Project-specific architecture
- Feature documentation
- Implementation guides
- Troubleshooting for this project
- Design decisions (this project only)
- Per-project deployment guides

**Examples (kicli-code-assist):**
- `doc/deployment/ARCHITECTURE.md` - How this project deploys
- `doc/deployment/IMPLEMENTATION.md` - Step-by-step guide
- `doc/deployment/TROUBLESHOOTING.md` - Common issues + fixes
- `doc/features/` - Feature docs
- `doc/refactorings/` - Past refactorings

---

## Document Aging & Archival Strategy

```
Timeline:
  Day 1  ───────── Document created in ~/dev_flow/
  ↓
  Week 1 ───────── Active, linked in README.md, frequently referenced
  ↓
  Week 2 ───────── Still current, referenced occasionally
  ↓
  Week 3 ───────── Starting to age, usually superseded by new session docs
  ↓
  Week 4 ───────── Move to _archive/ (or delete if merged into new summary)
         
         Checkpoint: If document contains evergreen info
                    → Extract to ~/dev_doc/ for long-term storage
                    → Keep reference in archived version
```

**How to Archive:**
```bash
cd ~/dev_flow/
mkdir -p _archive
mv OLD_DOCUMENT.md _archive/OLD_DOCUMENT.md
# Add note at top of archive doc: "Superseded by [NEW_DOC.md], archived 2026-10-31"
```

**How to Promote to Evergreen:**
```bash
# If ~/dev_flow/DOCUMENT.md contains patterns useful for all projects:
1. Extract the generic part → ~/dev_doc/PATTERN_NAME.md
2. In original doc, add note: "See also ~/dev_doc/PATTERN_NAME.md for generic patterns"
3. After 2 weeks, archive original
```

---

## Navigation Decision Tree

```
START: I have a question about...

├─ "How do I...?" (Procedures)
│  └─ QUICK_REFERENCE.md (~/dev_flow/)
│
├─ "What happened in the deployment?" (Recent Events)
│  └─ DEPLOYMENT_SESSION_SUMMARY.md (~/dev_flow/)
│
├─ "What's the status of the ecosystem?" (Current State)
│  └─ ECOSYSTEM_AUDIT_REPORT.md (~/dev_flow/)
│
├─ "How should I structure...?" (Standards & Patterns)
│  └─ ~/dev_doc/ (find by topic)
│     ├─ DOCUMENTATION_ORGANIZATION_GUIDE.md (docs)
│     ├─ script_organization_standards.md (code)
│     ├─ yaml_config_administration.md (config)
│     ├─ security_guidelines.md (security)
│     └─ textual_tui_best_practices.md (UI)
│
├─ "How does THIS PROJECT work?" (Project Details)
│  └─ project/doc/ARCHITECTURE.md
│     ├─ project/doc/deployment/
│     ├─ project/doc/features/
│     └─ project/doc/refactorings/
│
├─ "What's the theory behind...?" (Concepts)
│  └─ ~/dev_doc/ (find by topic)
│     ├─ ai_agent_intelligence_architecture.md (agents)
│     ├─ MULTI_REPO_ORCHESTRATION_STUDY.md (multi-repo)
│     └─ yaml_config_administration.md (config system)
│
└─ "Why isn't [old doc] here anymore?" (Archive)
   └─ ~/dev_flow/_archive/ (find by name or date)
```

---

## File Naming Conventions

### ~/dev_doc/ Files
- **Format**: `DESCRIPTIVE_TOPIC.md` or `topic_subtopic.md`
- **Examples**: 
  - `yaml_config_administration.md`
  - `security_guidelines.md`
  - `textual_tui_best_practices.md`
- **No dates** (these are evergreen)
- **Hierarchy**: Subdirs for large topics (architecture/, cli-management/)

### ~/dev_flow/ Files (Root)
- **Format**: `TITLE_SESSION_SUMMARY.md` or descriptive name
- **Examples**:
  - `DEPLOYMENT_SESSION_SUMMARY.md`
  - `ECOSYSTEM_AUDIT_REPORT.md`
  - `QUICK_REFERENCE.md`
  - `AUDIT_COMPLETION_SUMMARY.md`
- **Include date in header** (Last Updated)
- **No dates in filename** (use README.md to track currency)

### ~/dev_flow/_archive/ Files
- **Format**: Original filename + context note
- **Examples**:
  - `CONFIG_SYSTEM_AUDIT.md` (2026-10-19, superseded by ECOSYSTEM_AUDIT_REPORT.md)
  - `DOCUMENTATION_AUDIT.md` (2026-10-19, historical)
- **Add note at top** when moving to archive:
  ```markdown
  > ⚠️ ARCHIVED: 2026-10-31
  > Superseded by: ECOSYSTEM_AUDIT_REPORT.md
  > Reason: Status became outdated after standardization session
  ```

---

## Cross-Referencing Rules

### From ~/dev_flow/ to ~/dev_doc/
```markdown
See also: [Generic Pattern](/home/flow/dev_doc/PATTERN.md) for cross-project context
```

### From ~/dev_doc/ to ~/dev_flow/
```markdown
Current status: See ~/dev_flow/ECOSYSTEM_AUDIT_REPORT.md
```

### From either to project/doc/
```markdown
Implementation details: See kicli-code-assist/doc/deployment/ARCHITECTURE.md
```

### From project/doc/ to ~/dev_doc/
```markdown
Standard patterns: See /home/flow/dev_doc/yaml_config_administration.md
```

---

## Maintenance Checklist (Monthly)

- [ ] Review README.md files (both ~/dev_doc/ and ~/dev_flow/)
- [ ] Check for broken cross-links
- [ ] Archive documents older than 4 weeks
- [ ] Promote evergreen patterns from ~/dev_flow/ to ~/dev_doc/
- [ ] Update "Last Updated" dates in headers
- [ ] Verify all session checkpoints are linked

---

## Summary Table

| Location | Purpose | Lifespan | Link To |
|----------|---------|----------|---------|
| `~/dev_doc/` | Global standards & patterns | Evergreen (yearly updates) | All projects |
| `~/dev_flow/README.md` | Project metadata index | Permanent | This folder |
| `~/dev_flow/*.md` (root) | Session reports & status | 2-4 weeks | Current readers |
| `~/dev_flow/_archive/` | Old/superseded docs | Indefinite (backup) | Historical reference |
| `project/doc/` | Project-specific | Per-release | That project |
| `project/doc/deployment/` | Per-project deployment | Per-release | That project |

---

**Status**: ✅ Maintained & Current  
**Scope**: Cross-documentation coordination  
**Next Review**: 2026-11-30

