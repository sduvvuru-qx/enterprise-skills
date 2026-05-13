---
name: M365 Auth Bridge
description: Use when needing M365 authentication state for any Astemo headless skill. Single source of truth for Chrome profile, VPN status, Playwright session, and cookie bridge patterns. All M365-dependent skills should reference this instead of duplicating auth instructions. Trigger on "m365 auth", "check auth", "vpn status", "browser auth", "chrome profile", "authenticate m365", or when any hl-* or astemo-* skill needs M365 access.
icon: icon.svg
metadata:
  category: automation/auth
  family: enterprise
  lifecycle: active
  canonical_slug: m365-auth
  depends_on: [astemo-work-context, hl-m365, browser-surface-bridge]
  icon_style: craft-category-glyph-v1
---

# M365 Auth Bridge

Unified authentication layer for all Astemo M365 headless operations. **Do not duplicate auth instructions in other skills** — reference this skill instead.

## Auth Surface Inventory

| Surface | Auth Method | Profile / Path |
|---------|------------|----------------|
| Chrome "Sabarish" | Browser profile cookies | `chrome-web-cli` via AppleScript |
| Playwright | `browser_tool` / MCP | Launched from Craft Agent |
| CDP DevTools | `chrome-web-cli --cdp` | `~/.local/bin/chrome-web-cli` |
| Cookie bridge | `pycookiecheat` extraction | `tools/craft-browser-bridge/` |
| Native apps | Keychain tokens | `security find-generic-password` |

## Chrome Profiles

```
# From AGENTS.md: sdluffy, Mac, ishuru, sduvvuru
# Work profile: "Sabarish" — has M365 auth (Outlook, Teams, SharePoint)
# CRITICAL: Only ONE Chrome instance can use a profile at a time
```

## VPN Status Check

```bash
# Quick VPN check — astemo-my.sharepoint.com resolves only on VPN
nslookup astemo-my.sharepoint.com 2>&1 | grep -q "NXDOMAIN" && echo "VPN DOWN" || echo "VPN UP"

# Public SharePoint works without VPN
nslookup astemogroup.sharepoint.com  # always resolves
```

**VPN may be required for:** Some internal SharePoint sites. OneDrive often works via Graph API without VPN.
**VPN NOT required for:** Public `astemogroup.sharepoint.com`, Outlook Web (conditional), Teams Web.

## Auth Resolution Pipeline

```
1. Check VPN status → if target needs VPN and VPN is down → stop, report "VPN down"
2. Check browser_tool availability → if present, use it (primary)
3. Check Playwright MCP → if available, use it (secondary)
4. Check chrome-web-cli → if Chrome running with Sabarish profile (tertiary)
5. Cookie bridge → extract cookies from Chrome via pycookiecheat (last resort)
6. If all fail → stop, ask operator to open authenticated session
```

## Mode Table

| Mode | When to use | What it does |
|------|------------|--------------|
| `check` | Before any M365 operation | Verify auth surface is available |
| `vpn` | When VPN status is unknown | Check and report VPN state |
| `bridge` | When Craft browser can't auth | Cookie bridge from Chrome |
| `cdp` | When need DevTools access | Launch CDP with Sabarish profile |

## Common Auth Failure Patterns

| Failure | Cause | Recovery |
|---------|-------|----------|
| `ERR_NAME_NOT_RESOLVED` | VPN down | Report, stop, wait for VPN |
| Sign-in page appeared | Session expired | Ask operator to re-auth in Chrome |
| `401 Unauthorized` | Token expired | Try cookie bridge, then ask operator |
| Element timeout on auth | Conditional access prompt | Ask operator to complete MFA |
| Rate limited (429) | Too many requests | Back off 60s, retry once |

## Usage in Other Skills

Other skills should include this in their Prerequisites section:

```markdown
## Prerequisites
- M365 authentication (see `m365-auth` for auth resolution)
```

And reference the auth check in their Pipeline:

```markdown
## Pipeline
1. Check M365 auth via `m365-auth` → if unavailable, stop
2. Proceed with extraction...
```

## Contract

- **Never enter credentials** — assume authenticated session exists
- **Never store tokens** — sessions are ephemeral
- **Report auth failures** — do not silently skip locked content
- **One Chrome profile at a time** — never launch multiple instances with same profile
- **VPN-dependent targets** — always check before attempting

## Skills That Depend on This

astemo-calendar, astemo-excel-online, astemo-teams, astemo-outlook, astemo-onedrive, astemo-pipeline, hl-m365, hl-sharepoint, m365-search, meeting-check, outlook-headless-workflow, sp-crawl, browser-surface-bridge
