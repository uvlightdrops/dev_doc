---
name: cross-reference-link-maintenance
description: Update cross-document references and relations when files are moved, renamed, or reorganized. Use after any file move to ensure all links remain valid and bidirectional relations stay consistent.
license: MIT
allowed-tools: []
---

# Cross-Reference Link Maintenance Skill

## Overview

When you move or rename documentation files, all references to that file must be updated to prevent broken links and maintain semantic coherence.

## Process

### Step 1: Identify All References
```bash
# Find all docs referencing the old path
grep -r "old/path/FILE.md" doc/
grep -r "old-file-name" doc/ --include="*.md"
```

### Step 2: Update Reference Relations
In **source docs** (files that pointed TO the moved doc):
```yaml
relations:
  # BEFORE
  - target: doc/old/path/FILE.md
    type: reference
    
  # AFTER
  - target: ../new/path/FILE.md
    type: reference
```

### Step 3: Update Moved Doc's Relations
The **moved doc itself** should reference back:
```yaml
relations:
  # BEFORE (if it had relations)
  - target: ../old/location/OTHER.md
    
  # AFTER (adjust paths)
  - target: ../../new/relative/path/OTHER.md
  
  # ADD: Relation to new parent README (if applicable)
  - target: README.md
    type: parent
    reason: "New hub for this folder"
```

### Step 4: Update Relations in Both Directions
For bidirectional links:
- If Doc A depends-on Doc B, then Doc B should have reference to Doc A
- Example:
  ```
  Backend doc (depends-on TUI doc):
    relations:
      - target: ../../../frontend-tui/FILE.md
        type: reference
        
  TUI doc (depends on backend):
    relations:
      - target: ../../../backend/layer-2/FILE.md
        type: depends-on
  ```

### Step 5: Verify No Broken Links
```bash
# Check for old path references
grep -r "old/path" doc/

# Should return: nothing (or only comments explaining the move)
```

### Step 6: Update YAML Headers
In moved doc, update:
```yaml
updated: [Today's date]
update_reason: "Moved from doc/old/path/ to doc/new/path/"
```

## Path Format Guidelines

- **Use relative paths:** `../../../folder/file.md`
- **Never use absolute paths:** `/home/flow/dev_flow/...` ❌
- **Verify paths exist** before committing
- **Count ../ carefully:** Each goes up one level

## Full Details

See: `dev_doc/doc/documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md`
