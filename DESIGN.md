# Design

Visual system for the Duel Masters card database web app. Written from the built
implementation in `index.html` (single file, no build step, no framework).

## Thesis

A card-shop binder page, not a SaaS dashboard. The grid **is** the product; chrome recedes
to a thin instrument rail across the top. Every interaction acknowledges input instantly —
motion never gates the visitor, because this is a reference tool used at speed.

## Color

Near-black ink ground under warm lamp. The five civilization colors are the only saturated
hues on the surface and carry the single coding system — a civilization owns its filter
chip, its grid dot, its card-edge tint on hover, and its segment of the deck balance bar.
Chrome is neutral: 1px hairlines plus tonal elevation, never glass-as-decoration.

| Token | Value | Role |
|---|---|---|
| `--bg` | `#0b0d12` | Page ground |
| `--bg-elev` | `#14171f` | Cards, inputs, drawer |
| `--bg-elev-2` | `#1c202b` | Tags, wells, hover ground |
| `--bg-elev-3` | `#252a38` | Toast, scrollbar thumb, raised chips |
| `--border` | `#272c3a` | Default hairline |
| `--border-strong` | `#394054` | Hover hairline |
| `--text` | `#eceef4` | Primary |
| `--text-dim` | `#a2a8ba` | Secondary |
| `--text-faint` | `#8b92a6` | Tertiary — set at 4.5:1 on `--bg`, do not darken |
| `--accent` | `#8f9dff` | Focus ring, deck membership, curve bars |

Civilizations: Light `#f7d64a` · Water `#45a9f5` · Darkness `#bb74dd` · Fire `#ff6b52`
· Nature `#4fd97f`. Dual-civilization cards render a split dot (a hard 50/50 linear
gradient), never a single wrong color.

**Contrast rule:** `--text-faint` is the floor. It passes 4.5:1 on `--bg` but only 3.6:1
on `--bg-elev-2` — never use it for body copy on the raised surfaces, only for labels and
meta text at the sizes currently shipped.

## Type

- **Display** — `Chivo` (600/700/900): headings, card names, buttons, selects, tags,
  numerals. A grotesque with enough character to read as a game artifact rather than a
  dashboard.
- **Body** — `Source Sans 3` (400/600): dense rules text, flavor text, search input.
  Chosen for legibility at 13–14px, since accurate rules text is a stated product priority.
- `font-variant-numeric: tabular-nums` globally — costs, counts and power values sit in
  columns and must not shimmy as they change.
- Rules-text measure capped at `68ch`.

## Motion

Fast and punchy, never cinematic. **Every duration is a ceiling, not a target.**

| Token | Value | Use |
|---|---|---|
| `--t-instant` | 90ms | Focus rings, color and border changes |
| `--t-fast` | 120ms | Card hover lift, backdrop fade, toast |
| `--t-base` | 150ms | Drawer slide, curve/bar growth, image fade-in |
| `--t-modal` | 200ms | Modal entrance (the one authored moment) |
| `--t-exit` | 110ms | Every exit — dismissal is always faster than arrival |

Easing: `cubic-bezier(0.16, 1, 0.3, 1)` for anything arriving or growing;
`cubic-bezier(0.5, 0, 0.85, 0.3)` for anything leaving. Never the browser default `ease`.

The card press is the signature interaction: hover lifts `translateY(-4px) scale(1.025)`
with a sharpened shadow and the civilization edge tint fading in; `:active` compresses to
`scale(0.985)` over 80ms, so a click feels like a physical button rather than a link.

**Banned here** (they fight the product's purpose): staggered load-in, scroll reveals,
parallax, lingering skeletons, and any animation the visitor must wait out before acting.

**Reduced motion:** `prefers-reduced-motion: reduce` collapses durations to ~0 and removes
the card lift and modal transform, but state changes still complete — the modal reaches
full opacity, focus rings still appear. Reduced motion means less movement, not less
feedback.

## Components

- **Instrument rail** — sticky, blurred, hairline-bottomed. Title + live count, then a
  full-width search, then civilization chips and compact selects. Wraps rather than clips.
- **Card** — a `<button>` (not a div), 5:7 scan, two-line clamped name, a centered civ-dot
  + set footer (no mana number here — it's already printed on the card art itself).
  Images fade in on load; a failed image degrades to a labeled tile, never a broken icon.
  Deck membership shows as an accent border plus a quantity badge. A **stepper** (`−`
  `+` and the count) sits over the foot of the scan — never the head, where the printed
  name and cost live — visible on hover/focus on desktop and always visible on touch
  (`hover: none`, plus a ≤720px fallback for hybrids). The `−` only renders once the
  card is actually in the deck. Adding must be undoable where it was done: a `+` with no
  `−` is an incomplete control, not a simpler one.
  The `.meta` block's height must stay measured, not guessed: `VGrid.measureMetaH()`
  renders a hidden probe at the real column width and reads its true height, because a
  hardcoded estimate silently drifts out of sync with row height (absolute-positioned
  rows) the next time type or padding changes, producing card-to-card overlap.
- **Deck view** — the drawer is a *summary*; the deck itself is seen as cards. "View deck
  as cards" turns the grid into the deck, one tile per copy, in deck order, with a banner
  naming the deck and a way back. Emptying the deck from inside the view exits it rather
  than leaving a blank grid.
- **Buttons** — filled surfaces, never dim outlines: the whole control carries the color
  and the label sits at full contrast on it (all ≥6:1 measured). Civilization chips take
  their civilization's color as the fill of the entire chip, with dark ink on top when
  selected — the color *is* the label, so it is never reduced to a dot beside gray text.
  Destructive actions (Empty) wear the Fire hue on the surface itself, not only on hover.
- **Modal** — scale `0.96 → 1` plus fade over 200ms in, 110ms out. Carries prev/next, a
  quantity stepper, and search-term highlighting in the rules text.
- **Deck drawer** — slides on `transform` (never an animated layout property). Advisory
  notices, a real mana curve, and a civilization balance bar, all from live card data.

## Browser surfaces

Themed rather than left to defaults: scrollbar track and thumb, text selection, and focus
rings. Focus is a two-layer `box-shadow` (not `outline`) so it follows each element's
border radius.

## Non-negotiables

1. **No build step.** Plain HTML/CSS/JS in one file, by deliberate decision.
2. **Virtualization stays.** Only rows near the viewport are in the DOM; 1010 live nodes
   is not acceptable on a phone. Anything touching the grid must go through `VGrid`.
3. **Deck rules stay advisory.** Report the 40-card minimum and 4-copy limit; never block.
4. **Motion ceilings hold.** Nothing above 200ms except with a stated reason.
