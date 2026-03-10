# Browser DevTools – Cursor Plugin

Cursor plugin that adds **Browser DevTools MCP** and **Node DevTools MCP** to your workspace: Playwright-powered browser automation and Node.js backend debugging via the Model Context Protocol. Uses the same MCP server as the [Browser DevTools MCP VS Code / Cursor extension](https://github.com/serkan-ozal/browser-devtools-mcp-vscode-extension); targets **browser-devtools-mcp** 0.3.x.

## What it does

### Browser (browser-devtools MCP)

- **Navigation** – Go to URL, back/forward (`navigation_go-back-or-forward`), reload (responses include ARIA snapshot with refs by default)
- **Execute** – Batch multiple tool calls in one request via the **execute** tool: use JavaScript and `callTool(name, input, returnOutput?)`. Inside the script, **`page`** (Playwright Page) is available on the browser platform for `page.evaluate()`, `page.locator()`, etc. Reduces round-trips and token usage.
- **Content** – Screenshots (with optional ref annotations), PDF, HTML/text extraction
- **Interaction** – Click, fill, hover, scroll, drag, keyboard (use refs from ARIA snapshot or getByRole/getByLabel/etc.)
- **Accessibility** – ARIA snapshot first for structure and refs; AX tree for layout and occlusion
- **Observability** – Web Vitals, console messages, HTTP requests, trace IDs
- **Stub** – Intercept and mock HTTP responses
- **React** – Inspect React components and elements
- **Figma** – Compare page with Figma design
- **Debug** – Tracepoints, logpoints, exception monitoring in browser

### Node.js backend (node-devtools MCP)

- **Connect** – Attach to Node process by PID, process name, Docker container, inspector port, or WebSocket URL (**debug_connect**, **debug_disconnect**)
- **Debug** – Tracepoints, logpoints, exceptionpoints, watch expressions (non-blocking); list/remove/clear probes
- **Snapshots** – Capture call stacks and local variables on probe hit; get/clear snapshots; **debug_status**, **debug_get-logs** for state and console output
- **Source maps** – **debug_resolve-source-location** to resolve bundle locations to original source

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

## Plugin contents

The plugin bundles **rules**, **skills**, **agents**, and **commands** that guide the AI when using Browser DevTools and Node DevTools MCP.

### Rules

Persistent guidance (Cursor Settings → Rules); some apply always, others when relevant or when invoked manually:

- **browser-automation** – Use browser-devtools MCP for automation; ARIA snapshot first, snapshot before interaction, strongly prefer **execute** for multi-step flows.
- **use-execute-for-multi-step** – Prefer the **execute** tool to batch 2–3+ tool calls in one request (reduces round-trips and token usage).
- **prefer-devtools-mcp** – Use MCP tools instead of CLI when the plugin is installed.
- **node-debugging** – Use node-devtools MCP for Node.js backend debugging (connect, tracepoints, logpoints, snapshots, etc.).

### Skills

Specialized capabilities (Agent Decides or `/skill-name` in chat):

- **browser-testing** – End-to-end and interaction testing with the browser.
- **execute-workflows** – Run JavaScript in browser/Node and batch MCP tool calls via the **execute** tool.
- **node-debugging** – Attach to Node, set tracepoints/logpoints/exceptionpoints, capture snapshots.
- **web-debugging** – Inspect console, network, and use tracepoints/logpoints in the browser.
- **observability** – Web Vitals, console messages, HTTP requests, trace IDs.
- **performance-audit** – Core Web Vitals and performance analysis.
- **visual-testing** – Screenshots and visual verification.

### Agents

Custom agent configurations for focused workflows:

- **default** – ARIA/snapshot-first workflow, execute for batch and run-JS, ref-based interaction.
- **scraper** – Extract structured data from web pages (navigation, dynamic content, pagination).
- **accessibility-auditor** – Audit pages for accessibility (ARIA, AX, compliance).
- **performance-analyzer** – Measure and analyze web performance (Core Web Vitals, recommendations).
- **design-qa** – Compare implementations with Figma designs.
- **qa-tester** – Test flows and interactions on the page.

### Commands

Agent-executable commands (browser and Node):

- **Browser:** `browse`, `click`, `fill`, `hover`, `scroll`, `screenshot`, `execute`, `accessibility`, `back`, `forward`, `reload`, `wait`, `console`, `network`, `webvitals`, `html`, `text`, `pdf`, `mock`, `intercept`, `react`, `figma`, `trace`, `resize`, `keypress`, `drag`, `select`, `run-js-browser`, `otel`, and related.
- **Node:** `connect`, `disconnect`, `tracepoint`, `logpoint`, `exceptionpoint`, `snapshots`, `status`, `logs`, `execute`, `run-js-node`, and related.

### Optional configuration

Add `env` entries under each server in `.mcp.json` to match the options supported by the [VS Code / Cursor extension](https://github.com/serkan-ozal/browser-devtools-mcp-vscode-extension) (same MCP server, extension provides a settings UI).

**Common (both platforms):**
- `PLATFORM` – `browser` or `node` (already set per server above)
- `TOOL_OUTPUT_SCHEMA_DISABLE` – `true` to omit tool output schemas (can reduce token usage)
- `AVAILABLE_TOOL_DOMAINS` – Comma-separated domains (e.g. `navigation,interaction,a11y`). Empty = all. Browser: a11y, content, debug, figma, interaction, navigation, o11y, react, run, stub, sync. Node: debug, run.

**Browser:** `BROWSER_HEADLESS_ENABLE`, `BROWSER_PERSISTENT_ENABLE`, `BROWSER_PERSISTENT_USER_DATA_DIR`, `BROWSER_USE_INSTALLED_ON_SYSTEM`, `BROWSER_EXECUTABLE_PATH`, `BROWSER_LOCALE`, `BROWSER_CONSOLE_MESSAGES_BUFFER_SIZE`, `BROWSER_HTTP_REQUESTS_BUFFER_SIZE`

**Node:** `NODE_INSPECTOR_HOST` (e.g. `host.docker.internal` when MCP runs in Docker), `NODE_CONSOLE_MESSAGES_BUFFER_SIZE`

**OpenTelemetry:** `OTEL_ENABLE`, `OTEL_SERVICE_NAME`, `OTEL_SERVICE_VERSION`, `OTEL_ASSETS_DIR`, `OTEL_INSTRUMENTATION_USER_INTERACTION_EVENTS`, `OTEL_EXPORTER_TYPE`, `OTEL_EXPORTER_HTTP_URL`, `OTEL_EXPORTER_HTTP_HEADERS`

**AWS / Bedrock:** `AWS_REGION`, `AWS_PROFILE`, `AMAZON_BEDROCK_ENABLE`, `AMAZON_BEDROCK_IMAGE_EMBED_MODEL_ID`, `AMAZON_BEDROCK_TEXT_EMBED_MODEL_ID`, `AMAZON_BEDROCK_VISION_MODEL_ID`

**Figma:** `FIGMA_ACCESS_TOKEN`, `FIGMA_API_BASE_URL`

For a settings UI and automatic env mapping, use the **Browser DevTools MCP** extension in VS Code or Cursor (same repo as this plugin’s “VS Code / Cursor extension” link below).

## Related

- **This plugin (repo):** [browser-devtools-cursor](https://github.com/serkan-ozal/browser-devtools-cursor)
- **MCP server (npm):** [browser-devtools-mcp](https://www.npmjs.com/package/browser-devtools-mcp)
- **VS Code / Cursor extension:** [browser-devtools-mcp-vscode-extension](https://github.com/serkan-ozal/browser-devtools-mcp-vscode-extension)
- **Docs:** [browser-devtools.com](https://www.browser-devtools.com)

## License

MIT
