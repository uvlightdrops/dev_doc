---
title: TUI Development Strategy - Design Decision Documentation
scope: cross_project
project: kicli-code-assist
status: active
created: 2026-11-05T19:45+01:00
updated: 2026-11-05T19:45+01:00
update_reason: Clarify two conflicting TUI design approaches discovered during Phase 3 refinement
agent_project: kicli-code-assist
cross_references: 
  - kicli-code-assist/doc/specifications/02_TUI_SPECIFICATION.md
  - kicli-code-assist/doc/guides/TUI_CLIENT_IMPLEMENTATION_PLAN.md
  - /home/flow/dev_flow/kicli-code-assist/.copilot/session-state/*/checkpoints/008-tui-implementation-complete-co.md
---

# TUI Strategy Clarification: Two Design Approaches

## Problem Statement

The kicli-code-assist project has **two conflicting TUI design specifications**:

1. **OLD SPEC** (October 2026): `doc/specifications/02_TUI_SPECIFICATION.md`
   - Terminal-based chat interface (File Browser + Chat + Input)
   - Inspired by CLI productivity tools
   - Focus on interactive file selection + conversation model
   - Uses SelectableFileList, ChatDisplay, MultilineInput widgets

2. **NEW SPEC** (November 2026, Session 20261105): `doc/guides/TUI_CLIENT_IMPLEMENTATION_PLAN.md`
   - Agent backend interface (Project Explorer + Agent Output + Request Input)
   - Exact analog to WebUI
   - Focus on code analysis workflow (agent orchestration)
   - Uses ProjectExplorer, AgentOutputPanel, InputPanel widgets

## Current Implementation Status

### What Was Built (Nov 5, 2026)
- **Implementation**: NEW SPEC approach (ProjectExplorer + AgentOutputPanel + InputPanel)
- **Status**: Phase 1-3 complete, Phase 4 CLI integration complete
- **Code**: ~1,150 LOC across 13 new files
- **State**: Fully functional TUI, backend connectivity working ✅
- **Commits**: 7745c49, 1258f17, fd6309a, 89234bc, e820b28, 1dd0e95, 7f7a5fe

### What Was NOT Built
- OLD SPEC components (SelectableFileList, ChatDisplay, MultilineInput)
- Chat history persistence
- File preview pane
- Role-switching UI

## Design Decision: Which Approach?

### OLD SPEC: Interactive Chat Model
**Pros:**
- More natural for exploratory code conversation
- File browser + preview + chat in single interface
- Session persistence could be implemented
- Familiar to users of CLI tools (tmux, vim)

**Cons:**
- Doesn't leverage full agent backend capabilities
- No visualization of reasoning traces, metrics, dependencies
- Requires custom chat state management
- Misses Phase 5.5.2 transparency features (WebSocket streaming, tracing)

### NEW SPEC: Agent Backend Interface (CHOSEN)
**Pros:**
- ✅ **Exact analog to WebUI** — same backend, different frontend
- ✅ **Leverages Phase 5.5.2 features** — reasoning traces, performance metrics, streaming
- ✅ **Consistent UX** — jump host users get same experience as WebUI users
- ✅ **Lower complexity** — reuses existing agent orchestration, no custom chat logic
- ✅ **Future-proof** — extends automatically when agent capabilities grow
- ✅ **Simpler testing** — same API surface, test once
- ✅ **Operational** — fits into DevOps workflow (K8s jump host, automated pipelines)

**Cons:**
- Less "natural" for exploratory chat (more structured, workflow-oriented)
- Requires understanding of agent modes (Explain, Suggest, Reason, etc.)
- No persistent chat history (stateless per request)

## Recommendation

**Use the NEW SPEC approach** (currently implemented) for these reasons:

1. **Operational Context**: User requested TUI for K8s jump host control, not for casual code exploration
2. **Feature Parity**: Jump host users need same agent capabilities as WebUI users
3. **Maintenance**: One codebase, one backend, two frontends (DRY principle)
4. **Phase Alignment**: Builds on completed Phase 5.5.2 (transparency & observability)
5. **Timeline**: Already working, just needs polish and documentation updates

## Documentation Updates Needed

### 1. Archive OLD SPEC
- Move `doc/specifications/02_TUI_SPECIFICATION.md` → `doc/specifications/archive/02_TUI_SPECIFICATION_LEGACY_2026.md`
- Add note: "This represents an earlier chat-based design. See TUI_STRATEGY_CLARIFICATION.md for context."
- Add YAML header with `status: archived, reason: superseded by agent-backend approach`

### 2. Clarify NEW SPEC Documentation
- Update `doc/guides/TUI_CLIENT_IMPLEMENTATION_PLAN.md`:
  - Add YAML header (missing currently)
  - Add section: "Why Agent Backend Interface?"
  - Reference this decision document
  - Update status → `in_progress` → `phase_3_complete, phase_4_in_progress`

### 3. Create Architecture Documentation
- New file: `doc/architecture/TUI_TEXTUAL_ARCHITECTURE.md`
  - Deep dive into widget hierarchy
  - Async patterns used
  - CSS layout strategy (why horizontal layout, why 60 char fixed width for tree)
  - Event flow: User input → TUI → HTTP API → Agent → WebSocket streaming → Display

### 4. Update Quick Start
- Verify `doc/guides/TUI_QUICK_START.md` reflects reality
- Add examples for each agent mode
- Include troubleshooting section

### 5. Add README to doc/specifications
- Create `doc/specifications/README.md` explaining what each spec file covers
- Link to architecture docs for implementations
- Note which specs are active vs. archived

## Implementation Checklist

### Phase 4 Completion (This Session)
- [ ] Commit TUI code (layouts, connectivity fix)
- [ ] Archive old spec with YAML header
- [ ] Update TUI_CLIENT_IMPLEMENTATION_PLAN with YAML header
- [ ] Create TUI_TEXTUAL_ARCHITECTURE.md deep dive
- [ ] Create doc/specifications/README.md
- [ ] Update TUI_QUICK_START.md

### Phase 5: Testing & Integration (Next Session)
- [ ] E2E test: File selection → Request → Agent response → Display
- [ ] Streaming test: WebSocket events display in real-time
- [ ] Test all agent modes (Explain, Suggest, Metrics, Refactor, etc.)
- [ ] Performance test: Large codebase (1000+ files)
- [ ] Error handling test: Backend unavailable, timeout, malformed response

### Phase 6: Production Readiness
- [ ] Keyboard shortcuts help modal
- [ ] Offline mode / cached responses
- [ ] Session persistence (optional, post-Phase 5)
- [ ] Performance optimization (Tree rendering, streaming buffer size)

## Timeline

- **Nov 5, 2026 - 19:53 MEZ**: This decision documented
- **Nov 5, 2026 - 20:00 MEZ**: Documentation updates (1.5 hours)
- **Nov 6-7, 2026**: Testing & Phase 5 integration
- **Nov 8, 2026**: Production readiness review

---

**Decision**: Proceed with NEW SPEC (Agent Backend Interface) approach.  
**Archive**: OLD SPEC (Chat Interface) — legacy, superseded.  
**Next**: Documentation updates + testing integration.