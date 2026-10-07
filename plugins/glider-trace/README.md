# GliderTrace Plugin

Connect your agent to local .NET command, test, and runtime evidence. This plugin includes the `glider-trace-runtime` skill.

## Install and connect

Install the [.NET 10 SDK](https://dotnet.microsoft.com/download/dotnet/10.0) and make `dotnet` available on PATH.
The plugin checks NuGet for the latest stable package at each server start. Network access is required.
A global product installation is optional. Check the launcher before connecting:

```bash
dotnet tool exec "glider-trace@*" --no-http-cache --verbosity quiet -- --version
```

Follow the [Claude Code or Codex plugin instructions](../../README.md#plugins-and-skills), using `glider-trace@glidermcp`.
Start a new client session after installation. The bundled stdio connection retains a 30-minute default tool timeout.
Remove or disable an existing manual connection for this product before you enable the plugin.
To preserve a manual connection, install [only the skill](../../install/README.md#install-skills-without-duplicate-servers).

Start the client from your repository. The agent checks the trusted workspace before it executes a command.
For an explicit workspace, use a [project connection](../../install/README.md#templates-and-project-preload) and install only the skill.

## Updates

Restart the MCP server to select a newer stable package. Other active processes keep their selected version.
A repository's `global.json` can select an SDK without `tool exec`.
See [SDK selection, fixed versions, and offline operation](../../install/README.md#versions-network-access-and-sdk-selection).

Server package updates and plugin updates are separate. Update this plugin to receive new skills, agents, and connection defaults.
In Claude Code, refresh the marketplace and update the plugin:

```bash
claude plugin marketplace update glidermcp
claude plugin update glider-trace@glidermcp
```

In Codex, use `/plugins` to manage the installed plugin. Start a new session after an update.
For a manually configured server, follow the [launcher migration](../../install/README.md#migrate-an-existing-net-connection).

## Claude Code specialist

The plugin includes `glider-trace:runtime-verifier` for substantial tasks. Its instructions preload `glider-trace:glider-trace-runtime`.
Ask Claude Code to delegate, for example:

> Use glider-trace:runtime-verifier to run the relevant tests for this change and report failures with their evidence.

Assign one owner for source edits and coordinate shared workspace changes and test processes.
The specialist inherits available tools and client permissions; its instructions guide tool choice rather than enforce isolation.
Use the skill directly for a small task. These agent definitions target Claude Code; Codex uses the bundled skill.
For a manual MCP connection, follow the [standalone agent instructions](../../install/README.md#claude-code-specialists).
