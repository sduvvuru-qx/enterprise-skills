# Outlook Formatting Rules

## Mobile-safe body construction

- Start from plain text.
- Normalize CRLF to LF.
- Trim leading/trailing blank lines.
- Collapse 3+ newlines to exactly 2.
- Do not paste blank `<div><br></div>` runs.
- Use compact HTML paragraphs only when HTML is required:
  - `<p style="margin:0 0 8px 0">Text</p>`
- If Outlook signature is already present, strip `Regards`, `Best regards`, and sender name from inserted body.

## Required pre-send checks

- No `\n\n\n` in visible body.
- No large gap before signature.
- Greeting appears before quoted original thread.
- Sent body preview starts with expected text after send.

## CLI

```bash
node bin/email-ingest.js format lint reply.txt
node bin/email-ingest.js format render reply.txt --html --signature-present
node bin/email-ingest.js draft build --body-file reply.txt --signature-present
```
