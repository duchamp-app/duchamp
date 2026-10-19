---
name: hook-mining
description: Build a hook bank for a niche and format, ranked by how long the ads ran. Use when the user asks for hooks, opening lines or headline ideas from a niche.
---

# Hook mining

Duchamp tools: `search_ad_hooks`, `search_ads`, `get_ad`. If they are not available, stop and tell the user to connect Duchamp first at https://duchamp.app/mcp.

Ask for the niche if the user did not give it. Two kinds of hooks: spoken (the first sentence said in video ads) and written (the first line of the ad body, across every medium — video ads have body copy too). Ask which, or do both. There is no medium filter on hooks, so written-hook counts and days cover every medium — label them "all media". If the user wants static ads only, skip `search_ad_hooks` for that part: page through `search_ads` with `niche`, `medium: "image"` (then `"carousel"`), `all: true`, `sort: "survival"` (up to 3 pages each), take the first line of each `body_excerpt` (title when empty), and group and count those yourself. If `has_next_page` is still true at the cap, call the counts a sample of the longest-running ads and do not claim which hooks are "reused most" beyond that sample.

## Steps

1. `search_ad_hooks` with `niche`, `sort: "longest"`, `limit: 50`:
   - spoken: `source: "speech"` — the first sentence said in the video, with the second it starts. A first sentence at 8s is not a two-second hook: keep `start_sec` in the table and put sentences starting after 3s in a separate "late first line" list.
   - written: `source: "copy"` — the first line of the ad body.
   Run it again with `sort: "most_ads"` to catch hooks reused across many ads. Use `q` when the user wants a keyword.
2. Check `coverage` on speech results: silent, pending, failed and no-file videos are not "no hook". Say how many were not transcribed.
3. Tag each hook with one type: confession of a doubt, question, number, relatable situation, comparison, promise / result, other. Open two or three with `get_ad` when the text alone is unclear.
4. Write ten new hooks for the user's product from the strongest types, with blanks like `[product]` and `[result]`. Do not reuse the original sentences.

## Output

- Table: hook, type, ads, active, longest days, start second (spoken), example (`start_ad_url` or an example `duchamp_url`).
- Which types last longest and which are reused most, in two or three lines.
- Ten new hooks with blanks.

## Rules

- Days running is Meta start date to last seen — it is not a measure of hook quality on its own. Say "ran longest", not "converts best".
- Transcripts are raw speech-to-text and can mishear brand names.
- Link examples with `duchamp_url`.

Activation prompt: `Use the hook-mining skill on [niche] [spoken|written].`
