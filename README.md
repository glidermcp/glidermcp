# GliderMCP

Local tools for coding agents. Start with **Glider** to navigate, analyze, edit, and refactor C#/.NET code through MCP.

[Website](https://glidermcp.com) · [Documentation](https://glidermcp.com/docs) · [Report a problem](https://github.com/glidermcp/glidermcp/issues/new/choose)

## Choose a product

| Product and installation | What it does |
| --- | --- |
| [**Glider**](https://glidermcp.com/glider/installation) | C#/.NET navigation, diagnostics, analysis, edits, and refactors |
| [**TGlider**](https://glidermcp.com/tglider/installation) | TypeScript and JavaScript navigation, analysis, and edits |
| [**Scout**](https://glidermcp.com/scout/installation) | Repository search across files, text, code patterns, and concepts |
| [**GliderTrace**](https://glidermcp.com/glider-trace/installation) | Command execution, tests, and .NET runtime evidence |

Each product connects independently to your MCP client. Choose its installation guide for requirements, connection instructions, and verification.

## Plugins and configuration

This repository provides plugins for Claude Code and Codex:

- [Glider plugin](plugins/glidermcp/README.md)
- [TGlider plugin](plugins/tglider/README.md)
- [Scout plugin](plugins/scout/README.md)
- [GliderTrace plugin](plugins/glider-trace/README.md)

Install the server required by your chosen plugin first. Glider, GliderTrace, and Scout plugins launch an installed executable.
The TGlider plugin launches through `npx` and requires Node.js/npm. It does not require Scout.

After installing Glider, connect its plugin with your chosen client:

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
For another product, replace the plugin name before `@` with `tglider`, `scout`, or `glider-trace`.

For direct MCP configuration, use the [configuration templates](install/README.md). Keep your existing client configuration and add only the servers you need.

## Price and privacy

The products will always be free for non-commercial use. Commercial use is free for now.
Each local server package expires one month after release and must be updated. See [pricing and license terms](https://glidermcp.com/pricing).

Servers process code locally. Your MCP client controls what returned content reaches its model provider.
See [privacy and telemetry](https://glidermcp.com/privacy) for product data policies and controls.

## Support and contributions

[Open an issue](https://github.com/glidermcp/glidermcp/issues/new/choose) and select the affected product or website.
Documentation corrections, configuration fixes, and feature requests are welcome. See [contribution guidance](CONTRIBUTING.md).

This repository contains public support resources, plugins, and configuration templates. It does not contain the product server implementations.
Its [MIT license](LICENSE) covers this repository's assets. Product packages have their own license terms.
