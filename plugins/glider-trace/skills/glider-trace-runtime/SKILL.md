---
name: glider-trace-runtime
description: Use GliderTrace to run .NET commands, diagnose builds and tests, capture process evidence, and inspect stored sessions and artifacts.
---

# GliderTrace runtime evidence

Use this skill when GliderTrace is connected and the task needs .NET execution or runtime evidence.
Follow the installed server instructions and schemas. Capabilities and runner options differ across versions and hosts.

## Check the workspace and capabilities

Call `trace_status` before the first investigation. Compare the trusted roots with the intended checkout.
Include `includeEnvironment: true` on an unfamiliar host or before selecting a build, restore, test, or capture backend.
Read [prerequisites.md](references/prerequisites.md) when a tool is missing or the user asks which dependencies are necessary.

Absolute paths must belong to a configured root. Relative paths use the primary root, which can differ from the shell directory.
A worktree switch does not retarget the server. Use an absolute target within an existing trusted root when possible.
Runtime workspace registration requires server support and explicit user intent to trust that root.
Coordinate workspace changes with other users of the server. A worktree alone does not isolate shared resources.
Repeat status after a restart or suspected scope mismatch, rather than before every call.

## Native license status

GliderTrace 1.4.0 supports native licenses and a sanitized `license` object in the existing status tool.
Verify the installed build's schemas and CLI help before using this behavior.
Activation requires an eligible paid account, an assigned developer seat, and current account and activation-service availability.
Published package support does not establish that public sales are open.
Status reads local state without a network request. Never request or publish credentials, complete grants, or profile contents.

Production is the default for the CLI and MCP startup. Unknown environment values are rejected.
Use `glider-trace auth login` only when the user requests activation.
Use `glider-trace license status --json` for local status and `glider-trace license refresh` for explicit renewal.
Select sandbox explicitly with `--environment sandbox` on each CLI command or `GLIDER_LICENSE_ENVIRONMENT=sandbox` for MCP startup.

Sandbox `valid` describes the grant; `commercialRights` remains false. Account login alone establishes no production commercial rights.
Production verifies approved public keys compiled into the build. Editable cached keys cannot add production trust.

Read `licenseType` and `notice` with `pricingUrl` and `termsUrl`. `valid` describes the signed grant, not free-use permission.
`commercial` requires verified production rights and includes `paidThrough`. `sandbox` identifies a test grant without production rights.
`free` preserves non-commercial and open-source use, 30-day organization evaluation, and commercial use until paid plans are offered publicly.
The packaged LICENSE controls the installed version. These fields supply no credentials or account identifiers.

Each environment has a separate default profile under `~/.glidermcp/licenses/` and a separate vault namespace.
Use `--profile-directory PATH` for an isolated CLI profile and `GLIDER_LICENSE_PROFILE_DIRECTORY` for the corresponding MCP process.
Keep production and sandbox in separate directories. Glider and GliderTrace share the selected profile for the OS user.
`glider-trace auth logout` clears local rights for both products that select that profile before remote cleanup.

Follow `action` for recovery. If `revocationPending` is true, repeat logout after service and credential access recover.

A valid grant works offline through its signed paid-period end, without daily renewal or a vault read.
Only a valid production commercial grant permits an expired package to start.
A trusted expired production grant requires successful bounded renewal, or the package needs an update.
Sandbox grants do not bypass package age.
Renewal cannot extend the paid period without a new verified grant. MCP startup never opens a browser or requests input.

The credential vault is the default. Current macOS app signatures can prevent access from the other product.
Windows and Linux native vault checks remain open.
Use `glider-trace auth login --credential-store file` only with the user's explicit consent to private file storage.
The file store uses OS-user permissions, without vault encryption. Do not silently change the credential store or weaken OS security settings.

## Select the operation

| Task | Tools | Guidance |
| --- | --- | --- |
| Restore, build, or test | `trace_restore`, `trace_build`, `trace_run_tests` | Read [execution.md](references/execution.md) for runner selection and test verdicts. |
| Ordered restore, build, and test | `trace_pipeline` | Use when the requested validation needs the ordered stages. |
| A custom supported command | `trace_run` | Preserve the repository's required arguments and working directory. |
| A command across an interactive interval | `trace_start`, `trace_stop` | Stopping terminates the started command; retain its session ID. |
| An existing process or memory capture | `trace_list_processes`, capture tools | Read [capture.md](references/capture.md) before capture. |
| Existing local evidence | `trace_import_artifacts`, `trace_import_otlp` | Import existing files when another execution is unnecessary. Read capture and attribution limits. |
| Stored failures or measurements | Session and analysis tools | Inspect coverage and the underlying artifacts before drawing conclusions. |

Use the narrowest operation that answers the task. A full pipeline is unnecessary for every source edit.
For repository text searches during an investigation, prefer Scout when available. Read `$scout-search` when installed.
Use scoped shell search when Scout is unavailable. Search results supplement the stored runtime evidence.
Give new sessions a useful label. Record the source revision, target, configuration, filters, and workload with the investigation.
Commands can restore packages, create build outputs, and run application side effects. The task's existing authorization still applies.

## Inspect evidence and recover omitted output

`success` describes the tool call. Read `status`, `exitCode`, and any test verdict for the actual execution outcome.
Results are compact JSON fields beside `success`. Do not invent a `responseDetail` or format parameter for ordinary reads.
Read `coverage` and warnings. Incomplete capture prevents an absence claim even when no matching events appear.
Output summaries and result pages can omit detail without changing capture coverage.

Use `trace_get_session` for stored status, findings, and artifacts. Use `trace_query_events` for filtered evidence and follow its cursor.
Follow an `outputLimit` read-only next call when present. Do not repeat a command or capture to recover omitted response text.
Use `trace_exceptions`, `trace_counters`, or `trace_hotspots` for evidence already present in the session.
These tools do not create missing measurements. A parser gap or empty sample list does not prove that nothing happened.

`trace_correlate_code` gives candidates in the current checkout. Read `correlationBasis` and `sourceIdentity` for each frame.
A source position or method match does not establish matching binaries, PDBs, or source revisions.
Follow `nextCursor` with the same session and kinds when the result supplies it.

## Retain, compare, and share

Find previous sessions with `trace_list_sessions`. Label filters match the whole label; follow the returned cursor.
Pin an important baseline with `trace_pin_session` when retention would otherwise remove it. Respect the quota and unpin when finished.

Use `trace_compare_sessions` for before/after evidence. Confirm equivalent targets, configuration, filters, and workloads separately.
An incomplete side produces an inconclusive comparison. Different test sessions can remain inconclusive because scope equivalence is unverified.
Do not describe an inconclusive comparison as proof that a fix worked.

Use `trace_export` for local summaries or artifact references. CI summary formats avoid copying raw artifact contents into the summary.
Raw exports, logs, binlogs, traces, and dumps can contain private data. Redaction does not prove that an artifact is safe to publish.
Local export does not authorize an upload, public comment, or code-scanning submission.
Before public feedback, check the product version, reproduction, and existing public issues. Follow installed feedback rules and publication authorization.
