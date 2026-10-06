# Duchamp plugin (Claude Code · Codex)

Your agent builds landing pages, images, carousels and launch videos from Duchamp templates. A fresh visual judge checks the rendered pixels, and the agent retries until the asset passes. It runs on your own Claude Code or Codex subscription. Duchamp never calls a model and never stores your material.

- `make`: build one or more assets with the build → render → judge → retry loop.
- `launch-kit`: read your product repo, confirm the facts, then make the full launch set.
- `visual-judge` agent (Claude Code): the fresh-context reviewer used by `make`.
- MCP server: `https://duchamp.app/api/mcp` (templates, references, recipes).

## Install

Claude Code:
```
/plugin marketplace add duchamp-app/duchamp
/plugin install duchamp@duchamp
```

Codex:
```
codex plugin marketplace add duchamp-app/duchamp
codex plugin add duchamp@duchamp
```

MCP only (no skills): `claude mcp add --transport http duchamp https://duchamp.app/api/mcp`

License: MIT.
