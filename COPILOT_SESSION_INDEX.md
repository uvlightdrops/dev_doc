# Copilot Session Index

**Purpose:** Central entry point for all active development work, memories, and critical information. Maintained continuously and linked from all sessions.

**Last Updated:** 2026-10-19 23:35  
**Current User:** @uvlightdrops  
**Active Projects:** kicli-code-assist (Phase 4.1), ki-core (async LLM), ki-knowledge, yaml-cfg-wizard

---

## 🔴 CRITICAL STATUS - RIGHT NOW

- **LLM Chat Messages**: NOT sending loaded files to LLM (BLOCKED - investigating logging)
- **Log Files Location**: kicli-code-assist logs → `/home/flow/dev_flow/kicli-code-assist/log/` (not configurable)
- **Running Instance**: PID 343532 - correct process, but check log timestamps
- **Latest Session**: Chat message submission fix (commit 360741b) - includes loaded files in API messages

---

## 📚 Critical Documentation (Always Up-to-Date)

### Architecture & Design
- **[CLI Architecture Roadmap](../dev_flow/ki-core/docs/CLI_ARCHITECTURE_ROADMAP.md)** - All app CLIs delegate to ki-core base commands
- **[YAML Config Sysadmin Guide](../dev_flow/kicli-code-assist/docs/specifications/YAML_CONFIG_SYSADMIN.md)** - Config file approaches and unification strategy
- **[Documentation Organization Guide](./DOCUMENTATION_ORGANIZATION_GUIDE.md)** - How to split generic vs project-specific docs
- **[Implementation Status](../dev_flow/ki-core/docs/IMPLEMENTATION_STATUS.md)** - Phase checklist (Phase 4.1 active)

### Project-Specific Docs
- **[kicli-code-assist Architecture](../dev_flow/kicli-code-assist/docs/architecture/)** - TUI app architecture, panels, integration layers
- **[kicli-code-assist Features](../dev_flow/kicli-code-assist/docs/features/FEATURES_IMPLEMENTED.md)** - 13 completed features documented
- **[Customer Requests](../dev_flow/kicli-code-assist/docs/tracking/customer_requests.md)** - HIGH PRIORITY: Chat + LLM debug

---

## 🧠 Stored Memories (User & Repository Scoped)

### Repository Memories (kicli-code-assist)
1. **Deployment documentation** - Outdated references (make prod-update no longer exists)
2. **Phase 3 completion** - Error handling + validation complete (11/14 config tests pass)
3. **Phase 2 completion** - Config system unified in ki-core
4. **Phase 4.1 Implementation** - Async LLM + error handling complete
5. **CLI command pattern** - echo_fn parameter makes commands CLI-agnostic

### User Memories (@uvlightdrops)
1. **Documentation organization** - Split: generic → ~/dev_doc, project-specific → project/docs/
2. **Deployment workflow** - No Makefile; git push + watch on target hosts
3. **Deployment infrastructure** - 3 environments (local dev, second dev, staging), HTTPS clones
4. **Error handling** - Prefer clear errors over fallback paths
5. **Dependency auditing** - Python-only scripts (no embedded Python in shell)
6. **Logging structure** - Logs → project-root/log/ with relative paths (NEW)

---

## 📋 Current Session Work

### Active Tasks
- [ ] **FIX BLOCKER**: Chat messages + loaded files not reaching LLM
  - Commit 360741b: Modified `_submit_chat()` to use `get_messages_for_api()`
  - Issue: Logs stopped writing after 20:58 (check `/home/flow/dev_flow/kicli-code-assist/log/kicli-llm.log`)
  - Need to verify: Is LLM query actually being called? Check for errors in main log.

- [ ] Update FEATURES_IMPLEMENTED.md with chat fix once verified
- [ ] Document actual deployment workflow (no make prod-update)
- [ ] Verify ki-core imports work (ConfigManager import errors in isolation)

### Recent Commits (This Session)
1. `360741b` - fix: include loaded files in chat LLM requests
2. Earlier work: docs reorganization, deduplication simplification, feature documentation

---

## 🔧 Key Technical Details

### Logging (CRITICAL - Easy to Get Wrong)
```
Location: /home/flow/dev_flow/kicli-code-assist/log/
Files: kicli.log, kicli-llm.log, kicli-context.log, kicli-workflow.log, kicli-errors.log
Mechanism: flowpy.setup_logger() with RELATIVE paths
Important: NOT configurable via ki.yaml (storage.log_dir is unused)
Timestamps: ISO 8601 format HH:MM:SS.mmm
```

