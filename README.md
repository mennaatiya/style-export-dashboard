# Style Export Intelligence Dashboard

An interactive Streamlit + Plotly dashboard for Style for Marble and Granite's
international export strategy. This is a **companion tool** to the written
Phase 1 / Phase 2 reports — the written report is the primary deliverable.

## What's inside

```
style_dashboard/
├── app.py                       # Streamlit application (7 pages via tabs)
├── requirements.txt
├── README.md                    # this file
└── data/
    ├── countries.csv            # 15 candidate export markets, scored
    ├── stones.csv                # the 5 priority stones
    ├── stone_country_scores.csv  # stone x country product-market fit (0-100)
    ├── segments.csv              # 10 B2B buyer segments, scored/prioritized
    ├── swot.csv                  # company-level SWOT with impact scores
    └── action_plan.csv           # 12-month phased roadmap
```

## Running it

1. Install Python 3.9+ if you don't already have it.
2. From this folder, install dependencies:
   ```
   pip install -r requirements.txt
   ```
3. Run the app:
   ```
   streamlit run app.py
   ```
4. Your browser will open automatically at `http://localhost:8501`.

## Dashboard pages

1. **Executive Overview** — KPI cards, top-10 country chart, management recommendation.
2. **Country Intelligence** — sortable/filterable country table, world map, radar comparison of up to 4 countries, per-country drill-down.
3. **Stone Intelligence** — select a stone to see its best-fit countries and positioning.
4. **Stone × Country Heatmap** — which stone to sell where, at a glance.
5. **Target Segments** — the 10 buyer segments ranked and scored.
6. **SWOT** — filterable strengths/weaknesses/opportunities/threats with impact scores.
7. **Action Plan** — the 12-month roadmap as a table and timeline chart.

Sidebar filters (Country, Stone, Buyer Segment, Country Priority) apply across
the Country and Segment pages.

## Important: data status

Every row in `countries.csv` and `stone_country_scores.csv` carries a
`data_status` value:

- **VERIFIED** — grounded directly in a cited external source (see the
  written Phase 1 report's Sources & Evidence section).
- **ESTIMATE** — analyst judgment, calibrated against the verified rows but
  not pulled from UN Comtrade / ITC Trade Map line-by-line. Use these to
  prioritize outreach, not to quote landed costs or make binding commercial
  commitments.

To upgrade ESTIMATE rows to VERIFIED, replace the relevant rows in
`data/countries.csv` and `data/stone_country_scores.csv` with figures from a
UN Comtrade / ITC Trade Map pull (this is the Phase 3 scope-out item in the
written report). The app will pick up any CSV edits automatically on refresh.

## Extending the dashboard

- To add a country: add a row to `countries.csv` (with lat/lon for the map)
  and five rows to `stone_country_scores.csv` (one per stone).
- To add a stone: add a row to `stones.csv` and one row per country to
  `stone_country_scores.csv`.
- All charts read directly from the CSVs — no code changes needed for new
  data, only for new chart types.
