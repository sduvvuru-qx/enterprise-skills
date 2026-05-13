---
name: Astemo Dropbox
description: 'Use when the user needs: Astemo-facing Dropbox adapter for cross-device staging, portable config sync, and project file transfer. Prefer this shim for Dropbox-backed Astemo work; it points to the canonical volume package.'
icon: icon.svg
metadata:
  depends_on: [hl-dropbox]
  install_kind: shim
  promoted_from: /Volumes/☯Duality/ShadowArchive/10-projects/headless-skills/packages/astemo-dropbox/SKILL.md
  source_cluster: astemo-connectors
  requires_mount: /Volumes/☯Duality
  canonical_skill_path: /Volumes/☯Duality/ShadowArchive/10-projects/headless-skills/packages/astemo-dropbox/SKILL.md
  category: enterprise/astemo
  family: astemo
  lifecycle: alias
  canonical_slug: astemo-dropbox
  icon_style: craft-category-glyph-v1
  aliases:
  - /astemo-dropbox
  - astemo dropbox
  - astemo-dropbox
  replaced_by: this
---

# astemo-dropbox

Installed shim for the portable Astemo Dropbox adapter.

Canonical source:
- `/Volumes/☯Duality/ShadowArchive/10-projects/headless-skills/packages/astemo-dropbox/SKILL.md`

Use the canonical package for routing, scope, auth, and path rules. Load `hl-dropbox` when you need the concrete Dropbox SDK workflows.

## Packet Safety Addendum

Before placing architecture, runtime, or SHADOW control-plane artifacts into
Dropbox:

1. scrub local absolute paths, hostnames, SSH aliases, mesh labels, command
   surfaces, and debug/doctor JSON
2. share compact Astemo-facing summaries and generic diagrams first
3. keep raw SHADOW implementation notes local unless explicitly approved
4. if an unsafe packet was started in Dropbox, move it to:
   `ShadowArchive/90-sensitive-sealed/dropbox-relay-retractions/`
   and regenerate a sanitized packet

Dropbox is the Astemo async relay, not a mirror of SHADOW internals.

$ARGUMENTS
