---
name: Astemo Outlook
description: 'Use when the user needs: Outlook email automation for Astemo — browser-based workflow with Craft browser_tool. Enriches with Astemo kernel metadata. Trigger on "check email", "outlook", "inbox", "email from Bryan", "work email", "respond to email", "flagged emails".'
icon: icon.svg
metadata:
  depends_on: [astemo-pipeline,outlook-draft-guardrails]
  filePattern: []
  bashPattern:
  - outlook
  - email
  - inbox
  - check mail
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: fh4s-astemo-outlook
  icon_style: craft-category-glyph-v1
  invisible_first: true
  tier: 2
---

# astemo-outlook — Outlook Web Automation (Astemo)

Astemo-enriched Outlook email access. Uses Craft browser_tool for automated inbox reads and email classification.

## DEAD PATHS — DO NOT ATTEMPT (learned from repeated failures)

| Path | Why it's dead |
|------|-------------|
| `export-cookies.py` → Outlook REST API (`outlook.office.com/api/v2.0`) | Cookie-exported tokens are not valid bearer tokens for the REST mail API. Returns auth errors every time. |
| `az` CLI token → Microsoft Graph API (`graph.microsoft.com/v1.0/me/messages`) | Token has `Directory.*` / `User.*` scopes only — **no `Mail.Read`**. Returns 403 every time. This is a permanent constraint. |
| Decoding JWT to "investigate" scopes | Theater, not debugging. We know the scopes are insufficient. |

## Access Paths

| Priority | Method | Focus Theft | Speed |
|---|---|---|---|
| **1** | `chrome-session Sabarish` → Outlook (persistent Chrome profile auth) | ⚠️ Opens Chrome window | ~5s |
| **2** | Ask operator to forward or summarize | ❌ None | ~0s |

### Path 1: chrome-session (primary — persistent auth)

```
chrome-session Sabarish https://outlook.office.com/mail/
```

Uses the Chrome "Sabarish" profile which **persists M365 SSO across invocations**. No login wall. Open it, read emails, operator can review manually.

**Do NOT use Craft browser_tool for Outlook.** Each Craft session gets its own Chromium partition — auth never persists, producing a dead login wall every time. There are 5+ zombie windows stuck on "Sign in" across past sessions as proof.

### Path 2: Ask operator

If chrome-session is unavailable or session expired, ask the operator to forward or summarize the email. Do NOT fall back to Craft browser or dead API paths.

## Account & Profile
- **Email:** sabarish.duvvuru.qx@astemo.com
- **Browser:** Chrome "Sabarish" profile (always)
- **URL:** https://outlook.office.com/mail/

## Key Contacts
| Name | Role | Email Pattern | Programs |
|------|------|---------------|----------|
| Bryan Voris | Sr. Director | voris | VEC, all programs |
| Kevin Butler | Program Manager | butler | FH4S, FH4L, XP7G |
| Hayakawa Shinichiro | JP DE Lead | hayakawa | FH4S, FH4L |
| Saitoh Ken | Engineering | saitoh | FH4S, VEC |
| Wallace Yang | Product Engineer (NC) | yang | FH4S heatsink |
| Rich Hexemer | Materials Team | hexemer | FH4S materials |
| Liam Jin | Business Dev | jin | VEC, Geely |

## Operations (Craft Browser)

### 1. Open Inbox
```
browser_tool open
browser_tool navigate https://outlook.office.com/mail/inbox
browser_tool snapshot
```

### 2. Search Emails
- Use `find "keyword"` to locate emails by subject/sender
- Outlook search syntax works in the search box:
  - `from:name` — sender
  - `subject:keyword` — subject
  - `hasattachment:yes` — has attachments
  - `received:today` / `received:this week`
  - `is:unread` — unread only

### 3. Read Email
- `click` on email subject in the list
- Extract: From, To, CC, Date, Subject, Body via snapshot

### 4. Draft Reply
- Click Reply button
- Type reply text
- STOP — leave as draft for operator to review and send manually

### 5. Navigate Folders
- **Sent Items:** navigate to folder in sidebar
- **Flagged:** use filter
- **Deleted:** navigate to folder in sidebar

## Astemo Enrichment

