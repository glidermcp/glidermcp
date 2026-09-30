# External tools and prerequisites

This map assumes a supported GliderTrace server is already running. Read the installed schemas and `trace_status` for version-specific support.
Call `trace_status` with `includeEnvironment: true` to inspect local tool discovery and enabled runner policies.
A found executable does not prove the selected process, target framework, adapter, or workspace is supported.

## Server and project toolchains

GliderTrace 1.3.0 package setup uses the .NET 10 SDK and `glider-trace` on `PATH`.
Follow the installed package's requirements when that version changes. Building and testing also require the repository's selected toolchain.
A newer SDK does not automatically satisfy a pinned `global.json`, legacy targeting pack, test adapter, or workload requirement.
Optional diagnostic executables must be visible on the server's `PATH`. The server can inherit a different environment from the shell.
Current collector discovery excludes executable paths inside trusted workspace roots. Workspace NuGet uses a separate explicit policy.

## Tool dependency map

| GliderTrace tool or operation | External dependency | Other requirement |
| --- | --- | --- |
| `trace_status` | No extra executable for ordinary status. Environment discovery checks installed tools. | A running server; interpret capability reasons. |
| `trace_add_workspace`, `trace_remove_workspace` | None. | Runtime workspace changes enabled by the operator; explicit intent for additional trust. |
| `trace_list_sessions`, `trace_get_session`, `trace_query_events`, `trace_compare_sessions`, `trace_pin_session` | None. | Stored sessions and accessible artifact storage. |
| `trace_export` | None. | Stored session; publishing the export is a separate action. |
| `trace_list_processes` | No diagnostic CLI needed. | Local process access and runtime discovery; process support differs by host. |
| `trace_build` with `auto` or `dotnet` | `dotnet` and the project's SDK, workloads, and targeting packs. | Build-compatible target under a trusted root. |
| `trace_build` with `msbuild` | Visual Studio or Build Tools MSBuild on Windows; discovery can use `vswhere`. | `--allow-executable msbuild`; required .NET Framework targeting packs where applicable. |
| `trace_restore` with `auto` or `dotnet` | `dotnet` and the project's SDK. | Required package sources and access. |
| `trace_restore` with `nuget` | Workspace-local `.nuget/NuGet.exe` and its Windows runtime prerequisites. | `--allow-executable nuget-workspace`; this runner does not forward MSBuild properties. |
| `trace_run_tests` with `auto` or `dotnet-test` | `dotnet`, the project's SDK, and its test dependencies. | Compatible test command mode and structured reports; see below. |
| `trace_run_tests` with `vstest` | `dotnet vstest`, compatible prebuilt test assemblies, and their adapters. | Select assemblies and a supported framework/platform. |
| `trace_run_tests` with `vstest-console` | Validated `vstest.console.exe` from Visual Studio or a supported Microsoft.TestPlatform package. | `--allow-executable vstest`; compatible assemblies, adapters, and targeting environment. |
| `trace_pipeline` | Dependencies of each enabled restore, build, and test stage above. | Explicit stage runners and policies must all be supported. |
| `trace_run`, `trace_start` | `dotnet` by default; another supported executable requires its policy and installation. | Executable policy is not permission for arbitrary shell commands. |
| `trace_run` with `collectCounters` | `dotnet-counters`. | Eligible live target; cannot combine with `collectTrace`. |
| `trace_run` or `trace_start` with `collectTrace` | No `dotnet-trace` installation needed; EventPipe collection is bundled. | Eligible CoreCLR process with accessible diagnostics IPC. |
| `trace_stop` | No extra executable. | Active command session owned by the same server process; stop terminates that command. |
| `trace_attach` with EventPipe | No `dotnet-trace` installation needed. | Fresh process identity, eligible CoreCLR runtime, and diagnostics access. |
| `trace_attach` with ETW | Bundled collector; no PerfView or `dotnet-trace` installation needed. | Windows Framework IIS profile, privileges, and sensitive machine-wide acknowledgment. |
| `trace_start_etw_capture`, `trace_stop_etw_capture` | Bundled ETW collector. | Elevated local Windows stdio server, supported profile, and both capture acknowledgments. |
| `trace_capture_dump` | No `dotnet-dump` installation needed; diagnostics IPC collector is bundled. | Eligible CoreCLR process, access, fresh identity, and sensitive-artifact acknowledgment. |
| `trace_capture_gcdump` | `dotnet-gcdump`. | Eligible CoreCLR process, access, fresh identity, and acknowledgment of sensitive data and full generation 2 GC. |
| `trace_import_artifacts`, `trace_import_otlp` | No capture CLI needed to import existing files. Supported parsers are bundled. | Supported format, trusted file path, and sensitivity/identity fields where required. |
| `trace_exceptions` | None. | Exception evidence already captured or imported. |
| `trace_counters` | No `dotnet-counters` installation needed for an existing counters artifact. | Supported counter data in the session. |
| `trace_hotspots` | No `dotnet-trace` installation needed for an existing trace artifact. Parser is bundled. | Captured CPU sample data and adequate parse coverage. |
| `trace_correlate_code` | Compatible SDK/MSBuild and resolvable project, package, and reference inputs. | Compiler services are bundled; no separate Glider MCP connection is needed. Source identity remains unverified. |
| `send_feedback` | No diagnostic executable. | Enabled feedback endpoint and network access; installed privacy and publication rules apply. |

Importing a dump or GC dump stores the artifact; it does not promise full heap or stack analysis.
Additional analysis tools can appear in later versions. Discover their schemas and dependencies before using them.

## Optional installations and test reports

When the user requests installation, install only the missing dependency required by the operation:

```sh
dotnet tool install --global dotnet-counters
dotnet tool install --global dotnet-gcdump
```

Check tool versions against the target runtime and host. Ensure the global tool directory reaches the server's `PATH`.
Restart the server when necessary to refresh its environment, then recheck status. Installation does not enable executable policies.

TRX reports depend on the test runner and project configuration.
Microsoft.Testing.Platform projects can require `Microsoft.Testing.Extensions.TrxReport` in the project for structured TRX evidence.
Check the repository's dependencies before suggesting that change. A global diagnostic tool installation does not provide the test reporter.
When a reporter is absent, preserve the command outcome and report the structured-evidence limit rather than claiming a test pass.
