---
name: board-to-brief
description: Turn a named Duchamp board into a production brief with three angles grounded in the saved ads. Use when the user wants a brief from a board or saved ads.
---

# Board to brief

Duchamp tools: `list_boards`, `get_board`, `get_ad`, `compose_transfer_brief`. If they are not available, stop and tell the user to connect Duchamp first at https://duchamp.app/mcp.

Boards live in the saved library, which needs a separate per-app opt-in on https://duchamp.app/saved/connect. If a call returns `ACCESS_REQUIRED`, give the user that link and stop.

## Steps

1. `list_boards` (with `scope: "all"` if the user means a team board), then pick the board the user named (ask if two match). `get_board` — for a board marked `scope: "team"`, pass `board_scope: "team"` (and to `compose_transfer_brief` too).
2. For up to 12 ad items, call `get_ad`. Skip URL items and items whose `material` is `link_only`, `failed` or `suppressed` — say how many you skipped and why. With no usable ad left, stop and ask the user to save observed ads to the board. With one, say the brief stands on a single reference and cite it alone.
3. Ask for the user's facts if they are not already in the conversation: brand, product, audience, the one promise, proof, CTA, assets they have, language (ko or en). Use only what they give. Never invent proof, numbers, reviews or prices.
4. `compose_transfer_brief` with `ad_ids` set to the ads you reviewed in step 2 (not `board_id` — that reloads the whole board, including the items you skipped) and those `facts`. It returns a reference prompt; it is a reference, not a reproduction of any ad.
5. Write the brief.

## Output

- Objective and audience (from the user's facts).
- What the board says: 3–5 patterns across the saved ads (hook, format, offer, length, CTA), each citing at least two ads by `duchamp_url` when the board has two or more usable ads.
- Three angles. For each: the angle in one line, why the board supports it (cite ads), two new hook lines written for the user's product, format and length, a 4–6 shot outline or frame plan, CTA.
- Do not copy: lines, logos, people or music from the saved ads.
- The reference prompt from `compose_transfer_brief`, labelled as a reference.

## Rules

- The brief stands on the saved ads and the user's facts only.
- Do not promise the same look or outcome as any saved ad.

Activation prompt: `Use the board-to-brief skill on my "[board name]" board.`
