# Stored evidence, detail, and fallback

## Reuse a sufficient summary

`trace_exceptions` groups stored matching exceptions by type, message, and first frame.
Group counts cover matching stored events, independently of the sample limit. They do not count uncaptured failures.
Each returned sample includes its full stored stack, which can be incomplete because of capture or analysis limits.
`sampleLimit: 0` omits samples; increase it only when samples answer a missing question.
`totalApprox` counts exception groups, not throws. `limit` limits groups, not occurrences.

Unreleased builds add `matchedEventCount`, `groupsTruncated`, and a `details` query. Check returned fields before using them.
`matchedEventCount` counts all matching stored events before group and sample limits.
`groupsTruncated` identifies groups omitted from this response. `samplesTruncated` identifies omitted occurrences within a group.
The `details` query selects exception events and preserves the summary's text and severity filters.
Use that query for omitted occurrences, then follow `nextCursor` with the same filters.
On older servers, construct the equivalent `trace_query_events` request from the installed schema.

Reuse an unchanged summary when it already answers counts, sample stacks, identity, and coverage for the task.
Request event detail for a specific missing occurrence or frame, not to repeat the same summary.
A stored stack can itself be bounded; another query cannot create missing frames.

## Select a capable fallback

First identify the unresolved fact, conflicting evidence, or required independent check.
Check the proposed reader's supported event kinds and fields before opening the raw artifact.
Use retained evidence when the reader can answer that question. State any remaining gap.
A reader that exposes only CLR `Exception/Start` cannot recover catch or termination events, even from a richer artifact.
This limit belongs to that reader; it does not establish every capability of GliderTrace or the artifact.

Complete throw analysis establishes neither per-throw handled status nor absence of other event kinds.
Source catch clauses and process survival can support a qualified inference. They do not prove that each observed throw was caught.
Use appropriate source, process, or additional event evidence for the requested conclusion, and retain its limits.
Perform independent verification when the user requests it, even when structured evidence already answers the diagnosis.

## Interpret results and coverage

`success` describes the tool call. Read `status`, `exitCode`, and any test verdict for the actual execution outcome.
Results are compact JSON fields beside `success`. Follow each tool schema for supported detail selectors.
Read `coverage` and warnings. Capture or analysis gaps prevent an absence claim when no matching events appear.
Output summaries and result pages can omit detail without changing capture coverage.

GliderTrace 2.0.1 and later expose `coverage.capture` and per-artifact `coverage.analysis`; `coverage.complete` remains the conservative aggregate.
Check returned fields and server instructions. Older sessions can lack decode records.
Each `runtimeExceptions` analysis reports `complete`, `partial`, or `unavailable`, observed/stored counts, and gaps.
A null `observedEvents` means unknown. Older nettrace sessions without decode records report unavailable analysis.
Complete analysis describes CLR throws within the retained artifact. It establishes neither provider coverage nor complete process history.

Use `trace_get_session` for stored status, findings, and artifacts. Use `trace_query_events` for filtered evidence and follow its cursor.
Set `kinds: ["exception"]` for exception detail. Omitted `kinds` includes every kind, including process and artifact metadata.
Follow an `outputLimit` read-only next call when present. Do not repeat a command or capture to recover omitted response text.
Use `trace_exceptions`, `trace_counters`, or `trace_hotspots` for evidence already present in the session.
These tools do not create missing measurements. A parser gap or empty sample list does not prove that nothing happened.

`trace_correlate_code` gives candidates in the current checkout. Read `correlationBasis` and `sourceIdentity` for each frame.
A source position or method match does not establish matching binaries, PDBs, or source revisions.
For code correlation, follow `nextCursor` with the same session and kinds.
For event queries, retain the session, kinds, text, and minimum severity across pages.

### Inspect runtime exception events

