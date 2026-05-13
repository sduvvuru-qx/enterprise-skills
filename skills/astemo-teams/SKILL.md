---
name: Astemo Teams
description: 'Use when the user needs: Headless Microsoft Teams automation for Astemo — read chats, channels, meeting recaps, transcripts, search conversations via Playwright browser. Absorbs headless Teams automation with Astemo context enrichment. Trigger on "check teams", "teams message", "meeting recap", "teams chat", "channel messages", "VEC chat", "meeting transcript".'
icon: icon.svg
metadata:
  depends_on: [astemo-pipeline]
  replaces: [hl-teams-astemo, hl-teams]
  filePattern: []
  bashPattern:
  - teams
  - check teams
  - meeting recap
  - teams message
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-teams
  icon_style: craft-category-glyph-v1
  invisible_first: true
  tier: 3
---

# astemo-teams — Headless Teams Web Automation (Astemo)

Astemo-enriched version of hl-teams. Extends general Teams automation with:
- Named meeting context (VEC, FH4S CFT, FH4S Weekly, AI Automation)
- INV channel navigation
- Meeting recap extraction with program tagging
- Action item feed → astemo-meeting-actions

## Account & Profile
- **Email:** sabarish.duvvuru.qx@astemo.com
- **Browser:** Chrome "Sabarish" profile
- **URL:** https://teams.microsoft.com/

## Key Meetings
| Meeting | Schedule | Participants | Programs |
|---------|----------|-------------|----------|
| VEC Updates | Fri 11:00-12:00 | Bryan, Hayakawa, Saitoh, +4 | All |
| FH4S CFT | Recurring | 9 speakers | FH4S |
| FH4S Weekly | Wed ~3:30 PM | 8 speakers | FH4S |
| AI Automation | Ad hoc | 3 speakers | Internal |

## Operations

## Invisible-First Paths

| Priority | Method | Focus Theft | Speed |
|---|---|---|---|
| **1** | Playwright / browser_tool (background) | ⚠️ May steal focus | ~5s |
| **2** | `chrome-session` (manual, operator drives) | ⚠️ Opens Chrome | ~5s |
| **3** | Outlook email fallback (cookie export) | ❌ None | ~2s |

**Note:** Teams has no usable REST API via cookies — the Graph API requires OAuth tokens (not cookies). Teams data extraction requires browser automation. Use Outlook email fallback (cookie export → curl) when Teams browser is unavailable to check for meeting-related emails.

### Teams Web — Browser-State Guardrails

Before declaring Teams blocked, confirm the actual page state:

```
browser_windows
browser_snapshot
# if snapshot is ambiguous:
browser_evaluate: document.body.innerText
```

Rules:
- Treat `navigate` warnings such as "security verification detected" as **signals**, not ground truth.
- If `snapshot` shows Teams nav, chat list, or message content, continue working; do **not** ask the operator to verify.
- Ask the operator for MFA/security action only when `snapshot` or visible page text confirms an actual verification/login blocker.
- For Teams-origin requests, inspect the active/visible chat thread first before searching SharePoint, Dropbox relay cache, or local reports.
- Always narrow to the latest message in the thread before acting; a later reply may supersede the original request.

### 1. Open Teams
```
browser_navigate: https://teams.microsoft.com/
browser_snapshot  # verify loaded — Activity/Chat/Teams/Calendar in left nav
```

### 2. Chat — Read Messages
```
browser_click: [Chat in left nav]
browser_click: [person or meeting name]
browser_snapshot  # message history
```

### 3. Channel — Read Posts
```
browser_click: [Teams in left nav]
browser_click: [team name to expand]
browser_click: [channel name, e.g., "INV"]
browser_snapshot  # channel posts
```

### 4. Meeting Recap (AI Summary)
```
browser_click: [Calendar in left nav]
browser_click: [past meeting entry]
browser_click: [Recap tab]
browser_snapshot  # AI-generated summary, action items, mentions
```

### 5. Meeting Transcript
```
browser_click: [Calendar → past meeting]
browser_click: [Transcript tab]
browser_snapshot  # speaker-labeled transcript with timestamps
```

### 6. Search
```
browser_press_key: Meta+e  # or click search box
browser_type: "search query"
browser_press_key: Enter
browser_snapshot  # results — filter by Messages/People/Files tabs
```

