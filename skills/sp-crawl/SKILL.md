---
name: SharePoint Crawl
description: 'Use when the user needs: Deep crawl any SharePoint site via REST API — enumerate folders, files, lists, and search. Uses authenticated Playwright browser session. Trigger on "crawl sharepoint", "sp crawl", "explore sharepoint", "map sharepoint site".'
icon: icon.svg
metadata:
  category: enterprise/astemo
  family: sp
  lifecycle: active
  canonical_slug: astemo-sp-crawl
  icon_style: craft-category-glyph-v1
---

# sp-crawl — SharePoint Deep Crawl

Recursively enumerate any accessible SharePoint site using the REST API via an authenticated browser session.

## When to Use

- Mapping a new SharePoint site's structure
- Finding files across program folders
- Building inventory JSONs for the kernel
- Searching across all 262 accessible Astemo SP sites

## Procedure

### 1. Navigate to the site
```
browser_navigate: https://astemogroup.sharepoint.com/sites/{SITE_NAME}
```

### 2. List all visible lists
```javascript
// browser_run_code
const resp = await page.evaluate(async () => {
  const r = await fetch("https://astemogroup.sharepoint.com/sites/{SITE}/_api/web/lists?$filter=Hidden eq false&$select=Title,ItemCount,LastItemModifiedDate&$orderby=LastItemModifiedDate desc",
    { headers: { 'Accept': 'application/json;odata=nometadata' } });
  return await r.json();
});
```

### 3. Walk folders recursively
```javascript
const base = "https://astemogroup.sharepoint.com/sites/{SITE}/_api";
const folders = await page.evaluate(async (url) => {
  const r = await fetch(url, { headers: { 'Accept': 'application/json;odata=nometadata' } });
  return await r.json();
}, `${base}/web/GetFolderByServerRelativeUrl('/sites/{SITE}/Shared Documents')/Folders?$select=Name,ItemCount,TimeLastModified&$orderby=TimeLastModified desc`);
```

### 4. Get files in a folder
```javascript
const files = await page.evaluate(async (url) => {
  const r = await fetch(url, { headers: { 'Accept': 'application/json;odata=nometadata' } });
  return await r.json();
}, `${base}/web/GetFolderByServerRelativeUrl('/sites/{SITE}/Shared Documents/{FOLDER}')/Files?$select=Name,Length,TimeLastModified&$orderby=TimeLastModified desc&$top=20`);
```

### 5. Cross-site search (searches ALL 262 sites)
```javascript
const search = await page.evaluate(async (q) => {
  const r = await fetch(
    `https://astemogroup.sharepoint.com/_api/search/query?querytext='${q}'&selectproperties='Title,Path,LastModifiedTime,SiteName,FileExtension,Size'&rowlimit=20&sortlist='LastModifiedTime:descending'`,
    { headers: { 'Accept': 'application/json;odata=nometadata' } });
  return await r.json();
}, query);
```

### 6. Save inventory
Write results to `kernel/{site-name}-inventory.json`.

## Key Sites

| Site | URL Suffix | Content |
|------|-----------|---------|
| USGRPxEV | /sites/USGRPxEV | INV DE team (5,459 docs) |
| NAGEN4VAFH4S | /sites/NAGEN4VAFH4S | FH4S program (8,957 docs) |
| NAIPMLocalization | /sites/NAIPMLocalization | IPM localization (1,932 docs) |
| HoXP7Ge-AxleAM | /sites/HoXP7Ge-AxleAM | XP7G program |
| Ho800V | /sites/Ho800V | XY9H / Honda 800V |
| hiams-GroupRules | /sites/hiams-GroupRules | SASG standards (35,272 docs) |

## Examples

- `sp-crawl USGRPxEV` — crawl the main INV DE site
- `sp-crawl NAGEN4VAFH4S depth:3` — deep crawl FH4S site
- `sp-crawl search "DFMEA severity"` — search across all sites


## Pipeline

```
Intent → Scan mesh → Collect data → Analyze → Report findings
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Summary report | Normal scan |
| `full` | Complete dump | Deep analysis |
| `json` | Raw data | Programmatic processing |

## Artifact Routing

- Scan reports: `ShadowArchive/80-reports/mesh-*.md`
- Audit logs: `ShadowArchive/80-reports/`

## Prerequisites

- Tailscale mesh connected
- SSH access to target nodes

## Contract

- Read-only scanning by default
- Never execute remediation without operator approval
- Include timestamps with all findings

