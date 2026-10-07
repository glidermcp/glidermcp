# TGlider plugin

Connect your agent to local TypeScript and JavaScript semantic tools.
The plugin includes the `tglider-typescript` skill and a stdio MCP connection.

## Install and connect

Install Node.js 24 or later with npm and `npx` on PATH. No global TGlider installation is required.
Follow the [Claude Code or Codex instructions](../../README.md#plugins-and-skills) with `tglider@glidermcp`.
The plugin launches `npx -y tglider@latest` with a 30-minute default tool timeout.
Restart the server to select a newer package. Registry access can be required; active processes retain their version.

Use [project configuration](https://glidermcp.com/tglider/installation) to preload an explicit workspace.
For an existing manual connection, install [only the skill](../../install/README.md#install-skills-without-duplicate-servers).
Remove or disable duplicate registrations before enabling the plugin.
See [fixed, offline, and global alternatives](../../install/README.md#npm-launchers-and-manual-alternatives).

## Claude Code specialist

For substantial tasks, ask: “Use `tglider:typescript-specialist` to implement this TypeScript change and check affected diagnostics.”
The specialist preloads `tglider:tglider-typescript` and inherits tools and client permissions.
Assign one writer and coordinate workspace changes. TGlider HTTP supports one initialized client session, not a shared multi-client connection.
Small tasks can use the skill directly. Codex uses the skill; this agent definition targets Claude Code.

Update the plugin separately from its server package to receive new skills and agent definitions.
In Claude Code, use `claude plugin marketplace update glidermcp` and `claude plugin update tglider@glidermcp`.
In Codex, use `/plugins`. Start a new session after the update.
