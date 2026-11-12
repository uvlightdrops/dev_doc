---
project: dev_doc
scope: guides
purpose: Memory organization, context lookup and recurring maintenance checks
created: 2026-11-05 11
agent_project: ki-ecosystem
status: active
semantic_blocks:
- stored-memories-persistent-across
- critical-files-frequently-referenced
- topics-requiring-extra-care-easy-to
- session-checkpoint-structure
- knowledge-update-flow
- most-critical-mistakes-self-reminder
- quick-status-check-template
- links-to-update-this-file
updated: "2026-11-12 10"
update_reason: Update the path to the shared AI-agent intelligence document
---
# Copilot Memory & Context Map

**For:** Self-organization of memories, context persistence, and preventing knowledge loss  
**Updated:** 2026-10-19 23:35

---

<a id="-stored-memories-persistent-across-sessions"></a>

## 🧠 Stored Memories (Persistent Across Sessions) [stored-memories-persistent-across]

```yaml block-meta
updated: "2026-11-12 10"
status: active
relations:
  - target: "../ai-agents/AI_AGENT_INTELLIGENCE_ARCHITECTURE.md#agent-intelligence-levels"
    type: reference
    reason: The persistent-memory index points to this conceptual agent model
```

### User-Scoped Memories (@uvlightdrops) - Apply Across ALL Repos

| Memory | Key Fact | Citation | Status |
|--------|----------|----------|--------|
| Documentation organization | Split generic (~/dev_doc) vs project (project/docs) | `/dev_doc/doc/documentation-guidelines/DOCUMENTATION_ORGANIZATION_GUIDE.md` | ✅ Active |
| Deployment workflow | No Makefile; git push + watch | User input | ✅ Active |
| Deployment infrastructure | 3 envs (2 dev + staging), HTTPS clones, no Docker | User input | ✅ Active |
| Error handling preference | Clear errors over fallback paths | User input | ✅ Active |
| Dependency auditing | Python-only scripts (no embedded Python in shell) | User input | ✅ Active |
| Logging structure (NEW) | Logs → project-root/log/ with relative paths | `logging_integration.py:37-91` | ✅ Active |
| Deployment notification | Inform user if make prod-update needed | User input | ✅ Active |
| AI agent architecture | Cognitive agent model and staged proposal | `../ai-agents/AI_AGENT_INTELLIGENCE_ARCHITECTURE.md` | 📖 Reference |
| Multiplayer architecture | Namespace 'ia3-two', v1.0.70 tag | Various files | 📖 Reference |
| Simulation mechanics | River generation, crop planting | Various files | 📖 Reference |
| Environment | Ubuntu 24 Linux | User input | ✅ Noted |

### Repository-Scoped Memories (kicli-code-assist) - This Repo Only

| Memory | Key Fact | Citation | Status |
|--------|----------|----------|--------|
| Deployment documentation (⚠️) | Outdated: make prod-update no longer exists | `PHASE_2_SUMMARY.md`, `DEPLOYMENT_READINESS.md` | 🔴 Needs update |
| Phase 3 completion | Error handling, validation, streaming, logging (11/14 tests) | `error_handling.py`, `validation.py`, `streaming.py` | ✅ Complete |
| Phase 2 completion | Config system unified in ki-core, logging integrated | `ki-core/config.py`, commits | ✅ Complete |
| Phase 4.1 Implementation | Async LLM, secret filtering, structured errors | `llm_integration.py`, `ki-core/security/` | ✅ Complete |
| CLI command pattern | echo_fn parameter makes commands CLI-agnostic | `CLI_ARCHITECTURE_ROADMAP.md:185-210` | ✅ Pattern |
| CLI architecture Phase 2 | run_config/profile/stage in ki-core, app delegates | `ki_core/commands/`, `kicli_code_assist/cli.py` | ✅ Complete |
| Config examples (⚠️) | No hand-maintained ki.yaml examples (use skeleton generation) | User decision | 📌 Policy |

