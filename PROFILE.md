# Strategic Financial Dashboard — Portfolio Profile

**One-liner:** A zero-backend financial cockpit that puts budget-vs-actual performance, cost structure,
marketing return, workforce and production & inventory for every business unit of a conglomerate on one
filterable, self-contained page.

---

## 1. Abstract

Multi-business groups report in silos: committed budget, delivered revenue, cost structure, marketing
return, stock build-up and headcount each live in a different system, so leadership reviews get
hand-assembled spreadsheets instead of one current, comparable view. This artifact rebuilds that review
as a **static, zero-backend cockpit** — a single `index.html` with all data inline — so the *analytical
design* (metric choices, budget-variance logic, ROMI attribution, drill structure) is inspectable without
exposing or depending on any warehouse. Sidebar filters recompute every KPI, chart and table from one flat
fact model (one row per unit per month, Jan 2025 – Jun 2026, BDT crore). Charts are Chart.js; exports are
browser-side Blob downloads. The visual system follows a shared, data-dense design spec: Fira typography,
a blue-and-amber token set, KPI cards with prior-period delta chips, sticky table headers with tabular
numerals, an accessible focus system, a first-paint load-in stagger, `prefers-reduced-motion`, light/dark
themes, and layouts from 390 to 1440 px. Every figure is synthetic; no real company is represented.

## 2. Problem Framing

A group that runs genuinely different businesses — here cement, agro feeds, steel and power (all fictional)
— reports on different cycles, in different ERP tables, in different units. The numbers that actually drive
decisions are not hard to find individually, but they are never on one current, like-for-like page:
*which unit is on plan, which is drifting, and why*. The original internal tool solved this by reading a
data warehouse; the portfolio version solves it by proving the *thinking* is portable — same summary-first
data model, same page anatomy (overview → financial → sales → budget → people → production/inventory), same
attribution method — minus the servers, credentials and ETL. The deliverable is the reasoning made runnable
by double-clicking a file.

## 3. Related Practice

The panels mirror techniques analysts already use rather than inventing new ones: **budget attainment and
variance analysis** (actual vs committed, bucketed into Ahead / On track / Watch / Behind), **cost-structure
/ profit-bridge decomposition** (revenue → COGS → operating expense → operating profit), **ROMI ledgers**
(increment measured against an organic run-rate baseline), **order-intake vs orders-placed dual-axis demand
tracking** (the signal that leads recognised revenue), and **stock-cover in months** as a working-capital
read. Headcount is treated as a productivity denominator (revenue and order value per employee). Each chart
answers a question an operator would already ask; the dashboard simply answers them together and on demand.

## 4. Methodology & Data Design

Data is one inline `const DATA` object (fully synthetic) shaped as four business units, each carrying
18 monthly arrays (revenue, budget, marketing, sales order value, orders, output, inventory) plus staff and
stock-mix records, and a separate campaign ledger with reporting months. A `factRow(unit, i)` expands this
into one row per unit per month. Derived metrics, with their exact formulas as encoded in the UI:

- **COGS** = revenue × (1 − gross margin);  **OpEx** = revenue × operating-expense ratio;
  **Operating profit** = revenue − COGS − OpEx.
- **Operating margin** = operating profit ÷ revenue.
- **Budget variance %** = (Σactual ÷ Σbudget) − 1, over the filtered rows. Status: Ahead ≥ +2%,
  On track −2%…+2%, Watch −8%…−2%, Behind < −8%.
