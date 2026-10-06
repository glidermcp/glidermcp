# Workspace, resources, and recovery

Technical guidance for agents and client integrators. Development-build capabilities require matching installed schemas.

## Workspace Scope

Scout searches the Git checkout that contains its launch directory, or the launch directory itself outside Git.
Pass `--root /path/to/repo` when your client starts elsewhere. The development build supports up to 16 roots; repeat `--root` to select startup roots.

Scout honors `.gitignore` and `.ignore`, includes other hidden files, and excludes `.git` and its own index.
Use `server_status` to confirm the root before interpreting an empty result.

For another checkout, call `load` with its absolute path. Outside roots require `acknowledge: true` on each load.
The development build accepts root objects and `mode: "add"` to retain other loaded roots.
Use IDs from `server_status.roots` with `find.scope.rootIds`, `sync.rootIds`, or `unload.rootIds`.
Omission selects all roots. The deepest loaded root owns files in overlapping directories, including nested worktrees.
Each worktree reads its own local configuration. Copy ignored configuration files when you create a worktree.

## Semantic Search

Add `--semantic` to enable natural-language queries for every startup root.
For mixed settings, omit that flag. Set `[semantic] enabled = true` in each root's `.glider/scout/config.toml` as needed.
Runtime load overrides take precedence over startup flags and root configuration.
In the development build, an omitted add override preserves its value; `false` disables it and `null` clears it.
The first use downloads a model and creates local embeddings in the background. Large repositories can take substantial time.
Lexical search remains available throughout; semantic requests use lexical results until the semantic index is ready.

`server_status` reports progress. Models and cached embeddings are shared across workspaces.
The semantic index leaves memory after 30 minutes without a semantic query and reloads on demand.
Old unidentified semantic stores rebuild once; cached model files do not need another download.
Worker allocation limits Scout-managed work. Search and embedding backends can use additional threads.
See the [documentation](https://glidermcp.com/scout) for model, device, and file-exclusion settings.

## Useful Options

Scout uses stdio. Run `scout --help` for the full CLI reference.

| Option | Purpose |
| --- | --- |
| `--root <path>` | Selects a workspace explicitly. |
| `--semantic` | Enables semantic search. |
| `--find-budget 10s` | Sets search deadlines. `0` disables them; other caps and client timeouts still apply. |
| `--parse-cap 4000` | Sets the structural file limit. |
| `--take-max 500` | Sets the maximum results per page. |
| `--load low` | Limits resource use. Accepts `low`, `medium`, `high`, or `full`. |
| `--idle-timeout-secs 3600` | Exits after an hour without client activity. Default: `0`, disabled. |
| `--no-telemetry` | Disables telemetry and feedback. |

Active requests prevent idle shutdown. Scout also exits when its parent process exits.
Semantic memory eviction keeps the process alive; it is separate from process shutdown.

## Troubleshooting

- Missing results: confirm the root, ignore rules, search mode, and case sensitivity.
- Partial results: narrow the scope or follow the returned continuation arguments. Partial coverage cannot prove absence.
- Semantic search returns lexical results: check whether the semantic index is enabled and ready.
- A structural pattern finds nothing: match the complete construct, including its modifiers.
- npm cannot find the binary: confirm your installation includes optional dependencies for your platform.
- A global command is missing: add the installation directory to `PATH`.
