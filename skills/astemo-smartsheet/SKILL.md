---
name: Astemo Smartsheet
description: 'Use when creating and manage Astemo project schedules in Smartsheet via API. Handles sheet creation, row population, Gantt configuration, and status tracking. Uses Sabarish''s Smartsheet token stored in macOS Keychain (service=smartsheet, account=api-token). Account: sabarish.duvvuru.qx@astemo.com. Trigger on: "smartsheet", "create schedule", "project timeline", "Gantt chart", "update smartsheet", any Astemo project scheduling.'
icon: icon.svg
metadata:
  bashPattern: smartsheet|gantt
  priority: 75
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-smartsheet
  icon_style: craft-category-glyph-v1
---

# Astemo Smartsheet Skill

## Authentication
```bash
# Token in keychain
TOKEN=$(security find-generic-password -s "smartsheet" -a "api-token" -w)
```

## API Patterns

### Create Sheet
```bash
curl -s -X POST "https://api.smartsheet.com/2.0/sheets" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name": "...", "columns": [...]}'
```

### Column Types
- Primary: TEXT_NUMBER (always first, primary=true)
- Status: PICKLIST with options ["Not Started","In Progress","Open","Finalized","Complete","Due Now","Target","Blocked"]
- Risk: PICKLIST with options ["H","M","L"]
- Lead Region: PICKLIST with options ["JP","US","Shared"]
- Dates: DATE (format YYYY-MM-DD)
- Owners: TEXT_NUMBER (not CONTACT_LIST — Smartsheet rejects non-email contacts)
- Predecessors: PREDECESSOR (only works on project-enabled sheets)

### Add Rows
```bash
curl -s -X POST "https://api.smartsheet.com/2.0/sheets/$SHEET_ID/rows" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '[{"toBottom": true, "cells": [{"columnId": ..., "value": ...}]}]'
```
Batch max ~50 rows per call. Use toBottom=true for append.

### Enable Project Settings (Gantt)
After creating a sheet, enable project settings to get Duration/Predecessors/% Complete auto-columns:
- This requires Start Date and End Date columns to exist
- Use PUT on the sheet with projectSettings

## Rules
1. Always batch rows (10-15 per API call) to avoid timeouts
2. Use section header rows for phase grouping (bold text, no dates)
3. Set Start/End dates for Gantt bar rendering
4. Account: sabarish.duvvuru.qx@astemo.com at Astemo Americas
5. Existing sheet: FH4S DE Activity Schedule — ID: 2231837406482308, Permalink: https://app.smartsheet.com/sheets/55hj858FpgJwW9mj2Q3XPW7JhqwhGfJm6Pg66H81

## Pipeline

```
Intent → Resolve program → Load Smartsheet config → API call → Format output
```

1. Resolve target program from context or explicit argument
2. Load Smartsheet IDs from program registry
3. Authenticate via keychain-stored API token
4. Execute sheet operation (read, create, update)
5. Format and present results

## Modes

| Mode | Output | When |
|------|--------|------|
| `read` (default) | Sheet data as structured output | "read smartsheet", "get schedule" |
| `create` | New sheet with program columns | "create schedule" |
| `update` | Push changes to existing sheet | "update smartsheet", "sync schedule" |

## Artifact Routing

- Smartsheet data: presented in chat as table/spreadsheet
- Sync reports: `ShadowArchive/80-reports/smartsheet-sync-YYYY-MM-DD.md`

## Fallback Chain

1. Smartsheet API via keychain token
2. `npm run program:smartsheet-fetch` for offline data
3. Browser automation (last resort, when API quota exceeded)

## Prerequisites

- Smartsheet API token in macOS Keychain (service=smartsheet, account=api-token)
- Program registry with Smartsheet sheet IDs
- Account: sabarish.duvvuru.qx@astemo.com

## Error Handling

- **Token expired**: Prompt operator to update keychain token
- **Sheet not found**: Verify sheet ID in program registry
- **Rate limited**: Back off 60s and retry

## Contract

- Never modify production sheets without operator confirmation
- Always dry-run sync operations first
- Include row count before/after any update operation
