---
name: tglider-typescript
description: Use TGlider for TypeScript and JavaScript semantic navigation, references, diagnostics, dependency topology, impact analysis, and refactoring.
---

# TGlider TypeScript Workflow

Use TGlider when working in a TypeScript or JavaScript repository and the task needs semantic facts rather than plain text search.

Check `server_status` against the intended checkout before semantic work. Wait for an active preload, or load the relevant workspace when needed.
Start with `find_code` for symbol discovery. Prefer semantic symbol, reference, export, diagnostic, hierarchy, call graph, impact, and dependency tools before reading source text. Use source reads only for bounded context after the semantic tools identify the relevant files or symbols.

For edits, gather evidence first with tools for references, callers, outgoing calls, diagnostics, dependency topology, and change impact. Prefer preview-first refactoring tools where available, and refresh diagnostics after meaningful edits.

TGlider has no text-search tool and does not require Scout. For repository-wide text search, use Scout's `find` when connected. Otherwise, use shell text search.

Read [workspace.md](references/workspace.md) for startup, HTTP, CLI options, and load recovery.
