---
name: ads-research
description: Research long-running ads on Duchamp — find long-running ads, break down an advertiser, compare pages, and keep a saved library and boards. Use for niche ads, one ad, an advertiser or saved ads.
---

# Duchamp ads research

Duchamp's server returns observed ads, advertiser breakdowns, comparisons, and a saved library. Signed-in results include creative URLs on Duchamp's mirror. Inline images are low-resolution previews for you; when showing the user, link `duchamp_url`. It does not reproduce ads. No tool calls a model when you call it. Many ads carry a stored AI analysis (`detail.analysis` on `get_ad`: format, hook, 15 emotional drivers, persona, creative targeting, product category, first product appearance) filled by a nightly batch — use it as a reference and check it against the keyframes and transcript; `search_ads` can filter by format, hook, evidence, ratio, language, persona and market. Saved items, boards, and watched advertiser pages are stored for the signed-in user. The saved-library tools need a separate per-app opt-in on https://duchamp.app/saved/connect (read, or read/write); signing in to the MCP server alone does not grant them. If a call returns `ACCESS_REQUIRED` or `READ_ONLY`, give the user that link and stop.

Do not invent numbers, reviews, prices, or dates. Impression counts and revenue are not in the data. Ad copy (title and body excerpt) is included because every MCP call is signed in. When you mention an ad, link its `duchamp_url` or the ad-library URL so the user can see the original there.

The ads research tools are not on a daily fetch meter.

For a whole workflow, use a skill pack: `evergreen-hunt` (90+ day ads clustered by hook), `weekly-competitor-sweep` (watched advertisers' last 7 days), `creative-test-autopsy` (testing vs scaling for one advertiser), `board-to-brief` (a board into a production brief), `hook-mining` (hook bank for a niche), `domain-war-room` (a competitor domain end to end), `angle-theft-detector` (the user's winning creatives from Performance against lookalike ads).

Order: `get_connection_status` once at the start of a session (it says whether this app may save, and the page to allow it), then `search_ads`, then `get_ad` for one ad, or `get_brand_anatomy`, then `save_to_library` or `watch_brand`.

