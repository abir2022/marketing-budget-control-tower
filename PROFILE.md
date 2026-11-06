# Marketing Budget & Expense Control Tower

> A zero-build, self-contained HTML dashboard that puts brand and trade marketing budget vs. actual spend for every business unit of a fictional group, `NexaGroup`, on one screen.

**Problem.** In a multi-business-unit group, marketing spend lives in dozens of disconnected trackers —
brand versus trade budgets, ATL/BTL/digital/OOH splits, monthly phasing, custodians who report at
different maturity levels. Leadership has no single fast answer to three questions: who is on budget,
who has actually reported spend, and where the money is going.

**Solution.** One static HTML file with three linked views — an executive **Overview**, a per-unit
**SBU-wise Analysis** drill-down, and a dedicated **Trade Marketing** tab — computed and drawn entirely
client-side with Chart.js. No backend, no bundler, no framework. It opens from a double-click and deploys
as-is; figures are synthetic demo data generated for a portfolio.

## Key features

- Overview KPI strip: total brand budget, expense to date, utilization %, reporting coverage, no-data units.
- Budget-vs-expense bar chart ranked by business unit, plus a campaign-mix doughnut (ATL / Digital / BTL / OOH / gifts / research / salary / other).
- Monthly budget-vs-actual chart for units that phase their spend, with automatic fallback to an expense-breakdown or utilization view where phasing is absent.
- Per-unit drill-down (dropdown) with its own KPIs, campaign-type doughnut, monthly phasing and a high-level ATL / BTL / Digital / Other read.
- Separate Trade Marketing tab so large variable sales commissions never skew brand-vs-brand comparisons.
- Responsive dark UI, tabbed layout, color-coded utilization bars and status chips (`Reporting` / `Budget only` / `No data`).
- Data model in one place: the dataset is inlined in `index.html` and mirrored in `data.json`. Served over `http(s)` the page re-reads `./data.json` and re-renders, so editing the JSON edits the dashboard; opened from `file://` the identical inlined copy is used.

## Tech stack

- HTML5 + hand-written CSS (no framework, no build step)
- Chart.js 4.4.1 (CDN)
- Vanilla JavaScript (ES2017+, async fetch)
- JSON data model (`data.json`)

## Synthetic demo data — disclaimer

**This is a portfolio demo with 100% synthetic data.** The organization (`NexaGroup`), the business
units (`Nexa Steel`, `Nexa Cement`, `Nexa AgroFeeds`, `Nexa Ceramics`, …), the custodian labels, the
tracker/source names and every figure are fictional. Original client, vendor and person identifiers were
removed, and the amounts were deterministically rescaled per business unit (factors roughly 0.83–1.12,
applied uniformly to each unit's budget, expense, category split and monthly phasing) so realistic
magnitudes and chart shapes remain while no real number is recoverable as-is. Custodian fields are
role placeholders (`Brand Lead (Steel)`), not people. Any resemblance to real organizations, people or
figures is coincidental.

## Data model

- Fiscal year FY 2026-27 (Jul 2026 – Jun 2027), figures in BDT Crore (1 Cr = 10,000,000 BDT), as of 30 Sep 2026.
- `brand_sbus[]` — 13 units: `name`, `custodian`, `status`, `budget_cr`, `expense_cr`, category columns (`atl_cr`, `digital_cr`, `btl_cr`, `ooh_cr`, `gift_cr`, `research_cr`, `salary_cr`, `other_cr`), optional expense-type columns (`outlet_opening_cr`, `outlet_decoration_cr`), `source`.
- `trade_sbus[]` — 2 units, same shape, tracked apart from brand spend.
- Monthly phasing lives only in `index.html` (per unit `monthly.budget` / `monthly.actual`) and survives the `data.json` merge.
- Current demo totals: 116.13 Cr brand budget, 6.66 Cr brand expense (5.7% utilization), 176.28 Cr trade budget.

## Run locally

```bash
python -m http.server 8080
# visit http://localhost:8080/   — data.json is then read live
# or simply double-click index.html (uses the identical inlined dataset)
```

## Vercel import note

Static site, no build. Import the repo, framework **Other**, leave build/output commands empty —
`index.html` is served as the root document and `./data.json` resolves next to it. No `vercel.json`
or install step required.
