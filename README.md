![Marketing Budget Control Tower](assets/banner.svg)

# Marketing Budget & Expense Control Tower — FY 2026-27 (Portfolio Demo)

A zero-build static dashboard that consolidates brand and trade marketing budget vs. actual
expense for every business unit of a fictional group, **NexaGroup**, on one screen: an executive
overview, a per-unit drill-down, and a dedicated trade-commissions tab — drawn client-side with
Chart.js on a dark, data-dense design system with a light-theme toggle.

[![Live demo](https://img.shields.io/badge/live%20demo-vercel-3B82F6?style=flat-square)](https://your-project.vercel.app)
[![Data](https://img.shields.io/badge/data-synthetic-D97706?style=flat-square)](#demo-data)
[![Build](https://img.shields.io/badge/build-none-10B981?style=flat-square)](#)

![Dashboard banner](assets/banner.png)

## Contents

- `index.html` — the dashboard (Chart.js 4.4.1 via CDN, no build step, no backend)
- `data.json` — the same synthetic dataset as a standalone file (brand units + trade units)
- `README.md` / `PROFILE.md` — this guide and the full methodology write-up

## Tabs

1. **Overview** — group brand-budget KPI strip, budget-vs-expense by unit, campaign-mix doughnut,
   monthly budget vs. actual for units that report monthly, and the full SBU roster table.
2. **SBU-wise Analysis** — per-unit drill-down: KPIs with delta chips, campaign-type split,
   monthly phasing (with automatic expense-breakdown / utilization fallbacks) and a high-level
   ATL / BTL / Digital / Other read.
3. **Trade Marketing Budget** — large variable sales commissions kept in their own pool so they
   never skew brand-vs-brand comparisons.

## Quick start

```bash
python -m http.server 8080     # then open http://localhost:8080/ — data.json is read live
start index.html               # or just double-click: identical inlined dataset, works file://
```

## Features

- **Dual-source data**: dataset inlined in `index.html` and mirrored in `data.json`; over
  `http(s)` the page re-reads `./data.json` and re-renders (edit the JSON, refresh, done); over
  `file://` or on any error the inlined copy is used. Monthly phasing lives in the inline copy.
- **Metric model** — utilization `= expense ÷ budget × 100`, remaining `= budget − expense`,
  campaign-mix aggregation across 8 cost categories; RAG bars at 75 % / 100 % thresholds.
- **Design v2 system** — navy token layer + light toggle, Fira Sans/Fira Code, sticky top bar
  with synthetic-data chip, KPI hover lift, tooltips + legends, sticky-header tables with
  tabular numerals, focus-visible rings, reduced-motion support, load-in stagger, responsive at
  390 / 768 / 1024 / 1440.

## Demo data

FY 2026-27, BDT Crore (1 Cr = 10,000,000 BDT), expense as of 30 Sep 2026. 13 brand units +
2 trade units; `status` is `reporting` (actuals posted) or `budget` (plan only). **All names,
custodian labels, tracker/source strings and figures are fictional synthetic demo data** —
role placeholders, not people; deterministic rescaling keeps magnitudes realistic.

## Tech

HTML5 · hand-written CSS custom-property tokens · Chart.js 4.4.1 (CDN) · vanilla JS (async
fetch hydration) · JSON data model. No framework, no bundler.

## Deploy

Vercel: import the repo → framework **Other**, root `.`, no build command; `data.json` sits
beside `index.html` and resolves relatively. Netlify/GitHub Pages work identically.

## License

MIT — demo content, no warranty, synthetic data.
