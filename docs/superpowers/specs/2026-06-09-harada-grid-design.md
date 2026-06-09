# Harada Grid — Design Spec

**Date:** 2026-06-09
**Status:** Approved → building
**One-liner:** A single-page web app for building and sharing a Harada Method "Open Window 64" goal chart. No accounts, no backend, link-shareable.

---

## 1. Background — the Harada Method / Open Window 64

The Harada Method's signature artifact is the **Mandalart** ("Open Window 64") chart — a 9×9 grid, famously used by Shohei Ohtani in high school.

Structure:

- **Center cell** of the whole grid = the **main goal** (the one big thing).
- The **8 cells around the center** = **8 supporting themes** (the pillars that achieve the main goal).
- Each of those 8 themes is **mirrored** into the center cell of one of the 8 surrounding 3×3 blocks.
- The **8 cells around each mirrored theme** = concrete **actions** for that theme.
- Total: 1 main goal + 8 themes + 64 actions.

```
┌───────┬───────┬───────┐
│ blk A │ blk B │ blk C │   each block = 3×3
├───────┼───────┼───────┤   center block holds:
│ blk H │ CORE  │ blk D │     main goal (middle)
├───────┼───────┼───────┤     + 8 themes (ring)
│ blk G │ blk F │ blk E │   outer block X holds:
└───────┴───────┴───────┘     its theme (middle) + 8 actions
```

## 2. Goals

- Let the user fill out a complete Open Window 64 chart in the browser.
- Make it effortless to **share** a finished chart with followers (link + image).
- Let anyone who opens a shared link **start their own** copy in one click.
- Ship fast, host free, zero backend, zero accounts.

## 3. Non-goals (YAGNI)

- No accounts, login, or cloud sync.
- No daily check-off / streaks / journaling (full Harada extras) — grid only.
- No collaboration / real-time multi-user.
- No server of any kind.

(These may come later; data model is kept simple enough to extend.)

## 4. Tech approach

**Single static `index.html`** — vanilla JS + CSS, no framework, no build step.

- Hostable on any static host (GitHub Pages, Netlify, Cloudflare Pages) or opened from a file.
- No external runtime dependencies — fully self-contained so it works offline and on any host.

## 5. Data model

One plain object is the entire state — what gets saved, shared, and exported:

```js
{
  title:    string,            // optional chart title / author line
  core:     { name, desc },    // main goal (grid center)
  themes:   [ {name, desc} x8 ],   // 8 supporting themes
  actions:  [ [ {name, desc} x8 ] x8 ]  // 8 actions per theme
}
```

- `themes[i]` is **mirrored**: it renders both in the center block's ring AND as the
  center of outer block `i`. Editing it in the focus card updates both places.
- The grid renders each cell's **`name`**; the **`desc`** is only shown/edited in the focus card.

### Cell → grid mapping

The 9×9 grid is 81 cells. Block `i` (0–7, clockwise/reading order around center) occupies a 3×3 region. Within the center block: middle = `core`, the other 8 = `themes[0..7]`. Within outer block `i`: middle = `themes[i]` (mirrored), other 8 = `actions[i][0..7]`. Mapping is computed, not stored.

## 6. Interactions

### Edit via focus card (the key interaction)
1. Click any cell → that cell **lifts above the grid** (raised z-index) and **animates/scales up into a centered focus card** using the **FLIP technique** (measure the cell's rect, animate from it to the centered card).
2. Rest of grid **dims + blurs** behind a backdrop.
3. Focus card shows two fields: **Goal name** (short, one line) and **Description** (multi-line notes).
4. Click outside / Esc / "Done" → card **smoothly animates back into its exact grid slot**; backdrop fades out.
5. Editing a theme in its card updates both mirrored positions live.

### Top bar
- Chart title / author (editable inline).
- Buttons: **Share link**, **Save image (PNG)**, **New / Clear** (with confirm).

### Sharing
- **Share link:** serialize state → encode → place in URL hash (`#c=...`). Opening that URL shows the filled chart in **read-only** mode with a prominent **"Make my own"** button that copies it into the visitor's own editable local copy.
- **Save image:** render the grid to a **PNG** (hand-drawn on a `<canvas>` from state, high-res) for posting to social.

### Persistence
- Auto-save state to **`localStorage`** on every edit; reload restores it.
- URL is only written when the user hits **Share** (keeps the address bar clean during editing).

## 7. Encoding (share links)

- `encode(state)` = minify to compact nested arrays (drop keys) → `JSON.stringify` → UTF-8-safe Base64 → URL hash.
- `decode(hash)` reverses it.
- Tradeoff: no compression lib = slightly longer URLs but **zero dependencies**. If URLs get too long in practice, add LZ-string compression later (documented in DECISIONS.md). Long URLs are fine for direct link sharing; if a platform truncates them, the PNG is the fallback share path.

## 8. Visual design

- **Clean minimal**, one accent color (a restrained vermillion **seal red** — a quiet nod to the method's Japanese origin without going full theme).
- Distinctive type pairing (characterful display serif + refined sans body); **no** Inter/Arial/Roboto/system defaults.
- Generous whitespace, precise grid lines, faint per-theme tints so the 8 blocks read at a glance.
- Empty cells show a subtle "+" / placeholder so it's obvious they're fillable.

## 9. Responsive / mobile

- 64+ cells are dense. On phones the grid scales down and is horizontally scrollable / pinch-zoomable; the **focus card is full-bleed** on small screens so editing is comfortable.
- Followers will open share links on phones — read-only view + "Make my own" must work on mobile.

## 10. Accessibility

- Cells are real buttons; focus card traps focus, closes on Esc, returns focus to the originating cell.
- Sufficient contrast; visible focus rings; the grid is keyboard-navigable.

## 11. Success criteria

- User can fill all 81 cells, refresh, and keep their work.
- Click→expand→edit→click-out animation is smooth (no layout jank).
- A share link round-trips: open in a fresh browser → see the exact chart → "Make my own" → editable copy.
- PNG export produces a legible, postable image of the chart.
- Works on desktop and phone.

## 12. Deliverables

- `index.html` — the whole app.
- `DECISIONS.md` — traits + decisions + how-to-run + known tradeoffs, for review.
- `SERVING.md` — how to host/share.
