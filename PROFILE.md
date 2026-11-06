# Marketing Budget & Expense Control Tower — Project Profile

## 1. Abstract

This project consolidates the marketing budget-versus-actual expense position of a fictional
multi-business-unit group ("NexaGroup") into one static, zero-backend control tower for FY 2026-27.
Thirteen brand-marketing units and two trade-marketing units are tracked in BDT Crore with an
as-of date of 30 Sep 2026. The dashboard computes group and per-unit utilization, aggregates an
eight-way campaign-type mix (ATL, Digital, BTL, OOH, gifts, research, salary, other), phases spend
month by month where trackers provide phasing, and separates variable trade commissions into their
own pool so brand-vs-brand comparisons stay fair. The dataset is dual-sourced: inlined in the HTML
and mirrored in `data.json`, with an http-time hydration step that lets the JSON file edit the live
dashboard while `file://` opening stays fully offline-capable. All figures are synthetic. The
interface implements a v2 design contract — token-driven dark/light theming, KPI cards with delta
chips, formula-bearing chart tooltips, sticky-header tables and reduced-motion-aware animation —
as a single portable artifact deployable to any static host.

## 2. Problem Framing

In a diversified group, marketing spend lives in dozens of disconnected spreadsheets owned by
different brand teams: some report a lump annual budget, some post monthly actuals, some mix
variable dealer commissions with discretionary brand money, and custodians are at very different
maturity levels. Leadership therefore cannot answer three routine questions quickly: what is
committed versus actually spent per unit, which units have genuinely started reporting, and where
the money is going by campaign type. Manual consolidation is a recurring, error-prone exercise.
The artifact encodes the consolidation once: one screen, three linked views, an explicit
reporting-status vocabulary, and a data contract (one JSON shape) that future real feeds can
satisfy.

## 3. Related Practice

The design follows familiar marketing-governance practice rather than novel theory. Budget-versus-
actual variance tracking with utilization ratios is standard in marketing finance; separating trade
spend (variable commissions, trade programs) from brand spend (discretionary ATL/BTL/digital)
mirrors how CPG and building-materials groups run dual marketing P&Ls. Campaign-type taxonomies
(ATL / BTL / Digital / OOH) come from agency media planning; ROMI-style productivity ledgers inform
the per-unit drill-down, though this view deliberately stops at budget discipline and does not
claim media-effectiveness measurement. Fiscal-year phasing charts reflect the Jul-Jun planning
cadence common in South Asian manufacturing groups.

## 4. Methodology & Data Design

**Data provenance (conceptual).** Figures were consolidated from per-unit budget trackers of
different shapes — overview sheets, top sheets, budget summaries, monthly trackers — then
normalized into one record type. For this portfolio copy every value is synthetic: identifiers and
people were replaced by role placeholders (`Brand Lead (Steel)`), tracker names are fictional, and
amounts are deterministically rescaled per unit (uniform factors ≈0.83–1.12 applied to budget,
expense, category split and monthly phasing together, so ratios and chart shapes survive while no
original number is recoverable).

**Schema.** Each unit record: `name`, `custodian`, `status ∈ {reporting, budget, no_data}`,
`budget` (FY commitment, Cr), `expense` (to-date actual, Cr), optional `cats{8}` budget split,
optional `expenseCats` by expense type, optional `monthly{budget[12], actual[12]}`, and a `source`
string naming the originating tracker. `data.json` uses flat `_cr` columns mapped back to this
shape by `rowToRecord()`.

**Metric definitions.**

- `Utilization (%) = expense ÷ budget × 100` (RAG bar: >100 red, >75 amber, else green).
- `Remaining (Cr) = budget − expense`.
- `Campaign mix share (k) = Σ units cats[k]` over the eight categories; doughnut of positive keys.
- `Monthly variance = budget[m] − actual[m]` per reporting unit; group monthly = Σ over units
  that report monthly (Nexa Mediplex + Nexa Breeze).
- `High-level split`: ATL = atl+ooh; BTL = btl+gift; Digital = digital; Other = research+salary+other.
- Reporting coverage = count(status="reporting") ÷ count(budget≠null).

**Transformation steps.** Load records → filter budget-bearing units → recompute totals →
aggregate category sums → derive utilization and coverage → render. No statistical modelling is
applied; the tool is a faithful consolidation layer.

