---
title: Copilot Memory Management
scope: AI session memory and checkpoint tracking
purpose: How to use and manage Copilot memory across sessions
status: active
created: 2026-11-05 23
updated: 2026-11-10 23
update_reason: Validate YAML metadata and align semantic blocks with section headings
project: dev_doc
agent_project: ki-ecosystem
semantic_blocks:
- files-in-this-folder
- quick-reference
- session-setup-checklist
---

# 🧠 Copilot Memory & Sessions

This folder contains guidance on using Copilot session memory, checkpoint tracking, and AI-assisted session coordination.

<a id="-files-in-this-folder"></a>

## 📋 Files in This Folder [files-in-this-folder]

### `COPILOT_MEMORY_MAP.md`
**How to use Copilot session memory (store, vote, management)**
- User vs. repository memory scope
- When to store, upvote, downvote
- Memory lifecycle and best practices
- Verification before using memories
- **→ Read this first** before working with memory

### `COPILOT_SESSION_INDEX.md`
**Checkpoint tracking and session history**
- Session metadata and artifacts
- Checkpoint archival
- Continuity across sessions
- Session state management
- **→ Use to** track prior work and session flow

### `COPILOT_DOCUMENTATION_BEHAVIOR.md`
**How Copilot should document during sessions**
- Session documentation patterns
- Checkpoint content guidelines
- Archival procedures
- Documentation timing
- **→ Reference when** setting up doc-focused sessions

<a id="-quick-reference"></a>

## 🎯 Quick Reference [quick-reference]

### When to Store Memory
- ✅ Verified best practices or patterns
- ✅ Architectural decisions with evidence
- ✅ Repository-specific conventions
- ✅ User's cross-repo workflow preferences
- ❌ Session-specific instructions
- ❌ Unverified claims
- ❌ Temporary task status

### Memory Scope
- **User-scoped** (this user across all repos): Cross-repo preferences, personal style
- **Repository-scoped** (this repo for all contributors): Project conventions, patterns

### Voting on Memory
- **Upvote**: Verified, useful, correct
- **Downvote**: Outdated, incorrect, or user requested deletion

<a id="-session-setup-checklist"></a>

## ✅ Session Setup Checklist [session-setup-checklist]

- [ ] Read relevant checkpoint from prior sessions
- [ ] Review MEMORY_MAP.md for memory best practices
- [ ] Check existing memories for context
- [ ] Note which memories to verify during this session
- [ ] Set up checkpoint naming convention
- [ ] Plan when/how to create next checkpoint

---

**Last Updated**: 2026-11-05  
**Status**: ✅ Active & Maintained