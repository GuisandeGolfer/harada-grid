# Open Window 64 Growth Roadmap

## Objective

Grow `harada-grid.com` into the most useful free Open Window 64 / Harada Method
planning tool for people who need to turn one goal into concrete actions. The
business model can include display ads later, but the first job is earning
repeat use, shares, and organic search visibility with a genuinely useful
tool and original educational material.

## Positioning

**Product:** an instant, private, no-login goal-chart maker with share links,
PNG export, and printable output.

**Core search language:**

- Harada Method template
- Open Window 64 template
- Mandala chart maker
- Ohtani goal sheet template
- 64-goal planner

Use "Ohtani goal sheet" only as an educational search term and make no claim
of endorsement or affiliation. The product is the Harada/Open Window 64 method,
not a celebrity fan site.

## Guardrails

- Keep the existing no-backend, no-login, single-file architecture unless a
  feature clearly requires a change.
- Do not create AI-generated pages at scale or thin keyword pages.
- Every guide and template must solve a distinct user problem and contain an
  original completed example, practical instructions, or both.
- Keep the chart useful without an AI API, account, payment, or cookie consent
  dependency.
- Do not add display ads until the site has substantive supporting content and
  real organic traffic. Avoid ads that interrupt editing, export, or printing.

## Phase 0: Baseline And Measurement

**Goal:** establish what exists before expanding the product.

1. Verify the live homepage matches the repository and works on desktop and
   mobile: editing, autosave, share link, make-a-copy, and PNG export.
2. Set up Google Search Console for `harada-grid.com` and submit a sitemap once
   multiple indexable pages exist.
3. Add privacy, terms, contact, and about pages before applying to an ad
   network. The about page should identify the actual creator and purpose of
   the tool.
4. Establish event tracking that does not collect chart contents:
   `chart_started`, `template_selected`, `share_clicked`, `png_exported`, and
   `print_clicked`.
5. Record a starting baseline: indexed pages, impressions, clicks, queries,
   chart starts, exports, and shares.

**Exit condition:** the current tool is stable, measurable, and has trustworthy
site-level pages.

## Phase 1: Improve The Core Tool

**Goal:** make the existing page the best answer for a user who lands directly
from search.

1. Add a template picker that loads sample chart data locally. Initial set:
   - Build a fitness habit
   - Launch a one-person service business
   - Change careers in 12 months
   - Study plan for a certification
   - Improve a baseball skill
   - Plan a creative project
2. Add a print-optimized layout and a blank printable PDF. Keep the chart
   editable before printing.
3. Add a concise on-page explanation of the 9x9 structure, the eight themes,
   and the 64 actions. It should answer the user's immediate question without
   burying the tool.
4. Add accessible titles, labels, keyboard behavior, and a clear empty-state
   walkthrough.
5. Preserve existing URL-share and localStorage formats. Any template loading
   must not overwrite a user's chart without an explicit confirmation.

**Exit condition:** a first-time visitor can choose a relevant example, edit it,
and export or share it in under five minutes.

## Phase 2: Build Original Search Content

**Goal:** create a small, high-quality content cluster that supports the tool.

Publish one strong page at a time. Each page should link to the tool and to at
least one relevant template. Start with:

1. `What is the Harada Method?`
2. `How to fill out an Open Window 64 chart`
3. `Ohtani goal sheet: structure, example, and blank template`
4. `Harada Method example: launching a service business`
5. `Harada Method example: a fitness goal`
6. `Mandala chart vs. a normal goal-setting worksheet`
7. `How to turn a 64-action plan into a weekly schedule`
8. `Printable Open Window 64 template`

Each example should include the reasoning behind the eight themes and several
concrete actions. Do not publish generic goal-setting prose or lightly rewritten
copies of competitors' pages.

**Exit condition:** at least eight useful, indexable pages and six working
templates exist, all with clear internal links.

## Phase 3: Earn Distribution And Feedback

**Goal:** validate that people use and share the tool before optimizing for ad
revenue.

1. Publish short build-in-public posts that show a real blank-to-completed chart
   and link to the matching template.
2. Ask early users where they got stuck and which goals they want templates for.
3. Share genuinely useful examples in relevant communities only where that
   community permits self-promotion; lead with the completed example, not the
   link.
4. Turn repeated user questions into improvements or new guides.
5. Review Search Console monthly for queries with impressions but weak click
   through rate; improve the matching title, description, page answer, or
   template rather than publishing more generic pages.

**Exit condition:** the site has recurring organic impressions, real use of
templates/exports/shares, and evidence of which use cases resonate.

## Phase 4: Monetize Carefully

**Goal:** add revenue without reducing the tool's usefulness or search quality.

1. Apply for AdSense only after the tool, guides, policy pages, and contact
   information are established. Keep ad density low.
2. Place ads on guides and template-explanation pages first. Do not place ads
   inside the editing grid, over the export flow, or in the print output.
3. Compare revenue per visit against frustration signals: lower chart starts,
   exports, or shares after ads are added means reduce or remove placements.
4. Test non-ad revenue before adding more ads:
   - a polished printable template pack
   - a small guided workbook
   - an optional one-time AI planning assist, only if its cost is covered
5. Keep the core planner free and functional.

**Exit condition:** revenue is additive, user behavior remains healthy, and no
monetization choice makes the product feel made-for-ads.

## Sequence And Scope

The next implementation milestone is **Phase 1, items 1-3**: local templates,
print support, and concise explanatory copy. Do not build user accounts,
cloud sync, a database, a full habit tracker, or an AI generator before those
basics prove valuable.

## Success Metrics

Review monthly:

| Area | Leading signal | Outcome signal |
| --- | --- | --- |
| Product | template selections and chart starts | exports and share links created |
| Search | indexed pages and impressions | organic clicks and returning visitors |
| Content | guide-to-tool click-through | template completion/use |
| Revenue | ad eligibility and low-friction placements | revenue per visitor without reduced use |

The primary score is not page count or ad impressions. It is whether a visitor
can make, use, save, print, and share a plan that helps them act.
