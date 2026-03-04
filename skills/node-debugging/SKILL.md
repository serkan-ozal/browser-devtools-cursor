---
name: node-debugging
description: Debug Node.js backend processes with non-blocking inspection. Use when the user needs to connect to Node.js processes (by PID, name, Docker, or inspector port), set tracepoints/logpoints/exceptionpoints, capture call stacks and local variables, run JavaScript in the process context, or inspect backend logs. Use the Node DevTools MCP tools.
---

# Node Debugging Skill

Use the **Node DevTools MCP** tools to debug Node.js backend processes without breakpoints: connect via Inspector Protocol, then use tracepoints, logpoints, exceptionpoints, and watch expressions.

## When to Use

- User wants to debug a Node.js server or backend process
- User needs to connect to a process by PID, process name, Docker container, or inspector port
- User wants tracepoints, logpoints, or exception monitoring without pausing execution
- User needs to capture call stacks, local variables, or run JavaScript in the process context
- User asks about backend errors, API handler bugs, or server-side debugging

## Guidance

1. **Connect first** – Use the Node DevTools MCP connect tool (by PID, process name, container, or `--inspector-port`). If the process isn’t running with `--inspect`, the server can attach via SIGUSR1 (no code change).
2. **Probes** – Put tracepoints (capture snapshot at line), logpoints (log expression), exceptionpoints (uncaught/all), and watch expressions. List/remove/clear as needed.
3. **Snapshots** – Get probe snapshots (tracepoint/logpoint/exception hits) to inspect call stacks and locals.
4. **Run** – Execute JavaScript in the connected Node process context when needed.

The plugin runs the **node-devtools** MCP server via the same `browser-devtools-mcp` package with `PLATFORM=node`.
