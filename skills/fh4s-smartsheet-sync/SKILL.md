---
name: FH4S Smartsheet Sync
description: 'Use when syncing the FH4S DE Activity Schedule between the local Excel tracker and the Smartsheet (ID: 2231837406482308). Read from Smartsheet API, update the tracker''s DE Activity sheet, or push tracker changes to Smartsheet. Uses keychain-stored API token. Perspective: US DE Lead (from shadow-sidecast team-map --program FH4S) at USGRP1. Trigger on: "sync smartsheet", "update smartsheet", "smartsheet sync", "push to smartsheet", "pull from smartsheet", "DE schedule sync".'
icon: icon.svg
metadata:
  depends_on: [astemo-work-context]
  priority: 70
  bashPattern: smartsheet|sync.*schedule
  category: enterprise/fh4s
  family: fh4s
  lifecycle: active
  canonical_slug: fh4s-smartsheet-sync
  icon_style: craft-category-glyph-v1
---


## Work context (read first — all dimensions)

Load **`astemo-work-context`**: `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md` — resolves **program** (Dropbox roots, registry), **role** (which hat / audience), **org** (site, team, reporting), **task** (what artifact), and **workflow** (SASG / gates / sequence) before loading narrow `fh4s-*` or customer-only skills. Literal `FH4S_4E0A_IPM` / SHADOW paths below are **FH4S examples** unless this skill is explicitly multi-program.

# FH4S Smartsheet Sync Skill

## Role Context
You are assisting the US DE Lead (from shadow-sidecast team-map --program FH4S) in keeping the DE Activity Schedule synchronized
between the local Change Point Tracker (Sheet 5: DE Activity) and the Smartsheet used by the
broader USGRP1 team. Smartsheet is the team-visible schedule; the local tracker is the US DE Lead's
working copy with more detail.

## Prerequisites
- Load **fh4s-context** for Smartsheet ID, org chart
- Load **astemo-smartsheet** for API patterns and authentication
- Load **astemo-xlsx** if updating local tracker

## Key Resources
| Resource | Value |
|---|---|
| Smartsheet ID | 2231837406482308 |
| Smartsheet Permalink | https://app.smartsheet.com/sheets/55hj858FpgJwW9mj2Q3XPW7JhqwhGfJm6Pg66H81 |
| Local Tracker | /Volumes/SHADOW/ASTEMO/fh4s/outputs/workbooks/FH4S_4E0A_IPM_Change_Point_Tracker.xlsx |
| Local Sheet | "DE Activity" (Sheet 5) |
| API Token | `security find-generic-password -s "smartsheet" -a "api-token" -w` |
| Account | (from shadow-sidecast team-map --program FH4S) |

## Smartsheet Columns (DE Activity Schedule)
Expected columns in the Smartsheet:
| Column | Type | Maps to Tracker |
|---|---|---|
| Task Name | TEXT_NUMBER (primary) | Activity description |
| Owner | TEXT_NUMBER | Responsible person |
| Department | PICKLIST | DE/PE/SQ/BUP/QA |
| Status | PICKLIST | Not Started/In Progress/Complete/Blocked |
| Start Date | DATE | Activity start |
| End Date | DATE | Activity end |
| Risk | PICKLIST | H/M/L |
| Lead Region | PICKLIST | JP/US/Shared |
| Notes | TEXT_NUMBER | Additional context |
| Related Parts | TEXT_NUMBER | Part numbers affected |

## Workflow

### Pull: Smartsheet → Local Tracker

#### 1. Fetch Smartsheet Data
```bash
TOKEN=$(security find-generic-password -s "smartsheet" -a "api-token" -w)
SHEET_ID=2231837406482308

# Get sheet with all rows and columns
curl -s "https://api.smartsheet.com/2.0/sheets/$SHEET_ID" \
  -H "Authorization: Bearer $TOKEN" | python3 -m json.tool > /tmp/ss_data.json
```

#### 2. Parse and Map
```python
import json, openpyxl

with open('/tmp/ss_data.json') as f:
    ss = json.load(f)

# Build column ID → name map
col_map = {c['id']: c['title'] for c in ss['columns']}

# Extract rows
for row in ss['rows']:
    cells = {col_map[c['columnId']]: c.get('value', '') for c in row['cells']}
    # Map to local tracker DE Activity sheet
```

#### 3. Update Local Tracker
- Open tracker with openpyxl
- Navigate to "DE Activity" sheet
- Match rows by Task Name (primary key)
- Update Status, Dates, Notes from Smartsheet
- Add new rows if Smartsheet has entries not in local
- Apply **astemo-xlsx** styling
- Save tracker

### Push: Local Tracker → Smartsheet

#### 1. Read Local DE Activity Sheet
```python
wb = openpyxl.load_workbook(TRACKER_PATH)
ws = wb['DE Activity']
local_rows = []
for row in ws.iter_rows(min_row=2, values_only=True):
    local_rows.append({...})  # map columns
```

#### 2. Fetch Current Smartsheet State
(Same as Pull step 1)

#### 3. Diff and Update
Compare local vs Smartsheet:
- **Modified rows**: Update via PUT
- **New rows**: Add via POST with toBottom=true
- **Deleted rows** (in local but marked complete): optionally archive in Smartsheet

```bash
# Update existing rows
curl -s -X PUT "https://api.smartsheet.com/2.0/sheets/$SHEET_ID/rows" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '[{"id": ROW_ID, "cells": [{"columnId": COL_ID, "value": "NEW_VALUE"}]}]'

# Add new rows
curl -s -X POST "https://api.smartsheet.com/2.0/sheets/$SHEET_ID/rows" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '[{"toBottom": true, "cells": [...]}]'
```

#### 4. Batch Limits
- Max 50 rows per API call (Smartsheet limit)
- If > 50 rows, batch into multiple calls
- Pause briefly between batches to avoid rate limits

### Bidirectional Sync

#### 1. Pull from Smartsheet (get team updates)
#### 2. Merge with local changes (local wins on conflict, log conflicts)
#### 3. Push merged result back to Smartsheet
#### 4. Report sync summary:
```
Smartsheet Sync Complete:
- Pulled: 3 status updates from Smartsheet
- Pushed: 5 local updates to Smartsheet
- Conflicts: 1 (Row "DFMEA Review" — local: In Progress, SS: Complete → kept local)
- New rows added to SS: 2
```

## Conflict Resolution
- **Default**: Local tracker wins (Sabarish's working copy is source of truth for DE items)
- **Exception**: If Smartsheet was updated by Ghen or a manager, flag for manual review
- Always log conflicts in the Change Log sheet of the tracker

## Cross-References
- **astemo-smartsheet**: API authentication, column types, batch patterns
- **fh4s-change-point-update**: Changes to tracker may trigger need to sync
- **fh4s-context**: Smartsheet ID, team members who edit the schedule
