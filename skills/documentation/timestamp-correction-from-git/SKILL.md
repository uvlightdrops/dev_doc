---
name: timestamp-correction-from-git
description: Correct YAML header timestamps by extracting creation date from git log and setting update date to today. Use when YAML headers have wrong created dates or when updating docs for the first time.
license: MIT
allowed-tools: []
---

# Timestamp Correction From Git Skill

## Overview

Ensures all documentation has accurate timestamps: `created` from original git commit, `updated` as today's date.

## Process

### Step 1: Get Original Creation Date
```bash
git log --follow --format="%aI" --diff-filter=A -- doc/path/to/file.md | tail -1
```

This returns: `2026-10-17T11:09:04+02:00`

### Step 2: Update YAML Header
```yaml
created: 2026-10-17T11:09:04+02:00  # From git log
updated: 2026-11-07T15:10:00+01:00  # Today's date
update_reason: "Brief explanation of what changed"
```

### Step 3: Timezone Handling
- Preserve timezone from git log (may differ: +02:00 summer, +01:00 winter)
- Today's date uses current timezone (+01:00 in November)
- **Do not normalize** — keep original timestamp as-is

### Step 4: Batch Process
For multiple files:
```bash
for file in doc/**/*.md; do
  created=$(git log --follow --format="%aI" --diff-filter=A -- "$file" | tail -1)
  echo "$file: $created"
done
```

## When to Use

- After creating new semantic headers
- After major doc reorganization
- When git history is reliable (no rewrites)
- When you need auditable "creation date" vs. "last modified"

## Important Notes

- `created` should NEVER change (tracks original authorship)
- `updated` changes every time doc is edited
- Timezone info is important (don't strip it)
- If git history missing, ask user for creation date

## Full Details

See: `dev_doc/documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md`