### 7. Files
```
browser_click: [Files tab in channel or chat]
browser_snapshot  # shared files list
```

## Chat vs Channel
- **Chat:** Direct messages (1:1 or group). Under Chat section. Private.
- **Channel:** Team-wide discussions. Under Teams section. Topic-based.
- **Meeting Chat:** Auto-created with meeting name + calendar icon. In Chat section.

## Astemo Enrichment
- Tag meeting recaps with program code (FH4S, VEC, etc.)
- Extract action items from recap → astemo-meeting-actions
- Generate training pairs from meeting decisions → astemo-pipeline

## Error Handling
- **"Use desktop app" prompt:** Click "Use the web app instead"
- **Loading:** Teams is JS-heavy — wait 3-5s after navigation
- **MFA:** Same as outlook — notify user, wait for approval
- **Stale session:** Refresh the page

## Pipeline

```
Navigate Teams Web (teams.microsoft.com)
  ↓
Locate Thread / Channel / Meeting
  ↓
Extract Content (messages / recap / transcript / files)
  ↓
Enrich with Program Tags (FH4S / VEC / FH4L / Internal)
  ↓
Route Output
  ├── Compact chat summary (default chat output)
  ├── Action items → astemo-meeting-actions
  ├── Meeting recaps → ShadowArchive/80-reports/
  └── Training pairs → astemo-pipeline (classification signals only)
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `unread` / default | "check teams", "teams messages" | Latest unread per chat — sender, preview, program tag |
| `recap` | "meeting recap", "AI summary" | Meeting AI-generated summary with action items |
| `transcript` | "meeting transcript", "what was said" | Speaker-labeled transcript with timestamps |
| `search:X` | "search teams for X" | Keyword query across messages, people, files |
| `actions` | "action items", "what do I owe" | Extracted action items only — owner, due date, evidence |
| `channel:X` | "INV channel", "VEC posts" | Channel-specific posts with program tagging |
| `full` | "everything", "full history" | Complete thread extract (use sparingly — token-heavy) |

Default output is compact: unread count, latest message per thread, program tags.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Meeting recap | `ShadowArchive/80-reports/teams-recap-YYYY-MM-DD-<meeting>.md` | Durable meeting extract |
| Meeting transcript | `ShadowArchive/80-reports/teams-transcript-YYYY-MM-DD-<meeting>.md` | Speaker-labeled source |
| Action items | → `astemo-meeting-actions` | Structured action list |
| Training pairs | → `astemo-pipeline` | Classification signals only (no message body content) |

Do not leave Teams extracts in `/tmp` — always route to a durable path.

## Fallback Chain

```
1. Teams Web App (Playwright / browser automation)  (primary — authenticated)
   ↓ (Teams web blocked / loading failure / security verification loop)
2. Outlook Email Fallback  (check for missed meeting-related emails from same participants)
   ↓ (no matching emails / Outlook also unavailable)
3. SharePoint Recording Fallback  (if meeting was recorded, check SharePoint recordings library)
   ↓ (no recording / SharePoint unavailable)
4. Ask operator  (explicit disclosure: "Teams unavailable — please check manually")
```

Always disclose which fallback was used and what data source was actually read.

## Prerequisites

- **Chrome "Sabarish" profile** — authenticated to `sabarish.duvvuru.qx@astemo.com` M365
- **Teams web access enabled** — some orgs disable web app; verify first
- **astemo-work-context** loaded — for meeting program classification
- **VPN** — required if off corporate network (Astemo conditional access)
- **Playwright MCP** connected — for browser automation operations

## Contract

- **Do not** expose internal action items or meeting recaps in customer-facing or Dropbox-visible artifacts.
- Meeting recaps and transcripts are **confidential** — treat as internal Astemo IP.
- **Do not** auto-send Teams messages — read-only automation unless operator explicitly requests.
- **Do not** store transcript/message body content in SHADOW training pairs — classification metadata only (program, department, content class).
- **Do not** surface JP-US meeting details in Honda/customer deliverables.
- Browser-state guardrails apply: verify actual page state before declaring Teams blocked — treat security verification warnings as signals, not ground truth.
