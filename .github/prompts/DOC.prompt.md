---
name: doc
description: Work on shared KI ecosystem documentation using applicable documentation skills.
argument-hint: "[Dokumentationsaufgabe und gewünschter Umfang]"
agent: agent
---

Perform the documentation task requested in the chat for the `dev_doc`
repository.

1. Read `.github/copilot-instructions.md`.
2. Review the descriptions in **all** `skills/documentation/*/SKILL.md` files.
   Load and follow every skill relevant to this task before editing. If skill
   loading is unavailable, read the full relevant skill files directly.
3. Inspect the target documents and repository state. Preserve unrelated
   changes and avoid broad rewrites or mass renames.
4. Make the smallest complete change, update directly related links and
   metadata, and validate the edited documentation.
5. Summarize the result concisely in German unless the user requested another
   language. Do not commit unless explicitly asked.
