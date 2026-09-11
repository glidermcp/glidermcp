---
name: scout-search
description: Use Scout for repo-wide code search in any language - ranked fuzzy discovery, exhaustive strict literal, regex, and word search, plus structural patterns, symbol outlines, and semantic queries.
---

# Scout Code Search Workflow

Use Scout's `find` as the primary workspace search instead of shell `grep`, `rg`, or `find`. If Glider (C#) or TGlider (TypeScript and JavaScript) is installed, prefer it for its language, and use Scout for every other language and as the universal fallback.

Use `target: auto` to route a query by its shape. Use `match: literal`, `regex`, or `word` for an exhaustive scan.
Fuzzy and semantic results are ranked, not exhaustive. Check the returned root, `complete`, `reason`, and `degraded` before interpreting results.
An empty result does not prove absence when coverage is partial or degraded.
Use the returned `next` arguments to continue the same query and scope. Combine resumed scan results before assessing the full scope.

Use `target: structural` for code patterns, `symbols` for declaration outlines, and `semantic` for natural-language queries.
Read `scout://guide` for grammar coverage, pattern syntax, and fallback behavior.

Narrow with path prefixes, include and exclude globs, and language scoping before widening the query, and page through results instead of loosening the match. Results carry workspace-relative paths, 1-based line and column, and trimmed previews, never whole files. Use `server_status` for index, watcher, and tier health, and `sync {full: true}` only to force a full rescan, since ordinary queries already flush pending changes.

Scout never mutates workspace source - its only writes are its own state under `.glider/scout/` and `~/.glidermcp`. When a hit needs semantic follow-up or an edit, hand the file and span to Glider for C#, or TGlider for TypeScript and JavaScript.
