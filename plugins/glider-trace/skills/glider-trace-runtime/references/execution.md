# Commands, builds, and tests

Check `trace_status` with `includeEnvironment: true` before selecting a runner on an unfamiliar host.
Use [prerequisites.md](prerequisites.md) for external dependencies. Availability also depends on executable policies and the repository's toolchain.

## Preserve the repository's command contract

Choose the intended solution or project explicitly when the root contains several targets.
Keep the repository's required SDK, configuration, test framework, filters, properties, and working directory.
For a custom argument set absent from a specialized tool, use `trace_run` with the supported executable and exact argument array.
Do not replace required test arguments with a similar command merely to obtain structured output.

```json
{"command":"dotnet","arguments":["test","--project","src/Example.Tests/Example.Tests.csproj"],"workingDirectory":"/absolute/trusted/checkout","label":"focused-tests"}
```

This example applies only to a repository whose test mode supports `--project`.
Microsoft.Testing.Platform and VSTest repositories can require different command forms. Follow the project's configuration.
A custom run stores process evidence; it need not provide the structured test verdict of `trace_run_tests`.

## Choose runners and stages

- Default build and restore runners use `dotnet`. Native MSBuild and workspace NuGet require their enabled executable policies.
- `dotnet-test` selects normal SDK tests. `vstest` uses `dotnet vstest` for prebuilt test assemblies.
- `vstest-console` uses native Visual Studio VSTest and requires its executable policy. Check adapter and target-framework support.
- Use `trace_pipeline` when ordered restore, build, and test meet the requested validation. It stops at the first unsuccessful stage.

Supply test assemblies for a VSTest runner. Do not infer that the server contains the project's adapters or targeting packs.
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
