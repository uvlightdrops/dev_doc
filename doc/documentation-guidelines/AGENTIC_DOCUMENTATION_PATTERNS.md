---
title: Agentic Documentation Patterns - Human-AI Collaboration in Docs
project: cross-project
agent_project: ki-knowledge
scope: architecture
purpose: Best practices for agents modifying documentation while maintaining human trust, auditability, and coherence
created: 2026-11-07 10
updated: 2026-11-10 23
update_reason: Validate YAML metadata and align semantic blocks with section headings
status: draft
cross_references:
- SEMANTIC_KNOWLEDGE_ORGANIZATION.md (foundation)
- ki-knowledge/docs (implementation target)
semantic_blocks:
- core-tension
- transparency-over-automation
- signed-edit-log
- locked-and-safe-zones
- change-proposals
- freshness-metadata
- conflict-resolution
- section-versioning
- audit-trail
- agent-trust-scoring
- failure-recovery
- collaboration-contract
- incremental-adoption
- open-questions
- knowledge-integration
- core-principle
---

<a id="free-form-thinking-the-core-tension"></a>

## Free-Form Thinking: The Core Tension [core-tension]

**The Problem We're Actually Solving:**

An agent can write code, generate docs, update examples... fast. But humans need to trust what the agent did. Not paranoia-trust ("did it inject malware?") but *coherence-trust*: "Did it understand what this doc is supposed to be? Did it break the narrative?"

**The Fear:**
- Agent updates a section and now it contradicts another section the agent didn't read
- Agent adds an example that's outdated in 2 weeks
- Agent changes a critical section and nobody notices
- Agent "helpfully" refactors the whole structure and humans hate it
- Humans review changes, approve them, then later realize they didn't understand what the agent actually meant

