# Publishing to the official MCP Registry

`server.json` in this folder describes the remote server for registry.modelcontextprotocol.io (schema 2025-12-11, the latest released version as of 2026-10-10). Publish it from a machine where you can sign in to GitHub as a member of the `duchamp-app` organization: the registry grants `io.github.duchamp-app/*` only to that GitHub login, and it verifies the namespace, not the remote URL.

```sh
brew install mcp-publisher            # or the release tarball from github.com/modelcontextprotocol/registry/releases
cd plugin
mcp-publisher validate server.json    # schema check, no network
mcp-publisher login github            # device-code flow in the browser; sign in as a duchamp-app member
mcp-publisher publish server.json
curl "https://registry.modelcontextprotocol.io/v0.1/servers?search=io.github.duchamp-app/duchamp"
```

Notes:
- Each remote URL can belong to only one server name. `https://duchamp.app/api/mcp` is registered once, under this name.
- To publish a new version, bump `version` in `server.json` (keep it equal to `plugins/duchamp/.claude-plugin/plugin.json`) and run `mcp-publisher publish` again. Version ranges are rejected; use plain semver.
- `_meta` keys other than `io.modelcontextprotocol.registry/publisher-provided` are dropped silently; we send none.
- A later option is DNS or HTTP login (`mcp-publisher login dns --domain=duchamp.app ...`) so the name becomes `app.duchamp/duchamp`; that is a separate server entry, not a rename.
- Glama: `glama.json` here claims the GitHub listing. Copy both files to the root of the public repo `duchamp-app/duchamp`, then run Glama's Claim flow once.
