# Duel Masters Card Database — Web App

A searchable, filterable browser for the Duel Masters card database (DM-01–DM-12 + Promos,
1010 cards) built in this project. Plain HTML/CSS/JS — no build step, no npm install required.

## Running it locally

Browsers block `fetch()` against local files opened directly (`file://...`), so you need a
tiny local web server. Pick whichever you have available:

**PowerShell (no extra install needed):**
```powershell
cd web
# any simple static server works; if you have Node.js:
npx serve .
```

**If you have Python installed** (this project's main dev machine did not, but yours might):
```
cd web
python -m http.server 8080
```

Then open `http://localhost:8080/` in your browser.

## Deploying

This is a fully static site (HTML + JSON + images) — deploy it as-is to any static host:

- **Vercel**: drag-and-drop the `web/` folder onto vercel.com/new, or `vercel deploy` from
  inside `web/` with the Vercel CLI. No build settings needed — it's already static.
- **GitHub Pages**: push this repo to GitHub, enable Pages, set the source to this folder
  (or repo root if `web/` becomes the repo root).

## How the data works

- `data/cards.json` — every card's full metadata (name, civilization, type, rarity, mana
  cost, power, rules text, flavor text, illustrator, image link), generated from
  `master_database.csv` by `scripts/04_build_web_data.ps1`.
- `data/facets.json` — the distinct list of civilizations/types/rarities/sets, used to
  populate the filter dropdowns without the frontend having to compute them.
- **Images are hotlinked from their original CDN** (`scans.shobu.io` for ~1004 cards,
  `static.wikia.nocookie.net` for 3 cards) rather than shipped in this repo — keeps the
  live site fast and the repo small. Only 3 cards whose *only* image source is the original
  Kaggle scan (no CDN copy exists) have their image copied into `images/` and committed
  directly, since there's no remote URL to hotlink for those.
- `images/backup/` contains a full local copy of every shobu.io + wiki image (904 files,
  ~214MB) as a durable offline backup, in case the CDNs ever go down or change. The live
  site does NOT load from this folder day-to-day — it's a safety net, not part of the
  running app. (See the project's top-level `CLAUDE.md` for the full sourcing story.)

## Updating the data

If `master_database.csv` changes (new cards, fixed data, etc.), regenerate the web data with:
```powershell
cd ..\scripts
powershell -File 04_build_web_data.ps1
```
This rewrites `web/data/cards.json` and `web/data/facets.json` and re-copies the 3
Kaggle-only fallback images. Nothing else needs to change.

## Roadmap / future upgrade path

Per project decision (2026-09-05): starting with a plain no-build HTML/CSS/JS site
deliberately, to get something fully working fast without framework/tooling risk. Documented
here for later:
- Migrate to React + Vite + Tailwind for nicer component structure and dev experience once
  the feature set grows past what plain JS comfortably handles.
- Move the local image backup (`images/backup/`) to a proper object store (Cloudflare R2 —
  user's stated preference) instead of sitting in the git repo, once that becomes worth the
  setup effort.
- Structured keyword-ability flags (Blocker / Slayer / Shield Trigger / Double Breaker /
  Power Attacker etc.) as real filterable boolean columns, parsed out of `rules_text` —
  currently only searchable as free text.
- Deck-builder / mana-curve tools, once the "just browse and study the database" use case
  is solid.

## Keyboard shortcuts

| Key | Action |
|---|---|
| `/` | Focus the search field |
| `←` `→` `↑` `↓` | Move between cards in the grid |
| `Enter` / `Space` | Open the focused card |
| `←` `→` (modal open) | Previous / next card |
| `+` / `−` | Add / remove one copy of the focused (or open) card |
| `d` | Toggle the deck drawer |
| `Esc` | Close the modal, then the drawer |

## Deck builder

A no-backend deck workbench lives in the right-hand drawer (press `d`, or the **Deck**
spine at the bottom-right).

- **Storage**: decks live in the browser's `localStorage` and in the URL — there is no
  server and no account. Clearing site data clears the deck, so use **Export** or
  **Copy share link** for anything you want to keep.
- **Sharing**: **Copy share link** puts the whole state (filters, open card, and the full
  deck) into the URL. Paste it anywhere; opening it restores exactly that view.
- **Deck code**: the compact `id*qty.id*qty` string at the end of an export. **Import**
  accepts either a bare code or a full share link.
- **Rules are advisory, never enforced.** The panel reports the 40-card minimum, the
  4-copy-per-card limit and civilization spread as notices, but never blocks you — the
  point is fast experimentation with combinations. This matches the upstream shobu.io
  engine, which performs no deck-size or copy-count validation server-side either (its
  `40` is a client-side constant in a dev harness, not a game rule).
- The mana curve and civilization balance bar are computed from real card data, live.

## Performance

The grid is virtualized: only the rows near the viewport exist in the DOM (roughly 50
cards instead of 1010), so scrolling stays smooth on phones. Search, filter and sort run
against a haystack precomputed once at load.
