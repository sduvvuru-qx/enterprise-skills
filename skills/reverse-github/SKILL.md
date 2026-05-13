---
name: Reverse GitHub
description: 'Use when the user needs: Audit, map, and manage all GitHub accounts — repos, orgs, SSH keys, tokens, activity, cross-account inventory. Use when checking GitHub identity, finding repos across accounts, managing multi-account setups, or auditing GitHub presence.'
icon: icon.svg
user_invocable: true
metadata:
  bashPattern: gh auth|gh api|github|git remote
  priority: 60
  category: dev/github
  family: dev
  lifecycle: active
  canonical_slug: reverse-github
  icon_style: craft-category-glyph-v1
---

# Reverse GitHub — Multi-Account Audit & Management

Map all your GitHub accounts, repos, orgs, keys, and activity across identities.

## When to Use

- "check my GitHub", "which account am I on", "find my repos"
- "audit GitHub accounts", "reverse engineer my GitHub"
- Managing multiple GitHub identities
- Finding repos scattered across accounts
- Checking SSH keys, tokens, or org memberships

## Known Accounts (Shadow Lab Fleet)

| Account | Email | Role |
|---------|-------|------|
| theleostark | theleostark@outlook.com | Primary personal |
| ishuru | — | Most active (84 repos) |
| Andystark | — | Alt personal |
| sdluffy | sabarish.duvvuru@gmail.com | SHADOW/shadow-lab |
| s4b7-ai | — | AI-specific |
| clawdai | clawdai.tltg@gmail.com | OpenClaw AI |
| clawdctrl | clawd.ctrl@gmail.com | OpenClaw Ctrl |

## Procedure

### 1. Current Auth Status

```bash
gh auth status
gh api user --jq '{login, email: .email, name: .name, repos: .public_repos, created: .created_at}'
```

### 2. Full Account Audit

For each known account, query the public API:

```bash
for acct in theleostark ishuru Andystark sdluffy s4b7-ai clawdai clawdctrl; do
  echo "=== $acct ==="
  gh api "users/$acct" --jq '{
    login, name, email: .email, bio,
    repos: .public_repos, followers: .followers, following: .following,
    created: .created_at[:10], updated: .updated_at[:10],
    blog: .blog, company: .company, location: .location
  }' 2>/dev/null || echo "NOT FOUND"
done
```

### 3. Repos Per Account

```bash
# For the active account:
gh repo list --limit 100 --json nameWithOwner,isPrivate,pushedAt,primaryLanguage \
  --jq '.[] | "\(.nameWithOwner) [\(if .isPrivate then "private" else "public" end)] \(.primaryLanguage.name // "—") pushed \(.pushedAt[:10])"'

# For other accounts (public repos only):
gh api "users/ACCOUNT/repos?per_page=100&sort=pushed" \
  --jq '.[] | "\(.full_name) [\(if .private then "private" else "public" end)] \(.language // "—") pushed \(.pushed_at[:10])"'
```

### 4. SSH Keys Audit

```bash
# List SSH keys on active account:
gh ssh-key list

# Check which keys are on each account:
for acct in theleostark ishuru Andystark sdluffy s4b7-ai clawdai clawdctrl; do
  echo "=== $acct ==="
  gh api "users/$acct/keys" --jq '.[].key' 2>/dev/null | head -3
done
```

### 5. Org Memberships

```bash
gh api user/orgs --jq '.[].login'
```

### 6. Token Scopes

```bash
gh auth status 2>&1 | grep "Token scopes"
```

### 7. Switch Active Account

```bash
# List configured accounts:
gh auth status

# Switch:
gh auth switch --user ACCOUNT_NAME

# Add new account:
gh auth login
```

### 8. Cross-Account Repo Search

Find a repo that might be on any account:

```bash
for acct in theleostark ishuru Andystark sdluffy s4b7-ai clawdai clawdctrl; do
  result=$(gh api "repos/$acct/REPO_NAME" --jq '.full_name' 2>/dev/null)
  [ -n "$result" ] && echo "Found: $result on $acct"
done
```

### 9. Activity Timeline

```bash
# Recent push events for active account:
gh api "users/$(gh api user --jq .login)/events?per_page=20" \
  --jq '.[] | select(.type == "PushEvent") | "\(.created_at[:10]) \(.repo.name) — \(.payload.commits[0].message // "no message")"'
```

### 10. Consolidation Recommendations

After auditing, suggest:
- Which accounts to archive (0 repos, no activity)
- Which repos to transfer to the primary account
- Which accounts should be added to a Shadow Lab org
- SSH key cleanup (remove duplicates, expired keys)

## Shadow Lab GitHub Strategy

Recommended consolidation:
- **shadowlab-cc** (org) — all Shadow Lab product repos
- **theleostark** (personal) — primary identity, personal projects
- **ishuru** (keep) — 84 repos, too much to migrate
- Others — evaluate for archival or migration


## Pipeline

```
Intent → Parse input → Execute API/tool → Structure output → Return
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Formatted result | Normal use |
| `json` | Raw JSON | Programmatic use |

## Artifact Routing

- Output in chat — no persistent artifacts unless operator requests

## Contract

- Never expose API tokens in output
- Respect rate limits

