---
name: ads-research
description: Research market-tested ads on Duchamp, save what matters, and turn a brand or a board into a transfer brief your agent renders with a recipe. Use when the user wants long-running ads, an advertiser breakdown turned into their own product, a comparison with their own page, or a board turned into a brief.
---

# Duchamp ads research

Duchamp's server returns observed ads, advertiser breakdowns, comparisons, a saved library, and a transfer brief. It never returns original creative files (video or image). It never calls a model. Facts you put in `compose_transfer_brief` come from the user's own context: the server does not store them and does not write them to the call log. Saved items, boards, and watched advertiser pages are stored for the signed-in user. The saved-library tools need a separate per-app opt-in on https://duchamp.app/saved/connect (read, or read/write); signing in to the MCP server alone does not grant them. If a call returns `ACCESS_REQUIRED` or `READ_ONLY`, give the user that link and stop.

Do not invent numbers, reviews, prices, or dates. Impression counts and revenue are not in the data. Ad copy (title and body excerpt) is included because every MCP call is signed in.

These tools are not counted toward the daily limit. `get_template` is. After a brief, fetch the recipe and render it.

Order: `search_ads` or `get_brand_anatomy`, then `save_to_library` or `watch_brand`, then `compose_transfer_brief`, then `get_template(recipe_id)`.

`compare_brands` takes at most 4 page ids. `watch_brand` wants an advertiser page id or an advertiser-page URL (`view_all_page_id`), not one ad's id.

## 1. Long-running ads

Call `search_ads` with `sort: "survival"` (the default). Read days running, survival, variants, and the signed-in copy. Save the ones the user wants with `save_to_library`.

Paste:

`Duchamp의 search_ads로 오래 버틴 광고를 찾아 줘. sort는 survival. 원본 영상은 없으니 집행 일수와 문구만 보고, 숫자·후기·가격은 지어내지 마.`

## 2. One advertiser, then my product

Call `get_brand_anatomy` for the page the user names. Show the long-running ads and the media mix. Ask for brand, product, audience, one-line claim, proof, CTA, and assets. Call `compose_transfer_brief` with that page and those facts. Then `get_template(recipe_id)`.

Paste:

`Duchamp의 get_brand_anatomy로 이 광고주를 해부하고, 내가 주는 제품 사실로 compose_transfer_brief를 만든 다음 get_template으로 레시피를 받아 렌더해 줘.`

## 3. My page next to competitors

Call `watch_brand` with `is_mine: true` for the user's page, watch the competitor pages, then `compare_brands`. Do not invent reach or revenue.

Paste:

`Duchamp의 watch_brand로 내 페이지를 is_mine true로 표시하고, 경쟁 페이지와 compare_brands로 비교해 줘. 노출 수나 매출은 지어내지 마.`

## 4. Board to brief

Read the board with `get_board` if the user wants to see it. Call `compose_transfer_brief` with `board_id` and the facts they gave. URL items on the board are not sources. Then `get_template(recipe_id)`.

Paste:

`이 보드를 Duchamp의 compose_transfer_brief로 전이 브리프를 만들고 get_template으로 레시피를 받아 렌더해 줘. 제품 사실은 내가 준 것만 써 줘.`