## 5. Analytical Framework

The UI encodes a discipline-first decision logic. (1) Separation: trade commissions are parked in
their own tab because their scale (166.84 Cr for one unit) would compress every brand budget into
visual noise and invalidate cross-brand comparison. (2) Status honesty: units are labelled
*Reporting*, *Budget only* or *No data* so leadership reads utilization in context — a 0 % bar on
a plan-only unit is a data-maturity fact, not an overspend signal. (3) Fallback chain in the
drill-down: monthly phasing if present → expense-type breakdown if only expense categories exist →
budget/expense/remaining bar as last resort, so every unit always has a defensible primary chart.
(4) The campaign-mix doughnut uses the eight-way taxonomy as the budget-deployment lens, with the
ATL/BTL/Digital/Other rollup as the executive summary of the same numbers.

## 6. System Design

Three files: `index.html` (structure + ~200 lines of CSS custom-property tokens + one inline
script), `data.json` (the dataset in flat form), `assets/banner.png`. Rendering is vanilla JS +
Chart.js 4.4.1 from CDN: three tab views share one `BRAND`/`TRADE` array pair; each view render
destroys the previous chart instances first (`destroyAll()`), so tab switches never leak canvases.
A guarded hydration step runs only under `http(s)` (`location.protocol` check), fetches
`./data.json` with `cache:"no-store"`, merges rows by unit name (preserving inline-only monthly
phasing), and redraws; under `file://` or on any fetch/parse error it silently keeps the inlined
copy — the page is thus simultaneously a live-data demo and a standalone double-click demo.
The visual layer follows the v2 contract: navy `--bg:#0F172A` token system, Fira Code for
numerals/headings, KPI cards (2 px hover lift + delta chips), tooltips whose data comes from the
same derived metrics as the tables, sticky-top bar with synthetic-data chip and a `data-theme`
light toggle that re-skins tokens and redraws charts. Static-only was chosen because the artifact
is a point-in-time control view; no server state exists to justify one.

## 7. Findings & Design Decisions

The synthetic data story: ≈116 Cr of brand budget with only ≈6.7 Cr (≈5.7 %) posted expense at
quarter-end and 8 of 13 units reporting actuals — a discipline gap more than a spend gap; one
cement trade pool dominates absolute spend (166.84 Cr), validating the tab separation; ATL-heavy
units cluster the top of the budget chart while campaign-mix concentration shows OOH and BTL as
typical second-order lines. Key design decisions: (1) amber (`#D97706`) replaces red for
expense/actual series — expense is not an error state, and red stays reserved for over-utilization
bars, keeping color semantics honest; (2) status chips and fallback chart logic are surfaced
prominently so data maturity is part of the read-out, not a footnote; (3) the roster keeps a
micro utilization bar per row so ranking and pace are read in one glance; (4) the JSON mirror was
kept contract-faithful (flat `_cr` columns) so a spreadsheet export could regenerate `data.json`
without touching JS.

## 8. Limitations & Validity

100 % synthetic: the group, units, custodians, tracker names and figures are fictional, and
rescaled magnitudes must not be read as any real company's spend. Utilization is a discipline
metric, not an effectiveness metric — no ROMI, attribution or outcome data is present. Expense
"to date" mixes reporting maturities (some units post monthly, some a single lump), so
cross-unit pace comparisons are indicative only. Category splits assume each unit's own taxonomy
maps cleanly onto the eight keys. The http/file:// dual path means stale-JSON risk exists if a
user edits the inline copy instead of `data.json`; the merge-by-name rule also means renaming a
unit in the JSON adds a row rather than updating it.

## 9. Reproducibility & Deployment

Run locally: double-click `index.html` (offline dataset path), or `python -m http.server 8080`
for live `data.json` hydration. Delivery checks performed: inline `<script>` extracted and passed
`node --check`; `data.json` passed `JSON.parse`; forbidden-brand grep returned zero in text files.
Deploy: Vercel import → framework **Other**, root `.`, no build/output overrides; `data.json`
resolves beside the document. Requires internet at view time only for the Chart.js CDN and Google
Fonts (graceful system-font fallback). Editing data for a demo: change values in `data.json`,
refresh the served page — no code edits needed.
