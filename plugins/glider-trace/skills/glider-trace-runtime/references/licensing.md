# Native license status and recovery

Read this reference for activation, expiry, license notices, or credential storage.


GliderTrace 1.4.0 supports native licenses and a sanitized `license` object in the existing status tool.
Verify the installed build's schemas and CLI help before using this behavior.
Activation requires an eligible paid account, an assigned developer seat, and current account and activation-service availability.
Published package support does not establish that public sales are open.
Status reads local state without a network request. Never request or publish credentials, complete grants, or profile contents.

Production is the default for the CLI and MCP startup. Unknown environment values are rejected.
Use `glider-trace auth login` only when the user requests activation.
Use `glider-trace license status --json` for local status and `glider-trace license refresh` for explicit renewal.
Select sandbox explicitly with `--environment sandbox` on each CLI command or `GLIDER_LICENSE_ENVIRONMENT=sandbox` for MCP startup.

Sandbox `valid` describes the grant; `commercialRights` remains false. Account login alone establishes no production commercial rights.
Production verifies approved public keys compiled into the build. Editable cached keys cannot add production trust.

Read `licenseType` and `notice` with `pricingUrl` and `termsUrl`. `valid` describes the signed grant, not free-use permission.
`commercial` requires verified production rights and includes `paidThrough`. `sandbox` identifies a test grant without production rights.
`free` identifies the free license terms for the installed version.
Read the packaged LICENSE for permitted use, evaluation periods, and commercial requirements. These fields supply no credentials or account identifiers.

Each environment has a separate default profile under `~/.glidermcp/licenses/` and a separate vault namespace.
Use `--profile-directory PATH` for an isolated CLI profile and `GLIDER_LICENSE_PROFILE_DIRECTORY` for the corresponding MCP process.
Keep production and sandbox in separate directories. Glider and GliderTrace share the selected profile for the OS user.
`glider-trace auth logout` clears local rights for both products that select that profile before remote cleanup.

Follow `action` for recovery. If `revocationPending` is true, repeat logout after service and credential access recover.

A valid grant works offline through its signed paid-period end, without daily renewal or a vault read.
Only a valid production commercial grant permits an expired package to start.
A trusted expired production grant requires successful bounded renewal, or the package needs an update.
Sandbox grants do not bypass package age.
Renewal cannot extend the paid period without a new verified grant. MCP startup never opens a browser or requests input.

The credential vault is the default. Current macOS app signatures can prevent access from the other product.
Windows and Linux native vault checks remain open.
Use `glider-trace auth login --credential-store file` only with the user's explicit consent to private file storage.
The file store uses OS-user permissions, without vault encryption. Do not silently change the credential store or weaken OS security settings.
