---
name: Headless TRMNL
description: 'Use when the user needs: Headless TRMNL device automation — push images, render templates, manage playlists, check device status, generate e-paper screens from live mesh/kernel data. Use when the user asks to "push to TRMNL", "update the display", "render for e-paper", "check TRMNL status", "generate TRMNL image", "what''s on the display", "screenshot to TRMNL", "TRMNL playlist", or any task involving programmatic control of the TRMNL e-paper device. Trigger on "push to display", "e-paper image",'
icon: icon.svg
metadata:
  category: automation/headless
  family: hl
  lifecycle: active
  canonical_slug: hl-trmnl
  icon_style: craft-category-glyph-v1
---

# hl-trmnl — Headless TRMNL Automation

Programmatic control of the xteink_x4 TRMNL e-paper device. Push images, render templates, manage playlists, and generate screens from live data — all from the CLI.

## Device Quick Reference

| Field | Value |
|-------|-------|
| Device ID | 41370 |
| BYOD Token | `1qsLJdoKJPtcyUTdTeqayg` |
| Account API Key | `user_crfhaacrc798tqf80km1n9du` |
| Webhook Image UUID | `f5bc3d4e-ab85-495a-9f02-7d7c126b24ca` |
| Webhook Image URL | `https://trmnl.com/api/plugin_settings/f5bc3d4e-ab85-495a-9f02-7d7c126b24ca/image` |
| Plugin Setting ID | 267861 |
| MAC | `937793dbb2e716a7f8f273cde78ceb28` |
| Display | 800x480, 1-bit B/W or 2-bit (4-gray) |
| Refresh | 14 min (configured) |
| Firmware | 1.7.4 (1.7.8 available) |
| API Base | `https://trmnl.app` or `https://trmnl.com` |
| Swagger | https://trmnl.com/api-docs/index.html |
| Dashboard | https://trmnl.com/devices/41370/developer/edit |

## Operations

### 1. Check Current Display

```bash
# Get current screen image URL (does NOT advance playlist)
curl -s "https://trmnl.app/api/display/current" \
  -H "Access-Token: 1qsLJdoKJPtcyUTdTeqayg" \
  -H "ID: 937793dbb2e716a7f8f273cde78ceb28" | python3 -m json.tool

# Download and view current image
IMG=$(curl -s "https://trmnl.app/api/display/current" \
  -H "Access-Token: 1qsLJdoKJPtcyUTdTeqayg" \
  -H "ID: 937793dbb2e716a7f8f273cde78ceb28" | python3 -c "import sys,json; print(json.load(sys.stdin)['image_url'])")
curl -sL "$IMG" -o /tmp/trmnl-current.png
# Then use Read tool on /tmp/trmnl-current.png to view it
```

### 2. Advance to Next Playlist Item

```bash
# GET /api/display advances the playlist — use when you want to skip forward
curl -s "https://trmnl.app/api/display" \
  -H "Access-Token: 1qsLJdoKJPtcyUTdTeqayg" \
  -H "ID: 937793dbb2e716a7f8f273cde78ceb28" \
  -H "FW-Version: 1.7.4" \
  -H "Model: XTEINK_X4" \
  -H "Width: 800" \
  -H "Height: 480" | python3 -m json.tool
```

### 3. Push Webhook Image (Most Powerful)

Generate any 800x480 1-bit PNG and push it directly to the display:

```bash
# Push a pre-rendered image (requires plugin UUID)
curl -X POST "https://trmnl.app/api/plugin_settings/{PLUGIN_UUID}/image" \
  -H "Content-Type: image/png" \
  --data-binary @/tmp/trmnl-shadow-lab.png
```

Rate limit: 12/hr standard, 30/hr TRMNL+

### 4. Push Merge Variables (Webhook Data)

```bash
# Push data to a private plugin template
curl -X POST "https://trmnl.app/api/plugin_settings/{PLUGIN_UUID}/data" \
  -H "Content-Type: application/json" \
  -d '{
    "merge_variables": {
      "title": "Shadow Lab",
      "nodes_online": 7,
      "nodes_total": 8,
      "disk_pct": "59%",
      "last_commit": "865d29b"
    }
  }'
```

### 5. Test Liquid Templates (Free, No Auth)

