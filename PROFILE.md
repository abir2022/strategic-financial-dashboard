# Strategic Financial Dashboard

**One-liner:** A zero-backend financial cockpit that puts budget-vs-actual performance, cost structure,
marketing return, workforce and production/inventory for every business unit of a conglomerate on one
filterable page.

---

## The problem

A multi-company group runs a handful of very different businesses (cement, agro feeds, steel, power).
Each one reports on its own cycle, in its own ERP tables, and the numbers that matter — committed budget
versus delivered revenue, cost structure, marketing return, stock build-up, headcount — live in separate
systems. Leadership reviews therefore depended on hand-assembled spreadsheets: no single, current,
comparable view of "which unit is on plan, which is drifting, and why".

## The solution

I first built this as an internal analytical dashboard wired to the group's data warehouse. For my
portfolio I rebuilt it as a **static, zero-backend cockpit**: a single `index.html` with all data inline,
so the analytical design — the metric choices, the drill structure, the budget-variance logic, the ROMI
attribution model — can be shown without exposing (or needing) any real system.

The narrative of the original build stays in the architecture note below; the deliverable here is the
thinking, made inspectable and instantly runnable.

## Features

- **Sidebar filters that genuinely drive everything** — SBU multi-select (with select-all / clear) plus a
  month-range period selector and last-6 / last-12 / full-range presets. Every KPI, chart and table is
  recomputed from the filtered fact rows.
- **Headline KPI cards** — net revenue, budget variance, operating margin, sales order value, marketing
  spend with blended ROMI, and headcount; each compared against the equally-sized prior window when the
  selection leaves room for one.
- **Budget vs actual** — stacked actual revenue by unit against the aggregated budget line, switchable
  between monthly and cumulative views, plus a diverging variance-by-unit chart and a status table
  (Ahead / On track / Watch / Behind).
- **Financial trends** — per-unit monthly revenue lines and a cost-structure breakdown (COGS, operating
  expenses, operating profit) of the selected period.
- **Sales performance** — order value against order count on a dual axis, the demand signal that leads
  recognised revenue.
- **Marketing & ROMI** — a campaign ledger per reporting month with organic baseline, marketing-led
  increment, spend and both top-line and bottom-line ROMI: `ROMI = (increment − spend) ÷ spend`, ranked
  and summarised by activity category.
- **Employee summary** — headcount stacked by department per unit, a monthly establishment trend, and a
  workforce table with active/inactive split, hires, exits, revenue per employee and order value per
  employee.
- **Production & inventory** — stacked output by month (unit-aware: thousand MT for the material
  businesses, GWh for power), inventory value trend per unit, stock-type mix, and a stock-cover table.
- **Monthly data table** — the filtered fact table underneath every chart, with row count.
- **CSV export** — one-click Blob download of the filtered monthly data and of the campaign ledger
  (UTF-8 BOM, so it opens correctly in spreadsheets).
- **Responsive single-file UI** — dark design system built on CSS custom properties, grid layout that
  collapses to a stacked mobile view, sticky table headers, tabular numerals, accessible focus states.

## Tech stack

| Layer | Choice |
|---|---|
| Structure | One hand-written HTML document, no framework |
| Styling | Plain CSS with a design-token layer (`:root` custom properties) |
| Charts | Chart.js 4 (CDN) — bar, stacked bar, line, dual-axis, doughnut, polar area |
| Logic | Vanilla ES6 JavaScript, one render cycle, charts destroyed and redrawn on filter change |
| Data | Inline `const DATA` object, fully synthetic |
| Export | `Blob` + object URL download |
| Build | None — open the file |

## Architecture note

The original internal version was a Streamlit application reading an on-premise Microsoft SQL Server data
warehouse (monthly finance, sales, inventory and production summary tables), with an ETL refresh step and
an authenticated Postgres branch so colleagues could reach it from anywhere. That lineage explains the
shape of this demo: the same summary-first data model (one row per unit per month), the same page
structure (overview → financial → sales → budget → people → production/inventory), and the same
attribution method behind the ROMI panel (organic baseline as a pre-campaign run-rate average, increment
as actual minus baseline, gross-margin conversion from top-line to bottom-line return).

The portfolio version deliberately drops every server, credential and warehouse dependency: the compute
engine is now a few dozen lines of JavaScript running in the browser. The analytical design is the point;
the infrastructure is not part of the story.

## Synthetic data disclaimer

Everything here is fictional. The conglomerate **NexaGroup** and its units — Nexa Cement, Nexa AgroFeeds,
Nexa Steel, Nexa Power — do not exist. All figures (18 monthly buckets, Jan 2025 → Jun 2026, in BDT crore)
are hand-generated to look plausible for a Bangladesh business landscape: seasonal construction and
festive demand patterns, unit-appropriate gross margins, realistic working-capital and headcount drift.
No real company, real financial record, or real person is represented, and the numbers must not be read
as any organisation's performance. Owner credit: **Built by Abir**.

## Run locally

```bash
# any one of these
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux
python -m http.server 8000   # then visit http://localhost:8000
```

No install, no build, no server required. Chart.js is loaded from a CDN, so the first load needs internet
access; everything else is in the file.

## Deploy (Vercel)

Static HTML with no build step:

1. Push this folder to a GitHub repository.
2. On Vercel, **Add New → Project → Import** that repository.
3. Framework preset: **Other**. Build command: **empty**. Output directory: **`.`** (root).
4. Deploy — `index.html` is served as-is.

Importing the folder directly (or dragging it into the Vercel dashboard) works too, since there is no
toolchain to detect.
