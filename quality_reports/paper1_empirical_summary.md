# Paper 1 — Empirical Analysis Summary
**As of:** 2026-06-30
**Notebook:** `scripts/main notebook/Paper1.ipynb` (54 cells, Python)
**Title:** Demand Shocks in the Italian Government Bond Markets

---

## 0. The Research Question in One Paragraph

You measure demand shocks at Italian Treasury (BTP) auctions using the intraday
price change in a narrow 16-minute window around the announcement of results
(10:59–11:15). These shocks are instrumented by the change in the Bank of Italy's
bid-to-cover ratio (ΔBTC\_BoI). The paper then asks: (a) do these demand shocks
actually predict the price response (first stage); (b) are they orthogonal to
fundamentals and ECB policy surprises (exogeneity / validity); and (c) do they
spill over into corporate debt markets and equities, and how does this depend on
market stress, shock sign, and maturity?

---

## I. Sample and Data

| Instrument | Maturities | Source | N (no-syndication auctions) |
|---|---|---|---|
| BTP price ticks | 3Y, 5Y, 10Y, 15Y, 30Y | MTS cash segment (.mat files) | 580–210 per maturity |
| Bid-to-cover ratio | same | Banca d'Italia (via all\_maturities.xlsx) | same |
| Comovement series | — | Bloomberg (COMOVEMENT.xlsx) | varies |
| ECB surprises | — | EA-MPD, Altavilla et al. (2019) | 315 GC meetings |
| Italian 5Y CDS | — | Bloomberg (daticdsebond/) | full span |
| CLIFS Italy | — | ECB | monthly, 1970–present |

**Pooled long-run sample** (used in main comovement tables): 10Y + 15Y + 30Y.
5Y is excluded from the pool throughout because the first stage is insignificant.
3Y is included in per-maturity regressions and some pool-4 robustness checks.

**Sample observations (first-stage regressions):**

| Maturity | N |
|---|---|
| 3Y | 198 |
| 5Y | 200 |
| 10Y | 208 |
| 15Y | 66 |
| 30Y | 57 |
| Pooled (10Y+15Y+30Y) | 331 |

---

## II. Demand Shock Construction

**Definition** (Lengyel & Giuliodori 2022, eq. 1):

$$D_t^{(m)} = \left[\ln P_{t,11:15}^{(m)} - \ln P_{t,10:59}^{(m)}\right] \times 100$$

- $P_{t,\text{pre}}$: last OTR tick price ≤ 10:59 on auction day $t$
- $P_{t,\text{post}}$: first OTR tick price ≥ 11:15 on auction day $t$
- Units: log-price change × 100 (approximately basis points)

**Normalisation** (within-maturity, following L&G 2022):

$$\tilde{D}_t^{(m)} = \frac{D_t^{(m)} - \mu^{(m)}}{\sigma^{(m)}}$$

This makes coefficients interpretable as the response per 1-standard-deviation
demand shock.

**Instrument:** $\Delta\text{BTC}\_\text{BoI}_t^{(m)}$ = first difference of the Bank of Italy
bid-to-cover ratio within the same maturity sequence. First-differencing
removes persistent structural demand differences across auction cycles.

---

## III. Shock Summary Statistics

### Panel A — Raw Shock $D_t^{(m)}$ [log-price × 100]

| Maturity | N | Mean | Std | Min | Max |
|---|---|---|---|---|---|
| 3Y | 199 | 0.021 | 0.069 | −0.180 | 0.525 |
| 5Y | 201 | 0.024 | 0.108 | −0.615 | 0.330 |
| 10Y | 209 | 0.053 | 0.208 | −0.635 | 1.663 |
| 15Y | 67 | 0.094 | 0.358 | −1.215 | 1.599 |
| 30Y | 58 | 0.178 | 0.511 | −0.608 | 3.586 |
| Pooled | 331 | 0.083 | 0.317 | −1.215 | 3.586 |

Positive mean across all maturities: prices systematically rise in the auction
window, consistent with the market absorbing new information about demand strength.
Volatility increases strongly with maturity (duration amplification).

### Panel B — Normalised Shock $\tilde{D}_t^{(m)}$

By construction: mean ≈ 0, std ≈ 1 per maturity.

**Distributional properties (diagnostic tests):**

