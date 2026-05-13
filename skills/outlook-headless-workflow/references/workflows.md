# Outlook Workflow Playbooks

## Check recent email needing response

1. Inspect existing browser/session state.
2. Snapshot Inbox in background.
3. Identify actionable emails.
4. Read only candidate threads.
5. Draft concise reply.
6. `format lint` and `draft build`.
7. Verify recipients.
8. Send only after approval.
9. Verify sent evidence.
10. Hide browser.

## Verify sent formatting

1. Capture Sent Items preview.
2. Capture reading pane text.
3. Run `sent verify` with expected body.
4. If preview has body but pane omits it, report Outlook rendering issue.
5. If pane has body with spacing errors, report formatting defect.

## External supplier reply

- Keep short and factual.
- Avoid overcommitting for other Astemo team members.
- Ask for clarification on date/time mismatch.
- Copy team for confirmation when needed.
