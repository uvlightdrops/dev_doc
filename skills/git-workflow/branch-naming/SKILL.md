---
name: branch-naming
description: Branch naming conventions and workflow integration
license: CC-BY-4.0
allowed-tools:
  - bash
---

# Branch Naming Skill

You are an expert in consistent branch naming that communicates intent and integrates with CI/CD.

## Core Conventions

### Format
```
<type>/<issue-number>-<description>
```

### Type
- **feature**: New feature (`feature/123-add-config-resolver`)
- **fix**: Bug fix (`fix/456-timeout-in-agent`)
- **refactor**: Code reorganization (`refactor/789-split-server-module`)
- **docs**: Documentation only (`docs/101-api-reference`)
- **perf**: Performance improvement (`perf/202-optimize-query`)
- **test**: Tests only (`test/303-add-coverage`)

### Issue Number
- Reference GitHub issue: `feature/123-...`
- Omit for typos/minor fixes: `fix/typo-in-comment` (if no issue)

### Description
- Kebab-case: `feature/123-add-config-resolver` ✓
- No: `feature/123-Add Config Resolver` ✗
- Concise: 2–4 words
- Examples:
  - `feature/42-github-oauth-login`
  - `fix/89-async-timeout-handling`
  - `refactor/156-extract-validation-module`

## Lifecycle

### Creation
```bash
# Create from main, ensuring it's up-to-date
git checkout main
git pull origin main
git checkout -b feature/123-describe-feature
```

### Development
- Regular commits: `git commit -m "feat(module): describe change"`
- Push often: `git push origin feature/123-describe-feature`

### Before Merge
```bash
# Update from main (rebase, not merge)
git fetch origin
git rebase origin/main

# Force-push your rebased branch
git push --force-with-lease origin feature/123-describe-feature
```

### After Merge
- Delete local: `git branch -d feature/123-describe-feature`
- Delete remote: `git push origin --delete feature/123-describe-feature`

## CI/CD Integration

Branch naming enables:
- Automated deployments: `main` → production, `develop` → staging
- PR workflow rules: `feature/*` requires 2 approvals, `docs/*` auto-merge
- Release notes: `feat/...` grouped as features, `fix/...` as fixes

## Review Checklist

- [ ] Type matches change (feature, fix, refactor)?
- [ ] Issue number included (if related to issue)?
- [ ] Description kebab-case and concise?
- [ ] Branch updated with main before PR?
- [ ] Branch deleted after merge?
- [ ] No stale branches >30 days old?

## Anti-Patterns

- Non-descriptive names (`feature/work`, `fix/stuff`)
- Personal names (`john-stuff`, `alice-experiment`)
- Mixed types in one branch (do separate branches)
- Branches pushed without commits
- Long-lived branches (>2 weeks unmerged, risk conflicts)

---

**Used in projects:** kicli-code-assist, ki-knowledge, ki-core  
**Related skills:** commit-standards, pr-workflow  
**Last updated:** 2026-11-07
