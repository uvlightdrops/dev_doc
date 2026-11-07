---
name: pr-workflow
description: Pull request structure, review process, and merge strategies
license: CC-BY-4.0
allowed-tools:
  - bash
---

# PR Workflow Skill

You are an expert in structuring pull requests for efficient review and safe merging.

## Core Principles

### 1. PR Title & Description

**Title Format:**
```
<type>: <subject> (Closes #<issue>)
```

Examples:
- `feat: add config resolver (Closes #123)`
- `fix: handle async timeout in agent (Closes #456)`
- `refactor: split server module (Closes #789)`

**Description Template:**
```markdown
## Changes
- Added ConfigResolver with smart merge strategy
- Support for environment variable overrides
- Backward compatible with existing config files

## Why
Fixes priority ordering where env vars didn't override config files.
Related to #123 (deployment workflow reliability).

## How to Test
1. Set `MODEL=gpt-4` env var
2. Run `pytest tests/test_config_resolver.py`
3. Verify override applied correctly

## Checklist
- [x] Tests added/updated
- [x] Documentation updated
- [x] No breaking changes
- [x] Ready for review
```

### 2. PR Size
- **Optimal:** 200–400 lines changed
- **Acceptable:** Up to 800 lines
- **Too large (>1000 lines):** Split into multiple PRs
  - Reason: Large PRs have lower review quality (reviewers get fatigued)

### 3. Review Process

#### Self-Review First
1. Reread all changes
2. Check for typos, obvious errors
3. Verify tests pass locally
4. Add clarifying comments where needed

#### Request Review
1. Tag reviewers (2–3 reviewers typically)
2. Set review deadline (24–48 hours)
3. Respond promptly to feedback

#### Addressing Feedback
- Commit changes (don't squash yet)
- Request re-review explicitly
- Do NOT force-push during active review (rewrites history confusingly)

#### Before Merge
- Ensure main branch is up-to-date
- Rebase on main (or squash if requested)
- All CI checks passing
- All reviewers approved

### 4. Merge Strategy

**Squash & Merge** (recommended for single feature):
- Combines all commits into one
- Cleaner history, easier git blame
- Use when: Feature had many intermediate commits

**Rebase & Merge** (recommended for multiple atomic commits):
- Replays commits on top of main
- Preserves individual commit messages
- Use when: Each commit is meaningful

**Merge Commit** (for long-lived features or major changes):
- Creates merge commit
- Shows feature branch existed
- Use when: Want to preserve branch history

## Automated Checks (CI/CD)

Typical checks before merge:
- ✅ Tests passing (pytest, linting)
- ✅ Code coverage >70%
- ✅ Branch up-to-date with main
- ✅ No conflicts
- ✅ At least 2 approvals (may vary by repo)

## Review Checklist

When reviewing a PR:
- [ ] PR title clear and references issue?
- [ ] Description explains why, not just what?
- [ ] Changes match the issue scope?
- [ ] Tests added for new behavior?
- [ ] No breaking changes documented?
- [ ] Code follows style guide?
- [ ] Documentation updated?
- [ ] Atomic commits or good squash message?

## Anti-Patterns

- Unrelated changes in one PR (split into separate PRs)
- Missing tests or coverage dropped
- PR description is empty ("see commits")
- Reviewing while tired (defer, don't approve blindly)
- Force-pushing during active review (confuses reviewers)
- Long-lived feature branches (>2 weeks, high merge conflict risk)
- Merging your own PR (always have another reviewer)

---

**Used in projects:** kicli-code-assist, ki-knowledge, ki-core  
**Related skills:** commit-standards, branch-naming  
**Last updated:** 2026-11-07
