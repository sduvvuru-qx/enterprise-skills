---
name: Astemo MD/MMD Design System
description: "DEPRECATED — use astemo-design-system instead. All tokens, format rules, shell anatomy, brand truths, and cross-app adapters are unified there."
icon: icon.svg
metadata:
  category: enterprise/astemo
  family: design
  lifecycle: archived
  canonical_slug: astemo-md-mmd-design-system
  icon_style: craft-category-glyph-v1
  replaced_by: astemo-design-system
---

# Redirect → `astemo-design-system`

This skill has been merged into the unified `astemo-design-system` skill.

Load `astemo-design-system` instead. It contains:

- Full token table (colors, typography, spacing)
- PPTX slide master reverse-engineering (theme XML, layout geometry, shape-by-shape)
- Shell anatomy and preservation rules
- Per-format rules: PPTX, XLSX, DOCX, MD, HTML, Mermaid, images
- Cross-app adapters: Typora, Obsidian notes, Obsidian Bases, JSON Canvas, Mermaid, Pandoc, HTML, Craft, and artifact indexes
- Brand truths and forbidden transforms
- PPTX slide-master/layout index reverse-engineering, including `python-pptx` standard slide index 5
- XLSX openpyxl constants (previously in astemo-xlsx)

## Scripts (still available here)

These utility scripts remain in this skill directory:

- `scripts/sync_astemo_typora.sh --verify` — sync/verify theme sources
- `scripts/render_typora_slides.py` — horizontal scroll-snap Typora slide deck
- `scripts/build_review_packet.py` — portable review packet builder
- `scripts/open_review_packet.sh` — open packet in Typora work mode
