---
name: Linear OAuth App Setup
description: Use when creating a Linear OAuth application to get client_id and client_secret for programmatic access, or when a service needs to authenticate as a Linear app rather than using a personal API key.
icon: icon.svg
metadata:
  category: system/security
  family: system
  lifecycle: active
  canonical_slug: linear-oauth-app-setup
  icon_style: craft-category-glyph-v1
---

# Linear OAuth App Setup

## Overview

Linear OAuth apps allow services to authenticate as an application rather than a personal user. This enables scoped permissions, webhook signatures, and the ability to act on behalf of users via OAuth flow. Use when a personal API key (`lin_api_...`) would couple the integration to an individual account.

## When to Use OAuth App vs Personal Key

| Scenario | Use |
|---|---|
| Automation for your own account only | Personal API key (`lin_api_...`) |
| Service acting as application (not you) | OAuth app (`client_id` + `client_secret`) |
| Webhook signature verification | OAuth app (webhooks use app secret) |
| Multi-tenant (many users authorize your app) | OAuth app with full OAuth flow |

## Create an OAuth Application

1. Go to **Linear Settings → API → OAuth applications**
2. Click **Create new application**
3. Fill in:
   - **Name**: e.g., `Shadow Automation`
   - **Description**: internal description
   - **Redirect URIs**: `http://localhost:3000/callback` (or your real callback)
   - **Scopes**: select what the app needs (read, write, issues, etc.)
4. Copy `client_id` and `client_secret` — **secret is shown once**

## Machine-to-Machine (No User OAuth Flow)

For server automations that don't need user delegation, generate a personal API key and store it separately. OAuth app credentials (`client_id` / `client_secret`) alone don't grant API access — you still need either:

1. An access token from the OAuth flow, OR
2. A personal API key from a Linear account

The `client_id`/`client_secret` pair is used to:
- Sign webhook payloads (verify with `client_secret`)
- Exchange auth code for access token during OAuth flow

## OAuth Flow (Authorization Code)

```bash
# Step 1: Redirect user to Linear authorization URL
# https://linear.app/oauth/authorize?client_id=CLIENT_ID&redirect_uri=REDIRECT_URI&response_type=code&scope=read,write

# Step 2: Exchange code for token
curl -X POST https://api.linear.app/oauth/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=$CLIENT_ID" \
  -d "client_secret=$CLIENT_SECRET" \
  -d "redirect_uri=$REDIRECT_URI" \
  -d "code=$AUTH_CODE" \
  -d "grant_type=authorization_code"
# Returns: { "access_token": "...", "token_type": "Bearer", "expires_in": ... }

# Step 3: Use access token
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ viewer { id name } }"}'
```

## Webhook Signature Verification

Linear signs webhook payloads with the app's `client_secret`:

```python
import hmac, hashlib

def verify_webhook(payload_bytes: bytes, signature_header: str, client_secret: str) -> bool:
    expected = hmac.new(
        client_secret.encode(),
        payload_bytes,
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(expected, signature_header)
```

## Workspace Scope Note

OAuth apps are created **per workspace** (team). If you create an app in the "Starknet" workspace, it can only authorize users from that workspace. Cross-workspace apps require separate registrations.

## Common Mistakes

| Mistake | Fix |
|---|---|
| Using `client_id`/`client_secret` directly as API auth | These don't authenticate API calls; need access token or personal key |
| Creating app in wrong workspace | Apps are workspace-scoped; check you're in the right org |
| Losing `client_secret` | It's shown once — store immediately in secrets manager |
| Not adding all scopes upfront | Changing scopes later requires users to re-authorize |
| Personal API key vs app key confusion | `lin_api_...` = personal key; OAuth app uses Bearer token from flow |


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

