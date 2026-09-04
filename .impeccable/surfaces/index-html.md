---
version: 1
slug: "index-html"
primary_target: "index.html"
related_targets: []
---

## Scope

`index.html` — the whole app: card browser (search/filter/sort/detail) plus a new deck
workbench surface. Visitor mode: **Operate**. Motion is feedback, never a gate.

## Audience and job

A Duel Masters collector studying the 2004-06 pool, plus strangers who open a shared
Discord link (phone as often as desktop). Job: find a card fast, read its real rules text,
and assemble reference decks to test combinations. No accounts, no backend, no build step.

## Direction contract

THESIS: A card-shop binder page, not a SaaS dashboard. The grid is the product and the
chrome recedes to a thin instrument rail; refuses the centered-hero + same-size-feature-card
arrangement the category ships. Deck building is a drawer over the binder, never a separate
route — you never lose your place in the pool.

OWN-WORLD: Near-black ink ground (#0b0d12) under warm lamp, the five civilization colors as
the only saturated hues and the sole coding system; each civ owns a filter chip, dot, deck
bar segment and card-edge tint. Chrome is 1px hairline + tonal elevation, no glass
decoration. Display voice: Chivo (condensed-ish grotesque with real character, self-hosted
via Google Fonts) for names/headings; body and dense rules text in Source Sans 3 for
legibility at 13-14px. Tabular numerals everywhere a count or cost appears.

STORY: The visitor lands mid-collection, types or clicks a civilization, and the grid
answers instantly. Clicking a card opens its full record; adding it to a deck slides a rail
that shows the curve and civilization balance building in real time. They copy a link and
the whole state travels.

FIRST VIEWPORT: Sticky instrument rail across the top — title + live count at left, search
field taking the remaining width, one row of five civilization chips and compact selects
beneath. Immediately below, the card grid at full bleed, ~7 columns desktop / 2-3 mobile.
Deck drawer docked right, collapsed to a spine until a card is added. Primary action is the
search caret, focused on load.

FORM: Binder-page grid with a docked instrument rail; ranked 1st of the derived structures
for an Operate reference tool. Refinement of the incumbent world (no concept-seed round —
this extends an established surface per new-work §3).

FINISH: unreviewed and undocumented is unfinished; this build ends with the finish review,
the verdict, DESIGN.md, and every shipping raster carrying its provenance.

## Unresolved

Strict-legality toggle deliberately deferred — decks are advisory-only by owner decision.
