---
name: glider-csharp
description: Use GliderMCP to navigate, edit, diagnose, refactor, and review C#/.NET code with semantic tools and the correct loaded workspace.
---

# Glider C# workflow

Use this skill for C#/.NET work when Glider is connected. Follow the installed server's instructions and tool schemas for supported arguments.
If a tool is unavailable, state the limitation and choose an available alternative. This skill does not install or configure servers.

## Verify the workspace

A project configuration can preload a solution with `--solution`. The portable plugin leaves the solution choice to the agent.
Call `server_status` before the first Glider operation in a task. Compare `solutionPath` and `solutionRoot` with the intended checkout.
A loaded workspace can belong to another worktree. Changing the shell directory does not change Glider's workspace.

- If nothing is loaded, call `load` with the relevant absolute `.sln`, `.slnx`, or `.csproj` path.
- After a worktree switch, load that worktree's solution or project if status still identifies the previous workspace.
- Verify the resulting status before semantic reads or edits. `reload` refreshes the current workspace; it does not select another worktree.
- If the intended workspace is already loading, wait for completion. A load of the same path can join the active operation.
- For a large solution, load the relevant project when its scope is sufficient. Widen the workspace when cross-project evidence is needed.

Repeat the status check after a server restart or suspected root mismatch. Do not add a status call before every tool call.
Relative scope paths use `solutionRoot`, which can differ from the repository root and watched directory. Prefer absolute paths when uncertain.
The file tools authorize paths under the loaded solution or project directory. Referenced project membership does not expand this boundary.
For a referenced file outside that root, load the containing `.sln` or `.slnx` whose directory contains the file, then retry.
The watched directory does not authorize file access. A successful read does not establish full-solution compiler health.

Agents connected to the same server share its loaded workspace. Coordinate root changes with other users, or use separate server instances.
A separate Git worktree alone does not isolate the server.

## Discover and read code

Select a path that fits the task. Read only the reference that applies.

| Task | Minimum useful path |
| --- | --- |
| Known file and source range | Read that bounded range directly. Discover a key only when the operation needs identity. |
| Unknown location or symbol | Start with `find_code` and inspect the selected match. Use `search_text` for literal text or regex. |
| Inheritance or call chain | Discover the exact symbol, then use hierarchy, references, or caller tools. |
| Contract refactor | Read [editing.md](references/editing.md). Inspect impact, preview the refactor, and validate affected callers. |
| External package API | Read [dependencies.md](references/dependencies.md). Inspect the selected assembly through `view_external_definition`. |
| Dependency audit | Read [dependencies.md](references/dependencies.md). Check usage, assets, graph coverage, and non-source requirements. |
| Diff review | Read [review.md](references/review.md). Select the baseline, inspect the diff, and assess relevant symbol impact. |

The consolidated discovery contract is unreleased. Use it only when the installed schema advertises `get_source` and `find_code.matchMode`.
Otherwise, follow [the released-contract fallback](references/discovery.md#released-contract-fallback). Do not infer support from this skill alone.

With the consolidated contract, `find_code` accepts `symbol` (default) and `fileOutline`.
The default `matchMode=closest` selects the first nonempty exact, prefix, or substring tier after scope and filters.
Use `matchMode=all` for broad exploration. Namespace, accessibility, kind, scope, sort, and page controls remain available.
Use direct relationship tools after selecting a key. Use `get_structure` for a known file and `search_text` for text or regex.
Read [discovery and source](references/discovery.md) for migration, partial declarations, source bounds, and exact-edit conditions.
For file patterns or files outside the loaded workspace, use Scout according to repository policy.

Choose the next operation from the task, not from a fixed checklist:

| Needed evidence | Next operation |
| --- | --- |
| Source declaration | With the consolidated contract, pass the selected key unchanged to `get_source`. Use the fallback reference for earlier servers. |
| Nearby source context | When a source path exists, read `filePath` with a bounded range around `lineNumber` through `get_file_contents`. |
| Declaration details or additional locations | Use `get_symbol_info` with the selected key. |
| External definition | Use `view_external_definition`; source tools require source declarations. |
| Interface or abstract implementations | Use `find_implementations` with the selected key. |
| Overrides of a virtual or abstract member | Use `find_overrides` with the member key. |
| Call sites or type dependencies | Use `find_callers`, or `get_type_dependencies` with a source type key. |

Stop discovery when the location, cause, intended change, and validation are sufficient.
Expand only to resolve a concrete uncertainty. A local body fix does not require a complete caller audit.
A contract or cross-project change requires the relevant callers, implementations, external definitions, and configuration coverage.

Pass returned `symbolKey` values unchanged to compatible tools. Keys are opaque, not permanent names.
Discover new keys after a worktree, signature, or project change, or when a tool reports a stale key.
Use returned file paths rather than guessing a filename from a type name.
Use an outline when it helps locate a declaration. A known range needs no redundant outline.
Avoid full-file text plus full-member expansion when one member or a returned key answers the task.
Use ordinary bounded source reads for exploration. Request exact raw text only for an impending guarded edit.

Use `search_text` for literal text in loaded documents. Check coverage and unreadable-document counts before interpreting zero matches as absence.
Follow returned pagination and partial-result guidance. A successful empty result differs from a failed or incomplete query.

For unreferenced files, other languages, or repository-wide text, use Scout's `find` when available as a separate server.
Use an appropriate language server for semantic work in another language. Scoped literal inspection can answer a text question. It cannot establish symbol identity, resolved references, or semantic absence.
Follow repository tool rules for shell inspection.
Shell commands remain appropriate for Git history, solution discovery, builds, and tests.

## Edit and validate

Read [editing.md](references/editing.md) before C# edits or refactors. It maps changes to tools and explains previews, refresh, and diagnostic scope.

Use Glider's incremental diagnostics during the edit loop. Avoid a full build after every source edit unless the user or repository requires it.
After a coherent change, check diagnostics at the affected project or solution scope and run relevant behavioral tests.
Use builds for build-specific behavior, generated artifacts, packaging, and required final validation.
Clean diagnostics do not establish correct behavior or a successful build.

## Review changes

Read [review.md](references/review.md) for code reviews. Combine the Git diff with `changed_symbols`, impact analysis, and relevant tests.
Choose the comparison baseline explicitly. Review deleted code, configuration, and behavior even when semantic results contain no current symbol.
For package APIs, dependency cleanup, or project architecture, read [dependencies.md](references/dependencies.md).

## Recover and report

Check `success`, application state, coverage, and recovery guidance before continuing.
If an edit applied but synchronization failed, refresh the workspace and verify the result. Do not repeat the write blindly.
For incomplete results, follow the suggested scope or pagination instead of claiming complete coverage.
For an ambiguous file outline, retain the path and select a project context. In `find_code`, use `scope.mode=project` and `scope.projectName`.
Development builds after 12.4.8 provide `candidateProjectNames` and retry arguments. Earlier builds name the contexts in the error.

Before reporting a suspected product defect, verify the root and retry when practical. State version, platform, expected behavior, and observed limits.
Search public issues for the same product and symptom. For an open issue lacking reproduction, contribute new sanitized evidence as a comment.
Use an authorized GitHub client, or give the user the issue URL and a draft comment. `send_feedback` cannot select that issue.
Use `send_feedback` for a distinct report and follow its installed privacy and preview rules.
Never publish private source or raw logs. Show the complete synthetic example and obtain user approval before publication.
