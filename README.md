# Energy affordability charts

Three interactive charts (Plotly) for the FREEDOM Act blog post. The
landing page (`index.html`) shows all three; each chart is also published at its own path
for embedding.

| Chart | Path | Suggested iframe height |
|-------|------|-------------------------|
| Energy burden by income quintile (2024) | `/burden/` | 440 |
| U.S. electricity demand forecast (24 → 166 GW) | `/demand/` | 420 |
| State demand vs. real price change, 2019–2025 | `/scatter/` | 800 |

## Embed

Each path is its own iframe-ready URL — one repo serves several embeddable charts:

```html
<iframe src="https://<owner>.github.io/freedom-act-charts/burden/"
        width="100%" height="440" style="border:0;" loading="lazy"></iframe>
```

## Data

All data is from public sources: U.S. Energy Information Administration (retail sales and
prices deflated by CPI-U; installed net summer capacity for the supply series), U.S. Census Bureau
American Community Survey PUMS (energy burden), and Grid Strategies (load-growth
forecasts). Every number is reproduced in the `EnergyAffordabilityAgenda` data repo
(`FINDINGS.md` + `data/processed/`), which is the source of truth for these charts.
