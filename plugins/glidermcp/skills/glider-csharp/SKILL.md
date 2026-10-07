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

Agents connected to the same server share its loaded workspace. Coordinate root changes with other users, or use separate server instances.
A separate Git worktree alone does not isolate the server.

## Native license status in development builds

Unreleased builds with native sandbox activation add a sanitized `license` object to the existing status tool.
Check installed schemas and CLI help before using this behavior. Native deployment and released availability remain open.
Sandbox `valid` describes the grant; `commercialRights` remains false. Account login alone establishes no production commercial rights.
Status reads local state without a network request. Never request or publish credentials, complete grants, or profile contents.

Glider and GliderTrace share one profile for the OS user. Login and logout affect both products that select that profile.
Use browser login only when the user requests activation. MCP startup never opens a browser or requests input.
A valid grant works offline through its paid period. Renewal cannot extend that period without a new verified grant.
Follow `action` for recovery. If `revocationPending` is true, repeat logout after service and credential access recover.

The credential vault is the default. Current macOS app signatures can prevent access from the other product.
Windows and Linux native vault checks remain open. File storage requires the user's explicit selection during login.
Do not silently change the credential store or weaken OS security settings.

## Discover and read code

Start with `find_code` for navigation. Select the intent that fits the question, such as symbols, references, callers, or file outlines.
Use `search_symbols`, `resolve_symbol`, or `get_symbol_at_position` when direct discovery is useful.

Pass returned `symbolKey` values unchanged to compatible tools. Keys are opaque, not permanent names.
Discover new keys after a worktree, signature, or project change, or when a tool reports a stale key.
Use returned file paths rather than guessing a filename from a type name. Inspect an outline before requesting large source ranges.

Use `search_text` for literal text in loaded documents. Check coverage and unreadable-document counts before interpreting zero matches as absence.
Follow returned pagination and partial-result guidance. A successful empty result differs from a failed or incomplete query.

For unreferenced files, other languages, or repository-wide text, use Scout's `find` when available as a separate server.
Use an appropriate language server for semantic work in another language. Use scoped shell inspection when available tools cannot answer.
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

Before reporting a suspected product defect, verify the root and retry when practical. State version, platform, expected behavior, and observed limits.
Search public issues for the same product and symptom. For an open issue lacking reproduction, contribute new sanitized evidence as a comment.
Use an authorized GitHub client, or give the user the issue URL and a draft comment. `send_feedback` cannot select that issue.
Use `send_feedback` for a distinct report and follow its installed privacy and preview rules.
Never publish private source or raw logs. Show the complete synthetic example and obtain user approval before publication.
