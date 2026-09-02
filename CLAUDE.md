# CLAUDE.md — Operating Manual for harada-grid ("Open Window 64")

Read this whole file before touching anything. When unsure, stop and ask Diego.

## 1. Purpose & Status

**This site is LIVE IN PRODUCTION at https://harada-grid.com.** It is a Harada-method
/ Ohtani "Open Window 64" mandala chart maker, branded **Open Window 64**: a 9×9 grid
(1 main goal, 8 themes mirrored into 8 blocks, 64 actions) with click-to-focus editing,
localStorage autosave, share-by-URL, and PNG export. It is strategically important:
it's Diego's proof-of-shipping and the planned seed of a larger habit app. Real users
have real charts saved in their browsers and real share links in the wild. Treat every
change as a production deploy, because it is one.

## 2. Architecture & Serving

- **The entire app is one file: `index.html`** (~550 lines: CSS + HTML + vanilla JS).
  No framework, no build step, no bundler, no npm, no server code, no env vars.
  Only external dependency: Google Fonts (Fraunces + Hanken Grotesk) — app still works
  offline with fallback fonts.
- **Repo:** `git@github.com:GuisandeGolfer/harada-grid.git`, branch `main`.
  Other files: `DECISIONS.md` (feature list, rationale, data model — read it),
  `SERVING.md` (run/host options), `docs/superpowers/specs/` (original design spec).
- **Hosting (verified 2026-07-06):** served by **Netlify** on `harada-grid.com`
  (`server: Netlify` response header). The live file is byte-identical to git HEAD.
  GitHub Pages is NOT enabled. There is no `netlify.toml` or `.netlify/` in the repo,
  so whether Netlify auto-deploys from GitHub or Diego deploys manually is not
  recorded here — **do not assume `git push` ships it**. Procedure: commit, push to
  `main`, then verify: `curl -s https://harada-grid.com | md5sum` vs
  `md5sum index.html`. If they differ, the deploy didn't happen — tell Diego so he
  can trigger/drag the Netlify deploy. Never claim "deployed" without this check.
- **Local testing:** `python3 -m http.server 8080` in this folder (see `SERVING.md`);
  on WSL2 open it from the Windows browser. Bind to `0.0.0.0` if testing from
  Diego's phone/Mac over Tailscale.
- **Key code landmarks** (comment banners inside `index.html`): `state`,
  `persistence` (`LS_KEY = "haradaGrid.v1"`), `share encoding` (`pack`/`unpack`,
  `b64encode`/`b64decode`, `encodeState`/`decodeState`), `grid render`,
  `focus card (FLIP)`, `PNG export`, `buttons`, `boot`.

## 3. Conventions

- Everything stays in `index.html`. Do not split into multiple files, add a `src/`
  tree, or introduce a build step.
- Vanilla JS + CSS only. No libraries, no CDN scripts, no npm packages.
- Keep the existing section banner comments; add code inside the matching section.
- Aesthetic is deliberate (see `DECISIONS.md`): warm paper background, one accent
  color `#c0392b` (seal red), Fraunces + Hanken Grotesk. Don't restyle casually.
- Data model (the whole thing): `{ title, core:{name,desc}, themes:[{name,desc}×8],
  actions:[[{name,desc}×8]×8] }`. Themes are stored once, rendered twice (ring +
  block center) — never duplicate theme data.
- YAGNI is policy: no check-offs, streaks, collaboration, analytics, or cloud sync
  unless Diego explicitly asks.

## 4. Mistakes a Weaker Model WILL Make Here — Named Rules

1. **Breaking every shared link ("schema drift").** Chart data is packed by `pack()`
   into `[title,[n,d],[[n,d]×8],[[[n,d]×8]×8]]`, JSON-stringified, URL-safe-base64'd
   into `#c=...`. Those URLs are out in the wild and are the *only* copy of shared
   charts. Rule: never change `pack`/`unpack`/`b64encode`/`b64decode` output shape.
   If a new field is truly needed, append it, keep `unpack` tolerant of old arrays
   (it already defaults missing fields), and test an OLD pre-change link still loads.
2. **Nuking users' saved charts.** Autosave lives in `localStorage` under
   `"haradaGrid.v1"`. Renaming that key, or changing the stored JSON so `normalize()`
   rejects it, silently deletes every user's chart. Rule: don't rename the key;
   any format change must still load existing stored data (test it).
3. **"It's just a small tweak" — shipping breakage to real users.** This is a public
   production site, not a sandbox. Rule: test locally per the checklist in §5 before
   pushing, and run the md5 deploy check from §2 after.
4. **Adding a backend, accounts, or a database.** No-backend is a *design decision*
   (see `DECISIONS.md`), not an omission. Sharing travels in the link; data lives
   on-device. Rule: never add auth, server code, APIs, cookies, or third-party
   storage. If a task seems to require one, stop and escalate.
5. **Adding dependencies or a build pipeline.** A weaker model reflexively reaches
   for React/Tailwind/html2canvas/npm. Rule: zero dependencies. The PNG export is
   hand-drawn on `<canvas>` on purpose; the FLIP animation is hand-rolled on purpose.
6. **Editing the wrong artifact / partial deploys.** There is exactly one deployable
   file. Rule: changes go in `/home/diegog/workspace/harada-grid/index.html`, get
   committed on `main`, and the live-vs-local md5 must match afterward. Don't create
   `index2.html`, `main.js`, or "refactor" files.

## 5. Quality Bar (all checkable — run before saying "done")

- [ ] `index.html` opens locally with no console errors.
- [ ] Fill goal + a theme + an action; refresh → still there (localStorage OK).
- [ ] Editing a theme updates BOTH copies (center ring + its block's center).
- [ ] Share link: open in incognito → chart renders read-only → "Make my own copy"
      makes it editable and clears the hash.
- [ ] **An old share link generated BEFORE your change still decodes correctly.**
- [ ] "Save image" produces the 1200px PNG and it looks right.
- [ ] Narrow window/phone: grid scrolls, focus card becomes a bottom sheet.
- [ ] Still exactly one HTML file, zero new dependencies, no build step.
- [ ] After push: `curl -s https://harada-grid.com | md5sum` == `md5sum index.html`.

## 6. Escalation — Stop and Ask Diego When

- A change would alter the URL `#c=` encoding, `pack`/`unpack`, or the localStorage
  key/format in any non-backward-compatible way.
- Anything touching Netlify/DNS/domain config, or the live md5 check fails after push.
- A feature request implies accounts, sync, a server, payments, or analytics.
- You want to add any dependency, file, or build tooling.
- Deleting or rewriting `DECISIONS.md`, `SERVING.md`, or the docs spec.
- The habit-app expansion: that's a strategy conversation, not a unilateral refactor.
