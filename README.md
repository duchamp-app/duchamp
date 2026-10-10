# Duchamp — curated design references for coding agents

**Duchamp** gives Claude Code, Codex and Cursor a reviewed library of landing pages, hero sections, image prompts, motion-video recipes, logo and pitch-deck recipes, fonts and motion backgrounds, through one remote MCP server: `https://duchamp.app/api/mcp`.

Your agent searches the library, inspects the real example (frames, video, design rules), fetches the recipe and renders the asset with code on your own machine. Duchamp never calls a model and never stores your material.

- Website: [duchamp.app](https://duchamp.app) · English overview: [duchamp.app/en](https://duchamp.app/en)
- Connect page: [duchamp.app/mcp](https://duchamp.app/mcp)
- MCP endpoint: `https://duchamp.app/api/mcp` (Streamable HTTP, OAuth 2.1 with a free Duchamp account, no API key)
- For AI readers: [llms.txt](https://duchamp.app/llms.txt) · [llms-full.txt](https://duchamp.app/llms-full.txt)

<a href="https://duchamp.app/ui/pages/saas-understand-clarity"><img src="https://duchamp.app/ui/saas-understand-clarity/hero-poster-desktop.webp" alt="Duchamp UI page: a SaaS landing page hero rebuilt from the recipe" width="100%"></a>

## What you get

| Area | What it is | Browse |
|---|---|---|
| **UI pages & heroes** | 9 finished landing pages and 9 hero sections for a fictional brand, each screen-recorded and shipped with a React + Vite + Tailwind v4 recipe your agent rebuilds from. The recipe starts with STEP 0 questions about your product. | [duchamp.app/ui](https://duchamp.app/ui) · [pages](https://duchamp.app/ui/pages) · [heroes](https://duchamp.app/ui/sections/hero) |
| **Image prompts** | Single images (posters, info cards, carousel covers) as ChatGPT image prompts that draw Korean text correctly, plus five-slide carousel styles. Most also have a coding-agent recipe. | [images](https://duchamp.app/readymade/images) · [carousels](https://duchamp.app/readymade/carousels) |
| **Motion video recipes** | 9:16 motion-graphics videos rendered as silent MP4 with React + HyperFrames. Code, not AI video. | [duchamp.app/readymade/motion](https://duchamp.app/readymade/motion) |
| **Logo & pitch-deck recipes** | A vector logo kit (mark, wordmark, lockups, favicons, OG image) written with Node code, and an investor deck rendered from your repo's facts to PNG slides and PDF. | [logos](https://duchamp.app/readymade/logos) · [decks](https://duchamp.app/readymade/decks) |
| **Fonts & motion backgrounds** | 53 free commercial-use Korean and Latin fonts with install code (web, npm, next/font), and text-free looping background videos (MP4, 4:5 and 9:16). | [fonts](https://duchamp.app/fonts) · [backgrounds](https://duchamp.app/readymade/backgrounds) |

Every template has the same four blanks: `[브랜드명]` (brand name), `[브랜드 색]` (brand color), `[주제]` (topic) and `[넣을 글자]` (the text to put in). The agent fills them only with what you confirmed. Brand facts, numbers, dates, prices and reviews are never invented.

## Connect in one line

```sh
sh -c "$(curl -fsSL https://duchamp.app/connect.sh)"
```

The script finds Claude Code and Codex, registers the server, and opens your browser to sign in with a free Duchamp account. There is no token to copy.

### Manual setup

**Claude Code**

```sh
claude mcp add duchamp --scope user --transport http https://duchamp.app/api/mcp
```

Then in a new session run `/mcp`, choose `duchamp` and press Authenticate.

**Codex**

```sh
codex mcp add duchamp --url https://duchamp.app/api/mcp
codex mcp login duchamp
```

**Cursor** — add to `.cursor/mcp.json`, then press Connect next to `duchamp` in Cursor's MCP settings:

```json
{
  "mcpServers": {
    "duchamp": { "url": "https://duchamp.app/api/mcp" }
  }
}
```

**Any other MCP client** — add a remote server named Duchamp with transport Streamable HTTP and URL `https://duchamp.app/api/mcp`. The first connection opens a browser for OAuth.

## Plugin install (skills + agent)

The plugin adds two skills and a judge agent on top of the MCP server.

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

Then sign in once: Claude Code `/mcp` → `duchamp` → Authenticate; Codex `codex mcp login duchamp`.

- `make`: build one or more assets with the build → render → judge → retry loop.
- `launch-kit`: read your product repo, confirm the facts, then make the full launch set.
- `visual-judge` agent (Claude Code): the fresh-context reviewer used by `make`.

Everything runs on your own Claude Code or Codex subscription.

## Tools

| Tool | What it does | Counts toward daily limit |
|---|---|---|
| `search_image_templates` | Find single images, carousel styles, motion videos, logo kits and pitch decks. Filter by use, ratio or occasion. | No |
| `get_reference` | Inspect one item: example frames, video URLs, authored visual and motion rules, palettes. | No |
| `get_template` | Full prompt or coding-agent recipe for an image, carousel, video, logo or deck. | Yes |
| `search_ui` | Find landing pages and hero sections by service type, intent and style. | No |
| `get_ui` | Section order, conditional variants, STEP 0 questions and the full React + Tailwind recipe. | Yes |
| `search_assets` | Find free fonts (by category and script) and motion backgrounds. | No |
| `get_asset` | Font install code (web, npm, next/font, agent prompt) with license, or background MP4 download URLs. | Yes |

Fetching a template, recipe or asset counts toward the same daily free limit as copying on duchamp.app. Searches and `get_reference` are not counted, so search and inspect before fetching.

## Example prompts

- "Build a landing page for my AI note-taking app using a Duchamp hero. Search Duchamp for SaaS hero sections, show me three, then fetch the recipe for the one you recommend and ask me its STEP 0 questions before you write code."
- "Render a 9:16 launch video for our opening on the date I give you. Find an open-countdown motion template on Duchamp, check the example video, fetch the recipe and render the MP4."
- "Give me a free Korean font with install code for Next.js. Search Duchamp fonts for a geometric sans with Hangul, then fetch the install snippet and license."
- "Make a weekend discount poster. Find a Duchamp poster template for a discount, get the full prompt and text limits, then fill the blanks with my brand color and the exact text I confirm."
- "Write a vector logo kit for a brand called Mono, primary #111111 on #F5F5F0. Fetch the Duchamp logo recipe and run it; I want favicons and an OG image too."

## How it works

1. **Search** one item at a time (`search_*`). Results carry inline previews and a `duchamp_url`.
2. **Inspect** the reference (`get_reference`): real frames, video URLs, the authored design rules.
3. **Fetch** the template (`get_template`, `get_ui`, `get_asset`): a prompt, or a paste-ready recipe.
4. **Render**: your agent runs the recipe with code (React + HyperFrames for video, React + Tailwind for UI, Node for logos) on your machine.
5. **Judge**: with the plugin, a fresh `visual-judge` opens the rendered pixels, scores them against fixed criteria, and the agent retries until it passes.

## Limits & account

- Sign in with a free Duchamp account on the first connection (OAuth in your browser). No API key.
- `get_template`, `get_ui` and `get_asset` share one daily free limit with copying on duchamp.app. If a fetch returns the daily-limit message, the agent relays it and stops.
- A Creator plan ($9/month or $79/year) is planned for higher limits. Until billing opens, everything is free. See [duchamp.app/en/pricing](https://duchamp.app/en/pricing).
- Items appear on their release date. Ids not listed on the site are not available.

## Privacy

Duchamp stores your account and usage records only. Your brand name, colors, copy and the files your agent renders stay on your machine. Duchamp never calls a model on your behalf.

What Duchamp does not do:

- No generation of human faces, human likenesses, face swaps, deepfakes, or synthetic voices. Duchamp does not create synthetic media of real people.
- No voice cloning, no audio or music generation, no adult content, no AI companion or relationship features.
- No display or re-hosting of other people's posts or accounts. Templates carry no original wording, images, or account names. Duchamp is not a downloader.
- No scheduling, publishing, channel connection, performance analytics, or server-side content generation. No engagement automation, and no access to any account the user does not own.
- No aggregation or resale of personal data, and no performance guarantees.

Rights in the original posts stay with their rights holders. To ask for a template to be taken down, write to sean@notique.co with the original URL.

## Links

- [duchamp.app](https://duchamp.app) · [English overview](https://duchamp.app/en) · [Connect](https://duchamp.app/mcp) · [Pricing](https://duchamp.app/en/pricing)
- [llms.txt](https://duchamp.app/llms.txt) · [llms-full.txt](https://duchamp.app/llms-full.txt)
- Company: Notique, Seoul · Contact: sean@notique.co

## License

MIT. See [LICENSE](./LICENSE).
