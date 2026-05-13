---
name: Outlook Headless Workflow
description: 'Use when the user needs: Headless/background Outlook email workflow for checking recent mail, drafting/replying, verifying sent messages, and avoiding mobile formatting defects. Use when handling Outlook/M365 email, Sent Items verification, reply formatting, browser session reuse, or when the user asks to check/respond to email without foregrounding the browser.'
icon: icon.svg
metadata:
  category: enterprise/astemo
  family: enterprise
  lifecycle: active
  canonical_slug: outlook-headless-workflow
  icon_style: craft-category-glyph-v1
  replaced_by: the
---

# Outlook Headless Workflow

## Defaults

- Reuse existing authenticated Outlook session first.
- Keep browser hidden/background; only show UI if auth/security or visual verification blocks automation.
- Never navigate over unrelated Teams/Calendar windows.
- Draft + verify before sending; send only after explicit approval or clear “go ahead”.
- Astemo email is RED tier: do not send raw content to cloud models.

## Quick commands

From `/Volumes/☯Duality/spaces/professional/sduvvuru-qx`, prefer the stable wrapper:

```bash
node tools/outlook-agent/bin/outlook-agent.js format lint reply.txt
node tools/outlook-agent/bin/outlook-agent.js draft build --body-file reply.txt --signature-present
node tools/outlook-agent/bin/outlook-agent.js sent verify --expected-file expected.txt --preview-file preview.txt --pane-file pane.txt
```

`tools/outlook-agent` currently delegates to `tools/email-ingest` while preserving a durable Outlook-specific command surface.

## Recent email workflow

1. Inspect existing browser windows/tabs first; do not open a fresh auth flow unless needed.
2. Snapshot inbox/read candidates in background.
3. Classify actionable vs FYI/marketing.
4. Draft compact plaintext reply.
5. Run formatting lint/build.
6. Verify recipients, external warnings, and no duplicate signature.
7. Send only if approved.
8. Verify sent preview/body evidence.
9. Hide browser session.

## Formatting rule

Canonical reply body is compact plaintext. Avoid rich blank paragraphs.

Bad:

```text
Hi Mark,


Thank you.



Regards,
```

Good:

```text
Hi Mark,

Thank you.

Could you please confirm Monday 5/18?
```

If Outlook signature is present, do not include `Regards` / name in inserted body; use `--signature-present`.

## Sent verification rule

Outlook web may show a correct Sent Items row preview while the reading pane omits the new reply and jumps into quoted original content. Classify this as rendering/conversation-view issue, not send failure, if preview/recipient evidence confirms body.

## References

- `references/formatting-rules.md` — compact mobile-safe formatting rules.
- `references/graph-mail.md` — Graph reply/replyAll/createReply findings.
- `references/failure-modes.md` — observed Outlook DOM/rendering failures.

## Pipeline

```
Inspect Browser Windows (reuse existing authenticated session)
  ↓
Snapshot Inbox / Search Results (background, no UI foreground)
  ↓
Classify Emails (actionable vs FYI, program, department)
  ↓
Draft Reply (compact plaintext, formatting lint, --signature-present)
  ↓
Verify (recipients, external warnings, no duplicate signature)
  ↓
Send + Verify Sent Items (preview/body evidence match)
  ↓
Route
  ├── Action items → astemo-meeting-actions
  ├── Email digest → ShadowArchive/80-reports/
  └── Hide browser session
```

## Modes

| Mode | Trigger | Output |
|------|---------|--------|
| `recent` / default | "check email", "inbox" | Recent actionable emails — sender, subject, classification, action flag |
| `draft` | "draft reply", "respond to" | Compact plaintext draft with formatting lint |
| `sent-verify` | "verify sent", "did it send" | Sent Items preview/body evidence comparison |
| `search:X` | "search for X" | Keyword query in Outlook search |
| `format` | "lint this", "format reply" | Run formatting rules on existing draft text |

Default output is compact: actionable email summary with program tags.

## Artifact Routing

| Artifact | Path | Notes |
|----------|------|-------|
| Email digest | `ShadowArchive/80-reports/email-digest-YYYY-MM-DD.md` | Cross-session continuity |
| Action items | → `astemo-meeting-actions` | Structured action list |
| Draft replies | Outlook Drafts folder (in-browser) | Left for operator review |
| Formatting rules | `references/formatting-rules.md` | Mobile-safe compact plaintext |

## Fallback Chain

```
1. Existing authenticated browser tab  (primary — no new auth flow)
   ↓ (no existing tab / session expired)
2. Background browser navigate to outlook.office.com  (reuse Chrome profile auth)
   ↓ (MFA required / conditional access block)
3. Ask operator  (explicit disclosure: "Outlook auth required — please approve MFA")
```

Never open a fresh auth flow unless existing sessions are unavailable.

## Prerequisites

- **Chrome profile** with active M365/Astemo SSO session
- **Browser automation** available (Playwright MCP or browser_tool)
- **outlook-agent CLI** at `tools/outlook-agent/bin/outlook-agent.js` (for format lint, draft build, sent verify)
- **Network access** — VPN if off corporate network (Astemo conditional access)
- **Astemo email RED tier awareness** — never send raw email content to cloud models

## Error Handling

| Failure | Recovery |
|---------|----------|
| MFA prompt | Notify operator; wait for approval; do not attempt credentials |
| Sent Items rendering mismatch | Check preview row separately from reading pane; classify as rendering issue if preview confirms body |
| Draft formatting defect | Run `format lint`; rebuild with `--signature-present` |
| External recipient warning | Flag for operator review before send |
| Mobile formatting broken | Apply compact plaintext rules; remove rich blank paragraphs |
| Browser focus lost to unrelated tab | Re-snapshot; do not navigate over Teams/Calendar windows |

## Contract

- **Never** click Send without explicit operator approval or clear "go ahead".
- **Never** send raw Astemo email content to cloud models — RED tier.
- **Keep browser hidden/background** — only show UI for auth/security/visual verification.
- **Never navigate over** unrelated Teams/Calendar windows.
- **Compact plaintext** is the canonical reply format — no rich blank paragraphs.
- **Preserve** existing Outlook signature — use `--signature-present` when signature already in draft.
- **Verify sent** — always check Sent Items preview/body evidence after sending.
- **Do not** expose email body content in SHADOW training pairs — headers and classification metadata only.