| Maturity | ADF | t-test (μ=0) | Ljung-Box(5) | JB | ARCH-LM | ρ(1) | Skew | Exc. Kurtosis |
|---|---|---|---|---|---|---|---|---|
| 3Y | Reject | Do not reject | Do not reject | Reject | Do not reject | 0.025 ns | +2.49 | 17.7 |
| 5Y | Reject | Do not reject | Do not reject | Reject | Do not reject | 0.006 ns | −1.12 | 9.8 |
| 10Y | Reject | Do not reject | Do not reject | Reject | Do not reject | −0.038 ns | +1.90 | 19.9 |
| 15Y | Reject | Do not reject | Do not reject | Reject | Do not reject | +0.285* | +0.93 | 9.2 |
| 30Y | Reject | Do not reject | Do not reject | Reject | Do not reject | −0.019 ns | +5.17 | 35.5 |
| Pooled | Reject | Do not reject | Do not reject | Reject | Do not reject | −0.001 ns | — | — |

**Key takeaways:**
- Shocks are stationary (ADF always rejects).
- Mean zero confirmed by t-test — identification window removes drift.
- No serial correlation in any maturity (Ljung-Box passes), except 15Y ρ(1)=0.285* — this is the only case where HAC correction is strictly load-bearing.
- Strong non-normality (JB rejects everywhere): heavy right tails, extreme kurtosis (especially 30Y: kurtosis 35.5). This justifies using Huber-T as primary estimator.
- No ARCH effects: volatility clustering is not a concern.

---

## IV. Main Regression: Demand Determinants (First Stage)

**Model:**

$$\tilde{D}_t^{(m)} = \alpha^{(m)} + \beta^{(m)} \cdot \Delta\text{BTC}\_\text{BoI}_t^{(m)} + \varepsilon_t$$

**Pooled spec (maturity FE, 10Y omitted):**

$$\tilde{D}_t = \alpha + \beta \cdot \Delta\text{BTC}\_\text{BoI}_t + \mu_{15Y}\mathbf{1}_{[m=15Y]} + \mu_{30Y}\mathbf{1}_{[m=30Y]} + \varepsilon_t$$

**Estimators:**
- OLS-HAC: Newey-West, L=3 (fixed), primary for R²
- Huber-T WLS-HAC: IRLS weights + WLS sandwich with HAC (L=3), primary for inference
- L=3 rationale: ΔBTC is a first-differenced monthly series (little structural serial correlation);
  Ljung-Box confirms near-zero residual autocorrelation; L=3 guards up to MA(3) without
  over-correcting (Newey-West automatic rule gives L=3–4 for N=60–210).

### Results

| Maturity | N | OLS β | OLS SE | OLS p | Huber β | Hub SE | Hub p | OLS R² |
|---|---|---|---|---|---|---|---|---|
| 3Y | 198 | 0.867 | 0.341 | 0.011** | **0.508** | 0.183 | 0.006*** | 0.036 |
| 5Y | 200 | 0.336 | 0.302 | 0.266 | **0.231** | 0.206 | 0.261 | 0.005 |
| 10Y | 208 | 0.791 | 0.487 | 0.104 | **0.783** | 0.277 | 0.005*** | 0.014 |
| 15Y | 66 | 0.786 | 0.292 | 0.007*** | **0.667** | 0.148 | <0.001*** | 0.051 |
| 30Y | 57 | 0.099 | 0.158 | 0.530 | **0.200** | 0.094 | 0.033** | 0.001 |
| Pooled | 331 | 0.549 | 0.199 | 0.006*** | **0.492** | 0.110 | <0.001*** | 0.014 |

**Pattern:**
- **3Y, 10Y, 15Y:** Robustly significant under both estimators. Huber and OLS agree on sign; OLS SE inflated by outliers (kurtosis 17–20), which is why Huber has tighter inference.
- **5Y:** Consistently insignificant (both estimators, any lag choice). Excluded from pooled sample.
- **30Y:** Marginally significant (Huber only). OLS β is near-zero; Huber upweights the moderate-sized bulk of observations. Given extreme kurtosis (35.5), Huber is the right call for inference.
- **Pooled:** Very robust — 4.5σ Huber, 2.8σ OLS.

**R² is intentionally low** (1.4–5.1%). This is not a weakness: the regression identifies the causal demand component of the within-window price move, not all price variation. A high R² would actually suggest mechanical correlation.

**BTC level vs. first difference (robustness):**
Using BTC\_BoI levels almost entirely loses significance (3Y, 5Y, 10Y, 30Y all insignificant under Huber; 15Y borderline).
**Conclusion:** first-differencing is the correct specification — it is the marginal auction-by-auction change in demand that drives the intraday price.

### Magnitude Analysis

Rescaling Huber coefficient to log-price basis points (Huber β × σ\_raw^(m)):

