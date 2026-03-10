---
name: browse
description: Navigate to a URL and interact with web pages using Browser DevTools MCP
---

# /browse

Navigate to a URL and interact with web pages using Browser DevTools MCP.

## Usage

```
/browse <url>
```

## Description

Opens a browser and navigates to the specified URL. **`navigation_go-to` returns an ARIA snapshot with refs (e1, e2, …) by default** — use the response refs directly for interactions; only call `a11y_take-aria-snapshot` if the page changed (e.g. SPA update) or you need a fresh snapshot. After navigation:
- Use refs from the go-to response for click, fill, hover (`interaction_click`, `interaction_fill`, etc.)
- Or take a new ARIA snapshot (`a11y_take-aria-snapshot`) if the DOM changed
- Screenshots (`content_take-screenshot`) — use `annotate: true` for labeled overlay
- Page content (`content_get-as-html`, `content_get-as-text`)
- Console and network (`o11y_get-console-messages`, `o11y_get-http-requests`)

## Examples

```
/browse https://example.com
/browse https://www.npmjs.com/package/browser-devtools-mcp
```

## MCP Tools Used

- `navigation_go-to` — Navigate to the URL; returns ARIA snapshot and refs by default (`includeSnapshot: true`). Optional `includeScreenshot`, `snapshotOptions`.
