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

**Scope (expanded 2026-11-03):** this is no longer a single-page toy. It is one tool in
Diego's **product ladder**: a set of tools/products that will later be derived from his
Obsidian vaults, the projects he works on, his skills, and AI. The longer-term aim is a
**single brand** that ties these together and can be linked to and referenced from his
personal-voice YouTube channel and Instagram. Practical consequences:
- New static pages and files are allowed (content/SEO pages, example charts, guides,
  `robots.txt`, `sitemap.xml`, images). Add every new public page to `sitemap.xml`.
- Write copy and structure with the wider brand in mind: the chart maker should be
  able to point to sibling tools and to Diego's channels later without a rewrite.
- The brand name, the other tools, and how they cross-link aren't decided yet. Don't
  invent them. Ask Diego.

**SEO status (2026-11-03):** `robots.txt` + `sitemap.xml` live. Google Search Console
and Bing Webmaster Tools set up and still indexing. Page already has a title/meta
description, canonical, OG tags, WebApplication JSON-LD, ~750 words of visible copy
("How the 9×9 chart works", "Shohei Ohtani's goal chart", 4-item FAQ). Known gaps: no
`<h1>`, no FAQPage JSON-LD, no `og:image`.

## 2. Architecture & Serving

- **The chart app itself lives in `index.html`** (CSS + HTML + vanilla JS). Static
  site files sit beside it at the repo root (`robots.txt`, `sitemap.xml`, and any new
  pages). No framework, no build step, no bundler, no npm, no server code, no env vars.
  Only external dependency: Google Fonts (Fraunces + Hanken Grotesk) — app still works
  offline with fallback fonts.
- **Repo:** `git@github.com:GuisandeGolfer/harada-grid.git`, branch `main`.
  Other files: `DECISIONS.md` (feature list, rationale, data model — read it),
  `SERVING.md` (run/host options), `docs/superpowers/specs/` (original design spec).
- **Hosting (verified 2026-07-06):** served by **Netlify** on `harada-grid.com`
  (`server: Netlify` response header). GitHub Pages is NOT enabled. There is no
  `netlify.toml` or `.netlify/` in the repo. **Netlify auto-deploys from `main`**
  (verified 2026-11-03: a push was live in ~20s). Procedure: commit, push to `main`,
  then verify every changed file live, e.g. `curl -s https://harada-grid.com | md5sum`
  vs `md5sum index.html`, and the same for any other file you touched. If they differ
  after a minute, the deploy didn't happen — tell Diego. Never claim "deployed"
  without this check. Missing files return Netlify's HTML "Page not found" page, so
  check the content you get back, not just that something loaded.
- **Local testing:** `python3 -m http.server 8080` in this folder (see `SERVING.md`);
  on WSL2 open it from the Windows browser. Bind to `0.0.0.0` if testing from
  Diego's phone/Mac over Tailscale.
- **Key code landmarks** (comment banners inside `index.html`): `state`,
  `persistence` (`LS_KEY = "haradaGrid.v1"`), `share encoding` (`pack`/`unpack`,
  `b64encode`/`b64decode`, `encodeState`/`decodeState`), `grid render`,
  `focus card (FLIP)`, `PNG export`, `buttons`, `boot`.

## 3. Conventions

- The chart app's code stays in `index.html` (don't split it into `main.js` etc.
  without asking). New pages are plain static `.html` files that reuse the same look;
  no `src/` tree, no build step.
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
6. **Editing the wrong artifact / partial deploys.** The repo is at
   `~/Desktop/harada-grid` on Diego's Mac. Chart-app changes go in `index.html`;
   everything is committed on `main`, and every changed file must match live
   afterward. Don't create `index2.html`, `main.js`, or "refactor" copies. A new page
   is fine; a duplicate of an existing one is not.

## 5. Quality Bar (all checkable — run before saying "done")

- [ ] `index.html` opens locally with no console errors.
- [ ] Fill goal + a theme + an action; refresh → still there (localStorage OK).
- [ ] Editing a theme updates BOTH copies (center ring + its block's center).
- [ ] Share link: open in incognito → chart renders read-only → "Make my own copy"
      makes it editable and clears the hash.
- [ ] **An old share link generated BEFORE your change still decodes correctly.**
- [ ] "Save image" produces the 1200px PNG and it looks right.
- [ ] Narrow window/phone: grid scrolls, focus card becomes a bottom sheet.
- [ ] Zero new dependencies, no build step. Any new public page is in `sitemap.xml`.
- [ ] After push: every changed file matches live (md5 check from §2).

## 6. Escalation — Stop and Ask Diego When

- A change would alter the URL `#c=` encoding, `pack`/`unpack`, or the localStorage
  key/format in any non-backward-compatible way.
- Anything touching Netlify/DNS/domain config, or the live md5 check fails after push.
- A feature request implies accounts, sync, a server, payments, or analytics.
  (Exception, approved 2026-10-18: the Gumroad Pro license check — see DECISIONS.md "Pro tier".)
- You want to add any dependency or build tooling. (New static pages/files are OK.)
- Naming the brand, adding cross-links to other tools or Diego's YouTube/Instagram,
  or changing the site's positioning: those are brand decisions, ask first.
- Deleting or rewriting `DECISIONS.md`, `SERVING.md`, or the docs spec.
- The habit-app / product-ladder expansion: that's a strategy conversation, not a
  unilateral refactor.
