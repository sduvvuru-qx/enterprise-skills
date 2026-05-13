---
name: Headless Dropbox
description: 'Use when the user needs: Headless Dropbox automation — file ops, sync, search, sharing, bulk operations via API and local filesystem. Enterprise-grade CLI for Astemo portable config and cross-device sync.'
icon: icon.svg
type: implementation
metadata:
  depends_on: [astemo-dropbox,astemo-work-context]
  category: storage/cloud
  family: hl
  lifecycle: active
  canonical_slug: hl-dropbox
  icon_style: craft-category-glyph-v1
  replaced_by: astemo-dropbox
---


## Work context (read first — all dimensions)

Load **`astemo-work-context`**: `/Volumes/☯Duality/skills/astemo-work-context/SKILL.md` — resolves **program** (Dropbox roots, registry), **role** (which hat / audience), **org** (site, team, reporting), **task** (what artifact), and **workflow** (SASG / gates / sequence) before loading narrow `fh4s-*` or customer-only skills. Literal `FH4S_4E0A_IPM` / SHADOW paths below are **FH4S examples** unless this skill is explicitly multi-program.

# hl-dropbox — Headless Dropbox Enterprise CLI

Full Dropbox automation via Python SDK (12.0.2) + local filesystem. 179 API endpoints mapped.

## Adapter Position

`hl-dropbox` remains the concrete Dropbox implementation surface.

For Astemo-facing requests, prefer `astemo-dropbox` as the routing and policy layer, then load this skill for the actual Dropbox SDK and local filesystem workflows.

## Setup

**SDK:** `dropbox` Python package (venv at `/tmp/dbx-env/`)
**Local path:** `~/Library/CloudStorage/Dropbox` (macOS CloudStorage provider)
**Symlink:** `~/Dropbox` → CloudStorage path
**Account:** Personal (Basic), host ID 19951336275

### OAuth Token
No token stored yet. To create one:
1. Go to https://www.dropbox.com/developers/apps
2. Create app → Scoped access → Full Dropbox
3. Generate access token
4. Store: `security add-generic-password -s "dropbox/api-token" -a "sdluffy" -w "TOKEN" ~/Library/Keychains/login.keychain-db`

### Python Usage
```python
import subprocess, json
token = subprocess.check_output(
    ["security", "find-generic-password", "-s", "dropbox/api-token", "-w"],
    text=True
).strip()

import dropbox
dbx = dropbox.Dropbox(token)
account = dbx.users_get_current_account()
```

---

## Local Dropbox Structure (Astemo)

```
~/Library/CloudStorage/Dropbox/
├── _Active/                    # Current working files
│   ├── Tools/PE-Framework/     # RAG engine config
│   ├── Tools/HTML-Apps/        # Portable web apps
│   ├── Projects/FH4S-IPM/      # FH4S project files
│   ├── Projects/XP7G/          # XP7G (cancelled)
│   ├── Reference/              # Org charts, GDT, screenshots
│   ├── Proposals/AI-Adoption/  # AI proposal materials
│   ├── Standards/SASG/         # SASG standard extracts
│   └── sd-astemo/              # Cross-device transfer staging
├── FH4S_4E0A_IPM/             # FH4S canonical structure
│   ├── 01_SASG_Documents/      # Quality docs (JP/US accountable)
│   ├── 02_Change_Point_Tracker/
│   ├── 03_Drawings/
│   ├── 04_Presentations/
│   ├── 05_Quality_Forms/
│   ├── 06_Schedules/
│   ├── 07_Meeting_Notes/
│   ├── 09_Reference/SASG_Templates/
│   ├── 10_Change_Points_History/
│   └── 90_Astemo_Config/       # Portable config (skills, kernel)
├── Screenshots/
├── _Sent/
├── _Incoming/
└── _Archive Index/
```

---

## API Endpoint Map (179 methods, 8 namespaces)

### files (75 methods) — Core File Operations
| Operation | Method | Use Case |
|-----------|--------|----------|
| List folder | `files_list_folder(path)` | Browse directories |
| List continue | `files_list_folder_continue(cursor)` | Paginate large folders |
| Get metadata | `files_get_metadata(path)` | File/folder info |
| Download | `files_download(path)` | Get file content |
| Download to file | `files_download_to_file(local, path)` | Save to disk |
| Upload | `files_upload(data, path, mode)` | Create/overwrite file |
| Upload session | `files_upload_session_start/append/finish` | Large file upload (>150MB) |
| Create folder | `files_create_folder_v2(path)` | Make directory |
| Delete | `files_delete_v2(path)` | Remove file/folder |
| Move | `files_move_v2(from, to)` | Rename/move |
| Copy | `files_copy_v2(from, to)` | Duplicate |
| Search | `files_search_v2(query)` | Full-text search |
| Get preview | `files_get_preview(path)` | PDF preview of Office docs |
| Get thumbnail | `files_get_thumbnail_v2(path)` | Image thumbnails |
| Get temp link | `files_get_temporary_link(path)` | 4-hour download URL |
| Save URL | `files_save_url(path, url)` | Download URL to Dropbox |
| Tags | `files_tags_add/get/remove` | File tagging |
| Revisions | `files_list_revisions(path)` | Version history |
| Restore | `files_restore(path, rev)` | Restore old version |
| Lock/Unlock | `files_lock_file_batch/unlock_file_batch` | File locking |
| Batch ops | `files_*_batch*` | Bulk copy/move/delete |
| Paper | `files_paper_create/update` | Create Paper docs |
| Download ZIP | `files_download_zip(path)` | Download folder as zip |
| Export | `files_export(path)` | Export Google/Paper docs |

