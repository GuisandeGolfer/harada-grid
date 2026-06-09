# Harada Grid — Decisions & Traits (review me)

A single-file web app for building and sharing a **Harada Method "Open Window 64"** goal chart.
Open `index.html` and you're running it — no build, no server, no accounts.

---

## What it does (feature list)

- **9×9 Open Window 64 grid**
  - Center cell = your **main goal** (dark).
  - 8 cells around it = your **8 themes** (red-outlined).
  - Each theme is **mirrored** into the center of one outer 3×3 block; the 8 cells around it = that theme's **actions**. 64 actions total.
  - Edit a theme once → both copies (center ring + block center) update live.
- **Click-to-focus editing (the headline interaction)**
  - Click any square → it **lifts above the grid** (raised z-index) and **expands into a centered card** via a **FLIP animation** (it literally grows out of the square it came from).
  - Rest of grid **dims + blurs**.
  - Card has two fields: **name** (short) + **description / notes** (long).
  - Click outside, press **Esc**, or hit **Done** → card **animates back down into its exact square**.
- **Auto-save** to `localStorage` — refresh and your chart is still there.
- **Share link** — packs the whole chart into the URL (`#c=...`), copies it to your clipboard. Anyone who opens it sees your chart **read-only** with a **"Make my own copy"** button.
- **Save image (PNG)** — renders the grid to a clean 1200px PNG for posting to followers.
- **New** — clears to a blank chart (with confirm).
- **Responsive** — grid scrolls on phones; the focus card becomes a full-width bottom sheet on small screens.

---

## Key decisions & why

| Decision | Why |
|---|---|
| **Single static `index.html`, vanilla JS, no framework, no build** | Scope is one screen. Hostable anywhere free, openable from a file, nothing to maintain. Fastest path to a thing you can test. |
| **No backend / no accounts** | Your explicit choice. The chart lives on-device; sharing travels *in the link*, so there's no server to run, pay for, or secure. |
| **Share state in the URL hash (Base64 of the JSON), not a database** | Zero infra. Your data never leaves your browser until you choose to share, and even then it's encoded in the link itself, not stored by anyone. |
| **No compression library** | Keeps the app 100% dependency-free and offline-capable. A fully-filled chart link is ~2.4k characters — fine for direct sharing. *Tradeoff:* a few platforms truncate long URLs; the **PNG export is the fallback** share path. If links prove too long in practice, dropping in LZ-string compression (~3KB) cuts them ~60% — noted as a future option. |
| **Hand-drawn PNG on `<canvas>`** (instead of an html-to-image library) | Self-contained, crisp, and I fully control how the shared image looks. No external dependency. |
| **FLIP technique for the expand/collapse** | Measures the square, then animates the card from that exact rect to center (and back). Smooth, GPU-friendly, no layout thrash. |
| **`localStorage` autosave, URL only touched on Share** | Address bar stays clean while you work; your progress survives refreshes/crashes. |
| **Themes stored once, rendered twice** | Matches the real Mandalart structure and guarantees the two copies can never drift. |

---

## Aesthetic traits

- **Clean minimal**, warm paper background, **one accent**: a restrained vermillion **seal red** (`#c0392b`) — a quiet nod to the method's Japanese origin without going full samurai theme.
- Type pairing: **Fraunces** (characterful display serif) for goal names/titles + **Hanken Grotesk** (refined sans) for UI — deliberately *not* Inter/Arial/system defaults.
- Each of the 8 blocks carries a barely-there hue tint (`hsl(theme*44 38% 97%)`) so the structure reads at a glance without shouting.
- Main goal is solid ink; themes are red-outlined; empty squares show a faint `+`.

---

## Data model (the whole thing)

```js
{
  title:   "…",                       // chart title / author line
  core:    { name, desc },            // main goal — grid center
  themes:  [ {name,desc} × 8 ],       // 8 supporting themes (mirrored)
  actions: [ [ {name,desc} × 8 ] × 8 ] // 8 actions per theme = 64
}
```
This single object is what gets saved, encoded into a share link, and drawn into the PNG.

---

## Known limits / things we deliberately skipped (YAGNI)

- No daily check-off, streaks, or journaling (full Harada extras) — **grid only**, by your choice.
- No login / cloud sync / public profile pages — sharing is link + image.
- No collaboration / real-time editing.
- Long share URLs on a fully-detailed chart (see compression note above).
- Fonts load from Google Fonts (needs internet for the *prettiest* render; the app still works offline with fallback fonts).

---

## How to run / test

See `SERVING.md`. Quickest: open `index.html` directly in a browser, or serve the folder and visit `localhost`.

**Test checklist for you:**
1. Click squares, fill the center (main goal), the 8 themes, some actions.
2. Confirm editing a **theme** updates both the center ring and its block's center.
3. Refresh → your work is still there.
4. **Share link** → open it in a private/incognito window → you should see your chart read-only → **Make my own copy** → it becomes editable.
5. **Save image** → check the PNG looks postable.
6. Open on your phone → grid scrolls, focus card is a bottom sheet.

After you've poked at it, tell me what to fix or refine.
