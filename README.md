# Marketing Budget & Expense Control Tower — FY 2026-27 (Portfolio Demo)

Self-contained HTML control tower that tracks marketing budget vs. actual expense across the business
units of a fictional group, **NexaGroup**. Portfolio demo version — **all data is synthetic demo data**
(fictional units, role placeholders instead of names, deterministically rescaled figures).

See `PROFILE.md` for the full project write-up.

## Contents

- `index.html` — the dashboard (Chart.js 4.4.1 from CDN, no build step, no backend)
- `data.json` — the same synthetic dataset as a standalone file (brand units + trade units)
- `PROFILE.md` — portfolio overview: problem, solution, features, stack, disclaimer, deploy note

## Structure

Three tabs:

1. **Overview** — group-wide brand budget, expense to date, utilization %, reporting coverage, no-data
   units, budget-vs-expense by unit, campaign-mix doughnut, monthly budget vs. actual for the units that
   report actuals monthly, and the full SBU roster table.
2. **SBU-wise Analysis** — per-unit drill-down: KPIs, campaign-type split, monthly phasing (or expense
   breakdown / utilization fallback) and a high-level ATL / BTL / Digital / Other read.
3. **Trade Marketing Budget** — variable trade commissions kept separate from brand spend.

## Data notes

- FY 2026-27 (Jul 2026 – Jun 2027); figures in BDT Crore (1 Cr = 10,000,000 BDT); expense as of 30 Sep 2026.
- 13 brand units + 2 trade units; `status` is `reporting` (actuals posted) or `budget` (plan only).
- The dataset is inlined in `index.html` **and** mirrored in `data.json`. Served over `http(s)` the page
  reads `./data.json` and re-renders, so both views stay in sync; from `file://` the identical inlined
  copy is used. Monthly phasing is kept from the inline copy.
- Business-unit names, custodian labels, source/tracker names and all figures are fictional and randomized.

## Run locally

```bash
python -m http.server 8080
# then visit http://localhost:8080/  — or open index.html directly in a browser
```

## Deploy (Vercel)

Static, no build: import the repo, choose framework **Other**, leave the build command empty.
`index.html` is served at the root and `data.json` sits beside it.
