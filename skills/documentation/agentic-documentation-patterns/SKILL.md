---
name: agentic-documentation-patterns
description: Apply agent-safe documentation patterns including signed commit blocks, locked/safe zones, change proposals, and freshness metadata. Use when writing docs that agents (including me) will modify, to maintain human trust and auditability.
license: MIT
allowed-tools: []
---

# Agentic Documentation Patterns Skill

## Overview

Patterns for documentation that agents (like me) will modify while maintaining human trust, auditability, and coherence.

## Core Patterns

### 1. Signed Commit Blocks
When an agent modifies a section:
```markdown
[Last updated by Agent on 2026-11-07 10:30 - regenerated from codebase scan]

The content here...

---
**Agent Edit Log:**
- 2026-11-07: Regenerated from code (high confidence)
- 2026-11-06: Refactored examples (medium confidence - review needed)
```

Confidence levels:
- `high`: Verified against tests/code
- `medium`: Changes made but not fully verified
- `requires-human-review`: Uncertain, needs approval

### 2. Locked Blocks & Safe Zones
Mark which sections agents can/cannot edit:
```yaml
agent_permissions:
  - block_id: code-examples
    write_permission: true
    auto_verify: true
    confidence: high
    
  - block_id: design-rationale
    write_permission: false
    reason: "Humans only - philosophical decisions"
```

### 3. Change Proposals
Agents propose changes before applying:
```yaml
agent_change_proposal:
  timestamp: 2026-11-07T10:30:00+01:00
  proposed_by: Agent-v2.1
  confidence: 0.87
  changes:
    - block_id: code-examples
      action: update
      reason: "API changed in v2.0"
      old_snippet: "..."
      new_snippet: "..."
      impact: "Examples now match current signature"
  human_review_required: true
```

### 4. Freshness Metadata
Track how "fresh" each section is:
```yaml
freshness_metadata:
  - block_id: code-examples
    last_verified: 2026-11-07
    auto_update_frequency: weekly
    stale_after_days: 14
    staleness_indicator: "⚠️ AUTO-EXAMPLES"
    
  - block_id: design-rationale
    last_verified: 2026-09-15
    auto_update_frequency: never
    stale_after_days: 180
    staleness_indicator: "📅 DESIGN (verified Sep)"
```

## Core Principle

Make agent changes **obvious**, **reviewable**, and **undoable**. Humans stay in control.

## Full Details

See: `dev_doc/doc/documentation-guidelines/AGENTIC_DOCUMENTATION_PATTERNS.md`
