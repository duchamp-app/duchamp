---
name: launch-kit
description: For someone building their own product (SaaS, app, tool) with an AI agent — read the repo, confirm the facts, then produce the launch set around the product (landing page or hero, launch image, launch carousel, short launch video, logo kit, pitch deck) through the Duchamp make loop. Use when the user says "런칭 준비", "출시 키트", "홍보물 만들어줘", "마케팅 소스", "launch kit", "make marketing assets for this product".
---

# Duchamp launch kit

The product works. What surrounds it (the page people land on, the images and video people share, the first post) still looks unfinished. This skill builds that set from the repo you are in. Each asset goes through the `make` skill's build → judge → retry loop.

## 1. Read the product (no questions yet)

Read what exists: README, package.json / pyproject, docs, the app's routes and screens, any existing landing page, `public/` assets (logo, colors), and the CSS/Tailwind theme (brand colors, fonts). Then draft a **fact sheet**:

- One sentence: what it is and who it is for.
- 3–5 things it actually does (from code or docs, not imagination).
- Brand: name, logo file if present, colors (HEX), fonts.
- Launch facts: date, price, platform, link. Usually **unknown**, so leave them as `[placeholders]`.
- Screens worth showing: routes or components that could be screenshotted.

Show the fact sheet and ask the user to correct or confirm it **in one message**. Ask at the same time where they will launch: own site, X, Threads, Instagram, Product Hunt, newsletter. Never fill a gap with a plausible guess.

## 2. Propose the kit

Default set. Drop what does not fit their launch channels:

| # | Asset | Duchamp source |
|---|---|---|
| 1 | Landing page, or only the hero if a site exists | `search_ui` by service/intent/style |
| 2 | Launch image (poster or info card, 4:5 or 1:1) | `search_image_templates` (`use: poster` or `card`) |
| 3 | 5-slide launch carousel (what it is → the problem → 3 things it does → how to start) | carousel style via `search_image_templates` |
| 4 | 9:16 launch video (silent motion graphics) | `search_image_templates` with `use: video` (e.g. an opening countdown when the launch date is known) |
| 5 | Logo kit, only if the product has no logo yet | `search_image_templates` with `use: logo` |
| 6 | Pitch deck, only if the user is raising or presenting | `search_image_templates` with `use: deck` |

Fonts for these assets: if the brand has no font yet, pick one with `search_assets` (`type: font`) and use `get_asset` for the install code and license. Use it in every asset.

Say plainly what Duchamp **does not cover**: launch post text (write it yourself from the confirmed facts and say so), app-internal UI (dashboards, admin, ERP screens), Product Hunt gallery sizes, OG image at exactly 1200×630. If the user wants one of these, offer the closest template and say it is a stretch. Do not pretend it is a fit.

Duchamp MCP needs a free Duchamp login on the first connection (OAuth in the browser, no API key; Claude Code `/mcp` → Authenticate, Codex `codex mcp login duchamp`). A full kit fetches about one template per asset, and each `get_template` / `get_ui` / `get_asset` counts toward the user's daily free limit (shared with copying on duchamp.app; searches and `get_reference` are free). If the limit message comes back mid-kit, finish the assets you already have recipes for and list the rest under "확인할 것 / To confirm".

Show the kit as the `make` checklist, then start. No extra confirmation is needed once the facts are confirmed.

## 3. Build each asset with `make`

Run the `make` skill for each asset in order (1 → 6, skipping what the user dropped). Assets should share one look: reuse the same brand colors and fonts, and if the landing page passed first, take its type scale and palette into the image and video briefs.

Product screenshots: if an asset needs the real product UI, run the user's app locally and capture real screens. Never mock up a fake UI and present it as the product.

## 4. Hand over

- A table: asset → file path → pass/score/tries → template link (`duchamp_url`).
- One combined "확인할 것 / To confirm" list across all assets (placeholders, launch date, price, plates that used example images).
- Where to put each file: e.g. hero code into `app/page.tsx`, or the video to post on X/Threads/Reels.
