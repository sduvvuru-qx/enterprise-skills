---
name: shadow-ent-tracker-operations
description: Use for enterprise trackers, schedules, workbooks, and Smartsheet-style table operations.
---

# shadow-ent-tracker-operations

Use when status, ownership, and dates live in tabular artifacts.

## When to Use

- tracker updates
- schedule reasoning
- workbook or table-driven status work
- Smartsheet or Excel Online operations

## Workflow

1. Identify the source-of-truth table surface.
2. Read status/owner/date semantics before editing.
3. Change the narrowest necessary rows or fields.
4. Preserve traceability for the update.

## Key Decisions

- **Live table exists**: prefer the live surface over exported copies.
- **Status unclear**: preserve the ambiguity instead of forcing a state transition.
- **Multiple trackers overlap**: identify which one is authoritative before editing.

## Output Shape

- source table
- affected row(s)
- proposed or completed update
- trace/reference back to source evidence

## Gotchas

- Updating a derivative workbook instead of the source-of-truth tracker.
- Changing row meaning without checking column semantics.
- Bulk-editing rows when only one status cell actually changed.
