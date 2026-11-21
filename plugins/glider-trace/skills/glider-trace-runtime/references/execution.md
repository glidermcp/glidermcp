# Commands, builds, and tests

Use compact status for normal execution. Request `includeEnvironment: true` to diagnose an unavailable runner or executable.
Use [prerequisites.md](prerequisites.md) for external dependencies. Availability also depends on executable policies and the repository's toolchain.

## Preserve the repository's command contract

Choose the intended solution or project explicitly when the root contains several targets.
Keep the repository's required SDK, configuration, test framework, filters, properties, and working directory.
Run tests with `trace_run_tests`; `trace_run` stores no test verdict, counts, or failure records.
Use `trace_run` for tests only when the repository requires arguments that `trace_run_tests` cannot express, and report that the run has no structured verdict.
`trace_import_artifacts` can import a TRX file that such a run wrote.
For other argument sets absent from a specialized tool, use `trace_run` with the supported executable and exact argument array.

```json
{"command":"dotnet","arguments":["run","--project","src/Example.App/Example.App.csproj","--","--check"],"workingDirectory":"/absolute/trusted/checkout","label":"app-check"}
```

A custom run stores process evidence only.

## Choose runners and stages

- Default build and restore runners use `dotnet`. Native MSBuild and workspace NuGet require their enabled executable policies.
- `dotnet-test` selects normal SDK tests. `vstest` uses `dotnet vstest` for prebuilt test assemblies.
- `vstest-console` uses native Visual Studio VSTest and requires its executable policy. Check adapter and target-framework support.
- Use `trace_pipeline` when ordered restore, build, and test meet the requested validation. It stops at the first unsuccessful stage.

Supply test assemblies for a VSTest runner. Do not infer that the server contains the project's adapters or targeting packs.
`tests` selects exact fully qualified names with every runner from GliderTrace 3.2.0; with `dotnet-test` it needs VSTest mode, and Microsoft.Testing.Platform projects reject it.
`framework` and `platform` with runner `vstest` need GliderTrace 3.2.1 or later.
Runner names reserved in status are unavailable until the installed server supports them.
Explicit stage runners let a supported pipeline combine NuGet, MSBuild, and native VSTest. Inspect its installed schema first.

Use `profile: "worktree"` for supported build, restore, or pipeline operations in a Git worktree.
It disables source-control manager queries and omits SourceLink metadata. It does not change compiled behavior.
Explicit properties override the profile where supported. Use the profile only when that metadata tradeoff suits the task.

## Interpret test results

Read `testVerdict`, `testOutcome`, report coverage, and count status alongside process status and exit code.
Unknown totals are not zero. Exact subtotals describe only the available structured reports.
An exited process with absent reports does not prove a test pass. A passing pipeline test stage requires complete evidence.

Enable failed-test reruns only when the task permits repeated execution. Read `flakyAnnotations` and rerun evidence.
Only a matching structured pass establishes recovery for that failed test. Missing, skipped, cancelled, or different-target results do not clear it.
A known-flaky annotation is a diagnostic lead, not permission to ignore the failure.

`timeoutMs` shares one deadline across initial tests and reruns. A stored timeout differs from caller cancellation.
Inspect the stored session after an interrupted request when available. Do not repeat side effects merely because a response was lost.
