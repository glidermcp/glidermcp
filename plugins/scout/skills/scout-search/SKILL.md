---
name: scout-search
description: Use Scout to locate files, text, declarations, code shapes, and behavior across a repository. Choose search modes and verify scope, coverage, and ranked results.
---

# Scout search workflow

Use this skill when Scout is connected and the task needs repository discovery or search.
Follow the installed tool schemas and `scout://guide`. Read the relevant reference below instead of loading every reference.

## Verify the workspace

Check `server_status` before the first investigation. Compare its root information with the intended checkout, and inspect index and tier health.
Scout indexes its startup root automatically. A normal session does not need `load` first.
Changing the shell directory does not change the server's workspace.

Use the installed root-selection or `load` contract when the intended checkout differs. Verify the resulting scope before searching.
Root-selection capabilities differ across versions; do not invent root arguments absent from the installed schema.
Coordinate root changes when other agents share the server. A Git worktree does not isolate a shared server.

Check the root returned by searches. Repeat status after a restart, worktree switch, or suspected scope mismatch, not before every call.
Queries normally flush pending file changes. Use `sync {"full":true}` only when a full rescan is justified.
If the watcher reports a stall, follow its recovery guidance before trusting search freshness.

## Choose a search mode

| Need | Target and match | Use the result for |
| --- | --- | --- |
| A file or approximate path | `files`, usually `fuzzy` | Candidate paths; use a strict match for exhaustive path matching. |
| A known string, API, or error | `content` with `literal`, `word`, or `regex` | Occurrences within the searched scope. |
| Approximate text when exact spelling is unknown | `content` with `fuzzy` | Ranked candidates, followed by exact searches. |
| A declaration name or outline | `symbols` | Syntax declarations; native matching uses name substrings. |
| A code shape | `structural` | Syntax matches, checked against actual source. |
| A behavior whose implementation terms are unknown | `semantic` | Ranked starting points for further investigation. |
| A quick exploratory query | `auto` | The route Scout selected; inspect it before interpreting matches. |

For an audit or a comparison between modes, select the target explicitly.
With `target: auto`, strict `match` values route to content, even when the query looks like a filename.
Read [search-modes.md](references/search-modes.md) for match modes, scope, routing, and structural examples.
Read [semantic-search.md](references/semantic-search.md) when semantic results are weak or model configuration is relevant.

Start with exact search when identifiers are known. Use semantic discovery when the task supplies behavior rather than names.
Use structural search when syntax distinguishes the cases, such as empty catch bodies or declaration modifiers.
Do not rank these modes by timings from one repository. Parse work, scope, cache state, and index readiness affect latency.

## Establish what the search covered

Inspect `complete`, `reason`, `degraded`, coverage fields, and `next`. Use `responseDetail: "full"` when more diagnostics are needed.
Strict matches can cover the eligible scope completely. Ignore rules, unreadable files, scope filters, and scan limits still constrain that scope.
Grammar coverage also constrains structural search and declaration outlines.

`complete` describes scan coverage, not the last result page. A ranked result remains non-exhaustive even after all returned pages.
`reason: ranked` is expected for fuzzy and semantic search; it does not indicate a failed scan.
A fallback result answers the fallback query, not necessarily the original structural or semantic question.

- Apply returned `next` arguments to the same query and scope. Replace `skip` with `continuationToken`, or vice versa.
- Combine results from all resumed scan legs. A completed final leg covers its remaining suffix.
- If offset pages change `pageIdentity`, discard the accumulated pages and restart without `skip`.
- If a saved fuzzy token expires, restart the query. Its saved ranking does not incorporate later edits.

Report absence only for the exact predicate and scope that a complete search covered.
A complete regex search for known retry APIs does not prove that no differently implemented retry mechanism exists.

## Read and review

Read the surrounding source before accepting a hit. Search previews are trimmed and can omit conditions or enclosing declarations.
Use returned paths and positions rather than guessing files from names.

A matching identifier or syntax node does not establish a resolved reference.
Use the installed tool schemas and guide to identify available symbol and reference capabilities.
Distinguish syntax matches from resolved relationships in the returned results. Do not assume planned capabilities are available.
Use scoped source inspection when the available tools cannot answer the question.

For reviews, use `changed_symbols` to map diff ranges to syntax declarations when appropriate.
Supply both sides' complete file text and the relevant changed range, following the installed schema.
Treat its output as structural context. It does not provide stable symbol identities, compiler impact, or proof of behavior preservation.
Review the diff itself, including deletions and files without grammar support.

Scout does not edit workspace source. Root loads and indexing can write Scout state; semantic setup can download model files.
Do not enable semantic indexing, replace models, or change ignore rules merely to make a query return more results.
Before public feedback, check scope, version, degradation, and reproducibility. Follow the installed feedback instructions and obtain publication authorization.
