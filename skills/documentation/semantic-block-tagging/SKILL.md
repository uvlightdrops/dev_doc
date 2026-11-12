---
name: semantic-block-tagging
description: Tag documentation H2 headings with unique semantic block IDs. Use when creating new doc structure or upgrading existing docs to make them machine-readable and navigable.
license: MIT
allowed-tools: []
---

# Semantic Block Tagging Skill

## Overview

Every major section (H2 heading) in a well-structured doc gets a unique semantic block ID, making docs queryable by tools and easier for humans to reference.

**Required: stable ID in every H2 heading and visible local metadata below it.
Do not duplicate local attributes in document frontmatter.**

## Block ID Format

- **Kebab-case:** `my-block-id`, not `MyBlockId` or `my_block_id`
- **Length:** ≤38 characters (for display)
- **Meaningful:** Reflect section content
- **Unique:** No duplicates within a single doc
- **Stable:** Don't change IDs once published (breaks links)

## Tagging Format

### 1. Local metadata (REQUIRED)
````markdown
## Overview [overview]

```yaml block-meta
updated: "2026-11-11 05"
status: active
relations: []
```

Section content.
````

### 2. H2 Heading Tags
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

**Verification:** Count real H2 headings = Count local metadata fences.
Ignore headings inside fenced examples. An optional `semantic_blocks`
header list contains IDs only and must match headings if present.

Each new or deliberately revised block requires local `updated`, `status`
and `relations` directly below its heading, not in the header. Use quoted `YYYY-MM-DD HH`,
`draft|active|archived`, and a relation list with `target`, `type`, `reason`
or `[]`. Local values are not inherited from document metadata.
Update only changed blocks; preserve stable IDs. Legacy ID-only indexes may remain, but do not count as complete metadata.
The authoritative contract is
`dev_doc/doc/documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md#block-metadaten`.

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

## Heading coverage

Every H2 heading is a semantic block and requires an ID and local
metadata fence, including examples, testing and reference sections.
H3 subsections do not automatically create separate semantic blocks.

## Relations to Blocks

When linking to a specific block:
```yaml
relations:
  - target: ../other-doc.md#section-title-id
    type: reference
    reason: "For semantic block details"
```

(Note: Path#block-id syntax for cross-doc block refs)

For a relationship originating from a particular block, put this list
inside that block's local `yaml block-meta` fence rather than at document level.
Verify the target block and a semantically appropriate reciprocal relation.

## Full Details

See: `dev_doc/doc/documentation-guidelines/SEMANTIC_KNOWLEDGE_ORGANIZATION.md`
