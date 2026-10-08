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
| Unknown location or symbol | Use `find_code`, `search_symbols`, or `resolve_symbol`, then inspect the selected match. |
| Inheritance or call chain | Discover the exact symbol, then use hierarchy, references, or caller tools. |
| Contract refactor | Read [editing.md](references/editing.md). Inspect impact, preview the refactor, and validate affected callers. |
| External package API | Read [dependencies.md](references/dependencies.md). Inspect the selected assembly through `view_external_definition`. |
| Dependency audit | Read [dependencies.md](references/dependencies.md). Check usage, assets, graph coverage, and non-source requirements. |
| Diff review | Read [review.md](references/review.md). Select the baseline, inspect the diff, and assess relevant symbol impact. |

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
