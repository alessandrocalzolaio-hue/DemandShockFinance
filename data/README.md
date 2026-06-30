# Data Directory

This directory holds all raw and processed data for Paper 1.
Data files are **not committed to git** (see `.gitignore`).

## Expected Files

| File | Description | Source |
|------|-------------|--------|
| `COMOVEMENT.xlsx` | Comovement series (corporate/private debt, equities) | [source TBD] |
| `BTC_BoI_*.csv` or similar | Bid-to-cover ratios by maturity, Bank of Italy | Banca d'Italia |
| `tick_cache/` | Raw on-the-run BTP tick data for non-auction-day placebo | [source TBD] |
| `EA-MPD/` | ECB monetary policy surprises (Altavilla et al. 2019) | ECB website |

## Notes

- The notebook (`scripts/main notebook/Paper1.ipynb`) expects data at paths
  configured in Cell 0 (`RESULTS_DIR`, data loaders in Cells 3–4).
- Maturities covered: 3Y, 5Y, 10Y, 15Y, 30Y.
- Sample: no-syndication BTPs only; Bank of Italy auctions.
- Update this file when data sources are finalized.
