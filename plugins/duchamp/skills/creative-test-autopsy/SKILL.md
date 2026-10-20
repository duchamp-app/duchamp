---
name: creative-test-autopsy
description: Split one advertiser's ads into what it is testing now and what it keeps scaling. Use when the user asks what a competitor is testing or scaling.
---

# Creative test autopsy

Duchamp tools: `get_brand_anatomy`, `search_ads`, `get_ad_duplicates`, `get_ad`, `get_collection_status`. If they are not available, stop and tell the user to connect Duchamp first at https://duchamp.app/mcp.

Ask for the advertiser if the user did not give one: a page id, an ad library page URL, or a homepage (then `find_advertisers_by_domain`). One advertiser per run; for two or more, run it per page and compare at the end.

## Steps

1. `get_collection_status` for the page. Under 14 observed days, say the split below is early and lean on start dates more than observed history.
2. `get_brand_anatomy`. Read `test_groups` (3+ ads that started the same day, with how many still run — a batch test), `long_runners`, `rankings`, `timeline` (new ads per week) and `media_mix`.
3. List the active ads once: `search_ads` with `page_id`, `status: "active"`, `all: true`, `sort: "recent"`, `limit: 24`, reading every `page` while `has_next_page` is true (stop at 5 pages = 120 ads and say the piles are a sample of the newest 120). Classify from this one list.
4. Sort the active ads into two piles:
   - **Testing**: started in the last 14 days, or part of a same-day batch whose siblings are still young.
   - **Scaling**: active 28+ days, or whose creative is reused across this advertiser's ads — the item's `duplicates` (this advertiser's ads using byte-identical files, itself included) is 2 or more. Same files with different copy or landing pages is a sign of scaling one creative. Use `get_ad_duplicates` only to show examples; its lists span advertisers and are capped, so do not count from them.
   Ads that fit neither: leave them out and say how many.
5. Open 3–5 ads from each pile with `get_ad` (keyframes, transcript, body copy). Compare format, hook, offer, length and landing between the piles.

## Output

- Header: advertiser, observed days, active ads, how many in each pile.
- Testing vs scaling table: format, hook style, offer, video length, landing — with one `duchamp_url` per cell where you can.
- What graduated: patterns that appear in scaling ads and in older test batches.
- What they are trying now: 2–3 bullets on the new batch.

## Rules

- `status: "active"` also returns ads with `activity: "unknown"` (not seen on the last partial collection). Say an ad is running only when `activity` is `"active"`; for `"unknown"`, give its `last_seen_on` instead.
- These piles come from observation dates and reuse — not from spend or results. Say "keeps running" or "reused", not "winning" or "profitable".
- Rank is a position in this advertiser's Meta impression-sorted list with its date — not an impression count, not performance, and not comparable across advertisers. Do not use it to sort ads into piles.
- Link every ad with `duchamp_url`.

Activation prompt: `Use the creative-test-autopsy skill on [advertiser].`
