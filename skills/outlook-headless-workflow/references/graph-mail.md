# Graph Mail Reference

Official APIs reviewed:

- `POST /me/messages/{id}/reply`
- `POST /me/messages/{id}/replyAll`
- `POST /me/messages/{id}/createReply`

## Key rules

- `reply` and `replyAll` send immediately and save to Sent Items.
- JSON body may specify either `comment` or `message.body`; not both.
- `replyAll` uses original sender and recipients.
- `createReply` creates a draft message; update draft body/recipients, then send.
- MIME requires full MIME content, headers included, base64 encoded.

## Agent preference

1. Use Graph for read-only and simple compact replies when delegated work token is available.
2. Use `createReply` when draft verification is required.
3. Fall back to browser DOM when token/source is unavailable.
4. Never invent credentials or switch accounts silently.