---

<a id="-critical-files-frequently-referenced"></a>

## 📂 Critical Files (Frequently Referenced) [critical-files-frequently-referenced]

### Architecture & Design Docs
```
~/dev_doc/
├── DOCUMENTATION_ORGANIZATION_GUIDE.md  → How to organize all docs
├── COPILOT_SESSION_INDEX.md             → This session's entry point
└── COPILOT_MEMORY_MAP.md                → This file

~/dev_flow/ki-core/docs/
├── CLI_ARCHITECTURE_ROADMAP.md          → All app CLIs delegate to ki-core
└── IMPLEMENTATION_STATUS.md             → Phase checklist (Phase 4.1 active)

~/dev_flow/kicli-code-assist/docs/
├── specifications/YAML_CONFIG_SYSADMIN.md  → Config file approaches
└── architecture/, features/, tracking/     → Organized into 6 folders
```

### Code Entry Points
```
~/dev_flow/kicli-code-assist/
├── kicli_code_assist/ui/app.py          → Main UI (CodeAssistantApp class)
│   ├── _submit_chat()                   → LLM query (line 496)
│   ├── _load_file_to_context()          → File loading (line 585)
│   └── self.loaded_files                → Deduplication list (line 94)
├── kicli_code_assist/llm_integration.py → Async LLM integration
├── kicli_code_assist/chat_session.py    → Message history + context
├── kicli_code_assist/logging_integration.py → Logging setup
└── log/                                 → ← ACTUAL LOG FILES
    ├── kicli-llm.log                    → LLM query logging
    ├── kicli.log                        → General app logging
    └── (others)
```

---

<a id="-topics-requiring-extra-care-easy-to-forgetget-wrong"></a>

## 🎯 Topics Requiring Extra Care (Easy to Forget/Get Wrong) [topics-requiring-extra-care-easy-to]

### 1. Logging Location & Relative Paths
**Problem:** Logs are written via `flowpy.setup_logger()` with RELATIVE paths  
**Solution:** Check `/home/flow/dev_flow/kicli-code-assist/log/` not elsewhere  
**Files:** `logging_integration.py:37-91`, actual logs in `log/` subdir  
**Why Easy to Forget:** Config has `storage.log_dir` but it's UNUSED; app uses fixed relative paths

### 2. Chat Message + File Submission
**Problem:** Loaded files not reaching LLM (just fixed)  
**Solution:** Use `chat_session.get_messages_for_api()` which includes system prompt + full history  
**Files:** `ui/app.py:496` (_submit_chat), `chat_session.py:123` (get_messages_for_api)  
**Why Easy to Forget:** Two separate concepts (file loading, message submission) that must be coupled

### 3. Deployment: No Makefile
**Problem:** Outdated docs reference `make prod-update`  
**Solution:** Just git push; watch for auto-deployment on target hosts  
**Files:** `docs/DEPLOYMENT_READINESS.md` (needs update)  
**Why Easy to Forget:** Standard practice elsewhere; user explicitly removed it

### 4. CLI Architecture: Apps Delegate to ki-core
**Problem:** Duplication of commands across app CLIs  
**Solution:** All shared commands live in `ki_core.commands.*`; apps import + delegate  
**Files:** `ki-core/docs/CLI_ARCHITECTURE_ROADMAP.md:40-60`  
**Why Easy to Forget:** Pattern must be applied consistently; easy to accidentally add app-specific version

### 5. Config Location (ki.yaml)
**Problem:** Multiple ki.yaml files exist; unclear which is used  
**Solution:** Each project has its own; parent ~/dev_flow/ki.yaml is inherited  
**Files:** `ki-core` loads; `app_config.py` merges  
**Why Easy to Forget:** Hierarchy not obvious; config can cascade

---

<a id="-session-checkpoint-structure"></a>

## 📝 Session Checkpoint Structure [session-checkpoint-structure]

Location: `~/.copilot/session-state/1bb7e508-5834-4491-9930-99243671cb6c/`