### LLM Integration
- **Module**: `kicli_code_assist/llm_integration.py` (AsyncLLMIntegration class)
- **Query Method**: `async def query(messages, system_prompt=None)`
- **Message Format**: List of `{"role": "user|assistant|system", "content": "..."}`
- **Chat Session**: `chat_session.get_messages_for_api()` includes system prompt + full history
- **Logging**: Uses `get_llm_logger()` → detailed request/response logging

### Chat Submission Flow
```
User types message → _submit_chat(text)
  1. chat_session.add_message("user", text)
  2. api_messages = chat_session.get_messages_for_api()  # Includes loaded files + system prompt
  3. response = await llm.query(messages=api_messages)
  4. chat_session.add_message("assistant", response)
  5. Display response in chat panel
```

### File Loading Flow
```
User presses L → _load_file_to_context(path)
  1. Check if file already loaded (deduplicate via self.loaded_files)
  2. Read file content
  3. Embed as: <file path="...">content</file>
  4. chat_session.add_message("user", embedded_content)  # Stored in messages list
  5. Green checkmark shown
  
When submitting chat:
  → All loaded files are in chat_session.messages
  → get_messages_for_api() returns them in API request
```

---

## 🚀 Deployment Notes

### Current Setup (No Makefile)
- Target hosts: 2 dev machines + staging (HTTPS clones, no Docker)
- Deployment: git push → watch for auto-deployment
- Notification: Tell user if `make prod-update` needed after code changes
  - Both ki-core AND kicli-code-assist changed? → Both need deployment

### Deployment Tooling
- **Harbor + Skopeo** for container management (no Docker on target)
- **Helm** for orchestration
- **Scripts**: `scripts/dev/start-local.sh` and `stop-local.sh` (ports 18080, 18081, 13000)

---

## 📁 Repository Structure

```
~/dev_flow/
├── kicli-code-assist/          # TUI code assistant app
│   ├── docs/                   # Project-specific: features, guides, tracking, architecture
│   ├── log/                    # ← ACTUAL LOG FILES HERE
│   └── ki.yaml                 # Config (no log_dir setting)
├── ki-core/                    # Shared base: commands, config, LLM
│   ├── docs/                   # Shared CLI architecture, implementation status
│   └── src/ki_core/
├── ki-knowledge/               # Knowledge management
├── yaml-cfg-wizard/            # Generic YAML config tool (no ki-core dep)
└── stackctl/
~/dev_doc/                       # Generic documentation (cross-project)
├── DOCUMENTATION_ORGANIZATION_GUIDE.md
├── COPILOT_SESSION_INDEX.md    # ← YOU ARE HERE
└── (other generic guides)
```

---

## 📞 Quick Reference - Most Common Issues

| Issue | Solution | File/Location |
|-------|----------|---------------|
| Logs not appearing | Check `/kicli-code-assist/log/` not `~/dev_flow/log/` | `logging_integration.py:37` |
| Chat messages not sending | Verify `llm.query()` called. Check for errors in `kicli-llm.log` | `ui/app.py:496` |
| Config not loading | Use `ki-core` imports from `/dev_flow/ki-core/src` | `app_config.py:22` |
| Deployment unsure | No `make prod-update` anymore - git push only | `user memory` |
| Files not in LLM request | Check `get_messages_for_api()` is called before `llm.query()` | `ui/app.py:515` |

---

## 📊 Phase Status

- **Phase 2**: ✅ Config system unified in ki-core
- **Phase 3**: ✅ Error handling + validation complete (11/14 config tests pass)
- **Phase 4.1**: 🔄 Async LLM + error handling (ACTIVE - chat fix in progress)
- **Phase 4.2+**: Planned (structure tests, documentation)

---

## 🔗 Session Checkpoints

For detailed history of what happened before this session, see:
- `./checkpoints/027-chat-message-submission-fix-wi.md` - Most recent
- `./checkpoints/026-session-1bb7e508.md` - Previous
- All 27 checkpoints available in session folder

Run `/read checkpoints/XXX-title.md` to see full context.

---

## ✅ Maintenance Checklist

- [ ] Update this file weekly with latest memory additions
- [ ] Link new critical docs here (architecture, guides, tracking)
- [ ] Update current session work section as tasks progress
- [ ] Note deployment requirements after each code change
- [ ] Archive completed tasks to respective FEATURES_IMPLEMENTED.md files
