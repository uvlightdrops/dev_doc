---
name: commit-standards
description: Commit message format, atomic commits, and meaningful commit history
license: CC-BY-4.0
allowed-tools:
  - bash
---

# Commit Standards Skill

You are an expert in writing clear, meaningful commit messages and making atomic changes.

## Core Principles

### 1. Commit Message Format
```
<type>(<scope>): <subject>

<body>

<footer>
```

### 2. Type (required)
- **feat**: New feature
- **fix**: Bug fix
- **refactor**: Code reorganization (no behavior change)
- **docs**: Documentation only
- **test**: Tests only
- **perf**: Performance improvement
- **chore**: Maintenance, dependencies

### 3. Scope (optional)
- Component or module affected: `feat(agent): add reasoning tracer`
- Omit if changes span multiple areas: `feat: redesign config system`

### 4. Subject Line
- Imperative mood: "add feature" not "added feature"
- ≤50 characters
- No period at end
- Lowercase start (except proper nouns)
- Example: `feat(config): add environment variable override`

### 5. Body (optional, recommended for non-trivial changes)
- Explain **why** (not what—that's in code)
- Wrapped at 72 characters
- Separate from subject by blank line
- Example:
  ```
  Implements ConfigResolver with smart merge strategy to support
  layered configuration (defaults → profile → stage → runtime).
  Fixes priority ordering in existing code where env vars were
  not properly overriding config file values.
  ```

### 6. Footer (optional)
- Reference issues: `Closes #123` or `Refs #456`
- Co-authored-by: `Co-authored-by: Name <email@example.com>`
- Example:
  ```
  Closes #123
  Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>
  ```

## Atomic Commits

Each commit should:
- Represent ONE logical change
- Pass tests independently (build doesn't break at any commit)
- Be understandable in isolation (git blame is useful)
- Be reversible (git revert doesn't cause conflicts)

### Example: Feature with 3 atomic commits
```
1. refactor(parser): extract parse_yaml helper function
2. feat(config): add ConfigResolver with merge strategy
3. test(config): add ConfigResolver tests
```

NOT one commit: `feat: refactor parser AND add ConfigResolver AND add tests` (too broad)

## Review Checklist

When reviewing commits:
- [ ] Type + scope clearly indicate change?
- [ ] Subject line ≤50 chars, imperative mood?
- [ ] Body explains **why**, not **what**?
- [ ] Commit is atomic (one logical change)?
- [ ] Commit passes tests independently?
- [ ] Related issues referenced?
- [ ] Co-authored-by trailer if collaborative?

## Anti-Patterns

- Mixing multiple changes (refactor + feature + docs in one commit)
- "WIP" or "temp" commits in main branch (squash or rebase)
- Long subject lines (>50 chars)
- Imperative mood mistakes ("refactored" not "refactor")
- No issue references for tracked work
- Broken builds between commits

---

**Used in projects:** kicli-code-assist, ki-knowledge, ki-core  
**Related skills:** agentic-documentation-patterns  
**Last updated:** 2026-11-07
