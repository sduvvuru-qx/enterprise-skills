---
name: Astemo Chrome
description: 'Use when the user needs: Astemo Chrome engineering workspace — M365 services, SharePoint, DevTools debugging, tab management, bookmarks. Reverse-engineer web apps for better engineering tooling. Use when setting up Chrome for Astemo, debugging SharePoint/Teams/Outlook, extracting data from web UIs, or managing the Astemo browser workspace.'
icon: icon.svg
metadata:
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-chrome
  icon_style: craft-category-glyph-v1
---

# Astemo Chrome Engineering Workspace

## MCP Servers Available
| Server | Purpose | Status |
|--------|---------|--------|
| `chrome-devtools` | CDP protocol — inspect, debug, extract DOM, network, console | Available after /clear |
| `chrome-control` | macOS Chrome automation — tabs, windows, navigation | Available after /clear |
| `iterm-mcp` | Terminal control from Codex | Available after /clear |

## Astemo Tab Layout (8 tabs)
1. **Outlook** — outlook.office.com/mail/
2. **Teams** — teams.microsoft.com/
3. **SharePoint** — astemogroup.sharepoint.com/sites/Hoe-Axle
4. **Recorder** — recorder.google.com
5. **VEC Tracker** — Google Sheets (Astemo_VEC_Tracker)
6. **Smartsheet** — DE Activity Schedule
7. **Codex.ai** — Chrome extension for web page interaction
8. **Smart Drawings** — localhost:8787/index.html

**Team mappings via shadow-sidecast:**
```bash
shadow-sidecast team-map --program FH4S
```
Returns current US team, Japan team, and cross-functional stakeholders.

## Reverse Engineering Web Apps

When extracting data from M365 web apps (SharePoint, Teams, Outlook):

### Strategy 1: Chrome DevTools Console
```js
// In DevTools Console (F12), extract visible data
document.querySelectorAll('table tr').forEach(r => console.log([...r.cells].map(c => c.textContent).join('|')))
```

### Strategy 2: Network Tab Intercept
1. Open DevTools → Network tab
2. Filter by `XHR` or `Fetch`
3. Trigger the data load (scroll, click)
4. Find the JSON response → Copy as cURL
5. Replay via terminal: `curl ... | python3 -c "import sys,json; ..."`

### Strategy 3: CDP via MCP
When chrome-devtools MCP is active:
- `chrome.runtime.evaluate(expression)` — run JS in page context
- `chrome.network.getResponseBody(requestId)` — intercept API responses
- `chrome.dom.getDocument()` — full DOM tree

### Strategy 4: Codex in Chrome Extension
Ask Codex in the Chrome sidebar to:
- "Copy all data from this table as JSON"
- "Extract all file names and URLs from this SharePoint library"
- "Summarize the email thread"

## SharePoint Data Extraction Patterns

### Document Library → File List
```js
// In SharePoint library page console:
const rows = document.querySelectorAll('[role="row"]');
const files = [...rows].map(r => ({
    name: r.querySelector('[data-automationid="name"]')?.textContent,
    modified: r.querySelector('[data-automationid="fieldValue_Modified"]')?.textContent,
    author: r.querySelector('[data-automationid="fieldValue_Editor"]')?.textContent
})).filter(f => f.name);
JSON.stringify(files, null, 2);
```

### Excel Online → Cell Data
Cannot access cross-origin iframe directly. Use:
1. **Keyboard** — Ctrl+A, Ctrl+C from within Excel Online
2. **Download** — File > Save As > Download a Copy
3. **Graph API** — If app registration available

## Teams Data Extraction

### Chat Messages
```js
// In Teams web app console:
const messages = document.querySelectorAll('[data-tid="messageBodyContent"]');
[...messages].map(m => m.textContent).join('\n');
```

### Meeting Recap
Navigate to: teams.microsoft.com → Calendar → Past meeting → Recap tab

## Outlook Email Extraction

### Search + Extract
```js
// In Outlook web console:
const emails = document.querySelectorAll('[role="option"]');
[...emails].map(e => ({
    subject: e.querySelector('[title]')?.title,
    from: e.querySelector('[data-name]')?.getAttribute('data-name'),
    preview: e.querySelector('.kvHnM')?.textContent
}));
```

## Setup: Enable Chrome Remote Debugging

For full CDP access, restart Chrome with:
```bash
# Kill existing Chrome
pkill -f "Google Chrome"
sleep 2

# Relaunch with debugging on the Astemo work profile
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
    --remote-debugging-port=9222 \
    --profile-directory="Profile 5" &
```

Then chrome-devtools-mcp can connect via CDP at ws://localhost:9222.
