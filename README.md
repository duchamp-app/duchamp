# Duchamp — ad research for coding agents

**Duchamp** is an ad-research tool for Claude Code, Codex, and Cursor. It finds the ads curated advertisers have kept running for 90+ days, and you save, compare, and pull the research materials into your agent, on the web or over one remote MCP server: `https://duchamp.app/api/mcp`.

Call `search_ads` first, then `get_ad` or `get_brand_anatomy`. Creative files, ad copy, keyframes, and transcripts need the signed-in account. Lists and counts on the site are public. A reference prompt from `compose_transfer_brief` is a reference. It does not promise the same look or the same outcome. The original ad stays a link (`duchamp_url`). `get_ad` returns timestamped keyframes, the speech transcript, full body copy, the landing URL, and observed numbers, plus a stored AI analysis when one exists (format, hook, 15 emotional drivers, persona, creative targeting, product category, first product appearance — filled by a nightly batch, Foreplay-style). Treat the analysis as a reference and check it against the materials; `search_ads` can filter by its fields. Product facts passed to `compose_transfer_brief` are not stored. Saved items, boards, and watched advertiser pages are stored for the signed-in user. Those tools need a separate per-app opt-in on [duchamp.app/saved/connect](https://duchamp.app/saved/connect).

38 tools are registered: 26 ad-research tools, 6 Performance tools, 2 label tools, and 4 marketing workspace tools. The table below is that set.

Performance (your own Meta ad accounts, read only): connect an ad account on [duchamp.app/performance](https://duchamp.app/performance), group accounts into workspaces (one per client or brand), then allow the app on [duchamp.app/performance/connect](https://duchamp.app/performance/connect). `list_workspaces` → `get_performance_insights` with a `workspace_id` or `account_ids` (group by ad name, copy, creative, landing page, headline, campaign, ad set, account, workspace or Audience Segment — New Audience, Engaged Audience, Existing Customer, sorted by the workspace's campaign-name rules or Duchamp's defaults; by a Meta breakdown at account level — country, age, gender, platform, placement, Meta customer segment; or compare rule groups) → `get_performance_timeseries` / `get_performance_creative` / `get_performance_ad`. Scores (hook, watch, click, convert, viral) are percentiles among every ad (or group) of the workspace in the same period — they are not an industry benchmark. Numbers use the ad set attribution setting and impression-time reporting unless you pick a window with `attr_click` (1d, 7d, 28d) and `attr_view` (none, 1d) — Meta no longer returns 7-day or 28-day view. Besides spend, ROAS, CPA, CTR and video rates there are website and Shop purchases, landing page views, add to cart, checkout, leads, drop-off rates and your custom conversions (`cc_<id>_total`, `_value`, `_cost`, `_roas`, listed by `get_performance_metrics` with a `workspace_id`).

Team boards (read only): `list_boards` with `scope: "team"` or `"all"` adds the team's boards (marked `scope: "team"`); pass `board_scope: "team"` to `get_board`, `export_board` and `compose_transfer_brief`, and use `list_saved` with `scope: "team"` for teammates' shared ads. This needs a separate team access per app on [duchamp.app/saved/connect](https://duchamp.app/saved/connect), and only teammates who turned on AI sharing appear — no notes, saved titles, link items or comments.

- Website: [duchamp.app](https://duchamp.app) · English overview: [duchamp.app/en](https://duchamp.app/en)
- Connect page: [duchamp.app/mcp](https://duchamp.app/mcp)
- MCP endpoint: `https://duchamp.app/api/mcp` (Streamable HTTP, OAuth 2.1 with a free Duchamp account, no API key)
- For AI readers: [llms.txt](https://duchamp.app/llms.txt) · [llms-full.txt](https://duchamp.app/llms-full.txt)

## What you get

| Area | What it is | Browse |
|---|---|---|
| **Explore** | Active ads that curated advertisers have kept running for 90+ days, collected daily (`all` shows every observed ad). Lists and counts are public. | [Explore](https://duchamp.app/ads) |
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

The plugin adds eleven skills (nine active, two paused) and a judge agent on top of the MCP server.

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
- Research skill packs — workflows the agent runs end to end. Say "use the <name> skill on …":
  - `evergreen-hunt`: ads that ran 90+ days in a niche, clustered by hook, with transcripts for the strongest.
  - `weekly-competitor-sweep`: your watched advertisers' last 7 days as a Slack-ready Monday brief.
  - `creative-test-autopsy`: what one advertiser is testing now vs what it keeps scaling.
  - `board-to-brief`: a named board turned into a production brief with three angles grounded in the saved ads.
  - `hook-mining`: a hook bank for a niche and format, ranked by how long the ads ran.
  - `domain-war-room`: a competitor's domain turned into its ads, hooks, landing pages, paid partners and attack angles.
  - `angle-theft-detector`: your winning creatives from Performance (your own Meta ad accounts) matched to lookalike ads from other advertisers, with how crowded each angle is.
- `connect-extension`: install and connect the Duchamp Chrome extension (opens the Chrome Web Store page in your Chrome, you click Add and Connect), then confirm the first save from the Meta Ad Library.
- `make` and `launch-kit`: paused. They call tools that are not registered. Do not call those tools. Use `ads-research`.
- `visual-judge` agent (Claude Code): a fresh-context reviewer. The `make` skill that used to call it is paused.

Everything runs on your own Claude Code or Codex subscription.

## Tools

| Tool | What it does | Counts toward daily limit |
|---|---|---|
| `search_reference_labels` | Search Duchamp's written breakdowns of long-running ads (advertiser, days running, format, hook, evidence, why it was picked, ad-library link). No creative files. | No |
| `get_reference_label` | One label in full: beats, layers, and transfer rules (what to swap, what needs your own facts, what never to invent). A research reference, not a reproduction. | No |
| `search_ads` | Search observed ads. By default only active ads running 90+ days; `all: true` shows every observed ad. Days running, survival, rank (position in that advertiser's own Meta impression-sorted list — not comparable across advertisers, not performance), signed-in ad copy, and creative URLs on Duchamp's mirror. Inline images are low-resolution previews. | No |
| `get_ad` | One observed ad with research materials: facts, history, full body copy, landing URL, variants, timestamped keyframe URLs (up to 16), the timestamped speech transcript, a label summary when one exists, up to three other long-running ads from the same advertiser, and creative URLs on Duchamp's mirror. | No |
| `get_ad_duplicates` | Every visible ad that uses the same image or video files as one ad: `same_creative` (all files identical, in order) and `same_asset` (shares at least one file, e.g. one carousel card). Byte-identical files only. `search_ads` with `group_by: "exact_creative"` collapses them into one item per creative. | No |
| `search_ad_hooks` | Hook bank: opening lines grouped across ads with distinct ad counts, active counts and days running. `source: "speech"` is the first sentence spoken in the video (with the second it starts), `source: "copy"` the first line of the ad body. Filter by advertiser page, niche or text. | No |
| `get_brand_anatomy` | One advertiser page: mix, long-running ads, positions in its own impression-sorted list, hooks, landings, CTAs. | No |
| `compare_brands` | Up to four advertiser pages side by side. No impression counts and no revenue. | No |
| `get_connection_status` | Call first in a new session. Confirms sign-in and whether this app may use the saved library and the marketing workspace, with the page to allow it and the next steps. | No |
| `get_extension_status` | Whether this user's Duchamp Chrome extension is connected (count, version, last used), and how to connect it. Poll it while guiding an install. | No |
| `get_brand_landings` | One advertiser's observed ads grouped by landing page (tracking parameters removed, product and app ids kept), with ad and active counts. | No |
| `get_brand_partners` | Pages Meta shows as paid partners ("Paid partnership with …") on one advertiser's observed ads, with ad and active counts, first and last seen dates and a Meta ad library link; also other tracked advertisers that show this page as partner, and up to 24 partnership ads (`ads`: partner page, days running, format, title, Duchamp link; running first, then longest running, not a performance rank). Creator names and deal terms are not in the data. | No |
| `get_creative_velocity` | One advertiser's ads launched, retired and active per day over 7, 14, 30 or 60 days (net change, peak day, average per day) and a timeline of the ads that ran, same creative optionally grouped, plus creative tests (2+ ads started the same day in the last 60 days: N/M still running and the ad that outlasted the rest — days running, not a rank), plus day signals with the change vs last week (ads running now for 90+ days, and creatives adding 3+ variants among ads started in the last 14 days — the advertiser turned on several versions, not a rise in reach). Days before Duchamp first observed the page are a lower bound. | No |
| `find_advertisers_by_domain` | Tracked advertiser pages whose ads send people to a domain or URL (subdomains included). A matching domain is not proof of ownership. | No |
| `get_collection_status` | How fresh and deep the observations are per tracked page (last seen, days observed, days ranked, last attempt) and pages collected per day for 7 days. Check it before trusting rank changes. | No |
| `list_watched_brands` | Advertiser pages this user watches, including the one marked as their own. | No |
| `list_saved` | This user's saved ads, labels, and https URLs, each with its note, tags and rating. Filter by `tag` or `min_rating`. `scope: "team"` lists teammates' shared ads and labels (paged). | No |
| `list_boards` | This user's boards. `scope: "team"`/`"all"` adds team boards. | No |
| `get_board` | One board. Ad items include observation numbers, signed-in copy, and creative URLs on Duchamp's mirror. Label items include a summary and no creative files. | No |
| `watch_brand` | Watch an advertiser page (page id or advertiser-page URL). `is_mine` marks the user's own page. | No |
| `unwatch_brand` | Stop watching an advertiser page. | No |
| `save_to_library` | Save an ad, a reference label, or an https URL, optionally with a note, tags and a 1–5 star rating. Notes and tags are not written to the call log. | No |
| `create_board` | Create a board. | No |
| `add_to_board` | Add saved item ids to a board. | No |
| `update_saved` | Change one saved item's title, note, tags (replaces all) or 1–5 star rating (`null` clears it) — the same fields as the saved card on duchamp.app. | No |
| `remove_from_board` | Remove saved item ids from a board. The items stay in the library. | No |
| `export_board` | One board as JSON (default) or CSV: a row per item with note, tags, rating, observed numbers, signed-in ad copy and links. No creative file URLs. CSV cells that could run as spreadsheet formulas are prefixed with `'`. Also gives a signed-in download link. | No |
| `compose_transfer_brief` | A reference prompt from ads, labels, a page, or a board, plus facts from the user's own context. A reference only, not a guaranteed reproduction. Those facts are not stored and not logged. | No |
| `list_workspaces` | The user's workspaces (groups of ad accounts per client or brand) with their enabled ad accounts (currency, timezone, synced coverage), unassigned accounts and saved reports. Needs a separate opt-in on the Performance connect page. | No |
| `get_performance_metrics` | Every Performance metric id, formula and unit, the score rules, grouping dimensions and attribution windows; with a `workspace_id` or `account_ids`, that scope's custom conversions too. | No |
| `get_performance_insights` | Top Performing (group by one dimension, including Meta breakdowns) or Comparison (rule groups) report with metrics, scores and net results, for a chosen attribution window, plus creative test targets: New Launched, Scaling, Winners and Losers counts against the workspace's own thresholds (set on the Creative Tests page), new creatives and the prior period. `preset` opens one of five sample reports (`creative_fatigue` with CPC and ROAS change from the prior period, `landing_pages`, `product_comparison` from ad-name tokens, `image_vs_video`, `video_analysis`) — preset groupings and sorting, not a verdict. | No |
| `get_performance_timeseries` | Daily, weekly or monthly values for a workspace, the accounts, or one report row (including one breakdown value), for a chosen attribution window. | No |
| `get_performance_creative` | One creative group: metrics, scores and the ads using it with their copy and assets. | No |
| `get_performance_ad` | One ad: assets, every metric and score, daily trend. | No |
| `list_marketing_projects` | List the user's marketing projects. Needs a separate opt-in on the marketing connect page. | No |
| `get_marketing_project` | Read one marketing project. | No |
| `create_marketing_project` | Create a marketing project. | No |
| `update_marketing_project` | Update a marketing project. Read the latest version first. | No |

The ad research tools are not on a daily fetch meter. 38 tools are in the table.

## Example prompts

First prompts (replace the [ ] parts):

- Brand snapshot — "Using the Duchamp MCP, look up [advertiser name or homepage] (find_advertisers_by_domain for a homepage, or facets.pages of search_ads for a name). Show the 6 newest live ads with search_ads (page_id, status active, sort recent, all true) and link each duchamp_url. Then 3 bullets: formats, offers, the most common angle."
- UGC script rewrite — "search_ads for live selfie-style video ads in [niche], pick the longest-running, pull its transcript with get_ad and rewrite it as a 30-second script for [your product]. Keep the structure, change the words."
- Offer scan — "search_ads for live ads in [niche] and list every offer (discounts, bundles, free shipping, guarantees, trials), ranked by how often they appear, with one ad for each of the top 3."
- Format mix check — "Count live ads in [niche] by medium (video, image, carousel) with search_ads, show the longest-running example of each and the under-used format."
- Ad copy swipe — "Find 10 image ads in [niche] that ran 90+ days with search_ads, pull their copy with get_ad and rewrite the best 5 for [your product]."

More:

- "Find long-running ads in my niche with search_ads. List the advertiser, days running, and duchamp_url. Do not invent impression counts or revenue."
- "Open this ad with get_ad, then get_brand_anatomy for the advertiser. Read the keyframes and transcript, then tell me the hook and the offer. Link duchamp_url."
- "Watch my page with watch_brand and is_mine true, watch a competitor, then compare_brands. At most four pages."

## How it works

1. **Check the connection** with `get_connection_status`. If the saved library is not allowed for this app, open the link it returns before saving.
2. **Search** with `search_ads`.
3. **Open** one ad with `get_ad`, or one advertiser with `get_brand_anatomy`.
4. **Compare** with `compare_brands` (at most four page ids) or save with the library tools after the saved-library opt-in.
5. **Hand off** with `compose_transfer_brief` when the user asks. It is a reference prompt. It does not promise the same ad.

## Limits & account

- Sign in with a free Duchamp account on the first connection (OAuth in your browser). No API key.
- The ad-research tools are not counted toward a daily limit.
- Pro is listed at $19/month or $190/year (5 competitor pages of your own collected daily, unlimited MCP). Once payments open, Free keeps search unlimited and opens 10 ads a day over MCP. Payments are not open yet, so everything is free with no limits for now. See [duchamp.app/en/pricing](https://duchamp.app/en/pricing).
- Items appear on their release date. Ids not listed on the site are not available.

## Privacy

Duchamp stores your account, usage records, saved items, boards, and watched advertiser pages. If you connect an ad account to Performance, Duchamp stores that account's ad metadata and daily performance numbers (and an encrypted read-only access token) for your account only — never in the public library or a team — and deletes them when you turn the account off, disconnect, or remove the app in Facebook settings. Performance numbers go to an AI app only after you allow that app on the Performance connect page. Product facts passed to `compose_transfer_brief` are not stored. Your rendered files stay on your machine. Duchamp does not call a model on your requests. Speech transcripts are made once per video when it is collected. Duchamp does not reproduce the ad. Signed-in ad research can include creative URLs on Duchamp's mirror. Reference labels do not include creative files.

What Duchamp does not do:

- No generation of human faces, human likenesses, face swaps, deepfakes, or synthetic voices. Duchamp does not create synthetic media of real people.
- No voice cloning, no audio or music generation, no adult content, no AI companion or relationship features.
- Templates: no display or re-hosting of other people's posts or accounts. Templates carry no original wording, images, or account names.
- Ads research: signed-in callers get creative URLs on Duchamp's mirror. Inline images in tool results are low-resolution previews for the agent. When showing the user, link `duchamp_url`. Reference labels still do not include creative files. Duchamp does not reproduce the ad.
- No scheduling or publishing. Performance reads only ad accounts you connect yourself (Meta, read only, ads_read) for performance reports. No server-side content generation. No engagement automation, and no access to any account the user does not own.
- No aggregation or resale of personal data, and no performance guarantees.

Rights in the original posts stay with their rights holders. To ask for a template to be taken down, write to hello@duchamp.app with the original URL.

## Links

- [duchamp.app](https://duchamp.app) · [English overview](https://duchamp.app/en) · [Connect](https://duchamp.app/mcp) · [Pricing](https://duchamp.app/en/pricing)
- [llms.txt](https://duchamp.app/llms.txt) · [llms-full.txt](https://duchamp.app/llms-full.txt)
- Company: Notique, Seoul · Contact: hello@duchamp.app

## License

MIT. See [LICENSE](./LICENSE).