| Maturity | σ\_raw | Huber β | Effect per 1σ shock (bp) | Effect per 1σ ΔBTC (bp) |
|---|---|---|---|---|
| 3Y | 0.069 | 0.508 | 0.035 | 0.008 |
| 10Y | 0.208 | 0.783 | 0.163 | 0.024 |
| 15Y | 0.358 | 0.667 | 0.239 | 0.069 |
| 30Y | 0.511 | 0.200 | 0.102 | 0.031 |

The magnitude pattern (15Y > 10Y > 30Y > 3Y in bp terms) reflects duration amplification
in the raw volatility, partially offset by a declining Huber coefficient at the long end.

---

## V. Cross-Market Spillovers

**Model (pooled long-run):**

$$\Delta Y_t = \alpha + \delta \cdot \tilde{D}_t + \mu_{15Y}\mathbf{1}_{[m=15Y]} + \mu_{30Y}\mathbf{1}_{[m=30Y]} + \eta_t$$

where $Y_t$ is the daily return/change of each comovement series, $\tilde{D}_t$ is the
normalised BTP demand shock. Maturity FEs absorb level differences across the pool.

### Panel A — Corporate and Private Debt (Pooled long-run: 10Y+15Y+30Y)

| Series | Label | N | OLS δ | SE | Huber δ | SE |
|---|---|---|---|---|---|---|
| iBoxx EUR Sovereign | IUS6 | 274 | +0.029* | (0.015) | +0.029*** | (0.011) |
| iBoxx EUR Corp (IG) | XBLC | 259 | +0.080*** | (0.027) | +0.046*** | (0.012) |
| FTSE EUR IG Bond | FTEBIGEURT | 325 | +0.059*** | (0.020) | +0.044*** | (0.012) |
| BBB EUR Corp (I09919EU) | BBB Corp | 315 | +0.065* | (0.035) | +0.031** | (0.015) |
| iBoxx EUR Corp (I02002EU) | Corp | 328 | +0.048*** | (0.015) | +0.034*** | (0.011) |
| Pan-EU HY (I02501EU) | HY | 328 | +0.044** | (0.022) | +0.030*** | (0.006) |
| ICE BofA EUR HY (BAMLHE00EHYITRIV) | ICE BofA HY | 329 | +0.043** | (0.021) | +0.033*** | (0.006) |

All positive and significant: a positive BTP demand shock raises corporate/private
debt prices across the credit quality spectrum (IG, BBB, HY). The magnitude is
roughly 3–5 bp per 1% effect (δ×100), with the broadest EUR corporate indices
(XBLC, FTEBIGEURT) showing the largest responses. Both HY indices (I02501EU and
ICE BofA) deliver nearly identical Huber estimates (~0.030–0.033***), with the
ICE BofA series now covering the full sample (N=329 vs 58 previously).

**OLS R² (pooled long):**
IUS6: 0.024; XBLC: 0.116; FTEBIGEURT: 0.053; I09919EU: 0.003; I02002EU: 0.008; ICE BofA HY: 0.022.
XBLC (R²=11.6%) shows the strongest signal-to-noise ratio.

### Panel B — Equities (Pooled long-run)

| Series | N | OLS δ | SE | Huber δ | SE |
|---|---|---|---|---|---|
| FTSE MIB | 331 | +0.195*** | (0.064) | +0.217*** | (0.049) |
| EURO STOXX 50 | 331 | +0.149*** | (0.054) | +0.148*** | (0.047) |
| MSCI Europe | 331 | +0.138*** | (0.049) | +0.140*** | (0.043) |

All highly significant and economically large: a 1σ positive BTP demand shock raises
FTSE MIB by 21.7 bp (0.217%), EURO STOXX by 14.8 bp, MSCI Europe by 14.0 bp.
The domestic effect (FTSE MIB) exceeds the pan-European one, consistent with a
home-bias transmission channel.

**Per-maturity patterns (Huber-T):**

| Maturity | FTSE MIB | SE | EURO STOXX | SE | MSCI Europe | SE | N |
|---|---|---|---|---|---|---|---|
| 3Y | +0.154** | (0.077) | +0.134* | (0.071) | +0.113* | (0.062) | ~199 |
| 5Y | +0.053 | (0.069) | +0.034 | (0.060) | +0.020 | (0.049) | ~201 |
| 10Y | +0.300*** | (0.064) | +0.207*** | (0.058) | +0.201*** | (0.050) | ~209 |
| 15Y | −0.061 | (0.106) | +0.012 | (0.088) | +0.040 | (0.081) | ~67 |
| 30Y | +0.187*** | (0.060) | +0.110* | (0.060) | +0.043 | (0.048) | ~58 |

