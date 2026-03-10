---
name: execute
description: Batch tool calls via JavaScript (no slash command; use from agent/skill or MCP)
---

# execute

Batch-execute multiple tool calls in a single request via custom JavaScript. No slash command — use the MCP tool `execute` from an agent/skill, or CLI `run execute`, or daemon POST `/call`. Code runs as the body only (no `async function` wrapper); use `await` and `return` directly.

## MCP tool

- **Tool name:** `execute`
- **Input:** `code` (required), `timeoutMs` (optional, default 30000, max 120000)

## VM bindings (inside your code)

### `callTool(name, input, returnOutput?)`

Call any registered MCP tool from inside execute. **Must be used with `await`** (returns a Promise).

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `name` | string | Yes | Tool name (e.g. `'navigation_go-to'`, `'a11y_take-aria-snapshot'`, `'content_take-screenshot'`). |
| `input` | object | Yes | Tool input (e.g. `{ url: 'https://example.com' }`, `{}`). |
| `returnOutput` | boolean | No | Default `false`. When `true`, the tool output is also added to the response `toolOutputs` array (and still returned from `callTool` for in-code use). |

- **Returns:** the tool’s output (e.g. `const out = await callTool('a11y_take-aria-snapshot', {});`).
- **Throws** on failure; execution stops; response includes `error` and `failedTool` (name + error).
- Max **50** `callTool` calls per execution.

Example:

```javascript
await callTool('navigation_go-to', { url: 'https://example.com' });
const snapshot = await callTool('a11y_take-aria-snapshot', { interactiveOnly: true });
await callTool('content_take-screenshot', { annotate: true }, true);  // true = include in response toolOutputs
```

### `page` (browser platform only)

Playwright **Page** instance. Available only on the **browser** platform (not Node). Use for direct Playwright API or to run script in the page.

- **`page.evaluate(pageFunction)`** — run JavaScript in the browser context; `return` for result.
- **`page.goto(url)`**, **`page.locator(selector)`**, **`page.click()`**, **`page.fill()`**, etc. — standard Playwright API.
- **`page.title()`** — page title (async).

All `page` methods that are async must be called with **`await`**.

Example:

```javascript
const title = await page.title();
const n = await page.evaluate(() => document.querySelectorAll('a').length);
```

## Response

- `toolOutputs`: outputs where `callTool(..., true)` was used.
- `logs`: console.log/warn/error.
- `result`: return value of the code (JSON-safe).
- On error: `error`, and optionally `failedTool` (name + error).

## See also

- [Node execute](../node/execute.md) — same tool on Node platform (no `page`; `callTool` only).
- CHECK_PLAN.md — execute row (code, timeoutMs; CLI / daemon).
- Browser reference — execute has no slash; use from agent/skill or CLI `run execute` or daemon POST `/call`.
