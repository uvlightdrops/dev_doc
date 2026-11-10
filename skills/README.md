# Agent Skills Registry

Central repository for cross-project agent skills used across kicli-code-assist, ki-knowledge, ki-core, and other projects.

## Directory Structure

```
skills/
├── documentation/          # Documentation patterns & standards
│   ├── semantic-knowledge-organization/
│   ├── agentic-documentation-patterns/
│   ├── documentation-maintenance-standards/
│   ├── hybrid-doc-splitting/
│   ├── timestamp-correction-from-git/
│   ├── cross-reference-link-maintenance/
│   ├── semantic-block-tagging/
│   ├── readme-table-generation/
│   ├── folder-structure-audit/
│   └── documentation-coherence-check/
│
├── code-quality/          # Code style, naming, quality standards (TBD)
├── testing/               # Testing patterns & strategies (TBD)
└── git-workflow/          # Git & version control standards (TBD)
```

## Using Skills in Your Project

### 1. Create Symlink

```bash
cd your-project/.github
ln -s ../../../dev_doc/skills skills
```

This relative target assumes sibling checkouts in `~/dev_flow/` and the central
repository at `~/dev_doc/`. In `dev_doc` itself, use `../skills` as the target.
The link contains no skill definitions: the central checkout must also exist
on each development machine. A repository-only cloud checkout cannot use this
external link without making the central collection available first.

### 2. Track the Link and Add Instructions

Do not ignore `.github/skills`. Commit the symlink, not copies of the central
files, together with `.github/copilot-instructions.md`:

```bash
git add .github/skills .github/copilot-instructions.md
```

In the instructions, require reviewing descriptions in
`.github/skills/*/*/SKILL.md` before a task and loading every relevant skill.
For documentation work, review **all** descriptions in
`.github/skills/documentation/*/SKILL.md` and apply every relevant documentation
skill. If automatic discovery or skill loading is unavailable, read the
relevant `SKILL.md` directly. Report broken links instead of silently skipping
the standards.

### 3. Access in Copilot

Copilot supports project skills in `.github/skills/`, including agent mode in
supported JetBrains plugin versions. GitHub's documented layout is
`.github/skills/<skill-name>/SKILL.md`; this collection keeps an additional
category level. Do not assume every client recursively discovers it or follows
external symlinks. The repository instructions provide a direct-read fallback.

In Copilot CLI, use `/skills reload` and `/skills list` to inspect discovery.
In PyCharm, start a new agent conversation after changing the setup and check
which instructions and skills the plugin loads. Naming the skill explicitly
also makes the intended workflow clear; invocation syntax depends on the client.

## All Skills by Category

### Documentation Skills (10 skills)
**Applicable to:** All projects

| Skill | Purpose |
|-------|---------|
| semantic-knowledge-organization | YAML headers, blocks, relations |
| agentic-documentation-patterns | Change proposals, signed commits |
| documentation-maintenance-standards | Validation, consistency checks |
| hybrid-doc-splitting | Split backend+frontend docs |
| timestamp-correction-from-git | Extract dates from git log |
| cross-reference-link-maintenance | Update links after moves |
| semantic-block-tagging | Tag H2 headings with IDs |
| readme-table-generation | Create folder index tables |
| folder-structure-audit | Find gaps & misplaced files |
| documentation-coherence-check | Verify no contradictions |

### Code Quality Skills (3 skills)
**Applicable to:** All projects

| Skill | Purpose |
|-------|---------|
| python-style-guide | PEP 8 conventions & project standards |
| design-patterns | Factory, Strategy, Observer, Builder, Adapter |
| error-handling | Custom exceptions, recovery hints, logging |

### Testing Skills (3 skills)
**Applicable to:** All projects

| Skill | Purpose |
|-------|---------|
| unit-testing | Fast isolation tests, mocking, AAA pattern |
| integration-testing | Multi-component workflows, test data |
| test-coverage | Meaningful metrics, >70% goal, branch coverage |

### Git Workflow Skills (3 skills)
**Applicable to:** All projects

| Skill | Purpose |
|-------|---------|
| commit-standards | Message format, atomic commits, types |
| branch-naming | `type/issue-description` conventions |
| pr-workflow | PR structure, review process, merge strategy |

## Adding Project-Specific Skills

If a skill is only relevant to one project, use the supported alternate
project location `.agents/skills/` rather than putting it through the central
symlink:

```
your-project/
├── .github/
│   ├── skills → ../../../dev_doc/skills    # Central skills (symlink)
│   └── copilot-instructions.md
└── .agents/
    └── skills/
        └── your-specific-skill/SKILL.md
```

Check your client's supported locations and discovery. Keep project-local
skills versioned and reference them in repository instructions if necessary.

## Adding New Skills to Central Registry

When creating a skill that other projects should use:

1. Create in `dev_doc/skills/[category]/[skill-name]/SKILL.md`
2. Add full documentation to `dev_doc/doc/documentation-guidelines/`
3. Update this README with skill description
4. Commit to dev_doc
5. All projects automatically inherit the skill via symlink

## Full Skill Documentation

Detailed guides for each skill are in:
- `dev_doc/doc/documentation-guidelines/SEMANTIC_KNOWLEDGE_ORGANIZATION.md`
- `dev_doc/doc/documentation-guidelines/AGENTIC_DOCUMENTATION_PATTERNS.md`
- `dev_doc/doc/documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md`
- `dev_doc/doc/documentation-guidelines/HYBRID_DOCUMENT_SPLITTING_SKILL.md`
- `dev_doc/code-quality-guidelines/PYTHON_STYLE_GUIDE.md` (TBD)
- `dev_doc/testing-guidelines/UNIT_TESTING_PATTERNS.md` (TBD)
- `dev_doc/git-workflow-guidelines/COMMIT_MESSAGE_STANDARDS.md` (TBD)

## Framework Reference

- GitHub Skills Docs: https://docs.github.com/en/copilot/concepts/agents/about-agent-skills
- Awesome Copilot Skills: https://awesome-copilot.github.com/skills/

---

**Maintained by:** Copilot + @uvlightdrops  
**Last updated:** 2026-11-09
