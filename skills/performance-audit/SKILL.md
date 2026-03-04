---
name: performance-audit
description: Analyze web and backend performance using Web Vitals, network timing, and metrics. Use when the user asks about page performance, load times, Core Web Vitals (LCP, CLS, INP), slow pages, or SEO performance factors.
---

# Performance Audit Skill

Use Browser DevTools MCP observability (o11y) and content tools to analyze performance.

## When to Use

- User asks about page performance or speed
- User wants to optimize load times or diagnose slow pages
- User needs Core Web Vitals metrics (LCP, CLS, INP, etc.)
- User asks about SEO performance factors

## Guidance

1. Use Web Vitals tools to get LCP, CLS, INP and related metrics (optionally with wait time or debug info).
2. Use HTTP request tools to inspect network timing, resource types, and status codes.
3. Use content tools (e.g. screenshot) to capture above-the-fold or full-page when needed.
4. Use run/JS tools to query `performance` API (e.g. longtask, resource entries) when relevant.
