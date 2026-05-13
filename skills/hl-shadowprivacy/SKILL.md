---
name: 'Headless ShadowPrivacy'
description: 'Use when the user needs: Headless privacy/config inventory helper for SHADOW. Use this to scan local config trees, scripts, and repo roots for callback endpoints, webhook paths, bind settings, Tailscale exposure knobs, and secret-bearing keys without printing the secret values. Good for requests like: "inventory OpenClaw privacy surfaces", "find webhook and OAuth callback settings", or "scan this config tree for risky auth/bind settings". Complements headless- shadowsecure.'
icon: icon.svg
metadata:
  install_kind: shim
  promoted_from: /Volumes/SHADOW/ShadowArchive/10-projects/headless-skills/packages/headless-shadowprivacy/SKILL.md
  source_cluster: shadow-headless
  requires_mount: /Volumes/SHADOW
  category: automation/headless
  family: hl
  lifecycle: active
  canonical_slug: hl-shadowprivacy
  icon_style: craft-category-glyph-v1
---

# headless-shadowprivacy

Installed shim for the project-local SHADOW skill.

Canonical source:
- `/Volumes/SHADOW/ShadowArchive/10-projects/headless-skills/packages/headless-shadowprivacy/SKILL.md`

Local wrapper:
- `/Volumes/SHADOW/ShadowArchive/10-projects/headless-skills/bin/headless-shadowprivacy`

Availability:
- Requires the SHADOW volume mounted at `/Volumes/SHADOW`.
- This installed shim is discovery-only and points back to the project-local package.
- If the volume is unavailable, the skill remains discoverable but is not runnable.

Load the canonical project-local skill for full instructions and runtime commands.

$ARGUMENTS
