---
name: ads-research
description: Research market-tested ads on Duchamp — find long-running ads, break down an advertiser, compare pages, and keep a saved library and boards. Use when the user wants long-running ads in their niche, one ad in detail, an advertiser breakdown, a comparison with their own page, or a reference prompt from what they saved.
---

# Duchamp ads research

Duchamp's server returns observed ads, advertiser breakdowns, comparisons, and a saved library — a research list with links and observed numbers. It never returns original creative files (video or image), and it does not reproduce ads. It never calls a model. Saved items, boards, and watched advertiser pages are stored for the signed-in user. The saved-library tools need a separate per-app opt-in on https://duchamp.app/saved/connect (read, or read/write); signing in to the MCP server alone does not grant them. If a call returns `ACCESS_REQUIRED` or `READ_ONLY`, give the user that link and stop.

Do not invent numbers, reviews, prices, or dates. Impression counts and revenue are not in the data. Ad copy (title and body excerpt) is included because every MCP call is signed in. When you mention an ad, link its `duchamp_url` or the ad-library URL so the user can see the original there.

These tools are not counted toward the daily limit.

Order: `search_ads`, then `get_ad` for one ad, or `get_brand_anatomy`, then `save_to_library` or `watch_brand`.

`compare_brands` takes at most 4 page ids. `watch_brand` wants an advertiser page id or an advertiser-page URL (`view_all_page_id`), not one ad's id.

`compose_transfer_brief` is optional. It returns a reference prompt built from chosen ads and facts from the user's own context; the server does not store those facts and does not write them to the call log. It is a reference only — not a guaranteed reproduction of the original ad.

## 1. Long-running ads

Call `search_ads` with `sort: "survival"` (the default). Read days running, survival, rank, variants, and the signed-in copy. To open one ad, call `get_ad` with its numeric `ad_id`. It returns the observation history, a Duchamp label summary when one exists, and up to three other long-running ads from the same advertiser. Original creative files are not included. Save the ones the user wants with `save_to_library`.

Paste:

`Duchamp의 search_ads로 오래 버틴 광고를 찾아 목록으로 보여 줘. sort는 survival. 원본 영상은 없으니 링크로 확인하게 하고, 숫자·후기·가격은 지어내지 마.`

## 2. One advertiser

Call `get_brand_anatomy` for the page the user names. Show the long-running ads, rankings, media mix, hooks, landings, and CTAs.

Paste:

`Duchamp의 get_brand_anatomy로 이 광고주를 해부해서 오래 돈 광고·노출 순위·매체 비율·CTA를 보여 줘.`

## 3. My page next to competitors

Call `watch_brand` with `is_mine: true` for the user's page, watch the competitor pages, then `compare_brands`. Do not invent reach or revenue.

Paste:

`Duchamp의 watch_brand로 내 페이지를 is_mine true로 표시하고, 경쟁 페이지와 compare_brands로 비교해 줘. 노출 수나 매출은 지어내지 마.`

## 4. A reference prompt from a board

Read the board with `get_board` if the user wants to see it. If they ask for a prompt, call `compose_transfer_brief` with `board_id` and the facts they gave. URL items on the board are not sources. Tell the user the prompt is a reference, not a reproduction.

Paste:

`이 보드를 Duchamp의 compose_transfer_brief로 참고 프롬프트로 만들어 줘. 제품 사실은 내가 준 것만 써 줘.`
