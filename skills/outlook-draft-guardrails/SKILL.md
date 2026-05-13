---
name: Outlook Draft Guardrails
description: "Use when preparing, editing, reviewing, or placing drafts in Outlook or Outlook Web for Sabarish. Trigger on Outlook, email draft, reply in thread, prepare reply, fix draft, send it, ready to send, signature, recipient check, Craft browser Outlook, Chrome Outlook, or any task involving composing workplace email. Enforces: never click Send, use existing thread for replies, preserve Outlook signatures, compact formatting, maximize human browser windows, and verify recipients/thread/body before handoff."
metadata:
  category: enterprise/work
  family: enterprise
  lifecycle: active
  canonical_slug: outlook-draft-guardrails
  icon_style: craft-category-glyph-v1
---

# Outlook Draft Guardrails

## Non-negotiables

- Do **not** click Send.
- Prepare the draft, verify it, then tell the user: `Ready for you to review and send.`
- Use the existing email thread for replies. Do not start a new email unless the user explicitly asks for a new email.
- Use Reply or Reply all based on the thread and user intent.
- Do not manually add signatures. Let Outlook apply the new-email or reply signature.
- If Outlook signature is already present, strip manual `Thanks`, `Regards`, and `Sabarish` from the inserted body.
- On this laptop, keep human-facing Outlook/Chrome windows maximized when handing control back.
- Prefer Craft browser for agent-controlled Outlook. Use the normal Chrome session only if auth/session constraints require it, and state why.

## Before opening a compose editor

1. Identify whether this is a new email or a reply.
2. If reply, find and open the original thread.
3. Confirm intended audience:
   - To
   - Cc
   - Reply vs Reply all
4. Confirm whether the message should include only the active thread participants or a broader CC list.
5. Confirm user will send manually.

## Draft insertion rules

Use compact formatting:

- Start from plain text.
- Collapse 3+ newlines to exactly 2.
- Do not paste blank HTML `<div><br></div>` runs.
- Preserve Outlook reply signature below the body.
- Insert the draft above the existing signature.
- Avoid double sign-off.

Use HTML only if needed. Prefer compact paragraphs:

```html
<p style="margin:0 0 8px 0">Text</p>
```

## Required verification before handoff

Read the visible draft and verify:

- Correct thread and subject.
- Correct To and Cc.
- For Japanese recipients, To/Cc and greeting order preserve original thread order or follow known seniority.
- Body starts with the expected greeting.
- No extra `@?`, broken greeting, or accidental annotation text.
- No em dashes if drafting as Sabarish.
- No duplicate signature or manual sign-off above Outlook signature.
- No large blank gap before signature.
- Send button is visible but not clicked.
- If using normal Chrome for user review, maximize the window.

Then respond:

```text
Ready for you to review and send.

Verified:
- Thread: [subject]
- To: [names]
- Cc: [names]
- Signature: Outlook reply signature present
- I did not click Send
```

## If the user says “send it”

Do not send. Interpret as prepare-to-send unless the user explicitly overrides the standing rule. Respond that the draft is ready for manual send.

## If a mistake happens

1. Stop.
2. State exactly what happened.
3. Do not compound the mistake with another send.
4. If appropriate, open recall, but explain that Outlook recall is not guaranteed.
5. Prepare the correct in-thread draft only after the user confirms.

## References

Read as needed:

- `references/outlook-checklist.md` for detailed step-by-step checklist.
- `references/dom-recipes.md` for safe Outlook Web DOM insertion and verification patterns.
- `references/failure-lessons.md` for concrete mistakes to avoid from the Bryan email incident.
- `scripts/lint_draft.py` to lint a plain-text draft for common violations.