- **Budget attainment** = Σactual ÷ Σbudget (shown against the aggregated dashed budget line).
- **Top-line ROMI** = (increment − spend) ÷ spend; **bottom-line ROMI** = (increment × gross margin − spend) ÷ spend;
  **Blended ROMI** = (Σincrement − Σspend) ÷ Σspend. Increment is measured over the organic baseline (the
  unit's pre-campaign run-rate), never over zero.
- **Headcount** is interpolated linearly between opening and closing establishment:
  `opening + (closing − opening) × t`, `t = i ÷ (months − 1)`.
- **Stock cover (months)** = average inventory ÷ monthly revenue; **revenue per employee** = period revenue ÷
  end-of-period headcount.
- **Run-rate / cumulative** view runs the same monthly series cumulatively for budget-vs-actual.

Prior-period deltas compare against an equally-sized window immediately before the selection, when one
exists. No transformation touches the stored figures; everything is computed at render time from the filter
state.

## 5. Analytical Framework

The UI encodes a decision loop: **filter (SBU × period) → compare against commitment (budget) → diagnose
(margin & cost structure) → trace the lead indicator (sales order value vs recognised revenue) → attribute
return (ROMI) → check capacity and capital (people, production, stock) → export for the meeting**. Thresholds
are made explicit on the panels (status bands, ROMI ≥ 1× tinting, margin sign) so colour and label carry the
same verdict a reviewer would give. Filters are the analytical primitive: because every panel reads from the
same filtered rows, changing a checkbox is a genuine what-if, not a cosmetic toggle.

## 6. System Design

Plain hand-written HTML + one vanilla ES6 script + Chart.js 4 (CDN) — deliberately static, offline-inspectable,
and deployable as a single file. A `state` object (period range, selected SBUs, monthly-vs-cumulative budget
mode) drives one `render()` cycle; charts are destroyed and redrawn on each change. Presentational colours live
in a CSS custom-property token layer (spec-exact `:root` block, blue `--brand`/`--brand-2` + amber `--accent`
only); a `data-theme="light"` variant remaps the same names, and Chart.js defaults read those tokens live so
axes, grids and tooltips re-colour with the theme. A thin sticky top bar carries the product mark, a synthetic
-data chip and the theme toggle; the sidebar holds the filters and an on-this-page index. CSV export is a Blob +
object-URL download (UTF-8 BOM). It is static because the story is the analysis, not the infrastructure — no
server, queue or credential is part of the point.

## 7. Findings & Design Decisions

The synthetic data is authored so the story is legible: the material units broadly track budget with a
festive-season swell (Oct–Dec), power is steady, and marketing-led ROMI clusters clearly above the organic
baseline for the trade-incentive and ATL campaigns while research/sponsorship items sit near break-even.
Design choices that made this readable: **KPI cards** with tabular-numeral values in the mono face and
prior-window **delta chips** (tinted ok/bad) rather than raw subtext; **section headers** with uppercase
eyebrow labels so each band states its question before its chart; **one card style, one radius scale, two
nesting levels max**; **equal content edges** so nothing drifts; and a **first-paint load-in stagger** (≤450 ms,
once, skipped under reduced motion) as the only entrance motion — hover lift and press feedback are kept for
interactions instead of decorating scrolls. Colour is never alone: status bands pair a tag with text, and unit
series pair colour with dots and legends.

## 8. Limitations & Validity

Everything is invented. **NexaGroup** and its units — Nexa Cement, Nexa AgroFeeds, Nexa Steel, Nexa Power —
do not exist; the 18 monthly buckets (Jan 2025 – Jun 2026, BDT crore) and the campaign ledger are
hand-generated to look plausible for a Bangladesh business landscape (seasonal construction and festive
demand, unit-appropriate margins, realistic working-capital and headcount drift). It reflects structure and
method, **not any real performance**; nothing here should be read as a real organisation's numbers or as an
investment signal. Headcount is a linear interpolation, not actual establishment data, and stock cover uses a
revenue basis for comparability rather than a physical COGS-throughput. Scope is presentation and analytical
logic; production-grade data lineage, permissions and refresh scheduling are intentionally out of frame.
Owner credit: **Built by Abir**.

## 9. Reproducibility & Deployment

```bash
open index.html          # macOS · Windows: start index.html · Linux: xdg-open index.html
python -m http.server    # optional local server; Chart.js + Fira fonts then load over CDN on first paint
```

No install, no build, no backend. To run on your own numbers, replace the `const DATA` object with one of the
same shape (units, 18-length monthly arrays, staff, campaigns). **Vercel:** Import the repo → Framework preset
**Other** → build command *empty* → output directory **`.`** → deploy; the file is served as-is. A CDN
connection is the only external dependency.