GliderTrace 2.0.1 and later normalize CLR `Exception/Start` events from EventPipe capture and nettrace import.
Use `trace_exceptions` for groups and `trace_query_events` with `kinds: ["exception"]` for individual throws. Read [capture.md](capture.md) for bounds.
`exceptionType`, `message`, and available frames describe the throw. Missing stacks remain an explicit analysis gap.

`runtimeSource` preserves the source PID, thread, event index, provider, event name, and artifact identity.
Its `timestampBasis` is `runtime`; the event timestamp describes the runtime event.
Use `runtimeSource.rawArtifactUri` for the retained trace and treat `sourceId` as opaque.

These events carry no text `exceptionRelation`. A throw alone establishes no catch status, unhandled failure, termination, or inner-exception relationship.
Zero observed throws with complete analysis describes only the retained artifact and supported event kind.
Inspect analysis gaps before interpreting absent exceptions. Reuse retained artifacts when another authorized reader can resolve an analysis gap.

### Recover text exception relationships

GliderTrace 1.4.1 and later expose `source` and `exceptionRelation` on recognized text exceptions.
Check the installed server instructions and returned fields before relying on this contract.
Versions before 1.4.1 lack these fields. Older stored sessions can also lack them.
Missing fields in older sessions mean unknown provenance. Use retained artifacts when structured evidence cannot answer the task.

Explicit inner-exception markers establish supported linear chains. `chainId` identifies a chain; `parentEvidenceId` identifies the enclosing exception.
`chainIndex` starts at zero. `parsedChainLength` counts recognized headers; `rootKnown` identifies a recognized outer header.
Neither field proves complete capture or recognition. Independent failures remain separate.
AggregateException branches and arbitrary multiline message continuations remain unsupported.

`source.sourceId` identifies one text input within the session. Treat the identifier as opaque.
`source.line` and `source.column` use one-based lines and UTF-16 columns within that source.
Use these positions for order within one `sourceId`, even when raw text is unretained.
They establish neither source-code identity nor runtime chronology across inputs. Source IDs establish no identity across sessions.

`source.rawArtifactUri` identifies retained raw text, or is null when no copy remains.
A known source ID with null raw URI means unretained text; older records without source IDs retain unknown identity.
The event `artifactUri` can identify normalized storage. Do not use it as raw text when `source.rawArtifactUri` is absent.
Chain relationships and source positions remain usable without a retained raw copy.
`source.timestampBasis` is `ingestion` for imported text or `collection` for captured command output.
Neither basis establishes original event time. Group `timestampBasis` can also be `unknown` or `mixed`.

Earlier builds retain at most three samples per group. Group rank does not establish source order.
GliderTrace 2.0.1 and later expose `sampleLimit`: one sample by default, with values from zero to three.
Check the installed schema; versions before 2.0.1 lack this parameter and `trace_query_log`.
`samplesTruncated` identifies omitted occurrences. Use `trace_query_events` for omitted members and full event detail.
`sourceRanges` gives per-source position bounds and counts; `chainCount` counts distinct known chains among stored matching events.
These summaries do not count unobserved failures. Missing optional sample metadata means unavailable, not a negative finding.
Filters, pages, group limits, and ingestion caps can exclude related exceptions. Parent IDs can refer outside the response or stored capture.
Read `coverage` and remove restrictive text or severity filters when related exceptions fall outside the current selection.
Keep `kinds: ["exception"]` for exception relationships and follow `nextCursor` with the same filters.
Complete ingestion does not establish complete parsing; complete samples do not establish a complete chain.

When available, `trace_query_log` retrieves retained raw context that normalized events omit, such as informational outcomes.
Use a returned source ID or an exact registered log artifact URI. Follow `nextStartLine` with the same source and filter.
`scannedThroughLine` can include a lookahead match beyond the returned page. Continue from `nextStartLine` to retain that match.
This is raw-source evidence through MCP. Read search completeness and warnings separately from ingestion coverage.
Use the original source when retained evidence cannot answer the task. Read [capture.md](capture.md) for import and query limits.

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
