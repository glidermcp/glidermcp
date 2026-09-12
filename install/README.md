# Direct MCP configuration

These templates connect your client to local GliderMCP servers without a plugin.
Copy only the server entries you need into your existing configuration.

| Product | Command used by the template | Requirement |
| --- | --- | --- |
| Glider | `glider` | .NET 10 SDK and `dotnet tool install --global glider` |
| GliderTrace | `glider-trace` | .NET 10 SDK and `dotnet tool install --global glider-trace` |
| TGlider | `npx -y tglider` | Node.js/npm with `npx` on `PATH` |
| Scout | `scout` | An installed Scout executable on the client's `PATH` |

TGlider does not require Scout. Add Scout independently when you need repository-wide search.
See the [product installation guides](https://glidermcp.com/products) for platform requirements and verification.

## Templates

- [Claude Code project configuration](claude-code/.mcp.json)
- [Codex configuration snippet](codex/config.toml)

Retain existing settings and unrelated servers. These templates do not install the product packages.

The [plugin directory](../plugins) provides an alternative to direct configuration.
