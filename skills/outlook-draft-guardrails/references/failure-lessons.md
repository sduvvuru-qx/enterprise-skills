# Failure Lessons

These are concrete lessons from the Bryan / AI tooling email incident.

## Failure 1: New compose instead of thread reply

**What happened:** A new email was created to Bryan only instead of replying in the existing Yingying/Bryan RFQ AI thread.

**Why it mattered:**

- Wrong audience.
- Wrong thread context.
- Wrong signature mode.
- User had to ask why it was not in the thread.

**Rule:** If the user is responding to a message, always find the original thread and use Reply/Reply all.

## Failure 2: Wrong signature behavior

**What happened:** New-email signature appeared when a reply signature should have appeared.

**Rule:** Do not manually manage signatures. Use the correct Outlook compose mode and let Outlook apply the correct signature.

## Failure 3: Agent clicked Send

**What happened:** Agent sent instead of preparing draft for manual send.

**Rule:** Never click Send for this user. Even if the user says `send it`, prepare the draft and say it is ready for manual review/send unless the user explicitly overrides the standing safety rule.

## Failure 4: Draft looked pasted

**What happened:** Draft had excess blank spacing and a manual close above the reply signature.

**Rule:** Use compact insertion, remove manual close, verify visible body before handoff.

## Failure 5: Human window too small

**What happened:** Browser was left in a small automation layout.

**Rule:** On this laptop, maximize the human-facing Outlook/Chrome window before handing back.

## Failure 6: Normal Chrome used for agent work

**What happened:** Agent used regular Chrome because it was authenticated.

**Rule:** Prefer Craft browser for agent-controlled Outlook. If Craft browser requires security verification, ask the user to complete it. Use normal Chrome only with a stated reason.

## Recovery protocol

If any of these happen again:

1. Stop immediately.
2. State the exact mistake without defensiveness.
3. Do not send another correction unless the user asks.
4. If recall is appropriate, attempt it and explain recall is not guaranteed.
5. Prepare the correct draft in the correct thread.
6. Verify and hand off for manual send.
