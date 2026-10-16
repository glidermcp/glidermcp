# Review C# changes

## Establish the comparison

Identify whether the review covers a pull request, branch, staged changes, or working-tree changes. Choose the corresponding Git baseline.
Use the requested baseline, or the repository's review convention. State the choice when it is not explicit.
Do not assume the current checkout, index, and pull-request head contain the same source.
For a staged-only review, analyze the index after-state rather than additional unstaged edits.
Use the Git selector below when supported. Otherwise, load an isolated index snapshot or disclose the semantic limit.

Verify that Glider has loaded the worktree containing the after-state. Refresh external changes before requesting semantic evidence.
Keep the reviewed source stable, or identify which evidence needs a fresh check after another edit.

## Map the diff to symbols

Read the ordinary Git diff. Use `changed_symbols` when declaration identity or classification helps answer a review question.

**UNRELEASED:** Use the `git` selector only when the installed schema advertises it.
Released Glider 13.1.1 requires explicit hunks and prior text, as described below.

| Comparison | Selector |
| --- | --- |
| Branch since its merge base | `{"git":{"mode":"branch","baseRef":"main"}}` |
| Two exact revisions | `{"git":{"mode":"direct","baseRef":"HEAD~1","headRef":"HEAD"}}` |
| Staged and unstaged changes from HEAD | `{"git":{"mode":"worktree","baseRef":"HEAD"}}` |
| Index changes from HEAD | `{"git":{"mode":"staged"}}` |
| Disk changes from the index | `{"git":{"mode":"unstaged"}}` |

Use separate single revisions in `baseRef` and `headRef`. Git range strings are rejected.
Branch mode uses the merge base; direct mode compares the exact revisions. The default `headRef` is HEAD.
Worktree and unstaged modes include untracked files by default; set `git.includeUntracked=false` to exclude them.
Do not combine `git` with `hunks` or `beforeFiles`.

Inspect `comparison` for resolved commits, before/after sources, file hashes, snapshot identity, and boundaries.
The tool analyzes a separate after snapshot. `sourceMatchesWorkspace` describes source correspondence only.
`afterMatchesWorkspace` describes full source and project metadata correspondence. False means that full correspondence is unproved; it does not prove stale source.
For example, `sourceMatchesWorkspace=true`, `projectMetadataCorrespondence="unknown"`, and `afterMatchesWorkspace=false` describe matching source with unverified project metadata.
`projectMetadataCorrespondence` remains unknown because evaluated imports lack a verified fingerprint. Matching source cannot establish matching project metadata.
Git mode therefore omits live symbol keys and impact suggestions. It cannot classify body-only versus signature changes.
Added or removed declarations and declaration spans still provide structural evidence. Inspect `symbolIdentityScope`, compatibility limits, and project changes directly.
These limits apply to Git mode; the explicit-hunk contract remains unchanged.
For later pages, retain the selector and pass `comparison.snapshotId` as `git.snapshotId` with the next `skip`.
Restart pagination after a snapshot mismatch. Inspect skipped files and completeness even when the symbol page is empty.

### Explicit hunks and released fallback

For Glider 13.1.1, prepare the selected Git comparison yourself. The hunk mode remains available in newer builds.

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

Stop diff investigation when the task questions and required coverage are satisfied. Expand only for a concrete unresolved risk.
This stopping rule does not replace required final builds, tests, or diagnostics.

## Validate and report

Check diagnostics at the scope affected by the change. Use relevant behavioral tests and required builds where appropriate.
A read-only review does not authorize edits, publication, or a merge. If execution is unavailable, state the missing evidence.
Report actionable findings with the affected location, concrete consequence, and supporting evidence. Separate confirmed defects from hypotheses.
