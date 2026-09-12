# Scout plugin

Search repository files, text, code patterns, and concepts through MCP.
This plugin starts Scout as a local stdio server.

## Install the server

Install Scout with Node.js/npm:

```bash
npm install --global @glidermcp/scout
```

For the shell installer or Homebrew, follow the [Scout installation guide](https://glidermcp.com/scout/installation).

The plugin runs the literal command `scout`. Ensure your MCP client can find that executable on `PATH`.
You can also configure an absolute path to the executable.

## Choose the repository

The bundled configuration passes no arguments. Scout starts from the client's working directory.
Use `--root <directory>` when you need an explicit repository path.

Scout runs independently from Glider and TGlider. TGlider does not have a `search_text` tool and does not require Scout.

See the [tool reference](https://glidermcp.com/scout/tools) for search modes, result coverage, and continuation behavior.
