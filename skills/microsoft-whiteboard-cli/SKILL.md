---
name: 'Microsoft Whiteboard CLI'
description: Use when creating, reverse-engineering, or operating a Microsoft Whiteboard command-line workflow, especially m365-wb, m365-wb-web-cli, WhiteboardAdmin PowerShell exports, whiteboard.microsoft.com web automation, Windows/WSL setup, or adapting Excalidraw-style scenes for Microsoft Whiteboard.
metadata:
  category: automation/browser
  family: automation
  lifecycle: active
  canonical_slug: microsoft-whiteboard-cli
  icon_style: craft-category-glyph-v1
---

# Microsoft Whiteboard CLI

## Core stance

Microsoft Whiteboard has a documented admin/export CLI surface, not a documented public canvas-object editing API. Treat `m365-wb` as an adapter:

- official operations through WhiteboardAdmin PowerShell
- web entry through `https://whiteboard.microsoft.com/`
- editable design source through Excalidraw-style JSON
- Whiteboard import through SVG/HTML packs or browser automation when necessary

## Use this workflow

1. Check official docs if the requested operation touches admin/export behavior.
2. Use `m365-wb docs` to show the reversed command surface.
3. Use `m365-wb ps ...` for Windows/WSL PowerShell commands.
4. Use `m365-wb scene ...` for Excalidraw-to-Whiteboard import packs.
5. Use `m365-wb-web-cli web ...` for whiteboard.microsoft.com browser automation gaps.

## Official command surface

Primary module: `WhiteboardAdmin`.

Common cmdlets:

| Cmdlet | Use |
|---|---|
| `Export-WhiteboardHtml` | Export boards to HTML using Graph-backed flow. |
| `Invoke-WhiteboardHtmlRetry` | Retry failed exports. |
| `Get-Whiteboard` / `Get-WhiteboardsForTenant` | Query Azure-backed boards. |
| `Set-WhiteboardOwner` / `Invoke-TransferAllWhiteboards` | Transfer ownership. |
| `Remove-Whiteboard` / `Restore-Whiteboard` | Delete/restore mapping. |
| `Get-WhiteboardSettings` / `Set-WhiteboardSettings` | Inspect/update settings. |

Docs:

- `https://learn.microsoft.com/en-us/powershell/whiteboard/overview?view=whiteboard-ps`
- `https://learn.microsoft.com/en-us/powershell/module/whiteboardadmin/?view=whiteboard-ps`
- `https://learn.microsoft.com/en-us/powershell/module/whiteboardadmin/export-whiteboardhtml?view=whiteboard-ps`

## Windows/WSL commands

```bash
cd tools/m365-wb
npm install
npm link
m365-wb --help
m365-wb-web-cli --help
m365-wb ps install --wsl
m365-wb ps export --mode User --environment AzureCloud --output-path 'C:\Exports'
m365-wb-web-cli web plan
m365-wb-web-cli web open --browser msedge
m365-wb-web-cli web auth-status --browser msedge --json
m365-wb-web-cli web reverse --browser msedge -o ./reverse-capture
m365-wb-web-cli web analyze ./reverse-capture/snapshot.json -o ./reverse-capture/selectors.json
m365-wb-web-cli web new-board --browser msedge
m365-wb-web-cli web inspect --browser msedge --json
```

Use `--run` only when the operator explicitly wants to execute through `powershell.exe`.

## Excalidraw adaptation

Prefer Excalidraw JSON as the source of truth for generated boards:

```bash
m365-wb scene svg board.excalidraw -o board.svg
m365-wb scene pack board.excalidraw -o ./whiteboard-pack --title 'Workshop board'
```

Then insert/drag `whiteboard-import.svg` into Microsoft Whiteboard web, or try:

```bash
m365-wb-web-cli web import-svg ./whiteboard-pack/whiteboard-import.svg --browser msedge
```

## Guardrails

- Do not claim direct canvas editing unless browser automation was actually used and verified.
- Do not request Global Administrator unless admin export/tenant operations require it; prefer least privilege.
- Do not embed tenant secrets, tokens, or hidden relay implementation details in generated packs.
- For work-laptop handoff, save only work-facing setup instructions or a relay-safe packet.


## Pipeline

```
Intent → Resolve target app/tab → Execute browser action → Extract result → Return structured output
```

1. Identify target (URL, app, tab, element)
2. Connect to browser via Playwright/browser_tool or AppleScript
3. Execute action (navigate, extract, interact, screenshot)
4. Return structured output

## Modes

| Mode | Output | When |
|------|--------|------|
| `extract` (default) | Data from browser | Read operations |
| `interact` | Perform browser action | Click, fill, automate |
| `debug` | DOM/state inspection | Troubleshooting browser apps |

## Fallback Chain

1. browser_tool (Craft Agents built-in)
2. Playwright MCP tools
3. AppleScript Chrome/Safari control
4. chrome-web-cli

## Prerequisites

- Browser with authenticated session
- Playwright MCP or browser_tool available

## Contract

- **Never enter credentials** — assume session exists
- **Never submit forms** without operator approval
- **Respect existing tabs** — do not close operator tabs without permission

