---
name: evergreen-hunt
description: Find ads that ran 90+ days in a niche, cluster them by hook, and pull transcripts for the strongest. Use when the user asks for evergreen or long-running ads.
---

# Evergreen hunt

Duchamp tools: `search_ads`, `search_ad_hooks`, `get_ad`, `get_collection_status`. If they are not available, stop and tell the user to connect Duchamp first at https://duchamp.app/mcp.

Ask for one thing if the user did not give it: the niche (Explore niche name) or the advertiser pages. Optional: a medium (video, image, carousel). Below, "scope" means `niche` — or, when the user gave advertiser pages, one run per `page_id` (merge the results by days running).

## Steps

1. `search_ads` with the scope, `all: true`, `status: "active"`, `sort: "survival"`, `group_by: "exact_creative"`, `limit: 24`. Keep ads with `days_running` of 90 or more and read the next `page` while the last item is still 90+ and `has_next_page` is true (stop at 3 pages and say the list is capped). Do not use `survival: "90일+"` here — it is the 90–179 day band and drops older ads. Add `medium` if the user gave one. If no active ad reaches 90 days, run the same search with `status: "ended"`. Ads that ran 90+ days still count as evidence: call them ended only when `activity` is `"ended"`; `"unknown"` means not seen on the last partial collection — say so instead.
2. `search_ad_hooks` with the same scope, `sort: "longest"`, `limit: 50` (add `live: true` unless step 1 fell back to ended ads) — once with `source: "speech"` (first sentence said in the video, with the second it starts) and once with `source: "copy"` (first line of the body). Match hook `examples` to the ads from step 1.
3. Cluster the ads by hook. Use `detail.analysis.hook` from `get_ad` when it exists; otherwise read the first line and the first keyframes yourself. Name each cluster in plain words (for example "question about a pain", "number up front", "before / after"). Five to eight clusters.
4. For the top five ads by days running, call `get_ad`. For video, quote the first one or two transcript sentences (or say the video is silent or not transcribed yet); for image and carousel ads, quote the first line of the copy and describe the first card. Name the offer and the CTA, and note where the landing goes. Transcripts are raw speech-to-text — say so when a brand name looks misheard.
5. Before you call anything a trend, check `get_collection_status` for the pages involved. A page observed on few days cannot support "keeps running" claims.

## Output

- One line: niche, how many evergreen ads, how many advertisers, the date range observed.
- A table of clusters: cluster, ads, longest days running, active, one example (`duchamp_url`).
- The five strongest ads: advertiser, days running, hook line, transcript opening (video) or first copy line, offer, CTA, landing — each with its `duchamp_url`.
- Two or three patterns that repeat across clusters.

## Rules

- `status: "active"` also returns ads with `activity: "unknown"` (not seen on the last partial collection). Say an ad is running only when `activity` is `"active"`; for `"unknown"`, give its `last_seen_on` instead.
- Days running is Meta start date to last seen. It is survival, not performance — do not call an ad "profitable" or "winning on ROAS".
- Do not invent impression counts, spend, revenue, reviews or prices.
- Link every ad you mention with `duchamp_url`. Do not rehost creative files.

Activation prompt: `Use the evergreen-hunt skill on [niche].`
