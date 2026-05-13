---
name: 'Headless ShadowSecure'
description: 'Use when the user needs: Headless OpenClaw exposure and surface audit helper for SHADOW. Use this to snapshot the public OpenClaw exposure watchboard, probe candidate endpoints, and classify obviously risky surfaces from the terminal without a browser UI. Good for requests like: "check if our OpenClaw systems are exposed", "probe this host or URL", "inventory headless tailnet surfaces", or "generate a quick defensive security snapshot". Inherits security patterns from the imported upstream headl'
icon: icon.svg
metadata:
  install_kind: shim
  promoted_from: /Volumes/SHADOW/ShadowArchive/10-projects/headless-skills/packages/headless-shadowsecure/SKILL.md
  source_cluster: shadow-headless
  requires_mount: /Volumes/SHADOW
  category: automation/headless
  family: hl
  lifecycle: active
  canonical_slug: hl-shadowsecure
  icon_style: craft-category-glyph-v1
---

# headless-shadowsecure

Installed shim for the project-local SHADOW skill.

Canonical source:
- `/Volumes/SHADOW/ShadowArchive/10-projects/headless-skills/packages/headless-shadowsecure/SKILL.md`

Local wrapper:
- `/Volumes/SHADOW/ShadowArchive/10-projects/headless-skills/bin/headless-shadowsecure`

Availability:
- Requires the SHADOW volume mounted at `/Volumes/SHADOW`.
- This installed shim is discovery-only and points back to the project-local package.
- If the volume is unavailable, the skill remains discoverable but is not runnable.

Load the canonical project-local skill for full instructions and runtime commands.

$ARGUMENTS
