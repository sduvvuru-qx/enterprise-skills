---
name: M365 Search
description: Use when searching across all 262 accessible Astemo M365 SharePoint sites using the Search API. Find documents, presentations, spreadsheets by keyword, program code, or topic. Trigger on "search m365", "find in sharepoint", "m365 search", "search across sites", "find document".
icon: icon.svg
metadata:
  category: enterprise/astemo
  family: m365
  lifecycle: active
  canonical_slug: astemo-m365-search
  icon_style: craft-category-glyph-v1
---

# m365-search — Cross-M365 Knowledge Search

Search across all accessible Astemo SharePoint sites (262 sites, 3.1M+ items) using the SharePoint Search API.

## When to Use

- Finding documents across program sites
- Searching for standards (SASG, SASP)
- Locating meeting minutes, presentations, trackers
- Cross-program knowledge discovery

## Procedure

### 0. Operator-provided query URL

If the operator gives a direct SharePoint query URL such as:

`https://astemogroup-my.sharepoint.com/query?q=gen4&searchScope=all`

use that first.

Why:

- it is already scoped to the operator's intent
- it is faster than broad recovery or cache-forensics
- it often surfaces Teams-shared files that are easier to open than hunting through chat history

Normalize it as:

- `astemogroup-my.sharepoint.com/query?...` -> operator OneDrive search
- not generic tenant-wide SharePoint search
- not Teams-first retrieval

Interpretation rule:

- `-my.sharepoint.com` means the operator's OneDrive / personal work files surface
- `shareScope=all` means search across that operator-scoped OneDrive surface first
- only expand outward to tenant / Teams recovery if the OneDrive search does not resolve the file

### 1. Via MCP tool (fastest, cached)
```
enterprise_m365_search query:"XP7G GCFT"
```

### 2. Via SharePoint Search API (live, most complete)
```javascript
// browser_run_code — requires authenticated session
const resp = await page.evaluate(async (q) => {
  const r = await fetch(
    `https://astemogroup.sharepoint.com/_api/search/query?querytext='${encodeURIComponent(q)}'&selectproperties='Title,Path,LastModifiedTime,SiteName,FileExtension,Size'&rowlimit=20&sortlist='LastModifiedTime:descending'`,
    { headers: { 'Accept': 'application/json;odata=nometadata' } }
  );
  return await r.json();
}, query);
```

### 3. Parse results
```javascript
const rows = resp.PrimaryQueryResult.RelevantResults.Table.Rows;
const results = rows.map(row => {
  const cells = {};
  for (const cell of row.Cells) cells[cell.Key] = cell.Value;
  return { title: cells.Title, site: cells.SiteName, ext: cells.FileExtension, modified: cells.LastModifiedTime };
});
```

## Search syntax
- Simple: `XP7G GCFT`
- File type: `filetype:xlsx XP7G`
- Site-scoped: `path:"https://astemogroup.sharepoint.com/sites/USGRPxEV" VEC`
- Exclude: `FH4 -FH4S -FH4L`
- Date range: `LastModifiedTime>=2026-03-01`

## Priority Rule

For "find the document I shared / sent / saw in Teams" requests:

1. operator-provided SharePoint query URL normalized to operator OneDrive search
2. operator OneDrive search
3. live Teams chat inspection
4. local Teams cache recovery

Do not jump to cache forensics if SharePoint search is available.

## Knowledge categories (from session crawl)
| Category | Total Items | Key Sites |
|----------|------------|-----------|
| Red Review/Gate | 3,619 | NAGEN4VAFH4S |
| Quality/DFMEA | 3,305 | NAIPMLocalization |
| Supplier/PPAP | 2,228 | PUR |
| Meeting Minutes | 2,201 | USAMHKFAINVDE |
| SASG Standards | 35,272 | hiams-GroupRules |
