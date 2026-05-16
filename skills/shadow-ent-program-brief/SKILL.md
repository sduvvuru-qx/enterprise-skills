---
name: shadow-ent-program-brief
description: Use to assemble concise program-state context across enterprise programs and adjacent engineering review lanes.
---

# shadow-ent-program-brief

Use when engineering work needs current program context before recommendations, tracker edits, or review output.

## When to Use

- "what's happening with this program"
- program status or context requests
- preparing for engineering or customer meetings
- when multiple adjacent programs could be confused

## Workflow

1. Identify the named program or infer the narrowest likely one.
2. Extract only current-state signals:
   - active / canceled / pending
   - key schedule or gate
   - major engineering risks or blockers
   - who matters for the topic
3. Separate stable facts from session-specific inference.
4. Return a brief shaped for the next decision, not a history dump.

## Key Decisions

- **Program ambiguous**: state the ambiguity and narrow by surrounding context.
- **Old status exists**: prefer the freshest reliable signal, but label it with time.
- **Cross-program spillover**: note relationships without collapsing distinct programs into one.

## Gotchas

- Collapsing distinct programs or variants into one status line.
- Treating a historical note as current state.
- Listing every fact instead of the ones that matter for the next move.

## Output Shape

- program
- current state
- immediate engineering concerns
- relevant stakeholders
- next decision or check

## Crystallized From

- Session: 2026-04-17 on JARVIS
- Intent: broader enterprise engineering role first, narrower personal role separate
- Pattern source: reduced from larger private program-context/status workflows
