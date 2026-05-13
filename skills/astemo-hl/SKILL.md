---
name: Astemo Headless
description: 'Use when the user needs: Headless Astemo enterprise automation — SharePoint file access, M365 login, Teams messages, Outlook email, Excel Online extraction. Use when user mentions "SharePoint", "download from SharePoint", "check Teams", "Outlook", "M365", "Astemo documents", "Hoe-Axle site", "astemogroup.sharepoint.com", or needs any Astemo corporate system access. Also trigger on "astemo-hl", "headless astemo", "corporate docs".'
icon: icon.svg
metadata:
  depends_on: [astemo-work-context,fh4s-change-point-update,fh4s-ingest]
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-headless
  icon_style: craft-category-glyph-v1
---


## Work context (read first — all dimensions)

Load **`astemo-work-context`**: `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md` — resolves **program** (Dropbox roots, registry), **role** (which hat / audience), **org** (site, team, reporting), **task** (what artifact), and **workflow** (SASG / gates / sequence) before loading narrow `fh4s-*` or customer-only skills. Literal `FH4S_4E0A_IPM` / SHADOW paths below are **FH4S examples** unless this skill is explicitly multi-program.

# Astemo Headless (astemo-hl)

Enterprise automation for Astemo M365 ecosystem via authenticated Chrome browser automation. All Astemo corporate systems require SSO — use browser session, never raw HTTP.

## Architecture

```
astemo-hl
├── sharepoint   — File download, document library navigation
├── excel-online — Extract data from SharePoint-hosted spreadsheets
├── teams        — Read/send Teams messages, check channels
├── outlook      — Read emails, search for specs/drawings
└── onedrive     — Access shared drives, download attachments
```

## Prerequisites

- Chrome (Sabarish profile) logged into M365 with Astemo SSO
- `cliclick` installed (`brew install cliclick`) for mouse automation
- Accessibility permissions granted to iTerm/Terminal for System Events

## Authentication Flow

Astemo uses Azure AD SSO. Authentication states:

| State | Indicator | Action |
|-------|-----------|--------|
| **Logged in** | SharePoint loads directly | Proceed with automation |
| **Session expired** | Redirect to login.microsoftonline.com | User must re-authenticate (MFA) |
| **MFA required** | Authenticator prompt | User must approve on phone |

**Check auth state:**
```bash
osascript -e 'tell application "Google Chrome"
    tell front window to make new tab with properties {URL:"https://astemogroup.sharepoint.com"}
end tell'
sleep 3
osascript -e 'tell application "Google Chrome" to return URL of active tab of front window'
# If URL contains "login.microsoftonline" → not authenticated
# If URL contains "sharepoint.com" → authenticated
```

## SharePoint Operations

### Known Sites

| Site | URL | Content |
|------|-----|---------|
| Hoe-Axle | `astemogroup.sharepoint.com/sites/Hoe-Axle` | XP7G supplier status, inverter docs |
| USGRP1 | TBD | Greenfield plant docs |

### Download File

```bash
# Method 1: Direct download URL
# Convert view URL to download URL:
# View:     ...Doc.aspx?sourcedoc={GUID}&file=NAME&action=default
# Download: .../download.aspx?UniqueId=GUID_NO_BRACES

osascript -e 'tell application "Google Chrome" to open location "DOWNLOAD_URL"'
sleep 8
# Check ~/Downloads/ for the file
ls -lt ~/Downloads/ | head -5
# Move to ASTEMO inputs
mv ~/Downloads/FILENAME /Volumes/SHADOW/ASTEMO/<program>/inputs/
```

### Extract Data from Excel Online

Excel Online renders in cross-origin iframes — JS injection doesn't work. Use keyboard automation:

```bash
# 1. Open the SharePoint Excel URL
osascript -e 'tell application "Google Chrome" to open location "SHAREPOINT_URL"'
sleep 4

# 2. Activate Chrome and click into cell area
osascript -e 'tell application "Google Chrome" to activate'
sleep 1
cliclick c:500,450  # Click below ribbon into cells

# 3. Select all + copy
osascript -e 'tell application "System Events"
    keystroke "a" using {control down}
    delay 1
    keystroke "c" using {control down}
    delay 2
end tell'

# 4. Read clipboard
osascript -e 'the clipboard as text'
```

