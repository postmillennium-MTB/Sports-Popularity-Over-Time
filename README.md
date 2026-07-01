# Sports Popularity Over Time

An interactive chart comparing the rise and fall of 17 individual (non-team) sports across two lenses — modeled search interest and estimated US participation — with an animated "play" mode, head-to-head pairings, and four school-branded color themes.

**File:** `sports-popularity.html`
**Type:** Single self-contained HTML file — no build step, no server, no external requests at runtime.

---

## What it does

- **Trends tab** — select any combination of 17 sports and watch their lines draw left-to-right across time, year by year.
- **Pairs tab** — five preset head-to-head comparisons (Skateboarding vs. Rollerblading, Tennis vs. Pickleball, BMX vs. Mountain Bike, Golf vs. Disc Golf, Skiing vs. Snowboarding) with peak values, current values, and the crossover year where one overtakes the other.
- **Two data modes:**
  - **Search Interest** (2004–2024) — relative search volume, indexed so golf's ~2000 Tiger Woods–era peak = 100. Limited to the Google Trends era.
  - **Estimated Participants** (1980–2024) — millions of US participants per year. This is the one that captures the pre-internet history search data can't: BMX's 1985 peak, rollerblading's 1996 boom, racquetball's 1980 high point.
- **Play controls** — Play/Pause, Reset, three speeds (Slow/Med/Fast), and a scrubber bar you can tap or drag to jump to any year directly.
- **Four color themes**, switchable via buttons at the top:

  | Theme | Colors |
  |---|---|
  | St Mark's | Blue with gold highlights |
  | St Phil's | Maroon and gold |
  | St Bernard's | White, light grey, navy |
  | St Thomas | White base with maroon trim and lettering |

  The two light themes (St Bernard's, St Thomas) automatically swap in a darker, more saturated line palette so all 17 sports stay readable against a white background. The two dark themes use a brighter palette tuned for contrast against navy/maroon.

### Current defaults on load
Opens in the **St Thomas** theme, **Estimated Participants** mode, with **Pickleball, Mountain Bike, Disc Golf, and Racquetball** selected, and **auto-plays at medium speed** from 1980.

---

## Data sources and methodology

Values are curated estimates, not a live data feed — the tool has no network calls of any kind. They're built by interpolating between a set of "anchor years" pulled from published sources:

- **SFIA (Sports & Fitness Industry Association) Topline Participation Reports**
- **NSGA (National Sporting Goods Association)** historical surveys
- Sport federation data (PDGA for disc golf, NSAA for snowsports, etc.)
- Google Trends benchmarks (search interest mode only)

Notable confirmed figures baked into the data:
- Racquetball peaked at ~12M US players in **1980**.
- Tennis was ~30M players in 1980, crashed to ~11M by 1995 (aerobics/racquetball competition), then recovered to ~17–18M.
- Rollerblading peaked at ~18M participants around **1996–97**, then collapsed within a decade.
- Snowboarding peaked at ~8.2M in **2001–02**, declining to ~5.8M by 2023–24.
- Mountain biking sits around 7M (2007) to 9M (recent years) per SFIA — a much smaller number than early inflated drafts of this tool used.
- Pickleball reached **19.8M** US participants in 2024 (SFIA 2025 Topline Report), up over 300% in three years.
- Skiing uses the broader "participated at least once" basis (~15M) to stay directly comparable with the snowboarding figures, which use the same basis.

Treat all values as **directionally accurate trend shapes**, not precise annual counts — especially for years before ~2000, where published year-by-year data is thinner. The "About the data" button inside the tool (ℹ️ icon, top right) shows this same summary to anyone viewing the page.

A **Facility Count** mode (tracking skatepark, bike park, and court construction over time) is stubbed in as a disabled "soon" button — a natural next step if useful for advocacy purposes, since it would show infrastructure investment against participation demand.

---

## Why it's one big HTML file

This tool bundles React, ReactDOM, and Recharts directly into the file (via esbuild) rather than loading them from a CDN like unpkg or jsDelivr. That's a deliberate tradeoff:

- **Pro:** Works completely offline, on flaky mobile connections, inside a Pinkbike iframe, or anywhere a CDN request might be blocked, rate-limited, or slow. There is nothing for the browser to fetch after the page itself loads.
- **Con:** The file is ~540KB instead of ~35KB, because the libraries live inside it. Code editors and file viewers may show a "syntax highlighting disabled due to code size" notice when you open the raw file — that's cosmetic only and has no effect on the tool running correctly.

This matches the deployment pattern used across the other Post Millennium Renaissance tools: single-file, zero external dependencies, GitHub Pages–ready.

---

## Deployment

Drop `sports-popularity.html` directly into a GitHub Pages repo, or embed it via an iframe wrapper page, same as the other tools in the [postmillennium-MTB](https://github.com/postmillennium-MTB) suite. No build step is required at deploy time — the bundling already happened when this file was generated.

---

## Editing this tool

Because the shipped file is minified and bundled, it isn't meant to be hand-edited directly. Changes should be made to the pre-bundle React source (`entry.jsx` — sport catalog, data arrays, theme definitions, and component logic) and re-bundled with esbuild:

```bash
npx esbuild entry.jsx --bundle --minify --loader:.jsx=jsx --jsx=automatic --outfile=bundle.js
```

The resulting `bundle.js` is then wrapped in a minimal HTML shell (a `<div id="root">` plus one inline `<script>` tag) to produce the final single-file output.

If you don't have the `entry.jsx` source file, ask Claude to reconstruct it from the shipped HTML, or request that changes be made and rebundled in a fresh session — provide the specific change and reference this README for context on the project's structure and conventions.
