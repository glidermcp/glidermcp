# Preview requirements and recovery

This reference is for agents and client integrators. Use the installed CLI help and schemas for the selected version.

## Runtime and connection

The recommended launcher is `npx -y --ignore-scripts jglider@next`.
It needs Node.js 24 or later and JDK 21 or later. The JDK must contain `java` and `javac` with matching major versions.
The package contains its server jar and never downloads Java.
Set `JAVA_HOME` to an absolute JDK directory to select it. An empty or invalid value stops startup.
Without `JAVA_HOME`, the launcher searches PATH and standard JDK locations.
On Unix, use an installed UTF-8 locale for Unicode paths; `LC_ALL` overrides `LC_CTYPE` and `LANG`.

The MCP server uses stdio. Add `--project /path/to/java-project` to preload a project, or use `load` after connection.
On Windows, clients that cannot launch npm shims directly need `cmd.exe /d /s /c` around the command.
Use the [installation guide](https://glidermcp.com/jglider/installation) for JSON examples.

The `next` tag selects the current preview. Restart the server to select an updated package.
An exact selector such as `jglider@0.1.0-preview.1` keeps a fixed version. Registry access can be required by npx.
A global installation is optional; use `npm install --global --ignore-scripts jglider@next`, then launch `jglider` with server arguments only.

## Safe mode and trusted imports

Safe mode supplies a reduced static model without build execution. Source reads need no Maven or Gradle installation.
Parser reads use Java 21 syntax. Missing semantic evidence in a static model is an expected limit.

Only an authorized operator should add `--trust-workspace /path/to/java-project` to permit build execution.
Dependency downloads require the separate `--allow-network-fetch /path/to/java-project` grant.
These grants can execute project build logic or retrieve dependencies. They are not required for ordinary parser reads.

Trusted imports need an installed or cached build distribution and the required dependencies.
JGlider does not download build distributions. Use `--maven-home` or `--gradle-home` for an installed distribution.
Use `--gradle-user-home` for its cache; `GRADLE_USER_HOME` takes precedence.
`--allow-build-tool-version-mismatch` permits a different distribution version for the selected root; it grants neither trust nor network access.

## Compiler scope

Use `--analysis-jdk /path/to/jdk-21` to select local release signatures, then restart the server.
Supported analysis uses JDK 21 layouts and Java 8 through 21 release settings.
The analysis JDK option does not grant build trust.

Gradle analysis requires an explicit compile-task release option. Unsupported compiler settings retain coverage gaps.
Maven analysis supports verified MAIN release or source and target settings.
Maven TEST source remains available to parser tools; compiler facts for that scope are unavailable in this preview.
Position columns count UTF-16 units from one. End positions are exclusive.

The preview exposes exact references and direct callers when semantic coverage is available.
It does not expose hierarchy, implementations, outgoing calls, or source edits.
Preserve this distinction when a request needs a broader analysis than the available model can provide.
