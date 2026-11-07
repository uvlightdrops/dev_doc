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

### 2. Update .gitignore

```bash
# your-project/.gitignore
.github/skills  # Symlink to dev_doc/skills
```

### 3. Access in Copilot

In PyCharm or Claude Code:
- Settings → GitHub Copilot → Customizations
- Skills are automatically discovered from `.github/skills/`
- Use `@skill-name` to reference a skill in conversation

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

If a skill is only relevant to one project, create it in that project's `.github/skills-local/`:

```
your-project/
├── .github/
│   ├── skills → ../../../dev_doc/skills    # Central skills (symlink)
│   └── skills-local/                       # Project-specific skills
│       └── your-specific-skill/SKILL.md
└── .gitignore: ".github/skills"
```

Copilot will load both central and local skills.

## Adding New Skills to Central Registry

When creating a skill that other projects should use:

1. Create in `dev_doc/skills/[category]/[skill-name]/SKILL.md`
2. Add full documentation to `dev_doc/documentation-guidelines/`
3. Update this README with skill description
4. Commit to dev_doc
5. All projects automatically inherit the skill via symlink

## Full Skill Documentation

Detailed guides for each skill are in:
- `dev_doc/documentation-guidelines/SEMANTIC_KNOWLEDGE_ORGANIZATION.md`
- `dev_doc/documentation-guidelines/AGENTIC_DOCUMENTATION_PATTERNS.md`
- `dev_doc/documentation-guidelines/DOCUMENTATION_MAINTENANCE_STANDARDS.md`
- `dev_doc/documentation-guidelines/HYBRID_DOCUMENT_SPLITTING_SKILL.md`
- `dev_doc/code-quality-guidelines/PYTHON_STYLE_GUIDE.md` (TBD)
- `dev_doc/testing-guidelines/UNIT_TESTING_PATTERNS.md` (TBD)
- `dev_doc/git-workflow-guidelines/COMMIT_MESSAGE_STANDARDS.md` (TBD)

## Framework Reference

- GitHub Skills Docs: https://docs.github.com/en/copilot/concepts/agents/about-agent-skills
- Awesome Copilot Skills: https://awesome-copilot.github.com/skills/

---

**Maintained by:** Copilot + @uvlightdrops  
**Last updated:** 2026-11-07
