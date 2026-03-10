---
name: execute
description: Batch tool calls via JavaScript (no slash command; use from agent/skill or MCP)
---

# execute

Batch-execute multiple tool calls in a single request via custom JavaScript. No slash command — use the MCP tool `execute` from an agent/skill, or CLI `run execute`, or daemon POST `/call`. Code runs as the body only (no `async function` wrapper); use `await` and `return` directly.

**Node platform:** Same tool as on browser; on Node there is no `page` binding — use `callTool` only to invoke other Node tools (e.g. `debug_connect`, `debug_get-probe-snapshots`).

## MCP tool

- **Tool name:** `execute`
- **Input:** `code` (required), `timeoutMs` (optional, default 30000, max 120000)

## VM bindings (inside your code, Node platform)

### `callTool(name, input, returnOutput?)`

Call any registered MCP tool from inside execute. **Must be used with `await`** (returns a Promise).

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `name` | string | Yes | Tool name (e.g. `'debug_connect'`, `'debug_get-probe-snapshots'`, `'debug_get-logs'`). |
| `input` | object | Yes | Tool input (e.g. `{ pid: 12345 }`, `{ types: ['tracepoint'] }`). |
| `returnOutput` | boolean | No | Default `false`. When `true`, the tool output is also added to the response `toolOutputs` array (and still returned from `callTool` for in-code use). |

- **Returns:** the tool’s output.
- **Throws** on failure; execution stops; response includes `error` and `failedTool` (name + error).
- Max **50** `callTool` calls per execution.

Example:

```javascript
await callTool('debug_connect', { pid: 12345 });
const snapshots = await callTool('debug_get-probe-snapshots', { types: ['tracepoint'] }, true);
```

### Node platform: no `page`

On the **Node** platform the VM does **not** receive `page`. Use `callTool` to run other tools. To run JavaScript inside the connected Node process you would need a tool that evaluates in the process; the execute VM itself runs on the MCP server.

## Response

- `toolOutputs`: outputs where `callTool(..., true)` was used.
- `logs`: console.log/warn/error.
- `result`: return value of the code (JSON-safe).
- On error: `error`, and optionally `failedTool` (name + error).

## See also

- [Browser execute](../../commands/browser/execute.md) — same tool; browser has `page` (Playwright) in addition to `callTool`.
- CHECK_PLAN.md — execute row (code, timeoutMs; CLI / daemon).
- Node reference — execute has no slash; use from agent/skill or CLI `run execute` or daemon POST `/call`.
