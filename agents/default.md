---
name: default
description: Default Browser DevTools MCP agent — ARIA/snapshot-first workflow, execute for run JS and batch, ref-based interaction
capabilities:
  - ARIA and AX snapshots before screenshot
  - Snapshot-first before any click/fill/hover
  - Execute for JavaScript and batch tool calls
  - Ref-based and selector-based interaction
---

# Browser DevTools MCP — Default Agent

When using Browser DevTools MCP, follow these rules so that page structure, interactions, and batch execution are done correctly.

---

## *** CRITICAL — TO UNDERSTAND PAGE STRUCTURE (do not default to screenshot) ***

Use ARIA snapshot first, then AX tree if needed. Use screenshot only when necessary.

After navigating to a page, your **FIRST** inspection call MUST be `a11y_take-aria-snapshot` (or `a11y_take-ax-tree-snapshot`), **NOT** `content_take-screenshot`.

**DO NOT** take a screenshot just to "see the page" or understand what is on it.

1. Call **`a11y_take-aria-snapshot`** FIRST for structure, roles, and semantics.
2. If you need layout, bounding boxes, or occlusion: call **`a11y_take-ax-tree-snapshot`**.
3. Call **`content_take-screenshot`** ONLY when you need to verify how something looks visually (e.g. design check, visual bug, or when ARIA/AX are insufficient). Do not use screenshot to understand page structure.

---

## *** CRITICAL — BEFORE ANY ELEMENT INTERACTION ***  
(click | fill | hover | select | drag)

**DO NOT GUESS SELECTORS FROM SCREENSHOTS.**

Take a snapshot first to get real selectors and refs:

- Call **`a11y_take-aria-snapshot`**, **`a11y_take-ax-tree-snapshot`**, or **`content_get-as-html`** (e.g. selector `"form"`) to see actual ids, roles, and markup.
- **`a11y_take-aria-snapshot`** returns a tree with refs (e1, e2, …) and stores them in context. In click, fill, hover, select, drag, scroll use the selector as:
  - a **ref** (`e1`, `@e1`, `ref=e1`),
  - a **Playwright-style expression** (e.g. `getByRole('button', { name: 'Login' })`, `getByLabel('Email')`, `getByText('Register')`, `getByPlaceholder('…')`, `getByTitle('…')`, `getByAltText('…')`, `getByTestId('…')`), or
  - a **CSS selector**.
  Refs are valid until the next snapshot or navigation; re-snapshot after page/DOM changes.
- Then use the returned selectors or refs for interaction. Guessing (e.g. `input[type="email"]`) often fails because real markup may use `type="text"`, wrappers, or different attributes.

**SNAPSHOT FIRST, THEN INTERACT.**

Before inspection: use **`sync_wait-for-network-idle`** for SPA/async content; use **`interaction_click`** with `waitForNavigation: true` when the click navigates to a new page.

---

## *** CRITICAL — PLAN THEN BATCH-EXECUTE ***

Design your workflow as small, focused plans:

1. **NAVIGATE** — go to a page; the response includes an ARIA snapshot with refs.
2. **PLAN** — read the snapshot to understand the page structure, then decide the next steps (e.g. fill fields, click buttons, take a snapshot).
3. **BATCH** — use the **`execute`** tool to run all planned steps in a **SINGLE** call via `callTool()`.

This is the **PREFERRED** way to perform multi-step interactions. Instead of making separate tool calls for each fill/click/select, batch them together:

```javascript
// execute tool code (e.g. form submit then new page):
await callTool('interaction_fill', { selector: 'e3', value: 'user@test.com' });
await callTool('interaction_fill', { selector: 'e5', value: 'secret123' });
await callTool('interaction_click', { selector: 'e7', waitForNavigation: true });
await callTool('a11y_take-aria-snapshot', {}, true);
await callTool('content_take-screenshot', {}, true);
```

**Benefits:** fewer round-trips, lower token usage, faster execution.

Use individual tool calls only when you need to inspect the result before deciding the next step.

---

## *** CRITICAL — JAVASCRIPT EXECUTION AND BATCH TOOL CALLS ***

There are **no separate run-JS tools**. Use the **`execute`** tool only.

- **Run JavaScript in the browser page:** use **`execute`** with `page.evaluate()`. Example: `code: "return await page.evaluate(() => document.title);"`. You have access to `page` (Playwright Page) and `callTool(name, input, returnOutput?)` inside the execute VM.
- **Run JavaScript on the server (Node VM, no DOM):** use **`execute`** without `page.evaluate()` — e.g. `code: "return Buffer.from('hello').toString('base64');"`. Same tool; built-ins and `callTool` available.
- **Node platform (backend debugging):** use **`execute`** with `callTool` only (no `page`). For run-JS-node flows, see `commands/node/execute.md`.

**Always use `await` with `callTool()`**; max 50 calls per execution. See `commands/browser/execute.md` and `commands/node/execute.md` for the full VM API.
