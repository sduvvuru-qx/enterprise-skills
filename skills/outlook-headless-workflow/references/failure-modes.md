# Outlook Failure Modes

## Reading pane omits sent body

Symptom:

- Sent Items preview contains reply body.
- Reading pane shows sent header then quoted original thread, missing current body.

Classification:

- `sent-preview-present-reading-pane-omits-body`.

Mitigation:

- Verify preview text, recipient confirmation, or Graph body if available.
- Do not assume send failed.

## Mobile whitespace expansion

Symptom:

- Phone shows large empty gaps in sent email.

Cause:

- Existing Outlook signature blank lines + synthetic blank paragraph blocks.

Mitigation:

- Use compact plaintext source.
- Run `format lint` and `draft build --signature-present`.
- Avoid `<div><br></div>` runs.

## Browser focus steal

Symptom:

- Outlook window pops to foreground or overwrites another app/session.

Mitigation:

- Inspect windows first.
- Use dedicated hidden Outlook session.
- Hide after operation.
- Ask before visible UI unless auth/security blocks.