**The Opportunity:**
- Agent can keep examples in sync with code (humans can't efficiently)
- Agent can spot orphaned docs and flag them
- Agent can enforce consistency across multiple docs at once
- Agent can generate boilerplate and let humans focus on *meaning*
- Agent can track who said what and when

---

<a id="core-principle-transparency-over-automation"></a>

## Core Principle: Transparency Over Automation [transparency-over-automation]

Don't hide what the agent changed. Make it **obvious**. Make it **reviewable**. Make it **undoable**.

This is different from code. Code changes are small and precise. Doc changes are semantic – you need context to understand if they're good.

---

<a id="pattern-1-the-signed-commit-block"></a>

## Pattern 1: The Signed Commit Block [signed-edit-log]

When an agent modifies a doc, it adds metadata to every change:

```markdown
## Architecture Overview

[Last updated by Agent on 2026-11-07 10:30 - regenerated from codebase scan]

The ProjectAnalyzer consists of...

---
**Agent Edit Log for this section:**
- 2026-11-07: Regenerated overview from code introspection (high confidence)
- 2026-11-06: Refactored examples (medium confidence - requires human review)
```

**Why?**
- Humans see immediately "an agent touched this"
- Humans know WHEN and WHY
- Humans can decide: "Do I trust this?" or "I need to hand-review this"
- Later, when someone reads the doc, they know "this part is auto-kept-in-sync" vs. "this was hand-written and frozen"

**The "confidence" flag matters:**
- `high confidence`: "I scanned the code and regenerated examples - they're correct"
- `medium confidence`: "I refactored this but didn't verify it against real tests"
- `requires-human-review`: "I made changes but I'm uncertain - please check me"

---

<a id="pattern-2-locked-blocks--safe-zones"></a>

## Pattern 2: Locked Blocks & Safe Zones [locked-and-safe-zones]

Not all parts of a doc should be agent-writable.

```yaml
---
agent_permissions:
  - block_id: code-examples
    write_permission: true
    auto_verify: true    # Run example code against tests
    confidence: high
    
  - block_id: design-rationale
    write_permission: false   # Human owns this
    reason: "Philosophical decisions only humans make"
    
  - block_id: performance-metrics
    write_permission: true
    auto_verify: false   # Can't verify without prod data
    confidence: low      # Agent says: "I generated this but I might be wrong"
---
```

**In the doc itself, mark the zones:**

````markdown
## Design Rationale [HUMAN-OWNED]
Why did we choose this architecture? Because...
*(Only humans write this. Agents may suggest edits as comments.)*

## Code Examples [AUTO-MAINTAINED]
Here's how to use it:
```python
analyzer = ProjectAnalyzer(project_root)
results = analyzer.analyze()
```
*(Agent regenerates these from source code & tests. Trust high.)*

## Performance Characteristics [AGENT-MAINTAINED-LOW-CONFIDENCE]
Typical analysis time: 4.6 seconds per 100 files
*(Agent computed this from benchmarks. Verify against your environment.)*
````

**Why?**
- Humans don't have to defend their design choices to an agent
- Agents don't accidentally overwrite human philosophy with generated text
- Expectations are clear: "This section auto-updates. That one doesn't."

---

<a id="pattern-3-change-proposals-not-invasive-edits"></a>

## Pattern 3: Change Proposals, Not Invasive Edits [change-proposals]

Agent shouldn't just modify docs silently. It should:

```yaml
agent_change_proposal:
  timestamp: 2026-11-07T10:30:00+01:00
  proposed_by: CodeImplementationAgent-v2.1
  confidence: 0.87
  changes:
    - block_id: code-examples
      action: update
      reason: "Found 3 outdated examples in code review"
      old_snippet: |
        analyzer = ProjectAnalyzer(root="/path/to/project")
      new_snippet: |
        analyzer = ProjectAnalyzer(project_root="/path/to/project")
      impact: "API changed in v2.0; examples now match current signature"
      
    - block_id: performance-metrics
      action: update
      reason: "Benchmarked new implementation"
      old_value: "4.6 seconds per 100 files"
      new_value: "2.1 seconds per 100 files (50% improvement)"
      impact: "Significant performance change - verify in production"
      
  human_review_required: true
  automatic_merge_ok: false
  
  comment: "Confidence on performance update is lower because benchmark
            ran on local machine, not prod. Recommend re-running on
            staging before merging."
```

**Workflow:**
1. Agent creates proposal (doesn't merge yet)
2. Human reviews: "Looks good" or "Stop, I need to understand this"
3. If approved: Agent merges and adds edit log
4. If rejected: Agent learns from rejection (feedback loop)

**Why?**
- Humans stay in control
- Changes are auditable (human approved this)
- Human understands the "why" before it's applied
- Builds trust over time

---

<a id="pattern-4-metadata-for-freshness--staleness"></a>

## Pattern 4: Metadata for Freshness & Staleness [freshness-metadata]

Each section tracks its "last verified" and "auto-update frequency":

```yaml
freshness_metadata:
  - block_id: code-examples
    last_verified: 2026-11-07
    auto_update_frequency: weekly   # Agent runs verification weekly
    verification_method: run_tests
    stale_after_days: 14            # If 14 days pass without update, mark stale
    staleness_indicator: "⚠️ AUTO-EXAMPLES"  # Show in rendered doc
    
  - block_id: design-rationale
    last_verified: 2026-09-15
    auto_update_frequency: never
    verification_method: manual
    stale_after_days: 180          # Hand-written docs can be older
    staleness_indicator: "📅 DESIGN (verified Sep 2026)"
    
  - block_id: performance-metrics
    last_verified: 2026-11-02
    auto_update_frequency: monthly
    verification_method: benchmark
    stale_after_days: 45
    staleness_indicator: "📊 PERF (Nov 2)"
```

**In rendered doc:**
```
## Code Examples [⚠️ AUTO-EXAMPLES | Updated: Nov 7]
...

## Design Rationale [📅 DESIGN | Verified: Sep 15]
...

## Performance [📊 PERF | Nov 2]
...
```

**Why?**
- Readers know "this example is fresh" vs. "this might be stale"
- Humans don't have to remember when they last updated something
- CI can flag "this doc block is overdue for verification"
- Builds narrative of trust: "Auto-maintained = fresh; Human-maintained = thoughtful"

---

<a id="pattern-5-conflict-resolution-in-multi-block-consistency"></a>

## Pattern 5: Conflict Resolution in Multi-Block Consistency [conflict-resolution]

Hardest problem: Agent updates Block A, and now it contradicts Block B (which is human-maintained).

```yaml
consistency_checks:
  - name: "Examples match current API"
    blocks: [code-examples, api-reference]
    rule: "All function signatures in examples must match api-reference"
    action: "Warn if examples are older than api-reference"
    owner: agent-validator
    
  - name: "Performance metrics vs. Benchmarks"
    blocks: [performance-metrics, implementation-notes]
    rule: "Performance values must be within 10% of stated implementation"
    action: "Fail if discrepancy > 10%"
    owner: ci-pipeline
    
  - name: "Design rationale consistency"
    blocks: [design-rationale, architecture-overview]
    rule: "Architecture overview must align with design decisions"
    action: "Flag for manual review if changes detected"
    owner: human-reviewer
```

**Workflow when conflict detected:**
1. Agent runs consistency check
2. If high-confidence conflict: Block the change, require human approval
3. If low-confidence conflict: Add comment "I think this might contradict [block], human please check"
4. Human resolves: "Yes update both" or "No, keep as-is" or "Here's the right fix"

**Why?**
- Docs stay coherent even when multiple agents/humans touch them
- Conflicts surface *before* merge, not in prod
- Humans make final call on "truth"

---

<a id="pattern-6-versioning-changes-not-just-files"></a>

## Pattern 6: Versioning Changes, Not Just Files [section-versioning]

When an agent makes significant changes, it documents it like a code changelog:

````markdown
## Code Examples [AUTO-MAINTAINED]

**Version History:**
- v2.1 (2026-11-07): Updated to match v2.0 API, added async example
- v2.0 (2026-11-02): Added error handling patterns
- v1.0 (2026-10-15): Initial examples

Here's how to use it:
```python
# v2.1 example
analyzer = ProjectAnalyzer(project_root="/path/to/project")
results = await analyzer.analyze_async()
```
````

**Why?**
- Readers see "what changed" without git history
- Easy to understand evolution of docs
- Helps humans decide "should I update my code to v2.1?"
- Creates narrative: "Oh, we added async in v2.1"

---

<a id="pattern-7-the-audit-trail-for-blame--trust"></a>

## Pattern 7: The Audit Trail for Blame & Trust [audit-trail]

Every change to doc metadata should answer:
- WHO changed it? (Human name or Agent ID)
- WHEN? (Timestamp)
- WHY? (Link to issue, PR, decision, or "automated verification")
- WHAT CHANGED? (Diff)
- WAS IT REVIEWED? (By whom, when)

```yaml
audit_trail:
  - timestamp: 2026-11-07T10:35:00+01:00
    actor: Agent-CodeImplementationAgent-v2.1
    action: update_block
    block_id: code-examples
    reason: "Automated verification - found 2 outdated examples in PR review"
    diff_summary: "Updated 2 function signatures, added 1 new example"
    requires_approval: true
    approval_status: pending
    
  - timestamp: 2026-11-07T10:40:00+01:00
    actor: Human-uvlightdrops
    action: approve_change
    comment: "Looks good, verified examples run locally"
    
  - timestamp: 2026-11-07T10:40:30+01:00
    actor: Agent-CodeImplementationAgent-v2.1
    action: merge_change
    reason: "Approval granted, auto-merging"
    commit_hash: abc1234
```

**Why?**
- Complete audit trail: who said what when
- Humans can trace "why is this the way it is?"
- If something breaks, you know who/what to contact
- Builds organizational memory

---

<a id="pattern-8-trust-scoring-over-time"></a>

## Pattern 8: Trust Scoring Over Time [agent-trust-scoring]

Each agent builds a "trust score" based on its proposal accuracy:

```yaml
agent_stats:
  agent_id: CodeImplementationAgent-v2.1
  proposals_made: 147
  proposals_approved: 142
  proposals_rejected: 3
  proposals_merged_then_reverted: 2
  trust_score: 0.96
  
  confidence_history:
    high_confidence_proposals:
      accurate: 98
      inaccurate: 1
      accuracy_rate: 0.99
    medium_confidence_proposals:
      accurate: 38
      inaccurate: 2
      accuracy_rate: 0.95
    low_confidence_proposals:
      accurate: 6
      inaccurate: 1
      accuracy_rate: 0.86
```

**Usage:**
- Low trust score? Require human approval for all proposals
- High trust score? Auto-merge non-controversial changes
- Track by confidence level: "Agent is 99% accurate on high-confidence, 86% on low-confidence"

**Why?**
- Transparency: Humans can see "is this agent trustworthy?"
- Incentivizes accuracy: Agents learn from rejections
- Enables gradual automation: Start with manual review, move to auto-merge as trust grows

---

<a id="the-scary-part-when-agents-are-wrong"></a>

## The Scary Part: When Agents Are Wrong [failure-recovery]

**Scenario:** Agent confidently updates a doc section. Later, human realizes it's wrong.

**Options:**
1. **Revert immediately**: Mark section as human-owned, lock agent out
2. **Post-mortem**: Agent analyzes why it was wrong, updates trust score
3. **Feedback loop**: Agent learns from mistake, improves validation
4. **Escalation**: Propose changing validation rules to catch this in future

**Example:**
```yaml
rejection_postmortem:
  timestamp: 2026-11-08T14:22:00+01:00
  rejected_by: uvlightdrops
  original_proposal: "Performance improved 50% in v2.0"
  actual_situation: "Measured in synthetic benchmark; real-world is only 10% improvement"
  reason_for_error: "Agent didn't distinguish synthetic vs. real benchmarks"
  remediation:
    - Revert: true
    - Lock block: performance-metrics (human-owned until agent improved)
    - Update validation: Add check "always cite benchmark type (synthetic/real)"
    - Agent feedback: "You were overconfident. Your accuracy on benchmarks dropped from 0.99 to 0.85"
```

**Why this matters:**
- Failure is data, not shame
- Builds better validation rules
- Humans learn what the agent struggles with
- Next time agent will be more cautious

---

<a id="the-social-contract-humans--agents-in-docs"></a>

## The Social Contract: Humans & Agents in Docs [collaboration-contract]

**Agent promises:**
- "I will tell you what I changed and why"
- "I will mark low-confidence changes clearly"
- "I will not touch your locked sections"
- "I will learn from rejections"
- "I will keep examples in sync with code"

**Human promises:**
- "I will review your proposals thoughtfully"
- "I will give you feedback when you're wrong"
- "I will maintain my sections carefully"
- "I will trust your high-confidence work"
- "I will use you to automate boring stuff"

**System promises:**
- "Changes are auditable and reversible"
- "Conflicts surface before merge"
- "Staleness is tracked and visible"
- "Nobody can hide a change"

---

<a id="practical-starting-simple"></a>

## Practical: Starting Simple [incremental-adoption]

Don't implement all patterns day one. Start:

1. ✅ **Week 1**: Signed commit blocks (who, what, when, why)
2. ✅ **Week 2**: Locked blocks & permissions (human vs. agent zones)
3. ✅ **Week 3**: Change proposals (review workflow)
4. ✅ **Month 2**: Freshness metadata (staleness tracking)
5. → **Month 3**: Consistency checks
6. → **Month 4**: Trust scoring & feedback loops

Each pattern builds on the previous.

---

<a id="open-questions-im-still-thinking-about"></a>

## Open Questions I'm Still Thinking About [open-questions]

- **Partial merges**: What if a proposal has 3 changes, human approves 2 but rejects 1? Do we need micro-commits?
- **AI hallucination in docs**: What if agent confidently writes something false? How do we detect this?
- **Cross-project consistency**: If Agent updates doc in Project A, should it auto-check doc in Project B for contradictions?
- **Natural language reasoning**: Can we ask agent "why did you propose this change?" and get a clear explanation?
- **Deprecation cascades**: If doc A is deprecated, should agent warn docs that reference it?
- **Temporal coherence**: Should docs linked in time (e.g., phase-by-phase roadmaps) auto-update to reflect completion?

These matter for v2.

---

<a id="tying-back-to-ki-knowledge"></a>

## Tying Back to ki-knowledge [knowledge-integration]

This framework should feed into ki-knowledge's query layer:
- **Knowledge graph**: Agents build it by maintaining consistent docs
- **Semantic queries**: "Show me all docs about authentication" - only auto-maintained sections are guaranteed fresh
- **Human-verified facts**: Locked blocks are assertions humans stand behind
- **Trust scoring**: External tools can decide "use this doc with high confidence" vs. "treat as draft"

Docs become machine-readable *because* they're human-trustworthy.

---

<a id="tldr-the-principle"></a>

## TL;DR: The Principle [core-principle]

**Make the agent's work transparent, reviewable, and reversible. Build trust through accuracy tracking. Let humans stay in control while agents handle repetitive coherence maintenance.**

Not "agent writes docs". More like: "agent proposes improvements, humans validate, system tracks everything".