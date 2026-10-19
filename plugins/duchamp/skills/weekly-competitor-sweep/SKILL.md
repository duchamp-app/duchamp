---
name: weekly-competitor-sweep
description: Turn the watched advertisers' last 7 days into a Slack-ready Monday brief. Use when the user asks what competitors launched this week.
---

# Weekly competitor sweep

Duchamp tools: `list_watched_brands`, `get_collection_status`, `search_ads`, `get_brand_anatomy`. If they are not available, stop and tell the user to connect Duchamp first at https://duchamp.app/mcp.

`list_watched_brands` reads the saved library, which needs a separate per-app opt-in on https://duchamp.app/saved/connect. If it returns `ACCESS_REQUIRED`, give the user that link and stop. If the watch list is empty, ask for competitor pages and offer `watch_brand` (also needs read/write access).

## Steps

1. `list_watched_brands`. Skip the user's own page (`is_mine`) unless they ask for it. At most 10 pages per sweep; say which ones you skipped.
2. `get_collection_status` with those `page_ids`. A page first observed less than 7 days ago (`coverage.first_observed_on`), with `stale_days` above 1, or whose latest collection was not complete (`coverage.last_attempt.outcome` is not `"complete"`, or there is no `last_attempt`) cannot show a full week — for that page, call its counts "at least N (observed)" or report a current snapshot, and say how many days were observed (`coverage.observed_days`).
3. For each page, `search_ads` with `page_id`, `all: true`, `sort: "recent"`, `limit: 24`. Results come newest start date first; read the next `page` while `has_next_page` is true and the last item still started within the last 7 days (stop at 5 pages and mark the count as partial). New launches are ads whose `start_date` is in the last 7 days (Asia/Seoul). Count them and group by medium.
4. For each page, `search_ads` with `page_id`, `status: "active"`, `all: true`, `sort: "survival"`, `limit: 3` — the ads it keeps running.
5. For each page, `get_creative_velocity` with `days: 7` — launched, retired and net change per day (days before `first_observed_on` are a lower bound; `?` days had no collection). Then `get_brand_anatomy`. Use `rankings` (Meta impression-sorted positions with the date they were taken; `change_7d` only when two ranked days 6–8 days apart exist) and `test_groups` (3+ ads that started the same day). Never treat rank as an impression count.

## Output (paste-ready for Slack)

```
*Competitor sweep — week of YYYY-MM-DD*
• <advertiser>: N new (video n / image n / carousel n). Top new: <hook in a few words> <duchamp_url>. Still running: <ad> (D days) <duchamp_url>. Rank: <only if change_7d exists>.
...
*Worth a look*: 2–3 bullets across competitors (a new offer, a format shift, a batch test).
*Coverage*: pages with fewer than 7 observed days, named.
```

## Rules

- Only report what the tools returned. No spend, impressions, revenue or "winning" claims.
- `status: "active"` also returns ads with `activity: "unknown"` (not seen on the last partial collection). Say an ad is running only when `activity` is `"active"`; for `"unknown"`, give its `last_seen_on` instead.
- "Ended" needs `activity: "ended"`; `unknown` means not seen on the last partial collection, not proof it stopped.
- Link every ad with `duchamp_url`.

Activation prompt: `Use the weekly-competitor-sweep skill on my watch list.`
