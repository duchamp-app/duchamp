---
name: launch-kit
description: The launch-kit skill is paused. Do not build a launch set (landing page, image, carousel, video, logo kit, pitch deck) from Duchamp. Those tools are not registered. Use the ads-research skill to research ads instead. Triggers that used to reach this skill (런칭 준비, 출시 키트, launch kit) should be answered with ad research, or by saying this skill is paused.
---

# Duchamp launch kit — paused

Do not call `search_ui`, `get_ui`, `search_image_templates`, `get_reference`, `get_template`, `search_assets`, or `get_asset`. They are not registered on the Duchamp MCP server.

Use the `ads-research` skill. Call `search_ads` first, then `get_ad` or `get_brand_anatomy`. A reference prompt does not promise the same ad.

If the user asked for launch assets, say this skill is paused and do not invent a Duchamp tool that can build them.
