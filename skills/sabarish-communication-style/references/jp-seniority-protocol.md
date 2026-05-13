# JP Seniority and Recipient Ordering Protocol

Use this for emails/Teams involving Japanese colleagues or JP-US mixed audiences.

## Core rule

Japanese colleagues should be addressed with `-san` and recipient/name order should respect seniority where practical, especially in formal emails, meeting follow-ups, review requests, and escalation-sensitive topics.

This applies to:

- greeting order
- To field order
- Cc field order
- action table name order
- meeting-note attendee/order references
- written lists of reviewers/approvers

## Honorifics

Use:

- `Hayakawa-san`
- `Hosoya-san`
- `Saitoh-san`
- `Ghen-san` only if this is how the user/team refers to him; otherwise use the established local style.

Do not drop `-san` for Japanese colleagues unless the user provides a specific exception.

## Ordering principle

When multiple JP colleagues are included, order names by seniority first, then role relevance.

Default order logic:

1. Highest seniority / manager / department lead
2. Program lead / design lead / owner
3. Reviewer / approver
4. Working-level engineer
5. Support / FYI

If seniority is unknown:

1. Use the order from the original thread.
2. Use org/context files if available.
3. Ask the user rather than guessing for sensitive emails.

## To vs Cc

### To field

Put people in To when they are expected to act, decide, or respond.

Order To recipients by:

1. senior decision maker who must answer
2. primary owner
3. secondary owner

### Cc field

Put people in Cc when they need visibility only.

Order Cc recipients by:

1. senior manager / lead visibility
2. program stakeholders
3. working-level FYI

Do not put a junior working owner before a senior JP manager if both are in the same recipient line and the topic is formal or sensitive.

## Greeting order

Match the To field order when greeting multiple named JP recipients.

Good:

```text
Hayakawa-san, Hosoya-san,
```

If mixed US/JP audience and JP seniority is central, keep JP senior order first if they are the primary recipients.

If Bryan/Kevin are the decision owners and JP are FYI, address Bryan/Kevin first and mention JP colleagues with `-san` in the body or Cc.

## Thread preservation exception

If replying in an existing thread, do not reorder recipients in a way that changes the thread dynamics unless needed. Preserve original To/Cc if the thread already established ownership.

Still use `-san` in the body.

## Action table order

For JP-heavy action tables, order rows by:

1. decision/approval owner
2. technical owner
3. supporting owner
4. FYI/follow-up owner

Example:

| Owner | Action | Due |
|---|---|---|
| Hayakawa-san | Confirm routing for JP DE follow-up | [date] |
| Hosoya-san | Confirm drawing status / CDR item | [date] |
| Sabarish | Update US-side tracker after confirmation | [date] |

## Safety check

Before finalizing a JP email, verify:

- Are all JP colleagues addressed with `-san`?
- Are To/Cc names ordered by seniority or existing thread order?
- Is the most senior person not accidentally buried after working-level names?
- Is the ask respectful and specific?
- Are idioms avoided?
- Is the message precise enough for translation?