Equity effects are concentrated at **3Y, 10Y, and 30Y**. 5Y is uniformly insignificant
(same as the first stage). 15Y shows near-zero or negative point estimates for all three
indices — an important anomaly worth noting, possibly related to the limited 15Y sample
(N=67) or the investor base for that maturity.

### Panel C — Exogeneity Check: Swaps, Commodities, and Spreads

Under clean identification, demand shocks should NOT affect macro-financial expectations
or global risk factors. Tested series (pooled long-run, Huber):

| Series | Pooled Huber δ | SE | Verdict |
|---|---|---|---|
| OIS 1Y EUR swap (EUSA0101) | −0.003 | (0.002) | ✓ Pass |
| IRS 6m-fwd-6m (EUR) | −0.002 | (0.001) | ✓ Pass |
| EURIBOR 3M | −0.001** | (0.0002) | ⚠ Borderline |
| ITIL 2Y-fwd-3Y inflation swap | −0.001 | (0.002) | ✓ Pass |
| ITIL 5Y-fwd-5Y inflation swap | −0.002 | (0.002) | ✓ Pass |
| EUIL 5Y-fwd-5Y inflation swap | −0.002 | (0.001) | ✓ Pass |
| EUIL 5Y-fwd-10Y inflation swap | −0.001 | (0.002) | ✓ Pass |
| Commodities (COMOPA) | −0.054 | (0.048) | ✓ Pass |
| EUR/CHF | +0.012 | (0.015) | ✓ Pass |
| EUR/USD | +0.013 | (0.024) | ✓ Pass |
| AUS/EUR (AUEUAH) | +0.112** | (0.044) | ⚠ Sig. |
| MOVE Index | −0.047 | (0.125) | ✓ Pass |
| **VSTOXX** | −0.186*** | (0.055) | ❓ Endogenous? |
| **VIX** | −0.117*** | (0.043) | ❓ Endogenous? |
| **Bund Futures (FGBLc1)** | +0.044** | (0.020) | ❓ Endogenous? |

**Notable findings requiring attention:**
1. **VSTOXX/VIX:** Significantly negative — positive BTP demand shocks reduce European
   and global volatility expectations. This is consistent with a risk-on/safe-haven
   reversal channel but complicates the pure exogeneity story: is this a causal spillover
   (demand certainty reduces vol), or is it that common global risk-off episodes drive
   both BTP demand and implied vol?
2. **Bund Futures:** Significantly positive pooled — positive BTP shocks raise German bond
   prices. This is a sovereign-sovereign spillover (euro area safe-haven rebalancing) and
   is arguably a real transmission channel rather than a validity threat.
3. **AUEUAH (AUS/EUR):** Positive and significant. Mechanism unclear — possible
   common factor or noise.
4. **EURIBOR 3M:** Tiny negative effect (−0.001**), likely mechanical (same-day).

**Assessment:** The core macro expectations channels (OIS, IRS, inflation swaps,
commodities, FX) are null — identification is credible. The VSTOXX/VIX results
call for careful framing: either they should be moved out of the exogeneity table
and framed as a risk-channel spillover finding, or explicitly discussed as a potential
confound.

---

## VI. ECB Surprise Orthogonality Test

**Test:** Does the nearest-prior ECB surprise predict the BTP demand shock?

$$\tilde{D}_t^{(m)} = \alpha + \gamma_1 \cdot \text{OIS1Y\_PR}_{s^*} + \gamma_2 \cdot \text{IT10Y\_MEW}_{s^*} + \varepsilon_t$$

where $s^* = \max\{s \in \mathcal{T}_\text{ECB} : s < t\}$ (nearest prior GC meeting).

| Maturity | N | R² | F-stat | p(F) | Verdict |
|---|---|---|---|---|---|
| 3Y | 199 | 0.034 | 2.863 | 0.060 | ✗ Reject (10%) |
| 5Y | 201 | 0.059 | 1.935 | 0.147 | ✓ Pass |
| 10Y | 209 | 0.058 | 0.851 | 0.428 | ✓ Pass |
| 15Y | 67 | 0.118 | 2.665 | 0.077 | ✗ Reject (10%) |
| 30Y | 58 | 0.028 | 1.465 | 0.240 | ✓ Pass |
| Pooled | 339 | 0.034 | 1.160 | 0.315 | ✓ Pass |

