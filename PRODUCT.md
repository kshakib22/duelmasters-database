# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Stack

Plain static HTML/CSS/JS, no build step, no framework. Deliberate v1 choice (documented in
README.md) to get a fully working site fast rather than a heavier frontend that risks being
broken/unfinished. A React+Vite+Tailwind migration is a documented future option, not planned now.

## Users

Primary: the project owner, a Duel Masters TCG collector/player, browsing and studying the
card database for personal reference. Secondary: other Duel Masters players/collectors the
owner shares the live link with directly (e.g. in a Discord or forum) — the site needs to make
sense to someone with no context on how it was built, not just the owner.

## Product Purpose

A searchable, filterable browser for a complete Duel Masters card database (DM-01 through
DM-12 + Promos, 1010 cards, 2004-2006 era). Lets a visitor find a specific card, browse by
civilization/type/rarity/set/mana cost, and see full card detail (rules text, flavor text,
illustrator, image) without needing the physical card or a fragmented set of other sites.

## Positioning

Existing card-lookup resources for this era are fragmented: the Fandom wiki lacks a bulk
searchable view and blocks automated access; shobu.io's own site is built for playing a live
duel, not browsing/studying the card pool. This project consolidates card metadata + images
from multiple sources (Kaggle dataset, shobu.io CDN, Fandom wiki) into one clean, complete,
purpose-built browsing tool — something no single existing source offers for this specific
card range.

## Operating Context

Visitors search/filter/browse on both desktop and mobile (a shared Discord/forum link will be
opened on phones as often as desktop). No account system, no backend — fully static data
(`data/cards.json`, `data/facets.json`) fetched client-side. Images are hotlinked from
`scans.shobu.io` / `static.wikia.nocookie.net` rather than self-hosted, to keep the site light.

## Capabilities and Constraints

- 1010 cards, 100% with a real image (1004 shobu.io, 3 Kaggle-scan-only, 3 Fandom wiki).
- Fields available per card: name, set/set number, civilization(s), race/subtypes, type,
  evolution flag, rarity, mana cost, power, rules text, flavor text (81% of cards — 190 have
  none recorded anywhere in the source data, not a bug), illustrator, image + image source.
- No structured keyword-ability flags yet (Blocker/Slayer/Shield Trigger/etc. are only
  searchable as free text within rules text, not filterable booleans) — documented future
  refinement, not current scope.
- No deck-builder, no user accounts, no backend — pure browse/search/filter tool.

## Evidence on Hand

Real production data already in the repo: `data/cards.json` (all 1010 cards with real
metadata/images), `data/facets.json` (real distinct filter values: 5 civilizations, 3 types,
5 rarities, 13 sets). No placeholder/fabricated content anywhere — every field traces back to
the Kaggle dataset, shobu.io's DuelMastersCards.json, or the Fandom wiki.

## Product Principles

1. Fast, direct browsing over cinematic presentation — this is a reference tool, not a
   showcase; every interaction should feel instant, never gated behind a slow animation.
2. Complete and accurate over polished-but-thin — flavor text, illustrator, and rules text
   accuracy matter as much as visual design (explicit owner priority).
3. Self-contained and stranger-legible — since the link gets shared with other collectors,
   nothing should depend on tribal knowledge of how the database was built.
4. Static and lightweight by design — no backend, no account system, no build step; keep the
   dependency surface minimal so the site stays fast and easy to maintain solo.

## Accessibility & Inclusion

No specific requirement established yet; standard reasonable practice is sufficient for now.