After extracting email content, classify with kernel metadata:
- **Program:** match subject/body against FH4S, FH4L, XP7G, VEC, Gen4VA
- **Department:** DE, PMO, SQ, Procurement based on sender role
- **Content class:** technical / commercial / quality / schedule
- **Action items:** scan for "please", "can you", "I need", "by [date]"

Feed action items → astemo-meeting-actions skill.
Feed Q&A pairs → astemo-pipeline for training.

## Error Handling
- **Login wall in Craft browser:** Tell operator once. Wait. Do NOT close the window. Do NOT try dead API paths.
- **MFA prompt:** User approves on their phone
- **Session expired:** Navigate again — session may refresh. If not, tell operator.
- **Loading:** Outlook web is heavy — wait for `network-idle` before snapshotting.

## Limitations
- No automated email sending — user reviews and sends manually
- Attachments download to browser default folder when user clicks them
- Rich HTML emails: snapshot may not capture full body — use scrolling

## Pipeline

```
Open Inbox (browser_tool → outlook.office.com/mail/inbox)
  ↓
Search / Filter (by sender, subject, date, program keyword)
  ↓
Read Email (from, to, cc, date, subject, body, attachments)
  ↓
Classify by Program (FH4S / FH4L / XP7G / VEC / Internal)
  ↓
Extract Action Items (scan for "please", "can you", "by [date]")
  ↓
Route Output
  ├── Action items → astemo-meeting-actions
  ├── Email summary → ShadowArchive/80-reports/
  └── Training pairs → astemo-pipeline (headers + classification only)
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `unread` / default | "check email", "inbox" | Recent unread summary — sender, subject, program tag, action flag |
| `from:X` | "email from Bryan", "from Hayakawa" | Sender-filtered list |
| `flagged` | "flagged emails", "priority" | Flagged/priority items only |
| `thread` | "thread view", "conversation" | Full conversation thread with context |
| `actions` | "what do I owe", "action items" | Extracted action items across recent emails |
| `program:X` | "FH4S emails", "Honda emails" | Program-classified email digest |
| `full` | "full inbox", "everything" | Complete extract (use sparingly — token-heavy) |

Default output is compact: unread count, latest emails with program tags and action flags.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Email digest | `ShadowArchive/80-reports/email-digest-YYYY-MM-DD.md` | Daily summary for cross-session continuity |
| Action items | → `astemo-meeting-actions` | Structured action list |
| Training pairs | → `astemo-pipeline` | Headers + classification only (no email body) |
| Draft replies | Outlook Drafts folder (in-browser) | Never auto-sent |

Do not leave email content in `/tmp` — always route to a durable path or discard.

## Fallback Chain

```
1. chrome-session Sabarish → outlook.office.com  (primary — persistent Chrome profile auth)
   ↓ (Chrome not running / profile unavailable / session expired)
2. Ask operator  ("Outlook unavailable — please forward or summarize the email")
```

Do NOT fall back to Craft browser or API paths. They are structurally broken for mail.

Always disclose which fallback was used. Never fall back to storing credentials or passwords.

## Prerequisites

- **Craft browser_tool** — built into Craft Agents
- **Chrome "Sabarish" profile** (fallback) — authenticated to `sabarish.duvvuru.qx@astemo.com` M365
- **M365/Outlook access** — active subscription, not blocked by conditional access
- **VPN** — required if off corporate network (Astemo conditional access)

## Contract

- **NEVER** click the Send button — all drafts are left for operator review and manual send.
- **NEVER** auto-send emails under any circumstances.
- **NEVER** use Graph API or cookie-export for mail access — these are permanently broken. See DEAD PATHS above.
- **NEVER** close a Craft browser window that has active auth — use `hide` instead.
- **NEVER** try more than one dead API path while waiting for operator to log in.
- **Preserve** Outlook signature in draft replies — do not strip or modify.
- **Do not** modify recipients (To/CC/BCC) without explicit operator approval.
- **Do not** store email body content in SHADOW training pairs — headers and classification metadata only.
- **Do not** expose internal email threads or action items in customer-facing or Dropbox-visible artifacts.
- Email digests are **operator-only** — do not relay to Dropbox without explicit approval.
- When drafting replies, follow `outlook-draft-guardrails` skill for formatting and recipient verification.
