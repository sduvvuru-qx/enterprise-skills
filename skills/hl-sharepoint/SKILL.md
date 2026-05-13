---
name: Headless SharePoint
description: 'Use when the user needs: Headless SharePoint Online automation — browse sites, document libraries, download files, search across sites. Primary: cookie export → SharePoint REST API (invisible). Fallback: Playwright browser. Trigger on "sharepoint", "download from sharepoint", "sharepoint docs", "document library", "shared documents".'
icon: icon.svg
metadata:
  replaces: [sp-download, sp-crawl]
  filePattern: []
  bashPattern:
  - sharepoint
  - download from sharepoint
  - sharepoint docs
  category: automation/headless
  family: hl
  lifecycle: active
  canonical_slug: hl-sharepoint
  icon_style: craft-category-glyph-v1
  invisible_first: true
  tier: 2
---

# hl-sharepoint — Headless SharePoint Online

General-purpose SharePoint document automation. **Primary path is invisible (curl + cookie export).** Browser is fallback only.

## Invisible-First Paths

| Priority | Method | Focus Theft | Speed |
|---|---|---|---|
| **1** | `export-cookies.py` → `curl` SharePoint REST API | ❌ None | ~2s |
| **2** | `browser-auth-sync` → `browser_tool` | ❌ None | ~5s |
| **3** | Playwright MCP (manual browser) | ⚠️ May steal focus | ~10s |

## Layer 1: Cookie Export → REST API (Primary)

### Export Cookies

```bash
# Export cookies for target tenant
python3 ~/.agents/skills/browser-surface-bridge/scripts/export-cookies.py \
  -p "Profile 5" -d sharepoint -o /tmp/sp-cookies.txt

# Use with curl — invisible, ~2 seconds
COOKIES="-b /tmp/sp-cookies.txt"
BASE="https://astemogroup.sharepoint.com"
```

### Operations via REST API

```bash
# Get site title
curl -s $COOKIES "$BASE/sites/Hoe-Axle/_api/web/title" \
  -H "Accept: application/json;odata=nometadata"
# → {"value":"(Ho) XP7G e-Axle [JP] (Migrated)"}

# List document libraries
curl -s $COOKIES "$BASE/sites/Hoe-Axle/_api/web/lists?\$select=Title,ItemCount,BaseTemplate&\$filter=BaseTemplate eq 101" \
  -H "Accept: application/json;odata=nometadata"

# Browse folders in a library
curl -s $COOKIES "$BASE/sites/Hoe-Axle/_api/web/GetFolderByServerRelativeUrl('/sites/Hoe-Axle/Shared Documents')/Files?\$select=Name,Length,TimeLastModified&\$top=20" \
  -H "Accept: application/json;odata=nometadata"

# List subfolders
curl -s $COOKIES "$BASE/sites/Hoe-Axle/_api/web/GetFolderByServerRelativeUrl('/sites/Hoe-Axle/Shared Documents')/Folders?\$select=Name,ItemCount&\$top=20" \
  -H "Accept: application/json;odata=nometadata"

# Download a file
curl -s $COOKIES "$BASE/sites/Hoe-Axle/_api/web/GetFileByServerRelativeUrl('/sites/Hoe-Axle/Shared Documents/file.xlsx')/\$value" \
  -o /tmp/downloaded-file.xlsx

# Search across site
curl -s $COOKIES "$BASE/sites/Hoe-Axle/_api/search/query?querytext='keyword'&rowlimit=10" \
  -H "Accept: application/json;odata=nometadata"

# List all sites
curl -s $COOKIES "$BASE/_api/search/query?querytext='contentclass:STS_Site'&rowlimit=20&selectproperties='Title,Path'" \
  -H "Accept: application/json;odata=nometadata"

# Clean up
rm -f /tmp/sp-cookies.txt
```

## Layer 2: browser_tool (Fallback)

When REST API doesn't cover the use case (visual preview, complex navigation):

```
browser_navigate: https://{tenant}.sharepoint.com/sites/{site-name}
browser_snapshot
```

### Operations

| Operation | Method |
|---|---|
| Browse library | Click Documents → snapshot |
| Download file | Click file → Download in toolbar |
| Preview file | Click file name → snapshot |
| Search | Click search → type query → Enter |
| Version history | Select file → ... → Version history |
| Excel Online | Click Excel file → snapshot |

## URL Patterns

| Resource | URL |
|---|---|
| Site | `https://{tenant}.sharepoint.com/sites/{name}` |
| Library | `.../sites/{name}/Shared Documents/` |
| REST API | `.../sites/{name}/_api/web/` |
| File download | `.../_api/web/GetFileByServerRelativeUrl('...')/$value` |
| Search | `.../_api/search/query?querytext='...'` |

## Error Handling

| Error | Fix |
|-------|-----|
| 401 Unauthorized | Re-export cookies (may have expired) |
| 403 Forbidden | No access to this site — inform operator |
| Empty response | Cookie auth insufficient — fall back to browser |
| Loading timeout | Wait 3-5s for large libraries |

## Security

- Cookie file contains decrypted auth tokens — `chmod 600`, delete after use
- Never store cookie files in durable paths
- REST API reads are read-only — no write operations without operator approval

## Contract

- **Never enter credentials** — assume authenticated session exists
- **Never modify/delete** SharePoint content without operator approval
- **Cookie files are ephemeral** — always clean up `/tmp/*cookies*`
- **Report auth failures** — do not silently skip locked content
