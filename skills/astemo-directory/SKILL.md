---
name: Astemo Directory
description: "Invisible Astemo org chart lookup via Microsoft Graph API (az CLI). Query employees by name, department, email, job title. Full directory access. Trigger on 'find employee', 'who is', 'org chart', 'colleague lookup', 'astemo directory', 'find contact', 'department members'."
icon: icon.svg
metadata:
  category: enterprise/astemo
  family: astemo
  lifecycle: active
  canonical_slug: astemo-directory
  icon_style: craft-category-glyph-v1
  invisible_first: true
  tier: 1
---

# Astemo Directory — BLOCKED

> **⚠️ OPERATOR: No Graph API access. This skill is non-functional until operator confirms Graph API is available.**

---

# Astemo Directory — Invisible Org Chart Lookup (BLOCKED)

Query the full Astemo employee directory via Microsoft Graph API. ~2 seconds. Zero browser. Zero focus theft.

## Authentication

Uses `az` CLI v2.85.0 with tenant-level Graph API access. Already authenticated as `sabarish.duvvuru.qx@astemo.com`.

```bash
# Verify auth
az rest --method get --url "https://graph.microsoft.com/v1.0/me?\$select=displayName,mail" --output json
```

## Quick Lookup Patterns

```bash
# By name
az-rest-user "Bryan Voris"
az-rest-user "Hayakawa"
az-rest-user "Hosoya"

# By department
az-rest-dept "New Model Develop XEV DE"

# By partial name
az-rest-search "Saitoh"
```

## Common Queries

### Find by Name

```bash
az rest --method get \
  --url "https://graph.microsoft.com/v1.0/users?\$filter=startswith(displayName,'Bryan Voris')&\$select=displayName,mail,jobTitle,department,officeLocation" \
  --output json
```

### Department Members

```bash
az rest --method get \
  --url "https://graph.microsoft.com/v1.0/users?\$filter=department eq 'New Model Develop XEV DE'&\$select=displayName,mail,jobTitle" \
  --output json
```

### Known Departments

| Department | Filter Value |
|---|---|
| US DE | `New Model Develop XEV DE` |
| JP DE | Use Japanese name — search by person instead |
| Quality Assurance | `Quality Assurance` |
| Procurement | `Procurement` |
| Manufacturing Engineering | `Manufacturing Engineering` |
| Product Engineering | `PE` |

### Manager Lookup

```bash
az rest --method get \
  --url "https://graph.microsoft.com/v1.0/users/{user-id}/manager?\$select=displayName,mail,jobTitle" \
  --output json
```

### Direct Reports

```bash
az rest --method get \
  --url "https://graph.microsoft.com/v1.0/users/{user-id}/directReports?\$select=displayName,mail,jobTitle" \
  --output json
```

### Groups / Distribution Lists

```bash
az rest --method get \
  --url "https://graph.microsoft.com/v1.0/groups?\$filter=startswith(displayName,'FH4S')&\$select=displayName,mail,description" \
  --output json
```

## Key Contacts (Verified via Graph)

| Name | Title | Department | Email |
|---|---|---|---|
| Duvvuru, Sabarish | Senior Engineer | New Model Develop XEV DE | sabarish.duvvuru.qx@astemo.com |
| Voris, Bryan | Sr. Department Head | DE | bryan.voris.rm@astemo.com |
| Butler, Kevin | Senior Manager | New Model Develop XEV DE | kevin.butler.eg@astemo.com |
| Saitoh, Ken | Lead Engineer | New Model Develop XEV DE | ken.saitoh.ns@astemo.com |
| Kothakonda, Vinay | Senior Engineer | New Model Develop XEV DE | vinay.kothakonda.ay@astemo.com |
| Shaw, Anil | Senior Engineer | New Model Develop XEV DE | anil.shaw.bc@astemo.com |
| Hosoya, Haruyasu | Staff | xEVBU/x生技本 | haruyasu.hosoya.jn@astemo.com |

## DE Team (Full)

```
Butler, Kevin       | Senior Manager   | kevin.butler.eg@astemo.com
Duvvuru, Sabarish   | Senior Engineer  | sabarish.duvvuru.qx@astemo.com
Kothakonda, Vinay    | Senior Engineer  | vinay.kothakonda.ay@astemo.com
Saitoh, Ken          | Lead Engineer    | ken.saitoh.ns@astemo.com
Shaw, Anil           | Senior Engineer  | anil.shaw.bc@astemo.com
```

## Limitations

- **No Mail/Calendar/Teams access** — tenant blocks those Graph scopes
- **Directory only** — can read users, groups, org structure
- **Manager/directReports** may be incomplete (depends on tenant configuration)
- **Photo available** — `/users/{id}/photo/$value` returns JPEG

## Integration with Other Skills

- `astemo-colleague-attunement` — enrich colleague profiles with directory data
- `shadow-sidecast` — team mapping from live org chart
- `astemo-meeting-prep` — attendee lookup before meetings
- `fh4s-context` — program team resolution from department + email patterns