`save_to_library`, `list_saved`, and `get_board` give each saved ad a `material`: `link_only` (not an observed ad yet — no copy or creative files stored; `watch_brand` its advertiser to collect it), `pending` (observed, creative files not mirrored yet), `ready`, `partial`, `failed`, or `suppressed` (taken down at the rights holder's request). Tell the user which one they got.

To start from a homepage or a URL, call `find_advertisers_by_domain`; a matching domain is not proof of ownership (app stores, social profiles and marketplaces are shared — check `shared_host`). To see where one advertiser sends traffic, call `get_brand_landings`. To see which pages appear as paid partners ("Paid partnership with …") on its ads — often a sub-brand, store or creator page — call `get_brand_partners`; most labelled ads name the advertiser itself, and creator names or deal terms are not in the data. Its `ads` lists up to 24 of those labelled ads as "<partner> and <advertiser>" (`partner_is_self` when only the advertiser itself is shown), running first and then longest running — a days-running order, not a performance rank. To see how fast an advertiser launches and retires ads, call `get_creative_velocity` (7/14/30/60 days; days before Duchamp first observed the page are a lower bound); its `tests` are batches of 2+ ads that started the same day, with how many still run and the survivor — the ad still running longest after others stopped, which is a days-running signal, not an impression rank; its `signals` count evergreen ads (running now for 90+ days) and creatives adding variants (3+ variants of the same creative among ads started in the last 14 days — the advertiser turned on several versions, not a rise in reach), each with the change vs last week (`delta` null means no record to compare; last week's variant counts reuse today's values). To skip repeats of one creative, call `search_ads` with `group_by: "exact_creative"` (each item then has `grouped_count`); to see every ad that reuses one creative — often with different copy or landing pages — call `get_ad_duplicates` (byte-identical files only; re-encoded or cropped versions are not matched). To compare opening lines, call `search_ad_hooks` — `source: "speech"` for what is said first in videos (with the second it starts; a first line at 8s is not a 2-second hook), `source: "copy"` for the first line of the body. Days running are Meta start date to last seen, not a measure of hook quality.

`compare_brands` takes at most 4 page ids. `watch_brand` wants an advertiser page id or an advertiser-page URL (`view_all_page_id`), not one ad's id.

`compose_transfer_brief` is optional. It returns a reference prompt built from chosen ads and facts from the user's own context; the server does not store those facts and does not write them to the call log. It is a reference only — not a guaranteed reproduction of the original ad.

## 1. Long-running ads

Call `search_ads` with `sort: "survival"` (the default). By default it returns only active ads running 90+ days; pass `all: true` for every observed ad. `rank` is the ad's position in its own advertiser's Meta impression-sorted list on `rank_observed_on` — not an impression count, not performance, and not comparable across advertisers. `activity` is `active`, `ended`, or `unknown` (not seen on the last partial collection, not proof it ended). Before you claim a trend or a rank change, call `get_collection_status` for those pages: a page observed on few days or ranked on fewer than 2 days cannot support it. Read days running, survival, rank, variants, the signed-in copy, and `media` (creative URLs on Duchamp's mirror). To open one ad, call `get_ad` with its numeric `ad_id`. It returns the observation history, the full body copy, the landing URL, other variants, timestamped keyframe URLs (up to 16), the timestamped speech transcript, a Duchamp label summary when one exists, up to three other long-running ads from the same advertiser, and that ad's creative URLs. Read the keyframes and the transcript yourself to name the hook, the offer, and where the CTA lands. Transcripts are raw speech-to-text and can mishear brand names; say so when it matters. Inline images are low-resolution previews. When you show the user, link `duchamp_url`. Save the ones the user wants with `save_to_library`.

Paste:

`Duchamp의 search_ads로 오래 버틴 광고를 찾아 목록으로 보여 줘. sort는 survival. 소재는 Duchamp 미러 주소다. 사용자에게 보여 줄 때는 duchamp_url 을 링크로 걸고, 숫자·후기·가격은 지어내지 마.`

## 2. One advertiser

Call `get_brand_anatomy` for the page the user names. Show the long-running ads, media mix, hooks, landings, and CTAs. `rankings` are positions inside this advertiser's list only — do not present them as performance or compare them with other advertisers.

Paste:

`Duchamp의 get_brand_anatomy로 이 광고주를 해부해서 오래 돈 광고·매체 비율·CTA를 보여 줘.`

## 3. My page next to competitors

Call `watch_brand` with `is_mine: true` for the user's page, watch the competitor pages, then `compare_brands`. Do not invent reach or revenue.

Paste:

`Duchamp의 watch_brand로 내 페이지를 is_mine true로 표시하고, 경쟁 페이지와 compare_brands로 비교해 줘. 노출 수나 매출은 지어내지 마.`

## 4. A reference prompt from a board

Read the board with `get_board` if the user wants to see it. If they ask for a prompt, call `compose_transfer_brief` with `board_id` and the facts they gave. URL items on the board are not sources. Tell the user the prompt is a reference, not a reproduction.

Paste:

`이 보드를 Duchamp의 compose_transfer_brief로 참고 프롬프트로 만들어 줘. 제품 사실은 내가 준 것만 써 줘.`

## 4b. Team boards (read only)

If the user is in a Duchamp team, `list_boards` with `scope: "all"` adds team boards marked `scope: "team"`. Pass `board_scope: "team"` with that id to `get_board`, `export_board` and `compose_transfer_brief`; `list_saved` with `scope: "team"` lists teammates' shared ads (pass `next_cursor` for more). Only teammates who turned on AI sharing appear, and names are null for those who did not. A team board's items have no notes or saved titles. On `ACCESS_REQUIRED`, give the user https://duchamp.app/saved/connect (team access for this app). You cannot change team boards from here.

## 5. Organize and export a board

`list_saved` (or `get_board`) gives each item's `id`, note, tags and rating; filter with `tag` or `min_rating`. Change them with `update_saved` — only the fields you send change, and `tags` replaces the whole list, so merge with the current tags first. Take items off a board with `remove_from_board` (they stay saved). `export_board` returns the board as JSON or CSV with the same columns as the download on duchamp.app; give the user its `download_url` if they want the file. Do not rate or tag ads on your own judgment unless the user asks — the rating is theirs.

Paste:

`Duchamp 저장함에서 #hook 태그가 붙은 광고를 list_saved로 보여 주고, 내가 고른 것만 update_saved로 별점 5점 줘. 그다음 이 보드를 export_board로 CSV로 내보내 줘.`