### sharing (35 methods) — Links & Collaboration
| Operation | Method |
|-----------|--------|
| Create shared link | `sharing_create_shared_link_with_settings(path)` |
| List shared links | `sharing_list_shared_links()` |
| Get link metadata | `sharing_get_shared_link_metadata(url)` |
| Share folder | `sharing_share_folder(path)` |
| Add file member | `sharing_add_file_member(path, members)` |
| Add folder member | `sharing_add_folder_member(id, members)` |
| List folder members | `sharing_list_folder_members(id)` |
| Revoke link | `sharing_revoke_shared_link(url)` |
| Transfer folder | `sharing_transfer_folder(id, to)` |

### file_properties (20 methods) — Custom Metadata
| Operation | Method |
|-----------|--------|
| Add properties | `file_properties_properties_add(path, groups)` |
| Search by property | `file_properties_properties_search(queries)` |
| Templates | `file_properties_templates_add/get/list/update_for_user` |

### file_requests (9 methods) — File Request Management
| Operation | Method |
|-----------|--------|
| Create request | `file_requests_create(title, dest)` |
| List requests | `file_requests_list_v2()` |
| Delete/Count | `file_requests_delete/count` |

### users (5 methods) — Account Info
| Operation | Method |
|-----------|--------|
| Current account | `users_get_current_account()` |
| Space usage | `users_get_space_usage()` |
| Features | `users_features_get_values(features)` |

### paper (18 methods) — Paper Docs
| Operation | Method |
|-----------|--------|
| Create/Update | `paper_docs_create/update` |
| Download | `paper_docs_download(doc_id, format)` |
| List | `paper_docs_list()` |
| Share | `paper_docs_users_add/list/remove` |
| Folders | `paper_folders_create(name)` |

### contacts (2 methods)
- `contacts_delete_manual_contacts()`
- `contacts_delete_manual_contacts_batch(emails)`

### auth (2 methods)
- `auth_token_revoke()`
- `auth_token_from_oauth1(token, secret)`

---

## CLI Workflows

### 1. Sync SHADOW → Dropbox (portable config)
```python
# Push Astemo config to Dropbox for cross-device access
dbx_push("kernel/", "FH4S_4E0A_IPM/90_Astemo_Config/kernel/")
dbx_push("fh4s/tracker/change-points.json", "FH4S_4E0A_IPM/02_Change_Point_Tracker/")
```

### 2. Cross-Device File Transfer
```python
# Stage file for work laptop pickup
dbx_upload("meetings/2026-03-31/FH4L_Status.pptx", "/_Active/sd-astemo/")
# On work laptop: file appears in Dropbox sync
```

### 3. Search Across All Files
```python
results = dbx.files_search_v2("FH4S change point DFMEA")
for match in results.matches:
    print(match.metadata.metadata.path_display)
```

### 4. Generate Temporary Download Link
```python
link = dbx.files_get_temporary_link("/FH4S_4E0A_IPM/04_Presentations/file.pptx")
print(link.link)  # valid for 4 hours
```

### 5. Bulk Organize (batch move)
```python
entries = [
    dropbox.files.RelocationPath(from_path, to_path)
    for from_path, to_path in moves
]
dbx.files_move_batch_v2(entries)
```

### 6. Version History / Restore
```python
revisions = dbx.files_list_revisions("/path/to/file.xlsx")
for rev in revisions.entries:
    print(f"{rev.rev} | {rev.server_modified} | {rev.size}")
# Restore: dbx.files_restore("/path/to/file.xlsx", rev_id)
```

### 7. Watch for Changes (longpoll)
```python
cursor = dbx.files_list_folder_get_latest_cursor("/_Active").cursor
changes = dbx.files_list_folder_longpoll(cursor, timeout=30)
if changes.changes:
    result = dbx.files_list_folder_continue(cursor)
    for entry in result.entries:
        print(f"Changed: {entry.path_display}")
```

---

## Module Location
`tools/shadow_enterprise_cli/tools_dropbox.py`

## Rules
1. Always use API for remote operations, local filesystem for reads
2. Store token in macOS Keychain, never in code
3. Use batch APIs for >5 file operations
4. Upload sessions for files >150MB
5. Local Dropbox path: `~/Library/CloudStorage/Dropbox` (not `~/Dropbox`)
6. `_Active/sd-astemo/` is the cross-device staging folder
7. `FH4S_4E0A_IPM/90_Astemo_Config/` is the portable config canonical location


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

