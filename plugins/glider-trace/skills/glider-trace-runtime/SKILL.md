---
name: glider-trace-runtime
description: Use GliderTrace to analyze .NET logs and runtime exceptions, run builds and tests, and inspect captured evidence.
---

# GliderTrace runtime evidence

Follow the installed server instructions and schemas. Read only the reference needed for the current task.

## Establish the workspace

Call `trace_status` once before the investigation. Compare its trusted roots with the intended checkout.
A shell directory change does not retarget the server. Relative paths use its primary root.
Use an absolute target under a trusted root. Additional workspace trust requires explicit user intent and server support.
Coordinate shared roots and sessions with other agents. Recheck status after a restart or suspected scope mismatch.

Unreleased builds offer compact status with `includeDetails`. Check the installed schema before supplying this argument.
Use the returned `next` call or `includeDetails: true` for full policies and local diagnostics.
Use `includeEnvironment: true` for full status plus executable probes before selecting an unfamiliar runner or capture backend.
Earlier versions already return full status by default and do not accept `includeDetails`.
Read [prerequisites](references/prerequisites.md) for dependencies, or [licensing](references/licensing.md) for activation and expiry recovery.

## Select the task path

| Task | Minimum useful path |
| --- | --- |
| Existing .NET log | Import once with `trace_import_artifacts`. Use `trace_exceptions` for groups, counts, and sampled stacks. |
| Live exception diagnosis | Read [capture](references/capture.md). Select a fresh process identity, capture a bounded interval, and inspect `trace_exceptions`. |
| A particular exception or chain | Use `trace_query_events` with `kinds: ["exception"]`. Follow `nextCursor` with the same filters. |
| INFO outcomes, retries, or correlations | Use `trace_query_log` for retained raw context. Follow `nextStartLine` with the same source and filter. |
| Restore, build, test, or custom command | Read [execution](references/execution.md). Preserve the repository's target, arguments, configuration, and test verdict requirements. |
| Other captures or artifact imports | Read [capture](references/capture.md) for dependencies, acknowledgments, target identity, and analysis limits. |
| Stored sessions, comparisons, or source correlation | Read [evidence](references/evidence.md) for detail recovery, provenance, and comparison limits. |

Keep the returned `sessionId`. Record the source revision, target, configuration, filters, and workload needed to interpret the result.
GliderTrace 2.0.1 adds runtime throw analysis, `sampleLimit`, and `trace_query_log`. Check schemas and returned fields on older servers.
Omitted event `kinds` includes all kinds; an initial page can contain process or artifact metadata instead of exceptions.

## Interpret the evidence and stop when sufficient

`success` describes the call. Read execution `status`, `exitCode`, and test verdicts separately.
Read warnings, `coverage.capture`, and `coverage.analysis`. Missing analysis records or null counts mean unknown, not zero.
Complete analysis covers supported events in the retained artifact. It establishes neither complete process history nor catch or unhandled status.
A throw alone proves no termination or inner-exception relationship. Text positions establish no original runtime timestamp or binary/source identity.

A summary already supplies group counts, sampled stored stacks, and coverage. Reuse it when those facts answer the task.
Conclude when the evidence supports the diagnosis, target identity, relevant call path, and stated limits.
Use filtered detail or pagination for a specific omitted fact. Follow any `outputLimit` read-only recovery call.
Do not repeat a command or capture to recover omitted response text.

Use raw fallback for a concrete unresolved question, contradiction, or relevant partial, unavailable, or unsupported analysis.
First verify that the reader supports the event kinds or fields needed to resolve that gap.
A throw-only reader cannot answer whether exceptions were caught or supply catch or termination events.
An explicit request for independent verification still requires that verification.
Read [evidence](references/evidence.md) for counts, stacks, provenance, supported relationships, and fallback limits.

Raw logs, traces, and dumps can contain private data. Local access or export does not authorize publication.
