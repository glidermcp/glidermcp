# Process capture and artifact import

Use capture when the investigation needs process measurements. Existing logs or artifacts can answer the task without another run.
Read [prerequisites.md](prerequisites.md) for collector dependencies and platform limits.

## Select a live target

Call `trace_list_processes` and inspect runtime evidence and per-operation capabilities.
Pass both `processId` and `targetId` from a fresh result to capture tools. A remembered PID alone cannot identify a process incarnation.
Process pages can drift. Reinspect the selected process when identity or accessibility changes.
Runtime version describes loaded modules; it does not prove the application's target framework.
Unknown capability fields do not establish support. Successful discovery does not guarantee access at capture time.

Use EventPipe for an eligible CoreCLR process. `trace_attach` captures an existing process for a bounded interval.
Use `trace_run` with `collectTrace` for a command launched through GliderTrace.
`trace_run` leaves trace collection off by default. `trace_start` enables it by default; select that option deliberately.
Use `collectCounters` when counters answer the question; that option and `collectTrace` are mutually exclusive on `trace_run`.

For a long-running command, keep the ID returned by `trace_start` and use `trace_stop` when the interactive interval ends.
`trace_stop` terminates the command and requires an active session in the same server process.
A second stop can fail because the command is inactive. Read the stored session instead.
Finish or transfer responsibility for that session before leaving the investigation.

## Memory dumps and Windows ETW

Use `trace_capture_dump` only when the user requests the sensitive local dump artifact.
Use `trace_capture_gcdump` only when the user requests that artifact and understands that capture triggers a full generation 2 GC.
Read the installed acknowledgment fields. Do not enable them on the user's behalf merely because capture might help.

For a verified Windows .NET Framework IIS worker, inspect the supported ETW backend and `framework-iis-cold-start` profile.
ETW requires explicit acknowledgment of sensitive and machine-wide capture. It can collect other processes and request values.
EventPipe and CoreCLR dump capture do not substitute for this Framework scenario.

For capture before the worker starts, use `trace_start_etw_capture` only with the supported elevated Windows stdio server.
Wait for provider readiness before triggering the worker through the permitted external action.
Select the fresh worker PID and identity, then supply them to `trace_stop_etw_capture`.
If the worker exits, a stop without a target leaves the artifact unbound. Candidate process starts remain unverified.
Read identity, provider, clock, stack, and loss coverage before attributing evidence to the worker.
An abrupt server crash can leave ETW active. Follow exact ownership and cleanup guidance before starting another capture.

## Import and analyze existing evidence

Use `trace_import_artifacts` for supported TRX, binlog, log, counters, nettrace, ETL, dump, or GC dump files.
Use `trace_import_otlp` for bounded OpenTelemetry JSON or NDJSON from a trusted workspace file.
Check the installed schema for sensitivity acknowledgment and supported formats. Imported raw files can contain private values.

ETL attribution requires the selected PID and a verified start time, or explicit uncertainty when supported.
For copied GliderTrace ETL, retain the original capture session ID and start time so the importer can verify identity and hash.
Uncertain events remain unattributed. Importing a file does not guarantee decoded stack, heap, or exception evidence.

Inspect the import's coverage, warnings, artifact references, and normalized events before selecting an analysis tool.
`trace_hotspots` summarizes captured CPU samples. `modulePath` identifies a binary; source paths require separate source evidence.
`trace_counters` needs counter data. `trace_exceptions` groups available exception evidence; it does not prove every exception was captured.
