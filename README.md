# Browser DevTools – Cursor Plugin

Cursor plugin that adds **Browser DevTools MCP** and **Node DevTools MCP** to your workspace: Playwright-powered browser automation and Node.js backend debugging via the Model Context Protocol.

## What it does

### Browser (browser-devtools MCP)

- **Navigation** – Go to URL, back, forward, reload
- **Content** – Screenshots, PDF, HTML/text extraction
- **Interaction** – Click, fill, hover, scroll, drag, keyboard
- **Accessibility** – ARIA snapshot, AX tree snapshot
- **Observability** – Web Vitals, console messages, HTTP requests, trace IDs
- **Stub** – Intercept and mock HTTP responses
- **Run** – Execute JavaScript in browser or sandbox
- **React** – Inspect React components and elements
- **Figma** – Compare page with Figma design
- **Debug** – Tracepoints, logpoints, exception monitoring in browser

### Node.js backend (node-devtools MCP)

- **Connect** – Attach to Node process by PID, process name, Docker container, or inspector port
- **Debug** – Tracepoints, logpoints, exceptionpoints, watch expressions (non-blocking)
- **Snapshots** – Capture call stacks and local variables on probe hit
- **Run** – Execute JavaScript in the connected Node process context

## Requirements

- [Node.js](https://nodejs.org/) (v18+ recommended; used by Cursor to run `npx browser-devtools-mcp`)
- [Cursor](https://cursor.com) with plugin support

## Installation

### From Cursor Marketplace

1. Open Cursor → **Settings** → **Plugins** (or [Cursor Marketplace](https://cursor.com/marketplace)).
2. Search for **Browser DevTools** and install.

### Local / development

1. Clone this repo (or add it as a submodule) into a directory Cursor can load plugins from, or use Cursor’s “Install from disk” / local plugin workflow.
2. Ensure the folder contains `.cursor-plugin/plugin.json` and `.mcp.json` at the repo root.

After installation, two MCP servers are available (same npm package, different platform): **browser-devtools** (default) and **node-devtools** (`PLATFORM=node`). Cursor starts them via `npx` when needed; no separate install required. Ask the AI to e.g. “navigate to …”, “take a screenshot”, or “debug this Node server” to use the tools.

## Related

- **This plugin (repo):** [browser-devtools-cursor](https://github.com/serkan-ozal/browser-devtools-cursor)
- **MCP server (npm):** [browser-devtools-mcp](https://www.npmjs.com/package/browser-devtools-mcp)
- **VS Code / Cursor extension:** [browser-devtools-mcp-vscode-extension](https://github.com/serkan-ozal/browser-devtools-mcp-vscode-extension)
- **Docs:** [browser-devtools.com](https://www.browser-devtools.com)

## License

MIT
