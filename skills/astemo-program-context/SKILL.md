---
name: Astemo Program Context
description: 'Use when the user needs: Alias — load astemo-work-context instead. Short trigger compatibility for program / Dropbox routing. Prefer astemo-work-context for full program · role · org · task · workflow resolution.'
icon: icon.svg
metadata:
  depends_on: [astemo-work-context]
  filePattern: []
  bashPattern:
  - program registry
  - dropbox root
  - which program
  - FH4S path
  category: enterprise/fh4s
  family: astemo
  lifecycle: alias
  canonical_slug: astemo-program-context
  icon_style: craft-category-glyph-v1
  aliases:
  - /astemo-program-context
  - astemo program context
  - astemo-program-context
  replaced_by: astemo-work-context
---

# astemo-program-context (alias)

**Graduated skill:** load **`astemo-work-context`** — `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md`

That skill owns **program, role, org, task, and workflow** resolution and links to:

- `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/PROGRAM-FILE-ROUTING.md`
- `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/program-registry.json`
- `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/ASTEMO-CONTEXT.md`

(Index: `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/ASTEMO-BUNDLE.md`. Root-level names under `spaces/professional/` are **symlinks** into `sduvvuru-qx/` for legacy path compatibility.)

Do not duplicate those rules inside per-program `fh4s-*` skills; **link** here instead.

## Contract

- This is an alias skill — routes to `astemo-work-context`
- Do not add substantial content here; update the canonical skill instead
- Maintained for trigger compatibility only