**Purpose:** Persist across CLI sessions without committing to git

**Structure:**
```
checkpoints/         → 27 checkpoints from prior work (index.md lists all)
files/              → Persistent artifacts (not committed)
  ├── deduplicator_reference.py     → Old complex module (archived)
  ├── textual_app_refactor_plan.md  → Architecture notes
  ├── left_panel_editor_architecture.md
  ├── implementation_strategy.md
  └── terminal_tabs_plan.md
```

**Key:** Before starting new work, read relevant checkpoints (e.g., `027-chat-message-submission-fix-wi.md`)

---

<a id="-knowledge-update-flow"></a>

## 🔄 Knowledge Update Flow [knowledge-update-flow]

### When User Corrects Me (Common Scenario)
1. **Acknowledge error** → Apologize, don't repeat mistake
2. **Store memory** → Use `store_memory()` tool
3. **Cite properly** → Include exact file:line or "User input: quotation"
4. **Scope carefully** → User scope (applies everywhere) vs repository scope (this repo only)

### When Adding New Facts
- **Don't:** Store obvious facts, temporary task status, versions, branches
- **Do:** Store patterns, conventions, technical gotchas, design decisions with citations

### When Memory is Outdated
- **Downvote first** → Explain why it's wrong/outdated
- **Then store new** → Better version with current info

---

<a id="-most-critical-mistakes-self-reminder"></a>

## 🚨 Most Critical Mistakes (Self-Reminder) [most-critical-mistakes-self-reminder]

⚠️ **CRITICAL:** If app logs go to WRONG directory (e.g. `/dev_art/hdgui/log/`):
- ❌ App is running from wrong pwd/context
- ✅ **FIX IMMEDIATELY:** Kill process (lsof shows log location), restart from `/kicli-code-assist/` dir
- Verify: `ps aux | grep kicli-assist` → check exe path, restart with `cd /kicli-code-assist && kicli-assist tui`

---

1. ❌ Checking `/home/flow/dev_flow/log/` instead of `/kicli-code-assist/log/`
   - ✅ **FIX:** Log location is relative paths, check pwd-based log dir

2. ❌ Forgetting that loaded files are stored in `chat_session.messages` as embedded XML
   - ✅ **FIX:** When submitting chat, MUST use `get_messages_for_api()` to include them

3. ❌ Assuming deployment uses `make prod-update`
   - ✅ **FIX:** Makefile gone; git push + watch only

4. ❌ Creating app-specific CLI commands instead of delegating to ki-core
   - ✅ **FIX:** Shared commands in `ki_core.commands.*`, apps import + delegate

5. ❌ Adding hand-maintained ki.yaml examples
   - ✅ **FIX:** Use skeleton generation instead (`config skeleton`)

6. ❌ Committing credentials or secrets
   - ✅ **FIX:** Use SensitiveDataFilter, never embed in source

7. ❌ LOSING CRITICAL INFO TOLD BY USER
   - ✅ **FIX:** IMMEDIATELY store as memory, act on it, don't let it be forgotten

---

<a id="-quick-status-check-template"></a>

## 📊 Quick Status Check Template [quick-status-check-template]

Use this when resuming work to get oriented:

```
QUICK STATUS (@uvlightdrops, kicli-code-assist)

Active Issue: [What's being worked on?]
Last Commit: [Most recent SHA + message]
Blockers: [Any problems preventing progress?]
Logs Location: /home/flow/dev_flow/kicli-code-assist/log/
Memories Updated: [Any new facts stored this session?]
Deployable: [Can changes be deployed or testing needed?]
```

---

<a id="-links-to-update-this-file"></a>

## 🔗 Links to Update This File [links-to-update-this-file]

This file should be updated:
- ✅ Weekly with new memory additions
- ✅ After each new repository memory is stored
- ✅ When critical bugs/gotchas are discovered
- ✅ When phase status changes

Last checked: 2026-10-19 23:35
