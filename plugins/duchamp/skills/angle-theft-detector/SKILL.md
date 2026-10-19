---
name: angle-theft-detector
description: Match the user's own winning ads to lookalike ads from other advertisers and tell how crowded each angle is. Use when the user asks if competitors run the same angle as their winners.
---

# Angle theft detector

Duchamp tools: `list_workspaces`, `get_performance_insights`, `get_performance_creative`, `get_performance_ad`, `find_advertisers_by_domain`, `search_ads`, `search_ad_hooks`, `get_ad`. If they are not available, stop and tell the user to connect Duchamp first at https://duchamp.app/mcp.

The winners come from the user's own Meta ad accounts (Performance). The lookalikes come from ads Duchamp observes on tracked advertiser pages, not the whole Meta Ad Library.

## Steps

1. `list_workspaces`. On `ACCESS_REQUIRED`, give the user its `access_url` (https://duchamp.app/performance/connect) and stop — do not retry. Use the workspace the user named; if there are several and they did not name one, list them and ask. Performance calls take one currency at a time: if the workspace's accounts share one currency, pass `workspace_id`; if not, ask which currency (or run once per currency) and pass that currency's enabled account ids as `account_ids`. Use the same scope and `range` in the insights and creative calls below (`get_performance_ad` takes one ad's `account_id` and `ad_id` instead). If a result says `complete: false`, the range is still syncing; if it says `ads_truncated: true`, the numbers cover only part of the ads — say so either way.
2. Find the winners: `get_performance_insights` with the scope, `range: 30`, `group_by: "creative"`, `sort: "spend"`, `limit: 25`, `metrics: ["spend", "purchases", "roas", "cpa", "link_ctr", "hook_rate"]` (rows and `net_results` carry only the metrics you ask for). `range` takes only 7, 14, 30, 90 or 365 (last N days up to yesterday); for any other period the user asks for, use the nearest of these and say so. Pick by the first branch that applies, and say which one:
   - **Purchases**: if `net_results.sum.purchases` is above 0, a winner has at least 1 purchase and a `roas` at or above `net_results.weighted_avg.roas`.
   - **No purchases**: a winner has a `link_ctr` at or above `net_results.weighted_avg.link_ctr`; for video, also a `hook_rate` at or above its weighted average.
   A null metric never qualifies. Among the 25 rows by spend, take at most 5 winners, highest spend first. If none qualify, say there is not enough data for this range and stop.
3. For each winner, `get_performance_creative` with the row's `key` as `row_key` — headline, copy, landing and assets of its ads (the top 10 by spend; when the row's `ads` is larger, the rest are in the numbers but not listed). A creative group is one image, one video, or one carousel or dynamic asset bundle, and its ads can carry different copy. If they share one angle, use it. If they do not, take the angle of its top-spend ad — the first entry of the row's `ad_refs`, matched to `ads` by `account_id` and `ad_id` (`ads` is not in spend order) — and mark the row "mixed copy" — the numbers belong to the whole group, not to that ad's copy. Use `get_performance_ad` only for one ad's daily numbers.
4. Write each angle in one line: the promise, the pain or desire it speaks to, the proof it uses and the offer. Then pick 2–4 short search phrases from its own wording — a product noun, the core claim, the offer — in the ad's language. Copy search is a substring match, so 1–3 words per phrase find more than a full sentence. Also note its hook type (반감고백, 질문, 숫자, 공감상황, 비교, 약속, or 기타) and evidence type (전후, 숫자, 기능, 계기, 언론, 할인, 이유, 후기, 비교, 시연, 권위, or 없음).
5. Find the user's own pages: `find_advertisers_by_domain` with the winners' landing domain. A domain match is not proof of ownership — resellers and affiliates send traffic to the same domain. If `shared_host` is true (an app store, marketplace or social site), the matches say nothing about the user — skip them and ask for the user's page ids instead. Otherwise show the pages it returns and ask the user which are theirs. Pages the user confirms are left out. Pages the user does not answer for are **unresolved**: show their ads as "links to your domain", but do not count them as competitors.
6. Collect candidates per angle, without opening them yet:
   - `search_ads` with `q` set to each phrase, `all: true`, `sort: "survival"`, `limit: 24` — not grouped, because ads that reuse one creative can carry different copy. Add `niche` when the user gave one. If `has_next_page` is true, read `page: 2` too, and no further.
   - `search_ad_hooks` with `q` set to the strongest phrase, once `source: "copy"` and once `source: "speech"`, `sort: "most_ads"`, `limit: 10`. A hook group counts `pages` without naming them and may include the user's own page; its `examples` carry only an ad id and `duchamp_url`, so the advertiser and days running are known only after `get_ad`. Take at most 3 examples per angle, from the groups with the most ads first; each open counts toward the 12 in step 7.
   - When the hook type is not 기타 and the evidence type is not 없음, one more `search_ads` with `hook`, `evidence` and `niche`, `all: true`, `sort: "survival"`, `limit: 24`, first page only — this only reaches ads that already have an analysis.
   Put every result in one list per angle, remove repeated ad ids, and drop ads from the user's confirmed pages (hook examples once opened).
