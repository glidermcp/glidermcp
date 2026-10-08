# C# edits and validation

## Choose the tool

Use the narrowest supported operation that expresses the intended change. Inspect the installed schema before constructing arguments.

| Intended change | Preferred tool | Preparation |
| --- | --- | --- |
| Rename a symbol and its references | `rename_symbol` | Discover the symbol and inspect change impact. |
| Move a type or member | `move_type` / `move_member` | Inspect impact, supported declaration forms, and reference updates. |
| Add a type or member | `add_type` / `add_member` | Identify the owning project or container. |
| Replace one member declaration | `replace_member` | Obtain its key and exact current declaration text. |
| Change or delete an exact source range | `replace_range` | Read raw source and preserve the expected text exactly. |
| Create or rewrite a C# file | `write_file` | Read existing content when applicable and verify the destination. |
| Remove unnecessary usings and sort the remainder | `organize_usings` | Select the document and project context. Preview the diff. |
| Normalize a C# document's layout | `format_document` | Preview document-wide changes before applying them. |

Glider's C# write tools do not replace file tools for project files or other languages.
For a move, inspect references the tool cannot update. Do not assume every reference form or partial declaration is supported.

## Preview, apply, and inspect

1. Inspect the code and tests that explain the change. Inspect callers for a contract change.
   Use `analyze_change_impact` before a rename or move. Expand a local fix only for a concrete uncertainty.
2. Set `applyChanges: false` when supported and inspect the proposed diff. Do not assume writes default to preview.
3. Apply the intended change after the preview is acceptable. A preview does not reserve the source against concurrent edits.
4. Inspect whether the operation applied, whether the workspace updated, and which diagnostics the tool checked.

For `replace_member`, obtain `replacementSpans` from `get_symbol_info`, then read the exact declaration with `get_file_contents`.
Exclude outer trivia from `expectedText` and `memberCode`. Select the declaration file when a partial member has several locations.
Request raw source only for the exact window of an impending guarded operation.
For `replace_range`, preserve `rawContent` whitespace and terminators in `expectedText`.
Development builds after 12.4.8 support `get_file_contents` with `contentMode: "raw"`. This mode returns one exact source representation.
Use it only when the installed schema advertises `contentMode`. Earlier versions require `includeRawContent: true` and return both representations.
The default and legacy `includeRawContent` behavior remain compatible. A null raw field requires a smaller window before an edit.
After a line-limit cut, use only the returned range. A truncated character window supplies no exact raw text.
Range lines and UTF-16 columns are one-based, and the end position is exclusive. Do not count Unicode characters as UTF-16 units.

After an expected-text conflict, read the current source and construct a fresh edit. Do not remove the conflict check to force a write.
If `applied: true` accompanies a synchronization failure, repair synchronization and inspect the source before any further edit.
Likewise, an omitted response does not mean a completed write should be repeated.

## Clean up edited documents

Use `organize_usings` after an edit or refactor when imports need cleanup. It removes unnecessary usings and sorts the remainder.
Use `format_document` when the edited document needs formatting. Formatting and import cleanup are separate operations.
Preview each with `applyChanges: false`, inspect the diff, then apply the accepted operation.
Keep cleanup within the task scope. Document-wide formatting can include unrelated lines; do not expand a small fix into repository-wide cleanup.
For linked files, select the relevant project context and check other affected configurations before accepting import removal.
Respect repository style rules and inspect the result rather than assuming a formatter establishes every style requirement.
A read-only review can recommend cleanup or inspect a preview; it does not authorize applying changes.

## Refresh the loaded workspace

Glider edit tools update the workspace as part of their operation. Inspect their update result instead of relying only on the watcher.
`write_file` can report that a new file remains outside the loaded project after reload. A disk write alone does not establish inclusion.

| Change outside Glider | Refresh action |
| --- | --- |
| Content of existing C# files | Watcher-enabled semantic reads usually ingest pending changes. Use `sync` when watching is disabled or explicit ingestion is needed. |
| Added, removed, or renamed files; project or reference changes | Use `reload` when an explicit structural refresh is needed. `sync` preserves the project graph. |
| Different worktree or solution | Verify status, then `load` the intended absolute solution/project path. |

## Interpret diagnostics

Automatic checks differ by tool. Read the returned scope and completeness before declaring the change clean.

- `add_member` and `add_type` check the new code within their supported project and target-framework coverage.
- `replace_member` checks the replacement across linked targets. It does not automatically repair or validate every caller.
- `replace_range` checks the edited documents across linked targets. Existing errors can block a guarded edit.
- `write_file` reports workspace update state. Request diagnostics rather than assuming a whole-solution compiler check.

Use `failOnErrors: true` where supported when a guarded edit is appropriate. Its exact scope follows that tool's schema.
An incomplete check is not a clean check. Do not silently disable the guard because existing errors or unavailable targets blocked it.

After a coherent change, use `get_diagnostics` for the affected project or solution. Compare against existing errors when necessary.
For broad cleanup, `diagnostic_hotspots` helps select the next area before inspecting individual diagnostics.

Run relevant tests to check behavior. Run required build, test, and package commands at validation checkpoints.
Use GliderTrace for execution evidence when available, or the repository's normal CLI commands.
Restore dependencies or build generated artifacts when the project requires them, then reload and check diagnostics again.
For example, generated COM interop assemblies can require a build before Glider can resolve their types.
