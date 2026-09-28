# Review C# changes

## Establish the comparison

Identify whether the review covers a pull request, branch, staged changes, or working-tree changes. Choose the corresponding Git baseline.
Use the requested baseline, or the repository's review convention. State the choice when it is not explicit.
Do not assume the current checkout, index, and pull-request head contain the same source.
For a staged-only review with additional unstaged edits, use an isolated snapshot of the index for semantic analysis.
Preserve the original working tree. If that snapshot cannot be loaded, disclose the limit and review the staged diff directly.

Verify that Glider has loaded the worktree containing the after-state. Refresh external changes before requesting semantic evidence.
Keep the reviewed source stable, or identify which evidence needs a fresh check after another edit.

## Map the diff to symbols

Read the ordinary Git diff first. Use `changed_symbols` to map changed C# line ranges to their containing declarations.
The tool does not fetch Git history or choose a baseline.

- Supply `hunks` with before/after paths and changed ranges from the selected comparison.
- Supply full prior text in `beforeFiles` for every file with a before-side, including modifications, renames, and pure deletions.
- Match each prior file path to its hunk's `beforePath`; deleted files are absent from the loaded after-state.
- A pure addition needs no prior file text. A pure deletion has no after-side path or range.
- Preserve both paths for a rename. Use the schema's one-based, inclusive changed-line ranges.

Keep the before text, diff, and loaded after-state consistent. Follow result pagination and inspect unresolved or partial results.

## Follow impact and behavior

Use returned current symbol keys with `analyze_change_impact` where available. Inspect relevant callers, implementations, overrides, and tests.
Use `get_cascade_impact` when a bounded transitive view materially helps the review.
Discover fresh keys when the current workspace cannot resolve a returned key. Never invent a replacement key from a name.

A body-only change can alter behavior without changing a signature. Review conditions, exceptions, state changes, and observable effects.
For signature changes, inspect affected call sites and contracts rather than relying only on errors in the changed declaration.

Keep the ordinary diff in the review. Deleted declarations may lack current keys, and project/configuration changes need separate inspection.
State semantic coverage limits instead of treating an empty symbol result as approval of the whole diff.

## Inspect dependencies and cleanup

For changed package or project references, read [dependencies.md](dependencies.md). Use package definitions and usage evidence to assess the change.
For unused code, `find_unused_symbols` identifies candidates with no non-self references. Check activation and external use before recommending deletion.
Keep formatting suggestions separate from correctness findings. Preview `organize_usings` or `format_document` only when relevant; preserve read-only scope.

## Validate and report

Check diagnostics at the scope affected by the change. Use relevant behavioral tests and required builds where appropriate.
A read-only review does not authorize edits, publication, or a merge. If execution is unavailable, state the missing evidence.
Report actionable findings with the affected location, concrete consequence, and supporting evidence. Separate confirmed defects from hypotheses.
