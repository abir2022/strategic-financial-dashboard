# Strategic Financial Dashboard

A zero-backend, single-file financial cockpit for a fictional conglomerate (**NexaGroup**): budget vs
actual per business unit, cost structure, sales, marketing ROMI, workforce and production & inventory —
all driven by sidebar SBU and period filters, every chart Chart.js, every figure inline and synthetic.
Modern dark-first design system (Fira type, blue + amber tokens) with a light-mode toggle.

![Dashboard preview](assets/banner.png)

[![Vercel](https://img.shields.io/badge/deploy-Import%20Git%20repo-0F172A?logo=vercel)](https://vercel.com/new)
[![No build](https://img.shields.io/badge/build-none-10B981)](#quick-start)
[![Data](https://img.shields.io/badge/data-synthetic-D97706)](#demo-data-disclaimer)

**Open `index.html` in a browser. Nothing to install, nothing to build.**

→ Full write-up — problem framing, methodology, metric formulas, architecture, limitations — is in
  **[PROFILE.md](PROFILE.md)**.

## Quick start

```bash
open index.html          # macOS — Windows: start index.html — Linux: xdg-open index.html
python -m http.server    # optional local server, then visit the printed URL
```

Chart.js and the Fira web fonts load from a CDN, so the first paint needs internet; everything else —
data, tokens, logic — lives in the file.

## Features

- **Sidebar filters that genuinely drive everything** — SBU multi-select (select-all / clear) plus a
  month-range selector with last-6 / last-12 / full-range presets. Every KPI, chart and table recomputes
  from the filtered fact rows.
- **KPI cards** with delta chips comparing against the equally-sized prior window.
- **Budget vs actual** — stacked actual revenue by unit vs the aggregated budget line (monthly /
  cumulative toggle), a diverging variance chart, and an Ahead / On track / Watch / Behind status table.
- **Financial trends** — per-unit revenue lines + a COGS · OpEx · operating-profit cost structure.
- **Sales** — order value vs order count on a dual axis.
- **Marketing & ROMI** — a campaign ledger with organic baseline, marketing-led increment and top-line
  and bottom-line ROMI, ranked and summarised by category.
- **People & Operations** — headcount by department, workforce productivity, and unit-aware production
  and inventory panels.
- **CSV export** of the filtered fact table and the campaign ledger (UTF-8 BOM for spreadsheet safety).
- **Accessible, responsive** — focus-visible rings, `cursor:pointer` on all controls, sticky table
  headers with tabular numerals, a first-paint load-in stagger, `prefers-reduced-motion` honoured, and
  layouts at 390 / 768 / 1024 / 1440.

## Design system

Dark-first tokens (Fira Sans body + Fira Code numerals; blue `#3B82F6` and amber `#D97706` only — no violet
theme accent), a light-mode toggle, KPI cards with prior-period **delta chips**, eyebrow section headers,
focus-visible rings, `prefers-reduced-motion`, a one-shot load-in stagger and layouts at 390 / 768 / 1024 / 1440.
Full shared contract: `../DESIGN-SPEC.md`.

## Demo-data disclaimer

All companies, units, campaigns and figures are fictional and synthetically generated (BDT crore,
monthly, Jan 2025 – Jun 2026). No real organisation, person or financial record is represented.

## Deploy

Vercel → **Add New → Project → Import** this repo · Framework preset **Other** · build command *empty* ·
output directory **`.`** · deploy. `index.html` is served as-is; dragging the folder in works too.

*Built by Abir.* · MIT license — code sample, provided as-is.
