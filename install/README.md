# Direct MCP configuration

These templates connect your client to local GliderMCP servers without a plugin.
Keep uses a separately managed shared HTTP server; these templates intentionally add no Keep startup entry.
Copy only the server entries you need into your existing configuration.

| Product | Command used by the template | Requirement |
| --- | --- | --- |
| Glider | `dotnet tool exec "glider@*" --no-http-cache --verbosity quiet --` | .NET 10 SDK and NuGet access |
| GliderTrace | `dotnet tool exec "glider-trace@*" --no-http-cache --verbosity quiet --` | .NET 10 SDK and NuGet access |
| TGlider | `npx -y tglider@latest` | Node.js 24+ with npm and `npx` on `PATH` |
| Scout | `npx -y @glidermcp/scout@latest` | Node.js 18+ and npm optional dependencies |
| JGlider preview | `npx -y --ignore-scripts jglider@next` | Node.js 24+ and JDK 21+ |

TGlider does not require Scout. Add Scout independently when you need repository-wide search.
See the [product installation guides](https://glidermcp.com/products) for platform requirements and verification.

## Templates and project preload

- [Claude Code project configuration](claude-code/.mcp.json)
- [Codex configuration snippet](codex/config.toml)

Retain existing settings and unrelated servers. The .NET launchers download packages on first use and cache package files.

For Glider, we recommend a project configuration that preloads the selected solution with `--solution`.
Use the [Glider configuration generator](https://glidermcp.com/glider/installation) to set your workspace and solution paths.
Choose a `.sln`, `.slnx`, or `.csproj`. For a large solution, select a project when its scope is sufficient.
The agent waits for the load to complete before it uses semantic tools.

The portable templates and plugins omit machine-specific paths. Glider's agent selects a solution after connection when none is preloaded.
For GliderTrace, set `--workspace` to your repository, or start the client from that repository.
Use the [GliderTrace generator](https://glidermcp.com/glider-trace/installation) for an explicit workspace.

## Migrate an existing .NET connection

Edit the existing server entry instead of adding a second registration. Set `command` to `dotnet`.
For Glider, the launcher arguments end at `--`. This complete example includes sample server arguments after that separator:

```json
["tool", "exec", "glider@*", "--no-http-cache", "--verbosity", "quiet", "--", "--default-timeout", "30m", "--solution", "/path/to/App.sln"]
```

Replace the sample server arguments with your existing flags, then add `--solution` if absent.
Set its path to your solution. Keep each workspace, transport, port, and timeout option only once.
For GliderTrace, this complete example includes a workspace and timeout after the launcher separator:

```json
["tool", "exec", "glider-trace@*", "--no-http-cache", "--verbosity", "quiet", "--", "--default-timeout", "30m", "--workspace", "/path/to/repo"]
```

Replace the sample server arguments with your existing flags, using the intended workspace path.
Keep each option only once. JSON and TOML use the same argument arrays here.
Restart the MCP server and ask the agent to confirm its version and workspace.
Existing global installations can remain; the launcher uses a separate package cache.

If you switch to a plugin, remove or disable the manual registration for that product first.
Keep your manual connection when you need project preload or custom server flags; install only its skill as described below.
Avoid editing a plugin cache because a plugin update can replace those edits.

## Versions, network access, and SDK selection

The .NET launcher checks for the latest stable package at each server start. A running server keeps its selected version until restart.
`@*` bypasses a local tool manifest pin. `--no-http-cache` refreshes feed metadata and requires network access.

Check `dotnet --version` from the server launch directory. A repository's `global.json` can select an SDK without `tool exec`.
Select .NET 10 for the launcher, or use a global installation. Preserve the project's SDK requirements.
For a fixed version, replace `glider@*` or `glider-trace@*` with `glider@<version>` or `glider-trace@<version>`.
Replace `<version>` with the required version number.

For offline operation, install the required version globally while online and launch that executable directly:

```bash
dotnet tool install --global glider --version <version>
dotnet tool install --global glider-trace --version <version>
```

Run only the command for your product. Replace `<version>` before execution.
Set `command` to `glider` or `glider-trace`. Remove the launcher arguments through `--` and keep only the server arguments.
Update global installations manually with `dotnet tool update --global <package>` while their servers are stopped.
A fixed or offline package still expires on its embedded expiry date.

## npm launchers and manual alternatives

TGlider and Scout use explicit `@latest` selectors for stable packages. JGlider uses `@next` for its preview.
npx caches downloaded packages but can require registry access. Restart the MCP server to select an update.
A running process keeps its selected package. Use an exact package version when you need a fixed release.

For TGlider, set `command` to `npx` and put `["-y", "tglider@latest"]` before your existing server arguments.
For Scout, use `["-y", "@glidermcp/scout@latest"]`. Preserve an explicit `--root` and other options.
For JGlider, use `["-y", "--ignore-scripts", "jglider@next"]`. Preserve `--project` and only the trust grants you intend.
Edit the existing registration, not a second entry.

On Windows, clients that require an executable use `cmd.exe` with `/d`, `/s`, and `/c`.
The last argument is one command string. For example:

```json
{"command":"cmd.exe","args":["/d","/s","/c","npx -y tglider@latest --workspace \"C:\\Projects\\MyApp\""]}
```

Use the [TGlider](https://glidermcp.com/tglider/installation), [Scout](https://glidermcp.com/scout/installation), or [JGlider](https://glidermcp.com/jglider/installation) guide for your client's format.
Keep shell control characters out of a Windows command string.

For global or offline use, install the required package version while online and launch its executable directly.
For example, use `npm install --global tglider@<version>` or `npm install --global @glidermcp/scout@<version>`.
Replace `<version>` before execution. For JGlider, retain `--ignore-scripts` during installation.
Set the client command to `tglider`, `scout`, or `jglider`, and remove the npx launcher arguments.
On Windows, retain the cmd.exe wrapper for npm shims if your client requires it.

Scout also supports [native installation](https://glidermcp.com/scout/setup#installers) without Node.js.
Use the same channel for manual updates. Fixed and offline installations still obey the package's embedded expiry.

Keep uses `@glidermcp/keep@next` for its alpha and a separately managed process.
Follow [Keep setup](https://github.com/glidermcp/keep/blob/main/tools.md#setup); connect clients to the same token-protected HTTP endpoint.
Do not add a Keep stdio startup entry for each agent. Keep the same store and token across server restarts.

## Install skills without duplicate servers

We recommend the skill for each product you use. Skills guide tool selection, workspace checks, and interpretation of results.
A [server plugin](../README.md#plugins-and-skills) installs its skill and its MCP connection together.
The Keep plugin supplies a skill only and preserves your existing HTTP connection.
If your connection already works, copy only the skill directory, including all its references.

| Product | Source directory | Skill name |
| --- | --- | --- |
| Glider | [`plugins/glidermcp/skills/glider-csharp`](../plugins/glidermcp/skills/glider-csharp) | `glider-csharp` |
| GliderTrace | [`plugins/glider-trace/skills/glider-trace-runtime`](../plugins/glider-trace/skills/glider-trace-runtime) | `glider-trace-runtime` |
| TGlider | [`plugins/tglider/skills/tglider-typescript`](../plugins/tglider/skills/tglider-typescript) | `tglider-typescript` |
| Scout | [`plugins/scout/skills/scout-search`](../plugins/scout/skills/scout-search) | `scout-search` |
| JGlider | [`plugins/jglider/skills/jglider-java`](../plugins/jglider/skills/jglider-java) | `jglider-java` |
| Keep | [`plugins/keep/skills/keep-knowledge`](../plugins/keep/skills/keep-knowledge) | `keep-knowledge` |

Download this repository or clone it into a directory you choose:

```bash
git clone https://github.com/glidermcp/glidermcp.git
```

Copy the selected skill directory to your project's `.claude/skills/` for Claude Code or `.agents/skills/` for Codex.
For example, from the downloaded repository on macOS or Linux:

```bash
mkdir -p /path/to/project/.claude/skills
cp -R plugins/glidermcp/skills/glider-csharp /path/to/project/.claude/skills/
```

For Codex, replace `.claude/skills` with `.agents/skills`. On Windows, copy the same directory with your file manager.
Review an existing copy before replacing it. Refresh copied skills when their source changes; copies do not update automatically.
Start a new client session and ask the agent to use the installed skill.
For another client, use its documented skill directory and activation procedure.

## Claude Code specialists

For substantial code tasks, use a product specialist to give the work explicit tool guidance.
Small tasks can use the skills in the main conversation.
The [Glider plugin](../plugins/glidermcp/README.md#claude-code-specialist) provides `glidermcp:csharp-specialist`.
The [GliderTrace plugin](../plugins/glider-trace/README.md#claude-code-specialist) provides `glider-trace:runtime-verifier`.
The [TGlider plugin](../plugins/tglider/README.md#claude-code-specialist) provides `tglider:typescript-specialist`.
The [JGlider plugin](../plugins/jglider/README.md#claude-code-specialist) provides `jglider:java-specialist` for read-only Java investigations.

For an existing manual connection, copy the relevant `agents/*.md` file into your project's `.claude/agents/`.
Install its standalone skill first. In the copied agent's `skills` field, remove the plugin prefix and colon.
For example, change `glidermcp:glider-csharp` to `glider-csharp`.
Use the copied agent name when you delegate: `csharp-specialist`, `runtime-verifier`, `typescript-specialist`, or `java-specialist`.
Restart Claude Code to discover the new definitions.

These agents inherit available tools and client permissions. Their role instructions select the product; they do not isolate its server.
Coordinate workspace changes, source edits, and test processes when multiple agents share the same connection.
See the [Claude Code subagent reference](https://code.claude.com/docs/en/sub-agents) for client capabilities and restrictions.
