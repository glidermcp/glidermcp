# Workspace startup and recovery

Technical guidance for agents and client integrators. Follow the installed tool schemas and CLI help.

## Run Modes

```sh
npx -y tglider@latest --workspace /path/to/repo
npx -y tglider@latest --transport http --port 5002 --workspace /path/to/repo
```

Stdio is the default. HTTP exposes MCP at `http://localhost:5002/mcp` and health at `/health`.

| Option                  | Purpose                                                            |
| ----------------------- | ------------------------------------------------------------------ |
| `--workspace <dir>`     | Loads a workspace at startup.                                      |
| `--no-watch`            | Requires `--workspace`; disables the file watcher for that load.   |
| `--default-timeout 30m` | Sets tool timeouts; accepts `ms`, `s`, or `m`. Use `0` to disable. |
| `--help` / `--version`  | Shows CLI help or the installed version.                           |

## Search Scope

TGlider answers semantic questions about TypeScript and JavaScript. For literal or regex searches, use your client's search or [Scout](https://glidermcp.com/scout/installation).

## Troubleshooting

- No workspace loaded: set `--workspace` in the client configuration or call `load`.
- Slow workspace load: allow it to finish. `load` is synchronous, and `server_status` requests wait for it.
- Inspect `lastLoad` for load duration and `workspaceWarmup` for subsequent progress. Load estimates persist across restarts.
- Long operations time out: increase `--default-timeout`.


The help and version commands remain available after package expiry.
