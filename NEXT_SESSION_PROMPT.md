Polish the frontend of the Duel Masters card database web app in `web/index.html`. It currently
works (search/filter/sort a 1010-card grid, click for detail modal) but looks generic and
"AI-slop" — flat hover states, system font, no motion, default browser feel. Fix that.

## The core problem to solve
Every interactive element feels static and cheap. Fix this with fast, punchy micro-interactions
— NOT slow cinematic fades. If in doubt, make it faster and snappier, not smoother and slower.

## Hard constraints on motion (do not deviate)
- Duration range: **100-180ms** for almost everything. Nothing above 220ms except the modal
  open/close, which can go up to 250ms.
- Easing: punchy, not linear and not a slow ease-in-out. Use something like
  `cubic-bezier(0.16, 1, 0.3, 1)` (snappy ease-out) for things appearing/growing, and a quick
  ease-in for things leaving. Avoid the default `ease` and avoid anything that feels like a
  gentle drift.
- Explicitly BANNED: staggered fade-ins on page load, cards "floating up" into place on scroll,
  parallax, skeleton shimmer loaders that linger, any animation the user has to wait out before
  they can act. This is a tool for fast browsing/searching, not a portfolio showcase — motion
  should acknowledge input instantly, never gate it.
- Respect `prefers-reduced-motion: reduce` — disable/shorten non-essential motion for users who
  set it.

## Concrete things to add
1. **Card hover**: currently `translateY(-3px)` with a slow-ish transition. Make it feel like a
   physical press-lift — snap up fast (~120ms), slight scale (1.02-1.03) combined with the lift,
   sharper shadow. On mouse-down/active, a quick compress (scale down slightly, ~80ms) before
   release, like a real button.
2. **Filter/search interactions**: when a filter changes and the grid re-renders, don't just
   swap the DOM — give removed cards a fast fade+scale-out (~100ms) and new/remaining cards a
   fast settle. Debounce is already there for search input; keep it snappy (~120-150ms), not the
   current value if it's higher.
3. **Modal open**: currently just toggles `display`. Give it a fast scale+fade entrance (start
   ~0.96 scale, 0 opacity → 1/1, ~180-220ms, snappy ease-out) and the backdrop a quick fade.
   Modal close should be faster than open (~120-150ms) since dismissal should feel immediate.
4. **Focus/active states on inputs and buttons**: the current border-color-only focus is weak.
   Add a fast, tight focus ring (box-shadow, not outline, so it respects border-radius) that
   appears instantly (~80-100ms) — no delay, this is an accessibility-relevant interaction and
   should never feel sluggish.
5. **Civilization color dots / tags**: consider a subtle instant color-pulse or scale-tick on
   filter selection to confirm the click registered — again, near-instant (~100ms), not a glow
   that lingers.
6. **Typography**: swap the default system font stack for something with more character for
   headings/card names (a good free option: Inter or Space Grotesk from Google Fonts, or a
   condensed display face if you want it to feel more "trading card game" and less "generic SaaS
   dashboard"). Keep body/rules-text in a highly readable font — don't sacrifice legibility on
   dense rules text for style.
7. **Loading state**: while `data/cards.json` is fetching (should be near-instant on a real
   deploy, but the code should still handle it), show something minimal and fast, not a long
   skeleton animation — a simple instant-appearing state is fine, this app doesn't need a loading
   spectacle.

## What NOT to do
- Don't add a UI framework or build step — this stays plain HTML/CSS/JS by design (documented
  decision in `web/README.md` under "Roadmap" — framework migration is intentionally deferred).
- Don't rewrite the filter/search/sort logic — it works. This pass is CSS/motion/typography only,
  plus the minimal JS needed to add enter/exit transition classes (e.g. toggling a class before
  removing a node from the DOM so the exit animation can play).
- Don't add sound effects, confetti, or anything gimmicky — the goal is "feels expensive and
  responsive," not "feels like a game menu."
- Don't slow anything down in the name of "smoothness." Every number above is a ceiling, not a
  target — err toward the fast end of each range.

## Before/after check
After changes, actually test in a browser (not just read the code) — click through search,
every filter dropdown, hover several cards, open and close the modal multiple times back to
back. It should feel instant and tight, never like you're waiting on the UI. If any interaction
feels like it has a half-beat of lag or drift, tighten it further.

## Context for this session
- Repo: https://github.com/kshakib22/duelmasters-database (private), deployed on Vercel with
  auto-deploy on push to `main` already configured — just commit and push, no manual deploy step.
- Data comes from `web/data/cards.json` (1010 cards) + `web/data/facets.json` (filter option
  lists) — both static, generated by `scripts/04_build_web_data.ps1` from the project's
  `master_database.csv`. Don't need to touch either for this task.
- Full project background/decisions: see `CLAUDE.md` and `plans.md` at the archive root.
