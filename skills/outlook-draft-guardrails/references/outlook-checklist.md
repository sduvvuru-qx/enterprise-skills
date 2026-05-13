# Outlook Draft Checklist

## Workflow

1. Use Craft browser for agent-controlled Outlook when authenticated.
2. If Craft browser is blocked by MFA/security verification, ask the user to complete auth.
3. Use normal Chrome only when the authenticated session is already there and the user accepts using it.
4. Keep the human-facing window maximized before handing back.
5. Never click Send.

## Reply vs new email

Use reply/reply all when:

- The user is responding to an existing email.
- The user says `reply`, `respond`, `send it` in the context of a thread.
- The subject and audience already exist.

Use new email only when:

- The user explicitly says `new email`.
- There is no existing thread.
- The user wants a separate subject/audience.

## Recipient checks

Before handoff, read the visible recipient chips.

Ask once if unclear:

- Should Bryan only receive this?
- Should we reply all to the thread?
- Should anyone be removed from Cc?
- For Japanese recipients, should To/Cc order follow seniority or preserve the original thread order?

Do not silently narrow a thread to one person.
Do not reorder Japanese recipients casually; preserve original thread order unless seniority/order is clear or the user confirms.

## Signature checks

Outlook reply signatures may already include:

- `Regards`
- Sabarish name
- phone number
- company block

If present, do not include another close in the drafted body.

Bad with signature present:

```text
Thanks,
Sabarish

Regards,
[Outlook signature]
```

Good with signature present:

```text
I can pull together the current request status and a short comparison if that helps.

Regards,
[Outlook signature]
```

## Formatting checks

Reject or fix draft if any are true:

- Greeting contains stray symbols, annotations, or `@?`.
- There are 3+ blank lines anywhere.
- There is a large blank gap before the signature.
- Body looks pasted with inconsistent font/size.
- Message begins too abruptly for the thread.
- Message turns a light thread into a heavy memo.

## Handoff phrase

Use this exact style:

```text
Ready for you to review and send.

Verified:
- Thread: [subject]
- To: [names]
- Cc: [names]
- Signature: Outlook reply signature present
- I did not click Send
```
