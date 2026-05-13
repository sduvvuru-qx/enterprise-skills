---
name: Headless SHADOW Automation
description: 'Use when the user needs: Headless .shadow automation router for SHADOW. Use this when a request involves running or checking .shadow config cycles, conversation cycles, cache verification or promotion, launchd install, or automation status review. Route privacy and config scans to headless-shadowprivacy and exposure or security checks to headless-shadowsecure.'
icon: icon.svg
metadata:
  install_kind: shim
  promoted_from: /Volumes/SHADOW/ShadowArchive/10-projects/headless-skills/packages/headless-shadow-automation/SKILL.md
  source_cluster: shadow-headless
  requires_mount: /Volumes/SHADOW
  category: automation/headless
  family: hl
  lifecycle: active
  canonical_slug: hl-shadow-automation
  icon_style: craft-category-glyph-v1
---

# headless-shadow-automation

Installed shim for the project-local SHADOW skill.

Canonical source:
- `/Volumes/SHADOW/ShadowArchive/10-projects/headless-skills/packages/headless-shadow-automation/SKILL.md`

Local wrapper:
- `/Volumes/SHADOW/ShadowArchive/10-projects/headless-skills/bin/headless-shadow-automation`

Availability:
- Requires the SHADOW volume mounted at `/Volumes/SHADOW`.
- This installed shim is discovery-only and points back to the project-local package.
- If the volume is unavailable, the skill remains discoverable but is not runnable.

Load the canonical project-local skill for full instructions and runtime commands.

$ARGUMENTS


## Pipeline

```
Intent → Authenticate browser session → Navigate to target → Extract/interact → Return structured output
```

1. Resolve target (URL, search query, operation)
2. Connect to authenticated browser session (Chrome profile)
3. Navigate and wait for page load
4. Extract data or perform interaction via Playwright/browser_tool
5. Return structured output (JSON, markdown, file)

## Modes

| Mode | Output | When |
|------|--------|------|
| `extract` (default) | Structured data extraction | Read/query operations |
| `interact` | Perform action, return result | Click, fill, submit |
| `monitor` | Continuous or periodic check | Watch, wait, alert |

## Artifact Routing

- Extracted data: presented in chat or saved to `ShadowArchive/80-reports/`
- Downloaded files: browser default download folder → classify → move to program structure

## Fallback Chain

1. Playwright MCP / browser_tool (primary)
2. Chrome DevTools Protocol via chrome-web-cli
3. AppleScript Chrome control (last resort)
4. If auth required and not available: stop and ask operator to open authenticated session

## Prerequisites

- Chrome with authenticated session for target service
- Playwright MCP tools or browser_tool available
- Network access to target service (VPN if enterprise)

## Error Handling

- **Auth wall / sign-in page**: Stop, inform operator, do not attempt credentials
- **Element not found**: Wait 3s, retry with relaxed selector, then report
- **Rate limited**: Back off 5s, retry once, then stop
- **VPN required**: Report "VPN down" and stop

## Contract

- **Never enter credentials** — assume authenticated session exists
- **Never click Send/Submit on final actions** (email, message) without operator approval
- **Respect rate limits** — 2s minimum between automated requests
- **Preserve browser state** — do not log out, close tabs, or clear cookies
- **Report auth failures** — do not silently skip locked content

