---
name: Teams Rich Compose
description: Use when composing or editing Microsoft Teams messages via browser automation, especially when line breaks collapse, CKEditor formatting misbehaves, or send approval/verification is required.
icon: icon.svg
metadata:
  category: automation/browser
  family: automation
  lifecycle: active
  canonical_slug: teams-rich-compose
  icon_style: craft-category-glyph-v1
---

# Teams Rich Compose

## Overview

Teams web uses a CKEditor-backed composer. Naive `type`, `fill`, direct `innerHTML`, or normal paste can collapse paragraphs into text like `Ryoko-san,For...`. Use the rich composer plus a synthetic paste event into a clean draft, then verify the DOM before sending.

## Hard Rule

Never send a Teams message without explicit user approval immediately before sending. Draft, verify formatting, and only click Send after the user says to send/do it.

## Reliable Workflow

1. Open the target Teams chat/group and confirm the title/participants.
2. Click the message box.
3. If rich formatting is hidden, click **Show Formatting options**.
4. If any bad draft exists, use the composer trash/delete button and confirm **Discard**.
5. Confirm the composer is clean:

```js
(() => {
  const el = document.querySelector('[contenteditable="true"][role="textbox"]');
  return { text: el?.innerText, html: el?.innerHTML };
})()
```

Expected clean state is approximately `text: "\n"` and placeholder HTML.

6. Insert formatted content using a synthetic paste event with `text/html` and `text/plain` into the focused clean composer:

```js
(() => {
  const el = document.querySelector('[contenteditable="true"][role="textbox"]');
  el.focus();

  const paragraphs = [
    'Hayakawa-san,',
    '',
    'Body paragraph one.',
    '',
    'Body paragraph two.',
    '',
    'Thank you.'
  ];

  const escape = s => s
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;');

  const html = paragraphs
    .map(p => p ? `<p>${escape(p)}</p>` : '<p><br></p>')
    .join('');
  const text = paragraphs.join('\n');

  const dt = new DataTransfer();
  dt.setData('text/html', html);
  dt.setData('text/plain', text);

  el.dispatchEvent(new ClipboardEvent('paste', {
    bubbles: true,
    cancelable: true,
    clipboardData: dt,
  }));

  return { initialText: el.innerText, initialHtml: el.innerHTML };
})()
```

7. Re-check after the async paste applies:

```js
(() => {
  const el = document.querySelector('[contenteditable="true"][role="textbox"]');
  const text = el.innerText;
  return {
    len: text.length,
    starts: text.slice(0, 150),
    tail: text.slice(-150),
    greetingBreak: text.startsWith('Hayakawa-san,\n'),
    hasCollapsed: /-san,\S/.test(text),
    html: el.innerHTML.slice(0, 1200),
  };
})()
```

8. Verify all are true/false as intended:
   - `greetingBreak: true`
   - `hasCollapsed: false`
   - no trailing test characters
   - body text present
   - composer value looks like paragraphs in `innerHTML`
9. Ask user for final approval if not already explicit.
10. Click Send only after approval and verification.
11. Verify send by checking the composer is empty and the sent text appears in chat.

## Common Mistakes

| Mistake | Symptom | Fix |
|---|---|---|
| Using `type` with `\n` | Newlines collapse: `Ryoko-san,For...` | Use rich composer + synthetic paste event |
| Using `fill` | Appends/duplicates or collapses text | Discard draft; use paste event |
| Direct `innerHTML` assignment | TrustedHTML blocked | Create paste `DataTransfer`; let CKEditor ingest it |
| Not clearing failed tests | Stray `ABAB`/`Y` remains | Use trash/delete + **Discard**, verify clean composer |
| Sending after a failed verify | Bad message reaches chat | Stop, discard, retry; never click Send until checks pass |

## Deleting Bad Drafts

If a malformed draft exists:

1. Click the composer trash/delete button.
2. If dialog appears, click **Discard**.
3. Verify clean composer before inserting new content.

Do not rely on `Cmd+A` or `Backspace`; Teams may not select CKEditor content correctly.


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

