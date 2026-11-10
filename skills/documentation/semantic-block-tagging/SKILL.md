---
name: semantic-block-tagging
description: Tag documentation H2 headings with unique semantic block IDs. Use when creating new doc structure or upgrading existing docs to make them machine-readable and navigable.
license: MIT
allowed-tools: []
---

# Semantic Block Tagging Skill

## Overview

Every major section (H2 heading) in a well-structured doc gets a unique semantic block ID, making docs queryable by tools and easier for humans to reference.

**⚠️ CRITICAL: Tags must appear BOTH in YAML header AND on H2 headings themselves**

## Block ID Format

- **Kebab-case:** `my-block-id`, not `MyBlockId` or `my_block_id`
- **Length:** ≤38 characters (for display)
- **Meaningful:** Reflect section content
- **Unique:** No duplicates within a single doc
- **Stable:** Don't change IDs once published (breaks links)

## Tagging Format - BOTH LOCATIONS REQUIRED

### 1. YAML Header (REQUIRED)
```yaml
---
semantic_blocks: [overview, component-inventory, design-principles, implementation-order, data-structures]
---
```

### 2. H2 Heading Tags (REQUIRED - DO NOT SKIP!)
```markdown
## Overview [overview]

Content here...

### Subsection (no tag)
(No tag needed - only H2 gets tagged)

---

## Component Inventory [component-inventory]

More content...

## Design Principles [design-principles]

...
```

**Verification:** Count H2 headings = Count semantic_blocks entries

## Typical Block IDs in kicli-code-assist

### Documentation Docs
- `semantic-overview` — Introduces the concept
- `yaml-header-format` — YAML structure
- `block-id-guidelines` — Block naming rules
- `implementation-checklist` — Step-by-step process

### Feature/Architecture Docs
- `architecture-overview` — High-level design
- `quick-start-guide` — Getting started
- `implementation-details` — How it works
- `operations-reference` — Usage & APIs
- `troubleshooting-faq` — Common issues

### Phase/Status Docs
- `completion-summary` — What was done
- `testing-results` — Quality assurance
- `next-steps` — What's planned
- `known-limitations` — Caveats

## Blocks NOT Marked

Support sections typically don't need IDs:
- Single `## Examples` section (obvious)
- `## See Also` (self-explanatory)
- `## Testing` (transient)
- `## Backward Compatibility` (standard section)

Only mark if the section is:
- Referenced from other docs
- A major topic
- Likely to be queried/analyzed

## Relations to Blocks

When linking to a specific block:
```yaml
relations:
  - target: ../other-doc.md#section-title-id
    type: reference
    reason: "For semantic block details"
```

(Note: Path#block-id syntax for cross-doc block refs)

## Full Details

See: `dev_doc/doc/documentation-guidelines/SEMANTIC_KNOWLEDGE_ORGANIZATION.md`