**Known issue:** Terminal reclaims focus during execution. Workarounds:
- Use `cliclick` for all mouse actions (doesn't steal focus)
- Run the full sequence as a single AppleScript
- Have user manually Cmd+Tab to Chrome, then Ctrl+A, Ctrl+C, paste back

### Navigate Document Library

```bash
# List files in a SharePoint folder
osascript -e 'tell application "Google Chrome" to open location
    "https://astemogroup.sharepoint.com/sites/Hoe-Axle/Shared%20Documents/"'
```

## Teams Operations

### Read Channel Messages
```bash
osascript -e 'tell application "Google Chrome" to open location
    "https://teams.microsoft.com"'
# Teams web app loads — use cliclick to navigate channels
```

### Search for Specs
```bash
# Teams search for technical specs
osascript -e 'tell application "Google Chrome" to open location
    "https://teams.microsoft.com/_#/search?query=FH4S%20PCU%20spec"'
```

## Outlook Operations

### Search Emails
```bash
osascript -e 'tell application "Google Chrome" to open location
    "https://outlook.office.com/search?query=FH4S+motor+output+kW"'
```

### Read Specific Email
```bash
# If you have a message ID or subject line, search for it
osascript -e 'tell application "Google Chrome" to open location
    "https://outlook.office.com/search?query=PCU+model+summary+Brian"'
```

## File Processing Pipeline

After downloading any file from SharePoint:

1. **Move to correct program directory:**
   ```
   /Volumes/SHADOW/ASTEMO/xp7g/inputs/   — XP7G files
   /Volumes/SHADOW/ASTEMO/fh4s/inputs/   — FH4S files
   /Volumes/SHADOW/ASTEMO/fh4/inputs/    — FH4 files
   ```

2. **Parse based on type:**
   - `.xlsx/.xlsm` → `openpyxl` (data_only=True)
   - `.pdf` drawings → `pikepdf` crop + smart-drawing extraction
   - `.pptx` → `python-pptx` for slide content
   - `.msg` → extract with `extract-msg` or read raw

3. **Update trackers/skills** with extracted data

## Limitations

- Cannot bypass MFA — user must authenticate manually
- Excel Online iframe is cross-origin — no direct DOM access
- Terminal focus issues — Chrome loses focus when commands execute
- SharePoint SPA content not accessible via `document.body.innerText`
- Recommended fallback: user downloads manually, agent processes locally

## Future: Playwright with Persistent Profile

To solve the Chrome focus issue, configure Playwright to use the real Chrome profile:
```bash
# Launch Chrome with remote debugging (run once)
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
    --remote-debugging-port=9222 \
    --user-data-dir="$HOME/Library/Application Support/Google/Chrome"
```
Then Playwright can connect via CDP and control the authenticated session without focus issues.

## Pipeline

```
Intent (SharePoint / Teams / Outlook / OneDrive / Excel Online)
  ↓
Authenticate Session (Chrome Sabarish profile SSO check)
  ↓
Navigate to Target (site / URL / search query)
  ↓
Extract / Interact (file download, message read, cell copy, search results)
  ↓
Classify by Program (FH4S / FH4L / XP7G / VEC / Ford)
  ↓
Route Output
  ├── Files → program inputs/ directory (with fh4s-ingest for drawings)
  ├── Messages/Emails → astemo-teams / astemo-outlook enrichment
  ├── Excel data → astemo-excel-online column mapping
  └── Action items → astemo-meeting-actions
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `sharepoint` / default | "SharePoint", "download from SharePoint" | Browse/download from SharePoint document libraries |
| `teams` | "check Teams", "Teams messages" | Read Teams channels and chats |
| `outlook` | "check email", "search email" | Search/read Outlook email |
| `onedrive` | "OneDrive", "my files" | Browse OneDrive files |
| `excel` | "Excel Online", "read spreadsheet" | Extract data from SharePoint-hosted workbooks |
| `search:X` | "search M365 for X" | Cross-service keyword search |

Default output depends on the detected sub-service — classify intent and route to the appropriate sub-skill.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Downloaded files | `~/Downloads/` → move to program `inputs/` | Never leave files in Downloads |
| Engineering drawings | → `fh4s-ingest` | 4E0A-prefix PDFs auto-routed |
| Tracker data | → `fh4s-change-point-update` | .xlsx workbooks flagged |
| Meeting/action items | → `astemo-meeting-actions` | Structured action list |
| Extracted Excel data | `ShadowArchive/80-reports/` | Durable extraction snapshots |

## Fallback Chain

```
1. Chrome Sabarish profile (AppleScript/cliclick)  (primary — existing SSO auth)
   ↓ (Chrome not running / profile unavailable)
2. Playwright with persistent Chrome profile (CDP connection)
   ↓ (CDP not available / browser automation blocked)
3. Microsoft Graph API  (if token available via hl-m365)
   ↓ (no API token / VPN down)
4. Ask operator  (explicit disclosure: "Corporate system unavailable — please download manually")
```

Always disclose which fallback was used and when the data was last fresh.

## Error Handling

| Failure | Recovery |
|---------|----------|
| MFA prompt | Notify operator — wait for phone approval, then retry |
| Session expired | Re-run Chrome profile auth check; ask operator to re-authenticate |
| VPN down | Check `tailscale status`; suggest reconnecting before retry |
| Terminal steals focus | Use `cliclick` for all mouse actions; run as single AppleScript block |
| Excel Online iframe cross-origin | Fall back to keyboard automation (Ctrl+A, Ctrl+C, clipboard read) |
| SharePoint SPA not rendering | Wait 5s, re-snapshot; try direct URL to document library |
| File download not starting | Check popup blocker; try alternate download URL construction |

## Contract

- **Never** enter credentials — assume authenticated session exists in Chrome profile.
- **Never** bypass MFA — stop and ask operator to approve on phone.
- **Never** expose internal SharePoint URLs, file paths, or team names in customer-facing artifacts.
- **Do not** store file content in SHADOW training pairs — classification metadata only.
- **Do not** auto-send emails or Teams messages — read-only unless operator explicitly approves.
- **Do not** leave downloaded files in `~/Downloads/` — always route to program structure.
- Astemo email is RED tier — do not send raw content to cloud models.
