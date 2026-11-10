---
title: Documentation Tracker Maintenance Rules
project: kicli-code-assist
scope: Copilot Directive
purpose: Rules for maintaining doc/DOCUMENTATION_MASTER_TRACKER.md in lean, actionable state
created: 2026-11-07T18:54:00+01:00
updated: "2026-11-10 20"
update_reason: "Relocate shared documentation into doc/ and update references"
status: active
---

# Documentation Tracker Maintenance Rules

## Purpose
Keep `doc/DOCUMENTATION_MASTER_TRACKER.md` lean, current, and immediately useful—**not** a general todo list or narrative document.

---

## 🟢 What Goes IN the Tracker

### 1. Status Matrix (Folder × Skills)
- Single table: all folders vs. all documentation skills
- One character per cell (✅ ⚠️ 🔶 ❌ ⏸️)
- Updated immediately after folder work

### 2. File-Level Details (Per Folder)
- **One table per folder being actively worked on**
- Columns: File | YAML | Blocks | Scope | Status | Brief Notes
- Brief notes only: file counts, block counts, specific gaps
- Example: "11 H2, 7 blocks (4 missing)" — no explanation needed

### 3. Session Log (What Was Done)
- Date | Action | Files | Status
- One-liner actions only
- Updated after each commit

---

## 🔴 What Does NOT Go IN the Tracker

- ❌ Long prose sections ("Next Steps", "Phase I/II/III")
- ❌ Detailed TODO lists
- ❌ Explanations of why something is broken
- ❌ Multi-paragraph audit findings
- ❌ Strategic planning
- ❌ Skills descriptions or procedures

**These belong in separate docs or announced verbally.**

---

## 🔵 Copilot Behavior Rules

### Rule 1: Announce Large Work First
**Before writing or reorganizing prose in tracker:**
1. Describe what you plan to do (one sentence)
2. Ask for approval
3. Only proceed if approved

**Example:** "Ich will den 'Audit Findings' Abschnitt rauskürzen und durch eine Session Log Zeile ersetzen. OK?"

### Rule 2: Update Matrix After Folder Work
**Immediately after modifying files in a folder:**
- Update that folder's row in the status matrix
- Update the corresponding file-detail table
- Commit tracker changes

### Rule 3: No Duplicate Information
- If it's in the file-detail table, don't repeat it in prose
- If it needs explanation, **announce it first** before writing

### Rule 4: Keep Rows Recent
- Remove folders from detail tables once they're complete (✅)
- Keep only actively-worked folders visible
- Archive completed work in commit log, not in tracker

---

## ✅ Update Pattern

After every documentation change:

```
1. Modify doc file(s)
2. Update DOCUMENTATION_MASTER_TRACKER.md:
   - Folder × Skills matrix (one char change)
   - File-detail table (one row update)
   - Session log (one row append)
3. Commit with reference to tracker
```

---

## 📋 Maintenance Checklist

- [ ] Status matrix has one character per cell (✅ ⚠️ 🔶 ❌ ⏸️)
- [ ] No folder detail table longer than 10 rows
- [ ] No prose longer than 2 sentences outside tables
- [ ] Session log entries are date | action | files | status
- [ ] All completed folders removed from detail view
- [ ] Updated timestamp at top of file

---

## When to Announce (Pause & Ask)

Ask user approval **before** doing any of these:
- Adding a new section to tracker
- Removing a section
- Creating new detail tables
- Writing more than 2 sentences of prose
- Reorganizing the overall structure

---

## Cross-Reference

- **Main Tracker:** `doc/DOCUMENTATION_MASTER_TRACKER.md`
- **Skills Reference:** `dev_doc/skills/documentation/`
- **Related:** `documentation-maintenance-standards` skill
