---
name: connect-extension
description: Install and connect the Duchamp Chrome extension, then confirm the first save. Use to save ads from the Meta Ad Library, connect the browser, or when get_extension_status shows none.
---

# Connect the Duchamp Chrome extension

The extension adds a "Duchamp에 저장" (save) button to every ad in the Meta Ad Library and a side panel that finds advertisers for the site the user is on. Installing a Chrome extension always needs the user's own clicks — there is no supported way to install it silently, so do not try (no policy files, registry keys, or `--load-extension`). Your job is to open the right pages, tell the user exactly what to click, and confirm each step through MCP. Target: under two minutes for a user who is already signed in to Duchamp.

## 1. Check

Call `get_extension_status` and keep its `install_url`. `connected` counts every browser on the account — a connection made on another computer, or from a Chrome where the extension was since removed, still shows `true`. So if it is true, show the connections (label, version, last used) and ask the user to click the Duchamp icon in **this** Chrome's toolbar (puzzle-piece menu → Duchamp) and say what the side panel shows. It lists "이 사이트" and Private Mode → this Chrome is connected, skip to step 4. It shows a **연결하기** button → installed but not connected here ("이 브라우저에서 연결 끊기" only clears this browser), go to step 3. No Duchamp icon → step 2.

## 2. Install

Open `install_url` (https://duchamp.app/extension/install) in the user's Chrome — not in your own browser tool:

- macOS: `open -a "Google Chrome" "https://duchamp.app/extension/install"`
- Windows: `start chrome "https://duchamp.app/extension/install"`
- Linux: `google-chrome "https://duchamp.app/extension/install"` (or `xdg-open`)

It goes to the Chrome Web Store listing. If it lands on duchamp.app/extension instead and the page says the store listing is not ready yet, tell the user the extension is not published yet and stop.

Tell the user: click **Chrome에 추가 / Add to Chrome**, then **확장 프로그램 추가 / Add extension**. The permissions it asks for are storage, side panel, tabs (to read the current tab's domain when the side panel is open) and alarms; it talks only to duchamp.app, and runs on the Meta Ad Library and the Duchamp connect page.

## 3. Connect

Right after install the extension opens https://duchamp.app/extension/connect. Tell the user: sign in if asked, check that the code on the page matches the code in the extension's side panel (toolbar icon → Duchamp), then click **연결 / Connect** once. No token is copied anywhere.

Poll `get_extension_status` every 10 seconds for up to 3 minutes until a new connection appears (`active` goes up, or a connection with a fresh `created_at`). If it stays false, ask the user to open the side panel and press **연결하기** again (it shows a new code), then keep polling.

## 4. First save

Pick one advertiser the user cares about — from `find_advertisers_by_domain` with the user's competitor site, or from `search_ads`. Open that advertiser's Ad Library page in the user's Chrome:

`https://www.facebook.com/ads/library/?active_status=active&ad_type=all&country=ALL&view_all_page_id=<page_id>&search_type=page&media_type=all`

Tell the user to click **Duchamp에 저장** under "Library ID" on any ad card. Then call `get_extension_status` again: with the per-app library grant (https://duchamp.app/saved/connect) it shows the latest save — ad id, media state and `last_ad_url`, the ad's page on Duchamp. Give the user `last_ad_url`. Without the grant `last_ad_url` is null and nothing else can confirm the save (`get_ad` without the grant does not read extension saves, and it finds ads Duchamp already collects whether or not the save worked) — relay https://duchamp.app/saved/connect, ask the user to allow read access for this app, then call `get_extension_status` again. Or ask the user to check that the button changed to **저장됨**.

Media state right after a save is `pending`: Duchamp copies the creative files at the end of its next collection run. Saved ads are visible only to the user who saved them. Private Mode (side panel) keeps new saves out of the shared pool.

Paste:

`Duchamp 확장을 설치하고 연결해 줘. get_extension_status 로 확인하고, 연결이 안 돼 있으면 설치 주소를 내 Chrome 으로 열어서 무엇을 누르면 되는지 알려 줘. 연결되면 경쟁사 광고 라이브러리 페이지를 하나 열어 주고, 내가 저장한 광고가 들어왔는지 확인해 줘.`
