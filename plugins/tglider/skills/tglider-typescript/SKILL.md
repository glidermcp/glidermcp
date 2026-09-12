---
name: tglider-typescript
description: Use TGlider for TypeScript and JavaScript semantic navigation, references, diagnostics, dependency topology, impact analysis, and refactoring.
---

# TGlider TypeScript Workflow

Use TGlider when working in a TypeScript or JavaScript repository and the task needs semantic facts rather than plain text search.

Start by loading the relevant workspace when needed. Prefer semantic symbol, reference, export, diagnostic, hierarchy, call graph, impact, and dependency tools before reading source text. Use source reads only for bounded context after the semantic tools identify the relevant files or symbols.

For edits, gather evidence first with tools for references, callers, outgoing calls, diagnostics, dependency topology, and change impact. Prefer preview-first refactoring tools where available, and refresh diagnostics after meaningful edits.

TGlider has no text-search tool and does not require Scout. For repository-wide text search, use Scout's `find` when connected. Otherwise, use shell text search.
