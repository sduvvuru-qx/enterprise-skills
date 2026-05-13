---
name: Astemo Chrome Bridge
description: Use when using chrome-web-cli bridge for scripted terminal access to Outlook, Calendar, and other M365 sites without browser GUI. Trigger on "outlook-web-cli", "chrome-web-cli", "bridge status", "calendar events cli", "mail from terminal", or when needing bash-pipeline access to browser-automated data.
icon: icon.svg
metadata:
  filePattern: []
  bashPattern:
  - chrome-web-cli
  - outlook-web-cli
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-chrome-bridge
  icon_style: craft-category-glyph-v1
---

# Astemo Chrome Bridge

Terminal access to Outlook Web and Calendar via `chrome-web-cli` bridge + `outlook-web-cli`. For scripted tasks where you want JSON output in bash, not browser snapshots.

## Architecture

```
Terminal (bash)                    Chrome (browser)
─────────────────                   ─────────────────
outlook-web-cli (Python)
  └─ calls → chrome-web-cli (Node)
                └─ connects via CDP → localhost:9222
                                        └─ Chrome with --remote-debugging-port
                                            └─ authenticated M365 session
```

## Key Constraint: Authenticated Profile

The bridge connects to Chrome via CDP on port 9222. Chrome MUST be running with:
1. `--remote-debugging-port=9222`
2. A profile that has authenticated M365 sessions (Outlook, Teams, SharePoint)

**Problem:** The bridge defaults to a fresh profile (`chrome-web-cli-devtools-profile`) with NO saved sessions.

**Solution:** Use `CHROME_WEB_CLI_PROFILE_DIR` to point to the DevTools MCP profile that already has auth.

## How to Use

### Step 1: Ensure Chrome Has Auth

**Option A: Use DevTools MCP's existing profile (recommended)**

DevTools MCP maintains an authenticated profile at:
```
~/.cache/chrome-devtools-mcp/chrome-profile
```

Launch Chrome pointing to this profile:
```bash
export CHROME_WEB_CLI_CHROME_EXE="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
export CHROME_WEB_CLI_PROFILE_DIR="$HOME/.cache/chrome-devtools-mcp/chrome-profile"

# If Chrome is already running with DevTools MCP, bridge can connect directly.
# If not, launch:
"$CHROME_WEB_CLI_CHROME_EXE" \
  --remote-debugging-port=9222 \
  --remote-debugging-address=127.0.0.1 \
  --user-data-dir="$CHROME_WEB_CLI_PROFILE_DIR" \
  --new-window &
```

**Option B: Symlink the Astemo work Chrome profile (Recorder trick)**

```bash
mkdir -p "$HOME/Library/Application Support/chrome-web-cli-devtools-profile"
ln -sfn "$HOME/Library/Application Support/Google/Chrome/Profile 5" \
  "$HOME/Library/Application Support/chrome-web-cli-devtools-profile/Default"
```

### Step 2: Verify Bridge Connection

```bash
chrome-web-cli diagnose
chrome-web-cli status --url-prefix "outlook.cloud.microsoft"
chrome-web-cli tabs
```

Expected states:

- `bridge: "cdp"` / `cdp_ok: true` means the Chrome DevTools Protocol endpoint is usable.
- `bridge: "applescript"` / `cdp_ok: false` means CDP is unavailable but the bridge can still read existing Chrome tabs non-disruptively via AppleScript.
- `diagnose` probes `/json/version`, `/json/list`, and common ports; a 404 on `127.0.0.1:9222` means something is listening but it is not exposing the Chrome DevTools JSON endpoints.

Non-disruptive visible-page text search:

```bash
chrome-web-cli find-text --url-prefix teams.microsoft.com "Raj throttle body"
chrome-web-cli visible-text --url-prefix teams.microsoft.com --max-chars 1000
```

These read the already-loaded tab only. They do not perform Microsoft server-side Teams/Outlook search.

### Step 3: Use outlook-web-cli

```bash
# Calendar events for a specific week
outlook-web-cli calendar open
outlook-web-cli calendar events --limit 50

# Navigate to a specific week
outlook-web-cli calendar prev-week --count 2
outlook-web-cli calendar events --date 2026-04-07

# Open Outlook mail
outlook-web-cli open
outlook-web-cli messages list --limit 10 --query "FH4S"

# Open a specific message
outlook-web-cli messages open --query "CFT meeting"

# Read current message body
outlook-web-cli messages current
```

### Step 4: Pipeline to jq/Python

The beauty of the CLI: JSON output pipes to other tools.

```bash
# Get today's calendar events as JSON, filter by keyword
outlook-web-cli calendar events --date 2026-04-16 | python3 -c "
import sys, json
events = json.load(sys.stdin)
for e in events:
    if 'Honda' in str(e) or 'CFT' in str(e):
        print(e['title'], '-', e.get('time',''), '-', e.get('organizer',''))
"
```

