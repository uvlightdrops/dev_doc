---
name: compact-page-titles
description: Use when creating or revising web pages, content views, standalone HTML pages or shared page shells. Keep titles concise and title areas compact, without large hero blocks.
license: MIT
project: dev_doc
agent_project: ki-ecosystem
scope: webdesign
purpose: Consistent concise titles and space-efficient page headers across projects
created: "2026-11-10 22"
updated: "2026-11-10 22"
status: active
semantic_blocks: [page-title-scope, concise-title-text, compact-title-layout, title-review]
---

# Compact page titles

## Scope [page-title-scope]

Apply this rule to content pages embedded in an application and to complete,
self-contained web pages, including standalone HTML exports. Apply it when
creating or revising page titles, shared page shells and header components.
Do not redesign unrelated pages as a side effect.

The default is both a short, meaningful title and a visually compact title
area. Use a larger promotional hero only when explicitly requested.

## Concise title text [concise-title-text]

- Name the page's actual subject or task in a short phrase.
- Omit filler such as "Welcome to", repeated application branding and long
  explanatory subtitles. Put explanations in the content or contextual help.
- Preserve the meaning of user-authored content titles. Do not silently
  rename documents, modify stored titles or remove essential context.
- Keep the browser `<title>` concise and identifiable. A short application
  suffix is appropriate when it helps distinguish tabs.

Prefer "Imports" over "Welcome to the comprehensive import management center".
Do not impose a fixed word or character limit that sacrifices clarity.

## Compact title area [compact-title-layout]

- Use the host application's existing typography and spacing tokens.
- Keep title size, line height and vertical margins modest; do not introduce
  oversized typography, viewport-height sections or decorative title banners.
- Place content or the primary working area directly after the compact header.
- Avoid repeating the same page title in the application shell and content.
  Keep application navigation distinct from the page heading.
- Preserve a meaningful heading hierarchy. Normally provide one page-level
  `h1`; an embedded view must coordinate with the host rather than adding a
  duplicate `h1` or removing the heading entirely.
- Allow long titles to wrap on narrow screens. Do not enforce compactness
  through clipping, unreadably small text or a fixed header height.
- Keep necessary actions and navigation accessible without letting them
  overlap the title or obscure the content.

## Review [title-review]

Check the page in its actual host shell or standalone output:

- The title identifies the subject without introductory filler.
- No unrequested hero block or oversized title spacing delays the content.
- A long title remains readable at narrow widths and with enlarged text.
- Embedded pages do not duplicate host headings or navigation.
- User-authored titles and accessible heading semantics remain intact.

Use existing UI checks or screenshots where available. Do not introduce a
new testing framework solely for this presentation rule.
