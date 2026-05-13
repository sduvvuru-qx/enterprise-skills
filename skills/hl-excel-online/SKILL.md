---
name: Headless Excel Online
description: 'Use when the user needs: Headless Excel Online automation — open workbooks, read cells/ranges, extract tables, navigate sheets, check formulas via Playwright browser. Works with any M365 account. Trigger on "excel online", "spreadsheet", "open workbook", "read excel from sharepoint", "excel web".'
icon: icon.svg
metadata:
  filePattern: []
  bashPattern:
  - excel online
  - spreadsheet online
  - workbook web
  - excel web
  category: automation/headless
  family: hl
  lifecycle: active
  canonical_slug: hl-excel-online
  invisible_first: true
  tier: 3
  icon_style: craft-category-glyph-v1
---

# hl-excel-online — Headless Excel Online (Generalizable)

General-purpose Excel Online automation via Playwright browser tools.
Works with any M365 workbook stored in OneDrive or SharePoint.

## Prerequisites
- Chrome profile with active M365 session
- Playwright MCP tools available
- Workbook must be in OneDrive or SharePoint (not local)

## Operations

### 1. Open Workbook by URL
```
browser_navigate: https://{tenant}.sharepoint.com/:x:/r/sites/{site}/_layouts/15/Doc.aspx?sourcedoc={id}
browser_snapshot  # verify workbook loaded, capture visible cells
```
Or from OneDrive/SharePoint, click the .xlsx file to open in browser.

### 2. Read Visible Cells
Snapshot captures the current viewport:
- Cell values, formatting, colors
- Row/column headers (A, B, C... / 1, 2, 3...)
- Active cell highlight
- Frozen panes and headers

### 3. Navigate to Cell/Range
```
browser_click: [Name Box — left of formula bar, shows "A1"]
browser_type: "G15"  # or "A1:Z50" for a range
browser_press_key: Enter
browser_snapshot  # viewport scrolls to target
```

### 4. Switch Sheets
```
browser_click: [sheet tab at bottom — "Sheet1", "Summary", etc.]
browser_snapshot  # new sheet loaded
```
Sheet tabs are visible at bottom of viewport.

### 5. Read Formula Bar
```
browser_click: [target cell]
browser_snapshot  # formula bar shows: =VLOOKUP(...) or raw value
```

### 6. Extract Table Data
For structured tables:
```
# Click any cell in the table
browser_click: [cell within table]
browser_snapshot  # table headers, filters, banded rows visible

# Select all table data
browser_press_key: Ctrl+A  # selects table region
browser_snapshot
```

### 7. Use Find
```
browser_press_key: Ctrl+F
browser_type: "search term"
browser_press_key: Enter
browser_snapshot  # highlights matches, shows count
```

### 8. Check Filters
```
browser_click: [filter dropdown arrow on column header]
browser_snapshot  # filter values, checked/unchecked items
```

### 9. Read Named Ranges
```
browser_click: [Name Box dropdown]
browser_snapshot  # lists all named ranges
browser_click: [named range]  # navigates to it
browser_snapshot
```

## Reading Strategy for Large Workbooks

1. **Snapshot first** — get visible area, sheet tabs, scroll position
2. **Navigate to Name Box** — jump to specific ranges vs scrolling
3. **Use Ctrl+End** — find last used cell to understand data extent
4. **Sheet-by-sheet** — switch tabs systematically

## Error Handling
- **Login page:** Check Chrome profile for saved credentials
- **MFA prompt:** "MFA required — approve on your phone", wait 30s
- **Read-only mode:** Workbook may open read-only if another user is editing
- **Loading:** Wait 3-5s for large workbooks, Excel Online renders progressively
- **Protected sheets:** Some sheets may be locked — visible but not editable
- **#REF / #VALUE errors:** Formula errors visible in cells, check formula bar

## Limitations
- Read-only by default — no cell editing for safety (use openpyxl for writes)
- Cannot run VBA macros (not supported in Excel Online)
- Very large workbooks (>50MB) may load slowly or partially
- Print area and page breaks not visible in web view
- Rate: 2+ seconds between clicks, 3-5s after navigation
- Complex charts render as images — data extraction from chart axes is approximate


## Pipeline

```
Intent → Authenticate browser session → Navigate to target → Extract/interact → Return structured output
```

1. Resolve target (URL, search query, operation)
2. Connect to authenticated browser session (Chrome profile)
3. Navigate and wait for page load
4. Extract data or perform interaction via Playwright/browser_tool
5. Return structured output (JSON, markdown, file)

## Modes

| Mode | Output | When |
|------|--------|------|
| `extract` (default) | Structured data extraction | Read/query operations |
| `interact` | Perform action, return result | Click, fill, submit |
| `monitor` | Continuous or periodic check | Watch, wait, alert |

## Artifact Routing

- Extracted data: presented in chat or saved to `ShadowArchive/80-reports/`
- Downloaded files: browser default download folder → classify → move to program structure

## Fallback Chain

1. Playwright MCP / browser_tool (primary)
2. Chrome DevTools Protocol via chrome-web-cli
3. AppleScript Chrome control (last resort)
4. If auth required and not available: stop and ask operator to open authenticated session

## Prerequisites

- Chrome with authenticated session for target service
- Playwright MCP tools or browser_tool available
- Network access to target service (VPN if enterprise)

## Error Handling

- **Auth wall / sign-in page**: Stop, inform operator, do not attempt credentials
- **Element not found**: Wait 3s, retry with relaxed selector, then report
- **Rate limited**: Back off 5s, retry once, then stop
- **VPN required**: Report "VPN down" and stop

## Contract

- **Never enter credentials** — assume authenticated session exists
- **Never click Send/Submit on final actions** (email, message) without operator approval
- **Respect rate limits** — 2s minimum between automated requests
- **Preserve browser state** — do not log out, close tabs, or clear cookies
- **Report auth failures** — do not silently skip locked content