**Interpretation:**
- Pooled (the relevant sample for spillover tables): PASS (p=0.315).
- 3Y and 15Y show weak rejection at 10% only. The significant coefficient is
  IT10Y\_MEW (the Italian sovereign channel in the ECB's MEW window), not the
  rate decision surprise (OIS1Y\_PR). This suggests some months where the ECB's
  actions affect Italian sovereign pricing also see unusual BTP auction dynamics —
  plausibly a common market-conditions factor, not reverse causation.

**ECB Robustness (two approaches):**
- **App. 1 (ECB controls):** Adding OIS1Y\_PR + IT10Y\_MEW directly as controls.
  Pooled β = 0.476*** (vs. 0.492*** baseline) — stable.
- **App. 2 (Orthogonalised shock):** Two-stage residual. Pooled β = 0.428*** — still
  strongly significant.
- **Concern:** 30Y loses significance in both approaches (App.1 p=0.403; App.2 p=0.365).
  This suggests the 30Y first stage may be partially driven by ECB-adjacent meetings.
  Robustness of 30Y in the pooled table is saved by pooling with the robust 10Y/15Y.

---

## VII. Stress-Period Heterogeneity

### Stress 1: Italian 5Y CDS median split

Median CDS on auction dates: 131.85 bp. High: N=161, Low: N=166.

**Selected results (Huber-T, pooled long-run):**

| Series | Low β | SE | High β | SE | p(diff) |
|---|---|---|---|---|---|
| XBLC (EUR Corp IG) | +0.074*** | (0.026) | +0.034** | (0.015) | 0.179 |
| FTEBIGEURT | +0.086*** | (0.030) | +0.029*** | (0.011) | 0.078† |
| FTSE MIB | +0.121** | (0.050) | +0.272*** | (0.074) | 0.094† |

- For corporate debt: spillovers tend to be larger in low-stress periods (more
  normal transmission). The difference is rarely significant.
- For equities (FTSE MIB): reversal — stronger in high-stress periods. This is
  consistent with a risk-on/flight-to-safety interpretation: when spreads are high,
  successful BTP auctions provide a stronger relief signal to equity markets.
- Overall: no statistically strong asymmetry at 5% level.

### Stress 2: CLIFS Italy 70th-percentile split (L&G 2022 approach)

CLIFS threshold: 0.1498. High: N=93, Low: N=238.

| Series | Low β | High β | p(diff) |
|---|---|---|---|
| I09919EU (BBB Corp) | +0.028** | +0.067*** | 0.015** |
| HY 250MM | +0.021*** | +0.060*** | 0.042** |
| FTSE MIB | +0.178*** | +0.258*** | 0.474 |

- Higher-credit-risk assets (BBB, HY) show significantly stronger spillovers
  in high-CLIFS months. This is consistent with a credit-channel story: under
  systemic stress, sovereign demand signals are more informative about credit conditions.

---

## VIII. Sign-Dependent Asymmetry

**Model** (L&G 2022, eq. 8):
Split-sample Huber-T on positive ($\tilde{D}_t \geq 0$, N=141) vs.
negative ($\tilde{D}_t < 0$, N=190) shocks.

**Selected results:**

Columns: Hub(+) = Huber-T on positive-shock subsample (N=141); Hub(−) = on negative-shock
subsample (N=190). SEs not reported by Cell 47 output.

| Series | Hub(+) | Hub(−) | p(diff) |
|---|---|---|---|
| XBLC | +0.093*** | +0.012 | 0.034** |
| FTEBIGEURT | +0.033* | +0.040 | 0.860 |
| FTSE MIB | +0.239*** | +0.309** | 0.629 |
| EURO STOXX | +0.174*** | +0.315*** | 0.251 |
| MSCI Europe | +0.158*** | +0.290*** | 0.231 |
| VSTOXX | −0.190*** | −0.381*** | 0.205 |
| VIX | −0.126* | +0.074 | 0.135 |
| EUR/USD | −0.004 | +0.134** | 0.055† |
| MOVE | +0.288*** | −0.095 | 0.239 |
| Bund Futures | +0.068* | −0.015 | 0.127 |

**Takeaways:**
- **XBLC:** Significant asymmetry (p=0.034) — positive shocks drive the corporate
  bond spillover; negative shocks do not. Directional channel.
- **Equities (FTSE MIB, EURO STOXX, MSCI):** Symmetric in the sense that the slope
  is positive in both subsamples and the difference test is insignificant (p=0.23–0.63).
  Within the negative-shock subsample the coefficient is larger but imprecisely estimated.
  Both directions resolve sovereign uncertainty, consistent with an information channel.
- **VSTOXX:** Significantly negative for **both** shock signs (−0.190\*\*\* positive,
  −0.381\*\*\* negative), difference insignificant (p=0.205). Positive BTP demand
  reduces European vol; weak BTP demand raises it even more strongly.
- **VIX:** Asymmetric. Significant only for **positive** shocks (−0.126\*); the
  negative-shock coefficient is +0.074 (insignificant). Global vol responds
  mainly to good news from BTP auctions, not bad news.
- **EUR/USD:** Negative shocks → EUR depreciation (Hub(−) = +0.134**, p-diff=0.055†).
  Weak BTP demand is read as sovereign stress, weakening EUR.
- **MOVE:** Positive shocks → MOVE rises (Hub(+) = +0.288\*\*\*); negative shocks
  → near zero. Successful auctions appear to reprice rate uncertainty upward.
- **Bund Futures:** Positive shocks only (Hub(+) = +0.068\*) — safe-haven rebalancing
  away from Bunds when BTP demand is strong.

---

## IX. Robustness Battery

### R1 — BTC Level vs. First Difference
Using BTC\_BoI level: all maturities insignificant except 15Y marginal (Hub p=0.053).
**Verdict:** First-differencing is the correct specification.

### R2 — Symmetric Window Sensitivity
Widening the window in steps: [10:40–11:35], [10:45–11:30], [10:50–11:25], [10:55–11:20]
vs. baseline [10:59–11:15].

**Pooled comovement coefficients (Huber, Panel A representative):**
- XBLC pooled: 0.047***, 0.046***, 0.047***, 0.053***, 0.046*** (baseline)
- FTEBIGEURT pooled: 0.059***, 0.055***, 0.052***, 0.048***, 0.044***

**Verdict:** Results are stable across all window specifications. The narrow baseline
window produces similar or slightly stronger signals than the wider windows,
confirming that the auction announcement interval is the relevant identification moment.

### R3 — Alternative HAC Lag Orders (L = 0, 1, 2, 3, 4, 5)

| Maturity | L=0 | L=1 | L=2 | L=3 (base) | L=4 | L=5 |
|---|---|---|---|---|---|---|
| 3Y | *** | *** | *** | *** | *** | *** |
| 5Y | — | — | — | — | — | — |
| 10Y | *** | *** | *** | *** | *** | *** |
| 15Y | *** | *** | *** | *** | *** | *** |
| 30Y | — | ** | ** | ** | ** | ** |
| Pooled | *** | *** | *** | *** | *** | *** |

Coefficients are **identically** 0.508, 0.783, 0.667, 0.200, 0.492 across all lag orders
(point estimates do not change with lag choice — only SEs vary). Significance pattern
is stable. **Verdict: fully robust.**

### R4 — Winsorization

| Maturity | OLS full | Huber-T (base) | OLS wins 1% | OLS wins 5% |
|---|---|---|---|---|
| 3Y | 0.867** | **0.508***| 0.772*** | 0.701*** |
| 5Y | 0.336 | 0.231 | 0.355 | 0.351 |
| 10Y | 0.791 | 0.783*** | 0.874* | 0.801** |
| 15Y | 0.786*** | 0.667*** | 0.767*** | 0.622*** |
| 30Y | 0.099 | 0.200** | 0.162 | 0.197** |
| Pooled | 0.549*** | 0.492*** | 0.588*** | 0.528*** |

OLS-winsorized ≈ Huber-T in sign and significance throughout. Key: OLS-full on 10Y
is insignificant but Huber is — winsorization restores OLS significance (OLS wins 1%
gives β=0.874*), confirming the gap is driven by outliers, not specification.
**Verdict: Huber-T choice is robustly justified.**

### R5 — Day-Before Placebo (Main Regression)

Using the same [10:59–11:15] window on the business day *before* each auction
as the dependent variable:

| Maturity | Post β (base) | Prev β | Post p | Prev p | Verdict |
|---|---|---|---|---|---|
| 3Y | 0.508*** | 0.137 | 0.006 | 0.346 | ✓ Pass |
| 5Y | 0.231 | −0.179 | 0.261 | 0.443 | ✓ Pass |
| 10Y | 0.783*** | 0.295 | 0.005 | 0.375 | ✓ Pass |
| 15Y | 0.667*** | 0.593 | 0.000 | 0.125 | ✓ Pass |
| 30Y | 0.200** | 0.317 | 0.033 | 0.109 | ✓ Pass |
| **Pooled** | 0.492*** | **0.400***| 0.000 | **0.017** | ⚠ FAIL |

**The pooled placebo failure is a red flag.** On the day before each auction,
ΔBTC\_BoI predicts the intraday BTP price move — which should be null.
This suggests either: (a) consecutive-auction-date overlap in the pooled sample
(10Y+15Y+30Y are sometimes auctioned on adjacent days, sharing some ΔBTC); or
(b) a structural pattern in market pricing the day before long-maturity auctions.
Per-maturity results all pass — the anomaly is in the pooling.
**This needs investigation and likely a discussion/robustness note in the paper.**

### R6 — Day-Before Placebo on Spillover Panels

Replacing $\tilde{D}_t$ with the previous-day BTP window shock in comovement regressions:

| Panel | Key finding |
|---|---|
| A (Corp debt) | Mostly null, but IUS6 (+0.016**) and FTEBIGEURT (+0.021**) have small positive placebo effects |
| B (Equities) | Fully null (FTSE MIB: −0.065, ns) |
| C (Exogeneity) | Some inflation swap and FX placebo effects (EUIL5YF5Y, EUIL5YF10Y significant) |

**Verdict:** Panel B spillovers are clean. Panel A has small but significant placebo
effects in two series — worth noting in the paper as a caveat (or explaining why
the pre-day window would be correlated with next-day corporate spreads).

### R7 — ECB Meeting-Day Exclusion

Dropping 4–10 auction dates per maturity that coincide with ECB GC announcement days:

| Maturity | N\_base | Dropped | β\_base | β\_excl |
|---|---|---|---|---|
| 3Y | 198 | 9 | 0.508*** | 0.473** |
| 5Y | 200 | 6 | 0.231 | 0.207 |
| 10Y | 208 | 4 | 0.783*** | 0.751*** |
| 15Y | 66 | 2 | 0.667*** | 0.653*** |
| 30Y | 57 | 4 | 0.200** | 0.260*** |
| Pooled | 331 | 10 | 0.492*** | 0.509*** |

**Verdict: fully robust.** Estimates stable or slightly stronger after exclusion.
The ECB 13:15 announcement is 2 hours after the 11:15 window close by construction —
this test confirms no anticipatory contamination.

### R8 — Crisis and COVID Exclusion

Crisis: 2011-07-01 to 2012-12-31 (Italian sovereign stress).
COVID: 2020-02-01 to 2020-12-31.

| Maturity | Baseline | Crisis excl. | COVID excl. | Both excl. |
|---|---|---|---|---|
| 3Y | 0.508*** | 0.366** | 0.494*** | 0.355** |
| 5Y | 0.231 | −0.004 | 0.283 | 0.014 |
| 10Y | 0.783*** | 0.620*** | 0.759*** | 0.594** |
| 15Y | 0.667*** | 0.717*** | 0.647*** | 0.695*** |
| 30Y | 0.200** | 0.200** | 0.195** | 0.195** |
| Pooled | 0.492*** | 0.474*** | 0.479*** | 0.460*** |

- Pooled estimates very stable (0.46–0.49***) regardless of sample.
- 3Y: weakens somewhat under crisis exclusion — part of the 3Y signal comes from
  crisis-era auction variability.
- 5Y: turns negative under crisis exclusion (was already insignificant).
- 10Y, 15Y, 30Y: all robust.
- **Verdict:** Main results are not an artefact of the Italian sovereign crisis.

### R9 — Wald Test: Pooling Validity

H₀: $\beta^{(m)}$ is identical across maturities in the pooled sample.
Tested via ΔBTC\_BoI × maturity interaction terms; F-test.

**Pooled long-run (10Y, 15Y, 30Y):**
- OLS-HAC: F(2,325)=2.103, p=0.124 — fail to reject (pooling valid)
- Huber-WLS-HAC: F(2,325)=3.040, p=0.049** — reject at 5%

**Pooled all 4 maturities (3Y+10Y+15Y+30Y):**
- OLS-HAC: F(3,521)=1.949, p=0.121 — fail to reject
- Huber-WLS-HAC: F(3,521)=1.959, p=0.119 — fail to reject

**Winsorization check on pooled\_long:**
After 1% or 5% winsorization, OLS-pooling always passes (p≈0.12–0.15).
The Huber rejection is driven by the 30Y interaction (β̂\_30Y significantly
below β̂\_10Y under Huber), but this survives winsorization at most levels.

**Assessment:** There is moderate slope heterogeneity: the 30Y coefficient
(~0.20) is substantially lower than 10Y (0.78). OLS accepts pooling; Huber
marginally rejects. **The pooled point estimate (0.49) is a meaningful average
but the paper should acknowledge that the 30Y maturity behaves differently.**

---

## X. Outputs Generated

All LaTeX tables are saved to `results/tables/`:

| File | Content |
|---|---|
| `HAC.tex` | Main BTC regression table (per maturity + pooled) |
| `diagnostics.tex` | Diagnostics table (all maturities) |
| `comov_pooled.tex` | Full comovement table (pooled long-run, all panels) |
| `comov_maturities.tex` | Per-maturity comovement table |
| `comov_panelA.tex` | Panel A only (corporate debt) |
| `comov_panelB.tex` | Panel B only (equities) |
| `comov_spillovers.tex` | Panels A+B combined |
| `comov_exogeneity.tex` | Panel C only (exogeneity series) |
| `magnitude_analysis.tex` | Magnitude table (σ, effect, effect/σ, bp) |
| `stress_split.tex` | Stress split (CDS median) |
| `stress_split_clifs.tex` | Stress split (CLIFS 70th pct) |
| `sign_split.tex` | Sign asymmetry table |
| `ecb_exclusion.tex` | ECB meeting-day exclusion |
| `crisis_covid_exclusion.tex` | Crisis + COVID exclusion |
| `prev_day_placebo_spillover.tex` | Day-before placebo on spillover panels |
| `winsorization.tex` | Main regression winsorization |
| `winsor_spillover.tex` | Spillover winsorization |
| `hac_lags.tex` | HAC lag sensitivity |

Figures saved to `results/figures/`:
- `hac_lags.pdf` (HAC lag sensitivity chart)
- Motivating charts (27/02/2014 price spike) — Cells 34–35

---

## XI. Open Econometric Issues

The following issues require attention before submission:

1. **Pooled placebo failure (R5):** ΔBTC\_BoI predicts the *previous-day* BTP window
   price change in the pooled sample (β=0.400**, p=0.017). Per-maturity: all pass.
   Root cause unclear — investigate whether 10Y/15Y/30Y auctions often fall on
   consecutive days and whether the ΔBTC is correlated within a few-day window.
   If yes, the pooled specification needs a guard against adjacent-auction contamination.

2. **5Y exclusion from pool:** The 5Y first stage is never significant. This should
   be explicitly motivated in the paper. One candidate: 5Y CTD (cheapest-to-deliver)
   dynamics, different auction mechanism, or different investor base.

3. **30Y heterogeneity:** The 30Y coefficient (~0.20) is materially lower than 10Y/15Y
   (~0.67–0.78). The Wald test borderline rejects pooling (Huber p=0.049). Consider
   either: (a) reporting the pooled result with a coefficient heterogeneity caveat;
   (b) reporting a separate 30Y block; or (c) framing the long-term pool as 10Y+15Y only.

4. **VSTOXX/VIX in Panel C:** These are significantly negative pooled. Either they
   belong in a "spillover to risk appetite" sub-panel (Panel B.2?), or the paper needs
   a clear argument for why this does not threaten identification.

5. **Inflation swaps at 30Y:** ITIL5YF5Y, EUIL5YF5Y, EUIL5YF10Y all show R²≈0.26–0.38
   at the 30Y maturity level (extremely high for this context). This is suspicious —
   could be a small-sample artifact (N=36–38) or genuine long-duration inflation
   repricing. Investigate.

6. **Bund Futures (FGBLc1):** Significant positive spillover (Panel C). Should this
   be reframed as a sovereign-to-sovereign transmission finding (moves to Panel B?)
   rather than an exogeneity concern?

---

## XII. Key Numbers to Cite (Pooled, Huber-T)

| Statistic | Value |
|---|---|
| Main β (Huber, pooled) | 0.492 (SE=0.110, t=4.47, p<0.001) |
| Main β (OLS, pooled) | 0.549 (SE=0.199, t=2.76, p=0.006) |
| Pooled N | 331 |
| FTSE MIB spillover δ (Huber) | 0.217 (SE=0.049, t=4.43, p<0.001) |
| EURO STOXX spillover δ (Huber) | 0.148 (SE=0.047, t=3.17, p=0.002) |
| XBLC spillover δ (Huber) | 0.046 (SE=0.012, t=3.78, p<0.001) |
| FTEBIGEURT spillover δ (Huber) | 0.044 (SE=0.012, t=3.60, p<0.001) |
| Effect FTSE MIB per 1σ shock (bp) | 21.7 bp |
| Effect XBLC per 1σ shock (bp) | 4.6 bp |
| HAC lag (fixed) | L=3 |
| Stress dummy (CDS): median | 131.85 bp |
| CLIFS threshold (70th pct) | 0.1498 |
