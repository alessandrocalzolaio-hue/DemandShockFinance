---
name: project-paper1
description: Paper 1 — Demand Shocks in the Italian Government Bond Markets. BTP auction identification, exogeneity, and cross-market spillovers.
metadata:
  type: project
---

## Core Facts

Working title: "Demand Shocks in the Italian Government Bond Markets" (to be refined).

**Why:** First paper of Alessandro's PhD thesis at University of Trento.
**How to apply:** Use this context to make better suggestions on empirical strategy, journal fit, and framing.

## Identification Strategy

Demand shock defined following Lengyel & Giuliodori (2021, JMCB):
- Log price change of on-the-run BTP in the 10:59–11:15 window around auction announcement
- Instrument: change in Bank of Italy bid-to-cover ratio (ΔBTC_BoI), first-differenced
- Sample: no-syndication BTPs only; Bank of Italy auctions
- Maturities: 3Y, 5Y, 10Y, 15Y, 30Y
- Pooled long series also constructed

## Estimators

- OLS-HAC (HAC lag L=1 baseline)
- Huber-T (robust to outliers)
- Results rescaled to log-price basis-point units for magnitude analysis

## Paper Structure (as of 2026-06-30)

- **Panel C (exogeneity):** Demand shock does NOT predict fundamentals or policy expectations. Orthogonality with ECB surprises tested via EA-MPD dataset (Altavilla et al. 2019, JME).
- **Panels A & B (spillovers):** Demand shock spills into corporate/private debt (A) and equities (B). Comovement series from COMOVEMENT.xlsx.
- **Stress-period heterogeneity:** Median-split on Italian 5Y CDS; CLIFS (70th percentile, L&G 2022).
- **Sign asymmetry:** L&G (2022) Equation 8 specification.

## Robustness Checks (already coded in notebook)

1. Symmetric window sensitivity (Section F)
2. Alternative HAC lags (H)
3. Winsorization / outlier treatment (I)
4. Day-before placebo (J)
5. Winsorization on spillover panels (I-SP)
6. ECB surprise orthogonality (L)
7. ECB robustness — control + orthogonalized shock (L-ECB)
8. ECB meeting-day exclusion (N)
9. Crisis + COVID exclusion (O)
10. Day-before placebo on spillover panels (P)
11. Wald test — pooling validity (M)

## Key Reference

Lengyel & Giuliodori (2021) — `master_supporting_docs/supporting_papers/J of Money Credit Banking - 2021 - LENGYEL - Demand Shocks for Public Debt in the Eurozone.pdf`
Online appendix: `master_supporting_docs/supporting_papers/jmcb12891_sup_0001_onlineappendix.pdf`
Altavilla et al. unbundling QE: `master_supporting_docs/supporting_papers/unbundling_qe_jpe.pdf`

## File Map

| Role | File |
|------|------|
| Primary analysis | `scripts/main notebook/Paper1.ipynb` (54 cells, Python) |
| Manuscript | `paper/Paper1.tex` (skeleton) |
| Bibliography | `Bibliography_base.bib` |
| Data | `data/` (to be uploaded) |
| Conference slides | `Slides/Paper1_slides.tex` (not started) |
| Quarto slides | `Quarto/Paper1_slides.qmd` (not started) |
