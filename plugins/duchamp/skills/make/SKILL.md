---
name: make
description: The make skill is paused. Do not build landing pages, images, carousels, videos, logo kits, or pitch decks from Duchamp. Those tools are not registered. Use the ads-research skill instead. Triggers that used to reach this skill (만들어줘, 랜딩, 캐러셀, 로고, 피치덱, make a hero) should be answered with ad research, or by saying this skill is paused.
---

# Duchamp make — paused

Do not call `search_image_templates`, `get_reference`, `get_template`, `search_ui`, `get_ui`, `search_assets`, or `get_asset`. They are not registered on the Duchamp MCP server.

Use the `ads-research` skill. Call `search_ads` first, then `get_ad` or `get_brand_anatomy`. A reference prompt from `compose_transfer_brief` is a reference. It does not promise the same look or the same outcome as the original ad. The original ad stays a link.

If the user asked for a landing page, image, video, logo, or deck, say this skill is paused and do not invent a Duchamp tool that can build it.
