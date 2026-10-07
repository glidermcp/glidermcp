# Keep skill plugin

Use shared documents and project knowledge through an existing Keep connection.
This plugin provides the `keep-knowledge` skill only. It starts no server and contains no MCP connection or token.

## Connect once, then install the skill

Follow [Keep HTTP setup](https://github.com/glidermcp/keep/blob/main/tools.md#setup) to start one token-protected server for your store.
Connect your clients to that server. Use the client's secret storage for the Bearer token; never put it in this plugin.
Only one process can open the store at a time. The token grants full access to that store.

Install with the [Claude Code or Codex instructions](../../README.md#plugins-and-skills), using `keep@glidermcp`.
Keep your existing manual HTTP registration: this plugin adds no duplicate connection.
Alternatively, install [only the skill directory](../../install/README.md#install-skills-without-duplicate-servers).
Start a new client session and ask the agent to use the Keep skill.

The `@glidermcp/keep@next` package is an alpha. The server needs a separate installation and update process.
Use the same token and store directory when you restart it. See the [tool and backup reference](https://github.com/glidermcp/keep/blob/main/tools.md).

## Example tasks

- Find the source and date of a project decision.
- Save an agreed note in an existing collection.
- Update a document while preserving another agent's changes.

The skill guides discovery, revision checks, retry identity, and evidence limits. It does not grant permission to change unrelated documents.
Update this plugin separately to receive revised skill guidance.
In Claude Code, refresh the marketplace and update `keep@glidermcp`. In Codex, use `/plugins` and start a new session.
