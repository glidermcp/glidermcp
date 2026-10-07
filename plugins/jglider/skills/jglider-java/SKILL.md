---
name: jglider-java
description: Use JGlider to inspect Java source, project models, compiler diagnostics, references, and direct callers in Maven or Gradle projects. This preview is read-only.
---

# JGlider Java inspection

Use this skill when JGlider is connected and the task needs Java source or compiler evidence.
Read the installed schemas first. The published preview has no edit, hierarchy, implementation, or outgoing-call tools.
Read [preview.md](references/preview.md) for runtime requirements, trusted imports, analysis limits, or connection recovery.

## Check the selected project

Call `server_status` before the first investigation. Compare module directories with the intended checkout and inspect trust, model status, and semantic readiness.
A shell directory change does not retarget the server. Coordinate a workspace change when other agents share the connection.
If no project is loaded, use `load` with the absolute project or build-file `path`.
The published load schema accepts the path; it does not accept trust or network grants.

Safe mode reads build files without executing them. Its static model can support parser reads while semantic analysis remains unavailable.
Use parser tools for the supported portion of the task and report missing compiler evidence.
Do not enable build execution or network downloads merely to remove a coverage warning.
An operator grants them separately with `--trust-workspace <root>` and `--allow-network-fetch <root>` at startup.

## Select evidence

Start with `find_code`, `search_symbols`, or `get_structure` to locate declarations.
Read bounded source through `get_type_source`, `get_method_source`, or `get_file_contents`.
Parser declarations and name matches do not establish compiler-bound references or runtime dispatch.

When semantic analysis is ready, use `resolve_symbol`, `get_symbol_info`, `get_symbol_at_position`, and `get_diagnostics` for compiler facts.
Pass a current semantic `symbolKey` to `find_references` or `find_callers`.
These relation tools exist in `0.1.0-preview.1`; a server that advertises them can still lack the required semantic model.
Read result coverage and omitted scopes before making an absence claim. Follow cursors with the same symbol and scope.
Caller results describe direct method or constructor call sites, not a complete runtime call graph.
Rediscover keys after reloads or source changes rather than treating them as permanent identifiers.

Read status and returned recovery advice after a failed or partial operation.
Use `sync` or `reload` according to the installed contract when external source or project changes invalidate the model.
Confirm that the refreshed state describes the intended checkout before continuing.

## Complete the task within the preview boundary

JGlider leaves source unchanged. For an implementation task, return source evidence and proposed edits to the assigned writer.
Another authorized editing tool can apply changes, but it cannot turn parser evidence into compiler proof.
Use Scout or scoped file inspection for repository text outside JGlider's scope.

Separate tool success, diagnostic coverage, and actual build or test outcomes in the report.
The preview disables telemetry and feedback. Tool presence alone does not establish that feedback publication is enabled.
