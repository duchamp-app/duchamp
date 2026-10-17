# Duchamp — ad research for coding agents

**Duchamp** is an ad-research tool for Claude Code, Codex, and Cursor. It finds the ads that are working for curated advertisers (running 90+ days, or in today's top 10 impression rank), and you save, compare, and pull the research materials into your agent, on the web or over one remote MCP server: `https://duchamp.app/api/mcp`.

Call `search_ads` first, then `get_ad` or `get_brand_anatomy`. Creative files, ad copy, keyframes, and transcripts need the signed-in account. Lists and counts on the site are public. A reference prompt from `compose_transfer_brief` is a reference. It does not promise the same look or the same outcome. The original ad stays a link (`duchamp_url`). Duchamp provides materials, not analysis: `get_ad` returns timestamped keyframes, the speech transcript, full body copy, the landing URL, and observed numbers, and your agent does the analysis. Product facts passed to `compose_transfer_brief` are not stored. Saved items, boards, and watched advertiser pages are stored for the signed-in user. Those tools need a separate per-app opt-in on [duchamp.app/saved/connect](https://duchamp.app/saved/connect).

20 tools are registered: 14 ad-research tools, 2 label tools, and 4 marketing workspace tools. The table below is that set.

- Website: [duchamp.app](https://duchamp.app) · English overview: [duchamp.app/en](https://duchamp.app/en)
- Connect page: [duchamp.app/mcp](https://duchamp.app/mcp)
- MCP endpoint: `https://duchamp.app/api/mcp` (Streamable HTTP, OAuth 2.1 with a free Duchamp account, no API key)
- For AI readers: [llms.txt](https://duchamp.app/llms.txt) · [llms-full.txt](https://duchamp.app/llms-full.txt)

## What you get

| Area | What it is | Browse |
|---|---|---|
| **Explore** | Ads that are working for curated advertisers, collected daily: running 90+ days or in today's top 10 impression rank (`all` shows every observed ad). Lists and counts are public. | [Explore](https://duchamp.app/ads) |
| **Brands** | One advertiser's breakdown. Watch a page, including a competitor. A page you add yourself is limited to 10. | [Brands](https://duchamp.app/ads/brands) |
| **Compare** | Up to four advertiser pages, from public records. No impression counts and no revenue. | [Compare](https://duchamp.app/ads/compare) |
| **Breakdowns** | Ads broken into labels. A research reference. The label does not include the creative file. | [Breakdowns](https://duchamp.app/readymade/references) |
| **Saved and Brief** | Saved ads, labels, and links, plus a reference prompt. The prompt does not promise the same ad. | [Saved](https://duchamp.app/saved) · [Brief](https://duchamp.app/transfer) |

Do not invent numbers, dates, prices, or reviews. Transcripts are raw speech-to-text and can mishear names. A person should check what the agent concludes.

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

The plugin adds three skills and a judge agent on top of the MCP server.

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

- `ads-research`: find working ads, open one ad with its materials, break down an advertiser, compare your page, and keep a saved library. This is the skill to use.
- `make` and `launch-kit`: paused. They call tools that are not registered. Do not call those tools. Use `ads-research`.
- `visual-judge` agent (Claude Code): a fresh-context reviewer. The `make` skill that used to call it is paused.

Everything runs on your own Claude Code or Codex subscription.

## Tools

| Tool | What it does | Counts toward daily limit |
|---|---|---|
| `search_reference_labels` | Search Duchamp's written breakdowns of market-tested ads (advertiser, days running, format, hook, evidence, why it was picked, ad-library link). No creative files. | No |
| `get_reference_label` | One label in full: beats, layers, and transfer rules (what to swap, what needs your own facts, what never to invent). A research reference, not a reproduction. | No |
| `search_ads` | Search observed ads. By default only ads that are working (active and running 90+ days or in the top 10 impression rank); `all: true` shows every observed ad. Days running, survival, rank, signed-in ad copy, and creative URLs on Duchamp's mirror. Inline images are low-resolution previews. | No |
| `get_ad` | One observed ad with research materials: facts, history, full body copy, landing URL, variants, timestamped keyframe URLs (up to 16), the timestamped speech transcript, a label summary when one exists, up to three other long-running ads from the same advertiser, and creative URLs on Duchamp's mirror. | No |
| `get_brand_anatomy` | One advertiser page: mix, long-running ads, rankings, hooks, landings, CTAs. | No |
| `compare_brands` | Up to four advertiser pages side by side. No impression counts and no revenue. | No |
| `list_watched_brands` | Advertiser pages this user watches, including the one marked as their own. | No |
| `list_saved` | This user's saved ads, labels, and https URLs. | No |
| `list_boards` | This user's boards. | No |
| `get_board` | One board. Ad items include observation numbers, signed-in copy, and creative URLs on Duchamp's mirror. Label items include a summary and no creative files. | No |
| `watch_brand` | Watch an advertiser page (page id or advertiser-page URL). `is_mine` marks the user's own page. | No |
| `unwatch_brand` | Stop watching an advertiser page. | No |
| `save_to_library` | Save an ad, a reference label, or an https URL. Notes and tags are not written to the call log. | No |
| `create_board` | Create a board. | No |
| `add_to_board` | Add saved item ids to a board. | No |
| `compose_transfer_brief` | A reference prompt from ads, labels, a page, or a board, plus facts from the user's own context. A reference only, not a guaranteed reproduction. Those facts are not stored and not logged. | No |
| `list_marketing_projects` | List the user's marketing projects. Needs a separate opt-in on the marketing connect page. | No |
| `get_marketing_project` | Read one marketing project. | No |
| `create_marketing_project` | Create a marketing project. | No |
| `update_marketing_project` | Update a marketing project. Read the latest version first. | No |

The ad research tools are not on a daily fetch meter. 20 tools are in the table.

## Example prompts

- "Find long-running ads in my niche with search_ads. List the advertiser, days running, impression rank, and duchamp_url. Do not invent impression counts or revenue."
- "Open this ad with get_ad, then get_brand_anatomy for the advertiser. Read the keyframes and transcript, then tell me the hook and the offer. Link duchamp_url."
- "Watch my page with watch_brand and is_mine true, watch a competitor, then compare_brands. At most four pages."

## How it works

1. **Search** with `search_ads`.
2. **Open** one ad with `get_ad`, or one advertiser with `get_brand_anatomy`.
3. **Compare** with `compare_brands` (at most four page ids) or save with the library tools after the saved-library opt-in.
4. **Hand off** with `compose_transfer_brief` when the user asks. It is a reference prompt. It does not promise the same ad.

## Limits & account

- Sign in with a free Duchamp account on the first connection (OAuth in your browser). No API key.
- The ad-research tools are not counted toward a daily limit.
- A Creator plan ($9/month or $79/year) is listed on the pricing page. Payments are not open yet. See [duchamp.app/en/pricing](https://duchamp.app/en/pricing).
- Items appear on their release date. Ids not listed on the site are not available.

## Privacy

Duchamp stores your account, usage records, saved items, boards, and watched advertiser pages. Product facts passed to `compose_transfer_brief` are not stored. Your rendered files stay on your machine. Duchamp does not call a model on your requests. Speech transcripts are made once per video when it is collected. Duchamp does not reproduce the ad. Signed-in ad research can include creative URLs on Duchamp's mirror. Reference labels do not include creative files.

What Duchamp does not do:

- No generation of human faces, human likenesses, face swaps, deepfakes, or synthetic voices. Duchamp does not create synthetic media of real people.
- No voice cloning, no audio or music generation, no adult content, no AI companion or relationship features.
- Templates: no display or re-hosting of other people's posts or accounts. Templates carry no original wording, images, or account names.
- Ads research: signed-in callers get creative URLs on Duchamp's mirror. Inline images in tool results are low-resolution previews for the agent. When showing the user, link `duchamp_url`. Reference labels still do not include creative files. Duchamp does not reproduce the ad.
- No scheduling, publishing, channel connection, or performance analytics. No server-side content generation. No engagement automation, and no access to any account the user does not own.
- No aggregation or resale of personal data, and no performance guarantees.

Rights in the original posts stay with their rights holders. To ask for a template to be taken down, write to sean@notique.co with the original URL.

## Links

- [duchamp.app](https://duchamp.app) · [English overview](https://duchamp.app/en) · [Connect](https://duchamp.app/mcp) · [Pricing](https://duchamp.app/en/pricing)
- [llms.txt](https://duchamp.app/llms.txt) · [llms-full.txt](https://duchamp.app/llms-full.txt)
- Company: Notique, Seoul · Contact: sean@notique.co

## License

MIT. See [LICENSE](./LICENSE).
