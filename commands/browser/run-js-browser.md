---
name: run-js-browser
description: Execute JavaScript code in the browser
---

# /run-js

Execute JavaScript code in the browser.

## Usage

```
/run-js <code>
```

## Description

Executes JavaScript code in the browser context. Has access to the page's DOM, window object, and all page APIs.

## Arguments

- `code` (required): JavaScript code to execute

## Examples

```
/run-js document.title
/run-js window.location.href
/run-js document.querySelectorAll('a').length
/run-js localStorage.getItem('token')
/run-js JSON.stringify(window.performance.timing)
```

## Advanced Examples

Get all form data:
```
/run-js Object.fromEntries(new FormData(document.querySelector('form')))
```

Check for specific element:
```
/run-js !!document.querySelector('.error-message')
```

## Security Note

Code runs with full page privileges. Be careful with untrusted inputs.

## How to run

Use the **execute** tool with `page.evaluate()` to run JavaScript in the browser. Example: `execute` with `code: "return await page.evaluate(() => document.title);"`. See [execute](./execute.md) for VM API (`page`, `callTool`).

## Server-side JS (Node VM)

For code that runs on the MCP server (Buffer, crypto, callTool, no DOM), use **execute** without `page.evaluate()` — e.g. `code: "return Buffer.from('hello').toString('base64');"`. Same tool; no separate /sandbox command.
