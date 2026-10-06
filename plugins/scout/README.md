# Scout plugin

Search repository files, text, code patterns, and concepts through MCP.
The plugin includes the `scout-search` skill and a local stdio connection.

## Install and connect

Install Node.js 18 or later with npm and `npx` on PATH. Keep npm optional dependencies enabled.
Follow the [Claude Code or Codex instructions](../../README.md#plugins-and-skills) with `scout@glidermcp`.
The plugin launches `npx -y @glidermcp/scout@latest`; no global Scout installation is required.
Restart to select a newer package. Registry access can be required; active processes retain their version.

Scout selects the checkout containing the client's launch directory. Use the [configuration generator](https://glidermcp.com/scout/installation) for an explicit root.
For an existing manual connection, install [only the skill](../../install/README.md#install-skills-without-duplicate-servers).
Remove or disable duplicate registrations before enabling the plugin.

For Homebrew, a native archive, or the standalone installer, retain your manual executable connection and add only the skill.
See [native installation](https://glidermcp.com/scout/setup#installers) and [fixed or offline alternatives](../../install/README.md#npm-launchers-and-manual-alternatives).

Scout runs independently from the language-specific tools. TGlider does not require Scout.
The [tool reference](https://glidermcp.com/scout/tools) explains search modes and result coverage.
The skill covers continuation and recovery; no specialized agent is required.

Update the plugin separately from its server package for new skill guidance.
In Claude Code, use `claude plugin marketplace update glidermcp` and `claude plugin update scout@glidermcp`.
In Codex, use `/plugins`. Start a new session after the update.
