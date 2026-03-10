---
name: run-js-node
description: Execute JavaScript in the connected Node.js process
---

# /run-js-node

Execute JavaScript in the connected Node.js process.

## Usage

```
/run-js-node <script>
```

## Description

Runs JavaScript in the connected Node.js process. Script runs as an async IIFE; use `return` for the value you want as output; async/await is supported. Access `process`, `require()`, globals. Use `PLATFORM=node` and connect first.

## Examples

```
/run-js-node process.memoryUsage()
/run-js-node require('os').cpus().length
```

## How to run

Run JavaScript in the connected Node process via the **execute** tool on the Node platform (no `page` binding; use `callTool` for other tools). See [execute](./execute.md).