```bash
# Render a template without deploying
curl -X POST "https://trmnl.com/api/markup" \
  -H "Content-Type: application/json" \
  -d '{
    "markup": "{{ title }} — {{ nodes_online }}/{{ nodes_total }} nodes online",
    "variables": {"title": "Shadow Lab", "nodes_online": 7, "nodes_total": 8}
  }'
# → {"data": "Shadow Lab — 7/8 nodes online"}
```

### 6. Generate 800x480 Mesh Status Image

```python
# Generate a 1-bit PNG from live mesh data
from PIL import Image, ImageDraw, ImageFont
import subprocess, json, datetime

img = Image.new('1', (800, 480), 1)
draw = ImageDraw.Draw(img)
font = ImageFont.truetype("/System/Library/Fonts/Menlo.ttc", 18)
font_sm = ImageFont.truetype("/System/Library/Fonts/Menlo.ttc", 14)
font_lg = ImageFont.truetype("/System/Library/Fonts/Menlo.ttc", 28)

# Header
draw.rectangle([0, 0, 800, 44], fill=0)
draw.text((16, 8), "SHADOW LAB", fill=1, font=font_lg)
now = datetime.datetime.now().strftime("%Y-%m-%d %H:%M")
draw.text((620, 14), now, fill=1, font=font_sm)

# Get live data from tailscale, df, git
ts = json.loads(subprocess.run(['tailscale', 'status', '--json'],
    capture_output=True, text=True).stdout)
online = sum(1 for p in ts.get('Peer', {}).values() if p.get('Online'))
total = len(ts.get('Peer', {})) + 1

# Draw stats, nodes, footer...
# Save
img.save('/tmp/trmnl-mesh.png')
```

Use the full generator script at the end of this skill for the complete implementation.

### 7. Account API (Requires `user_xxxxx` Key)

Get your key from https://trmnl.com/account, then:

```bash
# List all devices
curl -s "https://trmnl.com/api/devices" \
  -H "Authorization: Bearer user_xxxxx" | python3 -m json.tool

# List playlist items
curl -s "https://trmnl.com/api/playlists/items" \
  -H "Authorization: Bearer user_xxxxx" | python3 -m json.tool

# Toggle playlist item visibility
curl -X PATCH "https://trmnl.com/api/playlists/items/{ID}" \
  -H "Authorization: Bearer user_xxxxx" \
  -H "Content-Type: application/json" \
  -d '{"visible": false}'

# List plugin settings (find your plugin UUIDs here)
curl -s "https://trmnl.com/api/plugin_settings" \
  -H "Authorization: Bearer user_xxxxx" | python3 -m json.tool

# Get user profile (timezone, locale, etc.)
curl -s "https://trmnl.com/api/me" \
  -H "Authorization: Bearer user_xxxxx" | python3 -m json.tool
```

## Image Spec

| Property | Value |
|----------|-------|
| Dimensions | Exactly 800x480 |
| Color depth | 1-bit (B/W) or 2-bit (4-gray) |
| Format | PNG (recommended), BMP3, JPEG |
| Max size | 5 MB (webhook image), 92,160 bytes (device limit) |
| Font | Inter family is native; Menlo/Courier for monospace renders well |
| Design | High contrast, no thin lines, no color |

## Automation Patterns

### Cron: Push mesh status every 15 min

Add to shadowlab.cc API or run as launchd:

```bash
#!/bin/bash
# trmnl-push.sh — generate + push mesh status
python3 /path/to/generate-trmnl-image.py
curl -X POST "https://trmnl.app/api/plugin_settings/${TRMNL_PLUGIN_UUID}/image" \
  -H "Content-Type: image/png" \
  --data-binary @/tmp/trmnl-mesh.png
```

### Deploy hook: Push after shadowlab.cc deploys

```bash
# In apps/web deploy script
npx astro build && vercel deploy --prebuilt --prod && \
  python3 generate-trmnl-image.py && \
  curl -X POST "https://trmnl.app/api/plugin_settings/${UUID}/image" \
    -H "Content-Type: image/png" --data-binary @/tmp/trmnl-mesh.png
```

### Screenshot mode: Point at any URL

Configure the Screenshot plugin in the TRMNL dashboard to capture `https://shadowlab.cc/trmnl` — TRMNL renders your page as e-paper-optimized image every refresh cycle.

## Related Skills

- **shadow-trmnl** — Device configuration, absolute surface concept
- **trmnl-plugin-dev** — Full plugin development guide, API reference, Liquid templating
- **xteink-firmware** — ESP32-C3 firmware builds, OTA management


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

