---
title: Semantic Knowledge Organization in Filesystems
project: cross-project
agent_project: ki-knowledge
scope: architecture
purpose: Foundation for orphan-free, machine-readable documentation as semantic networks
created: 2026-11-07 10
updated: 2026-11-10 23
update_reason: Validate YAML metadata and align semantic blocks with section headings
status: draft
cross_references:
- ki-knowledge/docs (implementation)
- kicli-code-assist/doc (case study)
semantic_blocks:
- problem-statement
- vision-semantic-knowledge-network
- implementation-path-from-simple-to
- preventing-orphans-mechanisms
- semantic-blocks-practical-example
- connection-to-ki-knowledge
- questions-for-refinement
- next-actions
---

<a id="problem-statement"></a>

## Problem Statement [problem-statement]

### The Orphan Documentation Crisis
**Symptom**: Documentation files exist but are unreachable from any navigation path
- No parent documents link to them
- No index knows about them
- Humans forget they exist; machines can't discover them
- **Result**: Stale, unmaintained docs that diverge from reality

### The Semantic Opacity Crisis  
**Symptom**: A `.md` file contains multiple semantic blocks, but relationships are implicit
```markdown
# ProjectAnalyzer Overview
## Architecture
## Implementation Details
## Configuration
## Future Work
```
**Questions a machine can't answer**:
- Which blocks are "ready for production" vs "conceptual"?
- Which relate to architecture (design) vs. operations (runtime)?
- Which other docs depend on this one?
- When this file changes, which docs need review?

### The Linkage Brittleness Crisis
**Symptom**: Documents are linked by implicit meaning, not explicit semantics
- `PHASE_5_3_SUMMARY.md` links to `code_implementation.py` by path guess
- Renaming breaks discovery
- No "relation type" (implements, deprecates, depends-on, etc.)
- Humans maintain cross-references manually

---

<a id="vision-semantic-knowledge-network"></a>

## Vision: Semantic Knowledge Network [vision-semantic-knowledge-network]

### Level 1: Explicit Metadata Layer
Every `.md` file declares:
```yaml
---
title: ...
semantic_blocks:           # NEW: What's in this doc?
  - id: arch-design
    type: architecture     # design | implementation | operations | roadmap | decision
    status: stable         # stable | draft | deprecated | deprecated-by-id
    version: 1.0
    
relations:                 # NEW: Links to other docs with semantics
  - target: file-or-id
    type: implements       # depends-on | deprecates | replaces | extends | references
    reason: "Brief why"
    
orphan_risk: low          # auto-assessed: high|medium|low (no inbound links)
---
```

### Level 2: Discoverable IDs
Instead of file paths, use **semantic IDs**:
```
doc/architecture/PHASE_5_3_SUMMARY.md
  → id: phase-5-3-code-impl (stable, implementation block)
  
doc/frontend-tui/PHASE_I2_PROJECT_ANALYSIS.md
  → id: phase-i2-project-analysis (stable, architecture block)
```

**Benefit**: Docs can move folders; links remain valid if they use IDs, not paths.

### Level 3: Relation Types (Graph Semantics)
```yaml
relations:
  - target: phase-5-3-code-impl
    type: depends-on
    reason: "Uses CodeImplementationAgent for refactoring suggestions"
    
  - target: phase-5-1-agent-coordination
    type: extends
    reason: "Builds on MetaCoordinationAgent framework"
```

Enables:
- **Dependency analysis**: "What breaks if I update phase-5-3?"
- **Discoverability**: "What docs build on this one?"
- **Deprecation tracking**: "What replaces this old doc?"

### Level 4: Semantic Blocks Within Files
Each major section is tagged:
```markdown
## Architecture [BLOCK: arch-design | status: stable]

## Future Work [BLOCK: roadmap | status: draft]

## Deprecated: Old API [BLOCK: deprecated-by: phase-6-refactor]
```

**Benefit**: Tools can extract "stable" blocks from "conceptual" blocks; status is queryable.

---

<a id="implementation-path-from-simple-to-sophisticated"></a>

## Implementation Path (from simple to sophisticated) [implementation-path-from-simple-to]

### Phase 1: Metadata Foundation (This Week)
- Add YAML headers with `semantic_blocks` array to all docs
- Define `orphan_risk` (manual assessment for now)
- Define core relation types: `depends-on`, `implements`, `deprecates`, `extends`, `references`
- Create `doc/INDEX_BY_RELATION.md` (auto-generated)

