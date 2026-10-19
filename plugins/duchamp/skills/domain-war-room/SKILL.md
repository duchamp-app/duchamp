---
name: domain-war-room
description: Turn a competitor's domain into its ads, hooks, landing pages, paid partners and attack angles. Use when the user gives a competitor homepage or URL.
---

# Domain war room

Duchamp tools: `find_advertisers_by_domain`, `get_brand_anatomy`, `search_ads`, `search_ad_hooks`, `get_brand_landings`, `get_brand_partners`, `get_collection_status`. If they are not available, stop and tell the user to connect Duchamp first at https://duchamp.app/mcp.

## Steps

1. `find_advertisers_by_domain` with the domain or URL. A match is not proof of ownership — app stores, social profiles and marketplaces are shared (`shared_host`). If `shared_host` is true or several advertisers come back, show them and ask which to use. If none come back, say no observed ads in Duchamp link to this domain (an advertiser may still be tracked if it sends traffic elsewhere, such as an app store), and ask for its advertiser page id or ad library URL to continue with step 2.
2. For each chosen advertiser (at most 3):
   - `get_collection_status` — observed days, so you know how much history backs the claims.
   - `get_brand_anatomy` — long runners, media mix, test groups, rankings, CTAs.
   - `search_ads` with `page_id`, `status: "active"`, `all: true`, `sort: "survival"`, `limit: 10` — what it keeps running.
   - `search_ad_hooks` with `page_id`, once `source: "speech"` and once `source: "copy"`, `sort: "longest"`.
   - `get_brand_landings` — where the traffic goes (tracking parameters removed).
   - `get_brand_partners` — pages Meta shows as paid partners on its ads. Most labelled ads name the advertiser itself; creator names and deal terms are not in the data.

## Output

- Who advertises for this domain (page names, ids, observed days, active ads).
- Ads that keep running: top 5 with days, format, hook, `duchamp_url`.
- Hooks: the longest-running spoken and written openings.
- Landing pages: grouped, with ad and active counts.
- Paid partners, if any.
- Attack angles: 3–5 hypotheses for the user — gaps in formats, offers or audiences they do not cover. Mark them as hypotheses from observed ads, not facts.

## Rules

- `status: "active"` also returns ads with `activity: "unknown"` (not seen on the last partial collection). Say an ad is running only when `activity` is `"active"`; for `"unknown"`, give its `last_seen_on` instead.
- No spend, impressions, revenue or conversion claims.
- Link every ad with `duchamp_url`.

Activation prompt: `Use the domain-war-room skill on [domain].`
