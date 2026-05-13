---
name: Headless M365
description: 'Use when the user needs: Headless Astemo enterprise automation — SharePoint file access, M365 login, Teams messages, Outlook email, Excel Online extraction. Use when user mentions "SharePoint", "download from SharePoint", "check Teams", "Outlook", "M365", "Astemo documents", "Hoe-Axle site", "astemogroup.sharepoint.com", or needs any Astemo corporate system access. Also trigger on "astemo-hl", "headless astemo", "corporate docs".'
icon: icon.svg
metadata:
  depends_on: [astemo-dropbox,astemo-hl,astemo-outlook,astemo-teams,astemo-work-context]
  category: enterprise/astemo
  family: hl
  lifecycle: active
  canonical_slug: hl-m365
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

## Scope Boundary

- Dropbox is adjacent infrastructure, not part of `astemo-hl` or the Astemo M365 lane.
- Use `astemo-dropbox` for Dropbox-backed staging, search, temporary links, and portable config sync.
- Keep `astemo-hl` for SharePoint, Outlook, Teams, Excel Online, OneDrive, and M365 SSO flows.

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
Input (trigger: SharePoint, Teams, Outlook, Excel Online, M365, corporate docs)
  ↓
Auth Check (Chrome session → M365 SSO state)
  ↓
Route to Sub-Operation:
  ├── SharePoint → download file, navigate library, search docs
  ├── Excel Online → extract data via keyboard automation
  ├── Teams → read channels, search messages
  ├── Outlook → search emails, read threads
  └── OneDrive → browse shared drives, download attachments
  ↓
File Processing (download → parse → update trackers)
  ↓
Artifact Generation
  ├── ~/Downloads/ → program inputs/ (downloaded files)
  └── ShadowArchive/80-reports/m365-extract-YYYY-MM-DD.md (extraction log)
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Auto-route to correct M365 sub-operation based on trigger | Any M365 trigger |
| `sharepoint` | SharePoint file operations | `download from SharePoint`, `browse documents` |
| `excel` | Excel Online data extraction | `extract from Excel`, `read spreadsheet online` |
| `teams` | Teams message operations | `check Teams`, `Teams channel` |
| `outlook` | Outlook email operations | `check email`, `Outlook`, `search email` |
| `onedrive` | OneDrive file operations | `OneDrive`, `my files`, `shared drives` |
| `status` | M365 auth state and session health | `M365 status`, `am I logged in` |

## Artifact Routing

| Artifact | Path | Purpose |
|----------|------|----------|
| Downloaded files | `~/Downloads/` → program inputs/ | Source docs from SharePoint/OneDrive |
| Extraction log | `ShadowArchive/80-reports/m365-extract-YYYY-MM-DD.md` | What was extracted and from where |
| Parsed data | Program tracker/skill update | Extracted data fed into FH4S/XP7G workflows |

## Fallback Chain

1. **Primary:** Chrome AppleScript automation with authenticated M365 session
2. **MFA required / session expired:** Report; ask operator to re-authenticate manually; cannot bypass MFA
3. **Chrome focus issues:** Use `cliclick` for mouse actions; run full sequence as single AppleScript; minimize terminal focus grabs
4. **Excel Online cross-origin iframe:** Keyboard automation (Ctrl+A, Ctrl+C); no DOM injection possible
5. **SharePoint SPA not readable:** Fallback to download URL construction from GUID; user manually downloads, agent processes locally
6. **Last resort:** Operator downloads files manually; agent processes from `~/Downloads/` → program inputs/

## Error Handling

| Failure | Recovery |
|---------|----------|
| MFA prompt appears | Stop; ask operator to approve on phone; wait for redirect |
| Chrome not running | Start Chrome; navigate to M365; check auth state |
| File not found in SharePoint | Search document library; suggest alternative paths; check permissions |
| Excel Online load timeout | Retry once; fall back to asking operator to download XLSX locally |
| Terminal steals focus during extraction | Re-run with `cliclick` instead of System Events; or combine into single AppleScript block |
| Downloaded file not in ~/Downloads/ | Check Chrome download settings; check for save dialog; suggest manual download |
| Volume unmounted (ASTEMO inputs/) | Save to ~/Downloads/ temporarily; move when volume mounts; note delay |

## Contract

- **Cannot bypass MFA.** This is a hard limitation. Always ask the operator to re-authenticate when needed.
- **Dropbox is adjacent, not M365.** Use `astemo-dropbox` for Dropbox operations. Keep `hl-m365` for SharePoint, Outlook, Teams, Excel Online, OneDrive only.
- **File processing is local.** Download first, then parse with openpyxl/pikepdf/python-pptx. Do not attempt in-browser parsing.
- **No credential exposure.** Extraction logs must not contain auth tokens, cookies, or SSO URLs with session IDs.
- **Externalization rule.** All extraction logs go to `ShadowArchive/80-reports/`. Never leave the only download record in chat.
- **Do not** send emails or Teams messages from this skill. It is read-only extraction. Use `astemo-outlook` or `astemo-teams` for composition.
- **Do not** modify SharePoint files. Read and download only.
- **Do not** attempt to automate Excel Online writes. Keyboard copy is read-only extraction.