## DevTools MCP vs Bridge

| Task | DevTools MCP | Bridge CLI |
|------|-------------|------------|
| Interactive browsing | YES (primary) | NO |
| Bash scripting | NO | YES (primary) |
| Outlook mail search | YES (click, snapshot) | YES (JSON output) |
| Calendar events | YES (navigate, snapshot) | YES (JSON output) |
| Pipelines to jq/python | NO (snapshot text) | YES (JSON) |
| Meeting prep automation | Manual | Scriptable |
| No browser window needed | NO (needs GUI) | YES for CDP; AppleScript fallback requires Chrome running but does not focus/navigate |

**Rule:** Use DevTools MCP for interactive work. Use bridge CLI for scripted/automated tasks and pipelines.

## Profile Conflict Warning

**CRITICAL:** Only ONE Chrome instance can use a profile at a time.

If DevTools MCP's Chrome is running:
- Bridge can connect to it IF it's on port 9222 (check DevTools MCP config)
- Bridge CANNOT launch its own Chrome with the same profile

If bridge launches Chrome with a profile:
- DevTools MCP will fail if it tries to use the same profile
- You must kill one before starting the other

**Safe workflow:**
1. Kill all Chrome instances
2. Launch Chrome with debug port + correct profile
3. Both DevTools MCP and bridge can now connect to the same Chrome instance
4. Bridge for CLI tasks, DevTools MCP for interactive tasks — they share the browser

## Cross-Reference with astemo-recording-identify

The bridge is ideal for pulling calendar events to cross-reference against recordings:

```bash
# Get all events for week of Apr 6
outlook-web-cli calendar open
outlook-web-cli calendar prev-week --count 2
outlook-web-cli calendar events --limit 50 > /tmp/calendar_week_apr6.json

# Then match recording timestamps against these events
python3 match_recordings.py /tmp/calendar_week_apr6.json /tmp/recordings.json
```

## Installation

```bash
# Bridge (Node)
npm install -g chrome-web-cli-os-bridge
ln -sf "$(which chrome-web-cli-bridge)" ~/.local/bin/chrome-web-cli

# Outlook CLI (Python)
git clone https://github.com/ishork/outlook-web-cli.git /tmp/outlook-web-cli
chmod +x /tmp/outlook-web-cli/outlook_web_cli.py
ln -sf /tmp/outlook-web-cli/outlook_web_cli.py ~/.local/bin/outlook-web-cli
```

## Environment Variables

| Variable | Default | Purpose |
|----------|---------|---------|
| `CHROME_WEB_CLI_HOST` | 127.0.0.1 | DevTools HTTP host |
| `CHROME_WEB_CLI_PORT` | 9222 | DevTools port |
| `CHROME_WEB_CLI_CHROME_EXE` | (auto-detect) | Chrome binary path |
| `CHROME_WEB_CLI_PROFILE_DIR` | (platform default) | Chrome user-data-dir |
| `CHROME_WEB_CLI_WAIT_MS` | 3000 | Wait time after Chrome launch |

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `fetch failed` | Chrome not running or no debug port | Launch Chrome with `--remote-debugging-port=9222` |
| `No page matched outlook.cloud.microsoft` | Chrome running but no Outlook tab | Navigate to Outlook first via `outlook-web-cli open` |
| Login page shown | Bridge profile has no auth | Use `CHROME_WEB_CLI_PROFILE_DIR` pointing to authenticated profile |
| `chrome-web-cli: command not found` | Binary not linked | Symlink `chrome-web-cli-bridge` → `chrome-web-cli` |
| `outlook-web-cli: command not found` | Python script not on PATH | Symlink to `~/.local/bin/` |
| Chrome crashes on launch | Profile already in use by another Chrome | Kill all Chrome first: `pkill -9 "Google Chrome"` |

## Crystallized From

- Session: 2026-04-16 (JARVIS workstation setup)
- Original task: Operator said "check chrome-web-cli-os-bridge" on ishork GitHub. Pulled and installed the bridge + outlook-web-cli. Hit profile conflict: bridge uses empty profile, DevTools MCP uses its own profile, neither has M365 auth. Had to figure out the correct profile routing.
- Key insight: The CLI tools were built for the work laptop (sduvvuru) where Chrome was pre-configured. On JARVIS, Astemo browser work must use Chrome `sduvvuru` / `Profile 5` (`dsabarish.work@gmail.com`), or a DevTools MCP profile that is explicitly authenticated to the same work account. Do not symlink or launch against `Default`; that is the personal `Sabbu` profile.
- Second key insight: Bridge CLI is for scripted/bash tasks. DevTools MCP is for interactive browser tasks. They complement each other, not compete.
