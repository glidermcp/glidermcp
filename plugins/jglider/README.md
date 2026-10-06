# JGlider plugin

Inspect Java source and supported compiler facts with the read-only JGlider preview.
The plugin includes the `jglider-java` skill, a stdio connection, and a Claude Code Java specialist.

## Install and connect

Install Node.js 24 or later and JDK 21 or later, with matching `java` and `javac` major versions.
Check the preview:

```sh
npx -y --ignore-scripts jglider@next --version
```

Follow the [Claude Code or Codex instructions](../../README.md#plugins-and-skills) with `jglider@glidermcp`.
The plugin uses the same launcher without a fixed project path. The agent selects your project after connection.
It starts in safe mode; it grants neither build execution nor dependency downloads.

Use [project configuration](https://glidermcp.com/jglider/installation) when you need `--project` or authorized startup flags.
For an existing connection, install [only the skill](../../install/README.md#install-skills-without-duplicate-servers).
Keep one registration per product. A new process selects the current preview; active processes retain their version.
See [runtime, trust, and compiler limits](skills/jglider-java/references/preview.md).

## Claude Code specialist

For substantial investigations, ask: “Use `jglider:java-specialist` to explain this Java module without executing build files or editing source.”
The specialist preloads `jglider:jglider-java`. It reports unavailable compiler evidence and returns proposed changes to a writer.
It inherits tools and client permissions; its role does not create a sandbox.
Use the skill directly for a small task. Codex uses the skill; this agent definition targets Claude Code.

Update the plugin separately from the preview package when skills or agent definitions change.
In Claude Code, refresh the marketplace and update `jglider@glidermcp`. In Codex, use `/plugins` and start a new session.