7. Confirm before you count: open candidates from that list with `get_ad` (body copy, transcript, keyframes, landing), one advertiser (`page_id`) at a time — the longest-running candidate of each advertiser first (hook examples have no advertiser until opened: open them in the order you took them, then group them with the rest). A lookalike makes the same promise to the same pain, with the same kind of proof or offer. Sharing a product word is not enough. Keep going until 5 competitor advertisers are confirmed or you have opened 12 candidates for the angle, then stop. Say how many you opened and how many you dropped. The searches are capped (two pages per phrase, three hook examples, twelve opens), so every advertiser count is a confirmed lower bound from a sample — say so, and say which cap you hit.
8. Rate each angle by distinct confirmed competitor advertisers (`page_id`) — not the user's pages and not unresolved pages. "Long-running" means a confirmed lookalike with `activity: "active"` and `days_running` of 30 or more; ended ads, `activity: "unknown"` and empty `days_running` count as advertisers but not as long-running. Take the first rating that applies:
   - **Crowded**: 5+ advertisers, or 2+ advertisers with a long-running lookalike.
   - **Contested**: 2–4 advertisers, or 1 advertiser with a long-running lookalike.
   - **Open**: 0 or 1 advertiser, none long-running.
   If unresolved pages also run the angle, say the rating could change once the user says whose pages they are.

## Output

- Header: workspace, range, currency, the metric that picked the winners, how many candidates you opened and how many you kept.
- One row per angle: ad name or creative, spend, ROAS or CPA, link CTR, hook rate (video), the angle in one line, confirmed advertisers (active / total), the longest-running lookalike with its days, and the rating. Link each lookalike with `duchamp_url`.
- For each Contested or Crowded angle: what the lookalikes share with the winner, and 2–3 ways the user could make it theirs — a different proof, audience, format or offer that the lookalikes do not use. Mark these as hypotheses.
- Open angles: say they look open in the searched sample of observed pages only.

## Rules

- The winners' numbers are the user's private ad-account data. Show them only to the user; do not save them to a board or put them in anything shared.
- Performance numbers use each ad set's attribution setting. Competitor ads have no spend, results or impressions in Duchamp — say "keeps running" or "active", not "winning" or "profitable".
- "Theft" is a working name: a lookalike shows the same angle, not that anyone copied anyone. Do not claim copying.
- `status: "active"` and `all: true` lists include ads with `activity: "unknown"` (not seen on the last partial collection). Say an ad is running only when `activity` is `"active"`; for `"unknown"`, give its `last_seen_on`.
- No lookalike found means none in the searched sample of pages Duchamp tracks, not none on Meta.
- Link every competitor ad with `duchamp_url`. The user's own ads have no Duchamp link — name them by ad name and ad id.

Activation prompt: `Use the angle-theft-detector skill on [workspace].`
