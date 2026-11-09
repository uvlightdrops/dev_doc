# Repository instructions

This repository contains shared, cross-project documentation and the central
agent-skill collection for the KI ecosystem. Keep documentation here
project-agnostic; project-specific implementation details belong in the
owning repository.

## Documentation skills

Before any task, review the descriptions in `skills/*/*/SKILL.md` and load
every relevant skill. The `.github/skills` symlink points to this repository's
own `skills/` collection.

Before documentation work, review the descriptions of **all** skills in
`skills/documentation/*/SKILL.md`. Apply every skill relevant to the task and
load its full instructions before editing; do not apply unrelated specialized
workflows just to check them off. If skill loading is unavailable, read the
relevant `SKILL.md` directly. The skill descriptions and files in `skills/`
are the source of truth for documentation workflows.

## Documentation conventions

- Use the applicable maintenance and semantic-organization skills for new or
  substantially revised documents.
- New documentation filenames use uppercase letters with a lowercase `.md`
  extension. Preserve existing names unless renaming is part of the task.
- Markdown headings at level H2 or above must not contain emojis.
- Preserve the document's established YAML-header convention and update its
  `updated` and `update_reason` fields when present.
- Keep shared standards here and link project-specific implementation docs
  from the owning repository when useful.
- Preserve unrelated worktree changes. Do not commit unless explicitly asked.

## Working style

Make focused, reviewable changes and validate links, filenames, headings and
front matter affected by the edit. Ask before broad reorganizations or mass
renames. Explain documentation changes concisely in German unless the user
requests another language.
