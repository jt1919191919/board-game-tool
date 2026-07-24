# Board Game Collection Manager

A single-page web app for cataloging, filtering, and sharing your board game collection, with optional GitHub sync and BoardGameGeek (BGG) auto-import.

**Live tool:** https://jt1919191919.github.io/board-game-tool/

---

## Overview

Runs entirely in the browser (one HTML file, vanilla JS). Data can live in local browser storage and/or sync to a GitHub repo file for backup and cross-device access. Games can be added manually or auto-filled by pasting a BGG game URL.

## Features

- **Game catalog**: add/edit/delete games, personal notes, thumbs up/down ratings
- **BGG auto-import**: paste a BGG URL, fetch official game data
- **Advanced filtering**: players, playtime, complexity, designers, publishers, categories, mechanics, custom rules
- **Image galleries**: multi-photo carousels per game, IndexedDB caching for offline speed
- **GitHub sync**: optional cloud backup/versioning via a personal access token
- **Collection sharing**: share a filtered, view-only version of your collection via a GitHub Gist link
- **Responsive**: works on desktop and mobile

---

## BoardGameGeek Integration

BGG's public XML API now requires a **registered application and an Authorization (Bearer) token** for every request — it's no longer open/anonymous. Full rules: https://boardgamegeek.com/using_the_xml_api

To work around BGG not sending CORS headers (which blocks browsers from calling it directly) and to keep the token off the client, fetching is done through a small **Cloudflare Worker** acting as a server-side proxy:

**How it works:**
1. Tool sends the BGG game ID to the Worker: `https://bgg-proxy.jamiertifft.workers.dev/?id=<bggId>`
2. The Worker calls BGG's official `xmlapi2/thing` endpoint, authenticated with a Bearer token, and parses the XML into clean JSON.
3. JSON is returned to the tool and auto-fills the Add/Edit Game form.

**Setup, for reference (already done):**
- Registered a non-commercial application at `boardgamegeek.com/applications` and got it approved.
- Generated an Application Token from that same page: https://boardgamegeek.com/application/7282/tokens
- Stored the token as a Cloudflare Worker **secret** named `BGG_TOKEN` (Worker → Settings → Variables and Secrets) — never in this repo or in `index.html`.
- Deployed the proxy logic to the `bgg-proxy` Cloudflare Worker via the Cloudflare dashboard: https://dash.cloudflare.com/ (logged in via Google).
- Token/secret values are saved in my password manager, not in any repo file.

**Compliance note:** Per BGG's usage terms for public-facing apps, the tool displays a "Powered by BGG" badge (linking to boardgamegeek.com) in the footer.

**If the Worker ever needs updating:** log into Cloudflare (https://dash.cloudflare.com/) → Workers & Pages → `bgg-proxy` → Edit code → redeploy. The Worker URL stays the same, so `index.html` never needs to change unless the URL itself changes.
If the token ever needs to be regenerated or checked, that's done here: https://boardgamegeek.com/application/7282/tokens

---

## Setup & Installation

### Basic (local only)
1. Save/download `index.html`.
2. Open it in a modern browser. Data saves to browser storage automatically.

### GitHub Sync
1. GitHub.com → Settings → Developer settings → Personal access tokens → generate one with `repo` scope.
2. In the tool: hamburger menu → **Connect GitHub** → paste the token.
3. Repo config (already set in code): owner `jt1919191919`, repo `board-game-tool`, file `games-data.json`.

### Editing the code
1. Go to `github.com/jt1919191919/board-game-tool/blob/main/index.html`.
2. Click the pencil icon → make changes → **Commit changes**.
3. Check the **Actions** tab to confirm the update.

---

## Using the Tool

**Add via BGG:** Add Game → paste BGG URL → Fetch Game Data → review/edit → Save.
**Add manually:** Add Game → fill in fields → Save.
**Filter:** text search, players, playtime, complexity, designer/publisher/category/mechanic, or custom rules.
**Rate:** pin favorites, hide games, reset anytime.
**Edit:** Edit mode → modify fields inline → save or discard.
**View:** cycle 1–5 columns, sort by name/year/players/playtime/complexity, click a game for full detail view.

---

## Data Storage

- **Local:** browser `localStorage`, structured per-game (bggId, name, players, playtime, weight, designers, images, notes, etc.)
- **GitHub:** same structure, saved to `games-data.json` in the repo, one commit per change
- **Cache:** IndexedDB for images/game data, for fast/offline loading

## Sharing Collections

- Filter your collection → hamburger menu → **Share Collection** → generates a GitHub Gist link
- Shared view excludes private notes and acquisition dates; recipients can apply their own filters/ratings but can't edit or save changes to the original

---

## Technical Architecture

- Single HTML file, vanilla JS, CSS Grid/Flexbox
- External libs: Swiper.js (carousels), Font Awesome (icons)
- External services: BGG XML API2 (via Cloudflare Worker proxy), GitHub API (storage + Gist sharing)
- Requires: ES6+, IndexedDB, Fetch API (any modern desktop/mobile browser)

## Security Notes

- GitHub personal access token is stored in browser `localStorage` — be mindful of this on shared devices.
- BGG Bearer token lives only as a Cloudflare Worker secret (never in this repo).
- Shared collections strictly exclude personal notes and acquisition dates.

---

## Future Ideas

- More secure GitHub auth flow
- Two-way sync with your actual BGG collection
- Multi-user/collaborative editing
