# GliderMCP

Local tools for coding agents. Start with **Glider** to navigate, analyze, edit, and refactor C#/.NET code through MCP.

[Website](https://glidermcp.com) · [Documentation](https://glidermcp.com/docs) · [Report a problem](https://github.com/glidermcp/glidermcp/issues/new/choose)

## Choose a product

| Product and installation | What it does |
| --- | --- |
| [**Glider**](https://glidermcp.com/glider/installation) | C#/.NET navigation, diagnostics, analysis, edits, and refactors |
| [**TGlider**](https://glidermcp.com/tglider/installation) | TypeScript and JavaScript navigation, analysis, and edits |
| [**Scout**](https://glidermcp.com/scout/installation) | Repository search across files, text, code patterns, and concepts |
| [**JGlider preview**](https://glidermcp.com/jglider/installation) | Read-only Java source and compiler evidence |
| [**Keep alpha**](https://github.com/glidermcp/keep#setup) | Shared local documents and project knowledge |
| [**GliderTrace**](https://glidermcp.com/glider-trace/installation) | Command execution, tests, and .NET runtime evidence |

Each product connects independently to your MCP client. Choose its installation guide for requirements, connection instructions, and verification.

## Plugins and skills

This repository provides plugins for Claude Code and Codex:

- [Glider plugin](plugins/glidermcp/README.md)
- [TGlider plugin](plugins/tglider/README.md)
- [Scout plugin](plugins/scout/README.md)
- [JGlider preview plugin](plugins/jglider/README.md)
- [Keep skill-only plugin](plugins/keep/README.md)
- [GliderTrace plugin](plugins/glider-trace/README.md)

We recommend the skill for each product you use. Server plugins bundle skills with their MCP connection.
The Keep plugin supplies only a skill; connect one shared Keep HTTP server separately.
Glider and GliderTrace plugins require the .NET 10 SDK and NuGet access. They select the latest stable package at each server start.
TGlider and Scout plugins use `npx` with `@latest`; a global installation is optional.
JGlider uses `npx -y --ignore-scripts jglider@next` and requires Node.js 24+ and JDK 21+.
TGlider requires Node.js 24+; Scout requires Node.js 18+ and npm optional dependencies. TGlider does not require Scout.

For a new Glider connection, install its plugin with your chosen client:

**Claude Code**

```bash
claude plugin marketplace add glidermcp/glidermcp
claude plugin install glidermcp@glidermcp
```

**Codex CLI**

```bash
codex plugin marketplace add glidermcp/glidermcp
codex plugin add glidermcp@glidermcp
```

Start a new Codex session after installation. See the [Codex plugin guide](https://learn.chatgpt.com/docs/plugins) for details.
For another product, replace the plugin name before `@` with `tglider`, `scout`, `glider-trace`, `jglider`, or `keep`.

Restart the MCP server to select an updated .NET package. A running process keeps its current version.
For Glider, we recommend [project preload](install/README.md#templates-and-project-preload) with an explicit solution path.

For an existing manual MCP connection, install [only the skill](install/README.md#install-skills-without-duplicate-servers) to keep your configuration.
Use the [migration instructions](install/README.md#migrate-an-existing-net-connection) to replace an older global .NET launcher.
Choose one connection per product; remove or disable a manual entry before enabling its server plugin.
Keep is the exception: retain your existing HTTP connection because its skill-only plugin registers no server.

For substantial Claude Code tasks, use the bundled [C# specialist](plugins/glidermcp/README.md#claude-code-specialist)
and [runtime verifier](plugins/glider-trace/README.md#claude-code-specialist).
The [TypeScript specialist](plugins/tglider/README.md#claude-code-specialist) and [Java specialist](plugins/jglider/README.md#claude-code-specialist) provide the same focused workflow.
These agents give the work explicit product guidance. Small tasks can use the skills directly.

## Price and privacy

The products will always be free for non-commercial use. Commercial use is free for now.
Future packages will expire 90 days after release. Existing packages retain their embedded expiry date. Update before expiry. See [pricing and license terms](https://glidermcp.com/pricing).

Servers process code locally. Your MCP client controls what returned content reaches its model provider.
See [privacy and telemetry](https://glidermcp.com/privacy) for product data policies and controls.

## Support and contributions

[Open an issue](https://github.com/glidermcp/glidermcp/issues/new/choose) and select the affected product or website.
Documentation corrections, configuration fixes, and feature requests are welcome. See [contribution guidance](CONTRIBUTING.md).

This repository contains public support resources, plugins, and configuration templates. It does not contain the product server implementations.
Its [MIT license](LICENSE) covers this repository's assets. Product packages have their own license terms.
