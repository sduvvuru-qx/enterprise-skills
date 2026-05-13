---
name: Astemo Calendar
description: 'Use when the user needs: Headless Outlook Calendar automation for Astemo — check meetings, JP-US syncs, DR/TR gates, attendee lists. Enriches with program context. Trigger on "calendar", "meetings today", "schedule", "JP meeting", "next meeting", "DR gate", "TR review", "Astemo calendar", "JP-US sync".'
icon: icon.svg
metadata:
  depends_on: [astemo-pipeline,fh4s-drawing-check,fh4s-meeting-prep]
  replaces: [hl-calendar-astemo]
  filePattern: []
  bashPattern:
  - calendar
  - schedule
  - meetings today
  - JP meeting
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-calendar
  invisible_first: true
  tier: 2
  icon_style: craft-category-glyph-v1
---

# astemo-calendar — Headless Outlook Calendar (Astemo)

Astemo-enriched version of hl-calendar. Extends general Calendar automation with:
- Program-aware meeting classification (FH4S, FH4L, VEC, Ford P703)
- JP-US sync identification (bilingual meetings with JP counterparts)
- DR/TR gate tracking from calendar events
- Action item extraction → astemo-meeting-actions

## Account & Profile
- **Email:** sabarish.duvvuru.qx@astemo.com
- **Browser:** Chrome "Sabarish" profile (always)
- **URL:** https://outlook.office.com/calendar/

## Key Meeting Patterns

| Pattern | Program | Frequency |
|---------|---------|-----------|
| "FH4S" in title | FH4S IPM | Weekly JP-US sync |
| "FH4L" or "Gen4 VA" | FH4L | Bi-weekly |
| "VEC" or "Value Engineering" | Cross-program | Monthly |
| "DR" or "Design Review" | Gate review | Per milestone |
| "TR" or "Test Review" | Gate review | Per milestone |
| "P703" or "Ford" | Ford P703 | As needed |
| Sakamoto/Inoue in attendees | JP counterpart | FH4S related |

## JP-US Meeting Detection

A meeting is likely JP-US if:
1. Attendees include `@astemo.com` AND `@astemo.co.jp` domains
2. Title contains Japanese characters or program codes
3. Scheduled between 8-10 PM ET (9-11 AM JST next day) or 7-9 AM ET (8-10 PM JST)
4. Sakamoto-san, Inoue-san, or other JP contacts are attendees

When JP-US meeting detected:
- Flag for `fh4s-meeting-prep` skill
- Note timezone considerations
- Check Change Point Tracker for open items to discuss

## Key Contacts in Meetings

| Name | Role | Meeting Context |
|------|------|-----------------|
| Bryan Voris | Manager | Program reviews, VEC |
| Sakamoto-san | JP DE Lead | FH4S drawing reviews, JP-US syncs |
| Inoue-san | JP Buyer | FH4S sourcing meetings |
| Kevin | DE colleague | FH4L, Gen IV VA reviews |
| Pankaj Saini | Lead Engineer | Standards reviews, plastic design |

## Workflow

1. **Use chrome-session** — `chrome-session Sabarish https://outlook.office.com/calendar/`
2. **Manual review** — Chrome window opens with authenticated session
3. Extract meeting info → classify by program
4. For JP-US meetings: load `fh4s-meeting-prep` context
5. For DR/TR gates: check `fh4s-drawing-check` status
6. Update `astemo-meeting-actions` if new items found

**Note:** chrome-session opens main Chrome app with existing authentication. Do NOT use Chrome DevTools MCP or Playwright — they create duplicate sessions without auth state.

## Pipeline

```
Query Calendar (date range / today / week)
  ↓
Extract Events (title, time, attendees, location, body)
  ↓
Enrich with Program Context (FH4S / FH4L / VEC / Ford P703)
  ↓
Classify Meetings (JP-US sync, DR/TR gate, standup, 1:1)
  ↓
Render Output
  ├── Compact schedule (default chat output)
  ├── ShadowArchive/80-reports/calendar-YYYY-MM-DD.md  (durable extract)
  └── Route to fh4s-meeting-prep (if JP-US meeting detected)
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `today` / default | "calendar", "meetings today", "schedule" | Today's meetings compact — program tag, time, attendees, JP-US flag |
| `week` | "this week", "week view" | 7-day schedule with program color coding |
| `jp-us` | "JP meeting", "Japan sync" | Filtered view of JP-US meetings only |
| `next` | "next meeting", "what's next" | Single next meeting with full detail + prep context |
| `gates` | "DR gate", "TR review", "design review" | Upcoming DR/TR gates with program milestone context |
| `full` | "full schedule", "everything" | Complete extract including body/previews |

Default output is compact: one line per meeting with program tag, time window, JP-US flag, and key attendees.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Calendar extract | `ShadowArchive/80-reports/calendar-YYYY-MM-DD.md` | Durable daily snapshot |
| Meeting prep context | → `fh4s-meeting-prep` skill | Auto-triggered for JP-US meetings |
| Action items | → `astemo-meeting-actions` | Extracted from meeting body/notes |
| Training pairs | → `astemo-pipeline` | Meeting classification signals |

Do not leave calendar extracts in `/tmp` — always route to a durable path.

## Fallback Chain

```
1. chrome-session Sabarish → outlook.office.com/calendar/  (primary — existing auth)
   ↓ (session expired / MFA required)
2. Microsoft Graph API  (if token available via hl-m365)
   ↓ (no API token / VPN down)
3. Local macOS Calendar.app  (cached Exchange events)
   ↓ (cache stale / no local calendar)
4. Ask operator  (explicit disclosure: "Calendar unavailable — please check Outlook manually")
```

Always disclose which fallback was used in the output.

## Prerequisites

- **Chrome "Sabarish" profile** — authenticated to `sabarish.duvvuru.qx@astemo.com` M365
- **chrome-session CLI** — available in PATH
- **M365/Outlook access** — active subscription, not blocked by conditional access
- **astemo-work-context** loaded — for program classification and Dropbox routing
- **VPN** — required if off corporate network (Astemo conditional access)

## Error Handling

| Failure | Recovery |
|---------|----------|
| Session expired | Re-run `chrome-session Sabarish` to refresh auth |
| MFA prompt | Notify operator — wait for phone approval, then retry |
| VPN down | Check with `tailscale status`; if disconnected, suggest reconnecting |
| Outlook maintenance page | Disclose in output, suggest retry in 15 minutes |
| No meetings found | Confirm date range; check if viewing correct calendar (not a shared calendar) |
| JS rendering incomplete | Wait 3-5s, re-snapshot; Outlook web is heavy on initial load |

## Contract

- **Do not** expose internal meeting prep context (fh4s-meeting-prep output) in customer-facing or Dropbox-visible artifacts.
- **Do not** auto-send meeting invites or accept meeting invitations without operator approval.
- **Do not** store attendee email addresses in SHADOW reports — use names and roles only.
- **Do not** classify meetings with personal/sensitive topics into training pairs.
- Calendar extracts are **internal-only** — do not relay to Dropbox unless explicitly requested.
- JP-US meeting flags are for operator awareness only — do not surface in Honda customer artifacts.
