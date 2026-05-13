# Outlook DOM Recipes

Use only when browser automation is needed. Prefer Craft browser for agent work. Do not click Send.

## Find visible draft fields

Use accessible labels first:

- To: `[contenteditable=true][aria-label="To"]`
- Cc: `[contenteditable=true][aria-label="Cc"]`
- Subject: `input[aria-label="Subject"]`
- Body: `[role="textbox"][aria-label="Message body"]`

## Insert body above signature

Never overwrite blindly. Insert above existing signature and remove any previous agent draft.

```js
const body = [...document.querySelectorAll('[role="textbox"]')]
  .find(e => e.getAttribute('aria-label') === 'Message body');

const wrapper = document.createElement('div');
wrapper.setAttribute('data-agent-draft', 'true');

for (const line of draftText.split('\n')) {
  const div = document.createElement('div');
  div.style.fontFamily = 'Aptos, Aptos_EmbeddedFont, Aptos_MSFontService, Calibri, Helvetica, sans-serif';
  div.style.fontSize = '12pt';
  div.style.color = 'rgb(0, 0, 0)';
  if (line === '') div.appendChild(document.createElement('br'));
  else div.textContent = line;
  wrapper.appendChild(div);
}

const old = body.querySelector('[data-agent-draft="true"]');
if (old) old.remove();
body.insertBefore(wrapper, body.firstChild);
body.dispatchEvent(new InputEvent('input', {bubbles:true, inputType:'insertText', data:draftText}));
```

## Better compact HTML insertion

If paragraph spacing becomes too large, use compact margins:

```js
const p = document.createElement('p');
p.style.margin = '0 0 8px 0';
p.style.fontFamily = 'Aptos, Calibri, Helvetica, sans-serif';
p.style.fontSize = '12pt';
p.textContent = paragraph;
```

## Verify no duplicate sign-off

Read body text and check:

```js
const text = body.innerText;
const manualClose = /(^|\n)(Thanks|Regards|Best regards),?\s*\n\s*Sabarish\b/im.test(text);
const signaturePresent = /Regards[\s\S]*Sabarish Duvvuru|Sabarish Duvvuru \|/i.test(text);
```

If both are true, remove manual close from the inserted body.

## Verify send not clicked

It is OK for Send to be visible. Do not click it.

```js
const sendVisible = [...document.querySelectorAll('button,[role="button"]')]
  .some(e => /^Send$/i.test((e.innerText || e.getAttribute('aria-label') || '').trim()));
```

## Avoid these failures

- Do not use a deeplink compose for replies. It creates a new email and wrong signature.
- Do not use `document.execCommand('selectAll')` in the body unless you intend to delete the signature.
- Do not insert raw HTML with uncontrolled `<div><br></div>` runs.
- Do not rely on Sent Items row preview as the only verification when a draft is still open.
- Do not shrink the user’s browser window for automation.