### Phase 2: ID System & Linkage (Next)
- Establish stable IDs for each doc's primary block
- Update all cross-references to use IDs, not paths
- Build bidirectional link verification (CI check)
- Detect orphans (docs with `orphan_risk: high` and zero inbound links)

### Phase 3: Semantic Navigation (Future)
- Machine-readable `doc/semantic-graph.json`
- Tools to query: "Show me all docs that depend on X"
- Deprecation warnings: "This block is deprecated; use Y instead"
- Impact analysis: "Changing this affects: [list]"

### Phase 4: RDF/Ontology (If Scale Demands)
- Export to RDF for larger ecosystems
- Merge with ki-knowledge semantic layer
- Enable SPARQL queries across doc networks

---

<a id="preventing-orphans-mechanisms"></a>

## Preventing Orphans: Mechanisms [preventing-orphans-mechanisms]

### 1. Mandatory Inbound Link Requirement
Every doc must have at least one parent linking to it:
```yaml
inbound_links_required: 1  # CI fails if zero
```

Exception: Top-level index docs (INDEX.md, ROADMAP.md).

### 2. Automated Discovery via Relation Graph
Index generator scans all `relations:` fields:
- Creates "This doc is referenced by: X, Y, Z"
- CI warns if a doc is only self-referential

### 3. Regular Orphan Audits
Script runs quarterly:
```
doc_audit.py
  → Finds: docs with zero inbound links
  → Output: report + recommendations (link it, merge it, delete it, archive it)
```

---

<a id="semantic-blocks-practical-example"></a>

## Semantic Blocks: Practical Example [semantic-blocks-practical-example]

### Current Problem
```markdown
# ProjectAnalyzer Overview

The ProjectAnalyzer is a tool that...
[50 lines of architecture thinking]
[50 lines of implementation details]
[30 lines of future ideas]

**Result**: Machine can't separate "this is stable/production-ready" from "this is brainstorm"
```

### Semantic Solution
```markdown
---
semantic_blocks:
  - id: analyzer-arch
    type: architecture
    status: stable
    version: 2.1
  - id: analyzer-impl
    type: implementation
    status: stable
    version: 2.0
  - id: analyzer-roadmap
    type: roadmap
    status: draft
---

## Architecture [BLOCK: analyzer-arch | stable]
The ProjectAnalyzer consists of three modules...

## Implementation [BLOCK: analyzer-impl | stable]
Located in `kicli_code_assist/analysis/`...

## Future Work [BLOCK: analyzer-roadmap | draft]
Potential enhancements...
```

**Benefits**:
- Tool extracts only "stable" blocks for release docs
- Humans know at a glance which sections are "lock in" vs. "ideas"
- Status changes are version-tracked

---

<a id="connection-to-ki-knowledge"></a>

## Connection to ki-knowledge [connection-to-ki-knowledge]

The ki-knowledge ecosystem needs exactly this:
- **semantic-index**: Stores all relations + blocks
- **query-engine**: "Find all docs about authentication" across all projects
- **linkage-validator**: Ensures no orphans, no broken relations
- **ontology-builder**: Merges project-specific semantics into unified model

This framework is the **content layer** beneath ki-knowledge's **query layer**.

---

<a id="questions-for-refinement"></a>

## Questions for Refinement [questions-for-refinement]

1. **Stability Axis**: Should `status` be more granular?
   - `stable` | `draft` | `deprecated` | `under-review` | `proposed`?

2. **Relation Types**: Are these 5 enough?
   - `depends-on`, `implements`, `deprecates`, `extends`, `references`
   - Need `composed-of`, `part-of`, `supersedes`?

3. **Version Tracking**: Should each semantic block have independent versions?
   - Or just file-level version?

4. **Machine Readability**: Should we extract `semantic-graph.json` at build time?
   - Enable external tools to query the doc network?

---

<a id="next-actions"></a>

## Next Actions [next-actions]
- [ ] Workshop: Refine this framework with team feedback
- [ ] Decide on final metadata schema
- [ ] Create semantic-header template for all new docs
- [ ] Implement Phase 1 validator script
- [ ] Retrofit existing docs in kicli-code-assist as case study