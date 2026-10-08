# Duchamp plugin (Claude Code · Codex)

Your agent builds landing pages, images, carousels, launch videos, logo kits and pitch decks from Duchamp templates. A fresh visual judge checks the rendered pixels, and the agent retries until the asset passes. It runs on your own Claude Code or Codex subscription. Duchamp never calls a model and never stores your material.

- `make`: build one or more assets with the build → render → judge → retry loop.
- `launch-kit`: read your product repo, confirm the facts, then make the full launch set.
- `visual-judge` agent (Claude Code): the fresh-context reviewer used by `make`.
- MCP server: `https://duchamp.app/api/mcp` (templates, references, recipes, fonts and motion backgrounds — what duchamp.app lists).

The first connection asks you to sign in with a free Duchamp account (OAuth in your browser, no API key). Fetching a template, recipe or asset counts toward the same daily free limit as copying on duchamp.app; searches and previews do not.

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

Then sign in once:
- Claude Code: run `/mcp`, choose `duchamp`, then Authenticate.
- Codex: `codex mcp login duchamp`.

MCP only (no skills): `claude mcp add --transport http duchamp https://duchamp.app/api/mcp`, then `/mcp` → Authenticate. Cursor: add the URL in MCP settings and press Connect.

License: MIT.
