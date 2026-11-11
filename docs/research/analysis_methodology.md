# Analysis Methodology Brief (METH)

**Date:** 2026-11-10 · **Status:** reconstructed · **Canonical source:** [SPEC.md §10](../SPEC.md)

> **How this file was produced.** The research agent drafted this brief, but its file write was blocked. Its condensed findings were recovered from the workflow journal, and the main session wrote this file. **SPEC §10 wins wherever this brief and the SPEC disagree.** §5 lists every place where the agent's original proposal differs from the SPEC and records which version was adopted.
>
> **Verification limits.** Web search was unavailable to the agent, and Crossref, SSRN, Morningstar and S&P returned 403. ETF data was fetched from StockAnalysis.com and avantisinvestors.com. DOIs were checked by confirming they redirect to the publisher; article text was not re-read. Items marked **[UNVERIFIED]** must be checked before the knowledge base ships. The main session recomputed every worked example in this file with Python, and corrected values are marked.

---

## A. ETF evaluation

Each metric gets a **pass / caution / fail** flag. Funds are never ranked on a single number, and they are compared only within the same category (core equity, factor, dividend, bond). SPEC §10.4 holds the composite ETF score.

### A.1 Cost
| Metric | Formula | Core: pass / fail | Factor: pass / fail |
|---|---|---|---|
| Expense ratio (ER) | as stated by the issuer | ≤ 0.10% / > 0.20% | ≤ 0.40% / > 0.60% |
| Tracking difference (TD) | fund TR − index TR, averaged over 3–5 years | — | — |
| Excess cost | −TD − ER | ≤ 0.10% pass | ≤ 0.10% pass |
| Tracking error | std(monthly difference) · √12 | shown for information only | — |

**Fee drag example.** $500/month for 30 years at a 7% gross return, monthly compounding, fee deducted from the return. *Main-session recomputation; the draft gave $609,909 / $584,000 / $504,769.*

| ER | Ending value | Lost to fees vs 0% |
|---|---|---|
| 0.03% | ≈ $606,400 | ≈ $3,600 |
| 0.25% | ≈ $580,700 | ≈ $29,300 |
| 1.00% | ≈ $502,300 | ≈ $107,700 |

The 0% baseline is ≈ $610,000. Evidence on fees: French (2008), where the cost of active investing was 0.67% a year of market value from 1980–2006 [ACAD]; Sharpe (1991).

### A.2 Size and liquidity
- **AUM.** Core funds pass at ≥ $1B. Below $100M fails because of closure risk. *(Practitioner heuristic, not a research finding.)*
- **Bid-ask spread.** Pass at ≤ 0.05% (core) or ≤ 0.15% (factor). Round-trip cost ≈ spread / price. Example: AVDV ≈ $0.05 / $105 ≈ 0.05%.

### A.3 Overlap and concentration
- Holdings overlap = Σ min(w_A,i, w_B,i). Above **70%** is flagged as "redundant".
- Look-through exposure to stock i = Σ_f (portfolio weight of fund f × weight of i in f) + direct holding of i. This feeds the FR-413 portfolio-fit panel (e.g., "AAPL is already 6.8% of your VOO").
- Concentration flags: top-10 weight > 35%; any single stock > 8%; any sector > 35% or > 1.5× its weight in VT. Note that S&P 500 funds are themselves concentrated: the top 10 were 40.6% in November 2026 [MKT].

### A.4 Factor regression (Fama-French 5 factors + momentum)
- **Data.** Ken French Data Library monthly files. File names were verified:
  - US: `F-F_Research_Data_5_Factors_2x3_CSV.zip` and `F-F_Momentum_Factor_CSV.zip`.
  - International: `Developed_ex_US_5_Factors_CSV.zip`, `Developed_5_Factors_CSV.zip` and `Emerging_5_Factors_CSV.zip`.
- **Model.** R_fund − RF = α + β_M·(Mkt−RF) + s·SMB + h·HML + r·RMW + c·CMA + m·MOM + ε. Monthly returns are in percent, and standard errors are Newey-West.
- **History.** At least 36 months is required; warn below 60. Fewer than 36 months gives a neutral score of 50 ("insufficient history"), per SPEC §10.4.
- **How to read the loadings.** SMB > 0.5 is a strong small-cap tilt, HML > 0.3 a value tilt and RMW > 0.2 a profitability tilt. A loading only counts when |t| > 2. Expected loadings for AVUV, QQQM and SCHD are **[UNVERIFIED]**, so compute them; don't hard-code them.
- **Use.** Factor fit is 20 points of the ETF score (SPEC §10.4), with target loading vectors set by the user's goal.

### A.5 Dividends, tax and asset location
- Rank funds by **total return, not dividend yield** (Miller & Modigliani 1961; Hartzmark & Solomon 2019, JF, doi:10.1111/jofi.12785).
- Annual dividend tax drag in a taxable account = yield × dividend tax rate. At 15%: SCHD (2.94% yield) ≈ 0.44% a year; VTI (1.07%) ≈ 0.16% a year.
- Asset location heuristic:
  - Bonds go in Traditional (pre-tax) accounts.
  - The highest expected-growth tilts go in the Roth.
  - Broad low-yield equity funds and VXUS go in taxable, because VXUS's foreign tax credit is only usable there.

### A.6 Reference data for core ETFs
ER and AUM were fetched 2026-11-10/11 from StockAnalysis.com. AVUV and AVDV ERs were cross-checked on avantisinvestors.com (as of 2026-01-01). Yields are trailing 12-month yields. **Refresh before use.**

| Ticker | Role | ER | AUM | Yield |
|---|---|---|---|---|
| VT | Global total market | 0.06% | $81.3B | 1.56% |
| VTI | US total market | 0.03% | $670.1B | 1.07% |
| VOO | S&P 500 | 0.03% | $1.03T | 1.08% |
| VXUS | Ex-US total market | 0.05% | $158.0B | 2.40% |
| AVUV | US small-cap value | 0.25% | $31.9B | 1.24% |
| AVDV | Intl small-cap value | 0.36% | $19.4B | 3.30% |
| AVGE | Global factor-tilted equity | 0.23% | $1.26B | 1.76% |
| AVGV | Global value | 0.26% | $480.8M | 2.00% |
| DFAC | US core equity (factor tilt) | 0.17% | $48.1B | 0.88% |
| SCHD | US dividend | 0.06% | $120.2B | 2.94% |
| VIG | US dividend growth | 0.04% | $114.4B | 1.46% |
| QQQM | Nasdaq-100 | 0.15% | $101.7B | 0.47% |
| BND | US aggregate bonds | 0.03% | $167.8B | 4.15% |

AVGV is below the $1B core-AUM pass threshold, which is acceptable for a factor fund.

### A.7 Model core templates for moderate risk
Illustrative only; the IPS sets the actual targets.
1. **One fund + bonds:** 80% VT / 20% BND.
2. **Three funds:** 50% VTI / 30% VXUS / 20% BND.
3. **Factor tilt:** 40% VTI or DFAC / 15% AVUV / 20% VXUS / 5% AVDV / 20% BND.

Only use the factor tilt if the user would stick with it through a 10-year stretch of lagging the S&P 500. Value lagged in 2023–2025 [ACAD], and factor premiums are haircut by 50% in projections (SPEC §10.7).

---

## B. Individual stock analysis

Scoring follows SPEC §10.3: winsorize at the 1st/99th percentile, rank as a percentile within the sector (with at least 20 peers), and compute block sub-scores. A block with less than 60% data coverage gets no score. Banks, insurers and REITs use separate metrics (P/B and ROE; FFO/AFFO for REITs).

### B.1 Quality
| Metric | Formula | Good / flag | Evidence |
|---|---|---|---|
| ROIC | NOPAT / (debt + equity − cash) | > 15%, or > WACC for 5+ years | QMJ profitability |
| Gross profitability | (revenue − COGS) / total assets | sector percentile | Novy-Marx 2013, JFE |
| FCF margin | (CFO − capex) / revenue | > 10% | — |
| Net debt / EBITDA | (debt − cash) / EBITDA | < 2 good; > 3.5 caution; > 4 is the SPEC HIGH_LEVERAGE flag | QMJ safety |
| Interest coverage | EBIT / interest | > 8 good; < 3 is a flag | — |
| Piotroski F-score | 9 binary signals | 7–9 strong; ≤ 3 weak | Piotroski 2000, JAR |

### B.2 Distress and manipulation screens
These are **proposed** checks; see §5. SPEC v1 does not include them as red flags.
- **Altman Z** (manufacturers): Z = 1.2·WC/TA + 1.4·RE/TA + 3.3·EBIT/TA + 0.6·MVE/TL + 1.0·Sales/TA. Above 2.99 is safe; below 1.81 is distress (Altman 1968, JF).
  - For non-manufacturers use Z'' = 6.56·WC/TA + 3.26·RE/TA + 6.72·EBIT/TA + 1.05·BVE/TL, where below 1.1 is distress.
  - Draft worked example: Z = 3.03, Z'' = 3.20.
- **Beneish M** = −4.84 + 0.920·DSRI + 0.528·GMI + 0.404·AQI + 0.892·SGI + 0.115·DEPI − 0.172·SGAI + 4.679·TATA − 0.327·LVGI (Beneish 1999, FAJ).
  - Flag above −1.78; practitioners often use −2.22. **[UNVERIFIED]:** check the cutoff against the original paper.
  - Draft worked examples: −2.47 is clean; −0.94 is flagged.

### B.3 Growth
- 5- and 10-year CAGR of revenue, EPS and FCF/owner earnings. Consistency = share of up years and the standard deviation of yearly growth.
- **Haircut extrapolated growth.** Growth rates persist poorly beyond chance (Chan, Karceski & Lakonishok 2003, JF) **[citation details UNVERIFIED]**. That is why SPEC caps g₁ at min(5-year revenue CAGR, 15%), and why growth is non-canonical (≤ 10% weight).

### B.4 Valuation
- **Multiples.**
  - Earnings yield = EBIT/EV. It was the best single value metric in Gray & Vogel (2012) **[UNVERIFIED]**.
  - P/E against its own 10-year history and against the sector.
  - FCF yield: above 5% is attractive.
  - PEG below 1 (Lynch heuristic).
- **SPEC DCF (canonical, §10.3.6).**
  - Uses owner earnings OE = CFO − capex − SBC per share.
  - g₁ for years 1–5, then g₂ = 5% for years 6–10.
  - Terminal value = OE₁₀ × 15 (worst case 10, best case 20).
  - r = 10% (Carlin's hurdle rate; CAPM is an option).
  - Three scenarios weighted 0.3 / 0.4 / 0.3.
  - MOS_PASS ⇔ worst-case IV ≥ price.
- **Draft DCF example** (Gordon-growth terminal, a teaching example). It shows how sensitive a DCF is to its inputs. *All figures below were recomputed by the main session.*
  - Inputs: base FCF $1.0B; 8% growth for 5 years, fading linearly to 3% over the next 5; r = 9.5%; terminal growth 3%; $2B net debt; 200M shares.
  - Result: EV $20.99B (terminal value $11.99B, or 57%), giving **$94.93/share**.
  - Sensitivity, varying one input at a time:
    - growth 5% → $78.70, growth 11% → $113.92;
    - r 8.5% → $114.88, r 10.5% → $80.33.
    - Changing both together spans $66.66–$138.03.
  - **Reverse DCF:** a $120 price implies **11.9%** growth in years 1–5. Flag it when the implied growth is above the stock's historical CAGR.
  - The r = 9.5% came from Damodaran's implied ERP of 4.41% (Nov 1, 2026), alongside the 5.29% Treasury rate used as the risk-free rate.
- **Margin of safety by uncertainty** (Morningstar-like): low 20%, medium 30%, high 40%, very high 50%. **[UNVERIFIED]:** the Morningstar pages returned 403. SPEC uses the worst-case IV ≥ price test instead; see §5.

### B.5 Dividend safety
- SPEC §10.3.5 holds the canonical 0–100 score: Safe ≥ 70, Borderline 50–69, Unsafe < 50.
- Supporting heuristics:
  - FCF payout below 60% is safe; above 80% is a flag.
  - 25+ consecutive increases is "aristocrat"-style.
  - **Chowder rule:** yield + 5-year DPS CAGR must be at least 12 when the yield is ≥ 3%, at least 15 when it is below 3%, and at least 8 for utilities. The thresholds come from the Seeking Alpha community, not a primary source.
  - Chowder examples: 2.9 + 8.5 = 11.4 (yield < 3, needs 15) fails; 1.2 + 9.0 = 10.2 fails; 3.5 + 7.0 = 10.5 (needs 12) fails.
- Dividend metrics are a **quality screen, not a return signal** (Hartzmark & Solomon 2019).

### B.6 Moat checklist
- The five Morningstar sources: intangible assets, switching costs, network effects, cost advantage and efficient scale.
- Quantitative evidence of a moat: ROIC above WACC across 10 years, plus stable gross margins.
- Wide moat means about 20 years of excess returns; narrow means about 10. **[UNVERIFIED]:** the Morningstar definitions page was blocked.
- Completing the checklist is required for the `quality_growth` style gate and for pre-trade checklist item 7.

### B.7 Sven Carlin's risk/reward quadrant
Source: svencarlin.com, 2024-11-22.
- **Risk** = permanent capital loss, or unsatisfying long-term returns. It is not volatility. "Risk is a function of price."
- **Reward** = conservative, cycle-averaged owner earnings relative to price.
- Plot candidates on the quadrant and favor only the low-risk / high-reward corner.
- In the app, the x-axis is the implied 10-year return from SPEC §10.3.6, and the y-axis is downside measured from the worst-case IV.

### B.8 Composite score (canonical = SPEC §10.3.3)
| Style | Quality | Value | Safety | Momentum | Dividend growth | Growth |
|---|---|---|---|---|---|---|
| `quality_value` (default) | 35 | 30 | 15 | 10 | 10 | — |
| `dividend_growth` | 40 | 25 | 20 | 5 | 10 | — |
| `quality_growth` (Carlson-style) | 45 | 20 | 15 | 10 | — | 10 |
| `deep_value` (Carlin-style) | 25 | 45 | 20 | 5 | 5 | — |

**Worked example** with SPEC weights, recomputed: Quality 82, Value 64, Safety 75, Momentum 48, Dividend 70, Growth 55.
- `quality_value` = 0.35·82 + 0.30·64 + 0.15·75 + 0.10·48 + 0.10·70 = **70.95**
- `dividend_growth` = 0.40·82 + 0.25·64 + 0.20·75 + 0.05·48 + 0.10·70 = **73.20**
- `quality_growth` = 0.45·82 + 0.20·64 + 0.15·75 + 0.10·48 + 0.10·55 = **71.25**
- `deep_value` = 0.25·82 + 0.45·64 + 0.20·75 + 0.05·48 + 0.05·70 = **70.20**

Each soft red flag subtracts 5 points, so one VALUE_TRAP flag drops `quality_value` to 65.95, which is "Watch".

**Why these weights.**
- They are simple, near-equal weights rather than optimized ones, because equal weighting is robust out of sample (DeMiguel, Garlappi & Uppal 2009, RFS) **[UNVERIFIED citation]**.
- Quality complements value (Novy-Marx 2013; Asness, Frazzini & Pedersen QMJ 2019).
- Value and momentum are negatively correlated (Asness, Moskowitz & Pedersen 2013, JF).
- Momentum is capped at 10 for turnover and tax reasons.

**Limitations (shown on every scorecard):**
- Free data has quality problems.
- Factors weaken after publication (McLean & Pontiff 2016).
- Testing many factors produces false positives (Harvey, Liu & Zhu 2016).
- Most single stocks underperform T-bills (Bessembinder 2018).
- There is no backtest, and survivorship bias applies (SPEC risk R-19).

The weights are **design choices**, not estimates from the research.

---

## C. Portfolio rules (canonical = SPEC P2, FR-105, FR-413, FR-804, FR-903)

- **Core-satellite.**
  - Core ETFs are by default ≥ 90% of equities and never < 80%.
  - `SATELLITE_CAP` defaults to 10% of equities (warn above it), with a strong warning above 20%.
- **Single-stock cap.** Warn above 5% of investable assets, with a strong warning above 10%. Look-through exposure from ETFs counts toward the cap.
- **Diversification.** A stock-only sleeve counts as "diversified" only with ≥ 30 stocks across ≥ 8 GICS sectors (FR-413). The draft's "8–15 names, any sector ≤ 30% of the satellite" is a reasonable **user preset** for a small sleeve, but it is not diversified by that definition.
- **Rebalancing** (FR-804).
  - Bands are ±5 pp absolute, or ±25% relative for sleeves under 20% (the Swedroe 5/25 rule); for example, a 10% sleeve has ±2.5 pp.
  - Drift is checked monthly; trades happen only when a band is breached; there is a full review once a year (Jaconetti, Kinniry & Zilbering 2010, Vanguard **[UNVERIFIED citation]**).
  - Rebalance with new contributions first, then inside tax-advantaged accounts, then with taxable sales.
- **Stress tests.** These simplified peak-to-trough figures are approximate inputs **[UNVERIFIED]**. The app replays actual monthly paths (SPEC §10.7).

| Scenario (equity, bonds) | 100/0 | 80/20 | 60/40 |
|---|---|---|---|
| GFC (−51%, +5%) | −51% | −39.8% | −28.6% |
| 2022 (−25%, −17%) | −25% | −23.4% | −21.8% |
| Dot-com (−45%, +25%) | −45% | −31.0% | −17.0% |
| COVID (−34%, 0%) | −34% | −27.2% | −20.4% |

  - The user states the largest dollar loss they would tolerate, and the app picks the bond share to match.
  - Example: a 15% satellite of 8 equal-weight stocks where one stock falls 80% costs only 0.15 × 0.125 × 0.80 = **1.5%** of the portfolio.
- **Behavioral guardrails.**
  - A 72-hour cooling-off period, configurable from 48 to 72 hours (FR-903).
  - A pre-committed IPS.
  - A decision journal entry with thesis, what must be true, a pre-mortem, a confidence level and a review date.
  - Showing the behavior gap (Dichev 2007, AER; Barber & Odean 2000, JF).
- **Cash.** Covered by [savings_and_account_strategy.md](savings_and_account_strategy.md) and SPEC §10.1–10.2. The persona defaults to 4.5 months of expenses, and the order of operations is starter emergency fund → 401(k) match → debt above 8% → …

---

## D. Expert-consensus aggregation (canonical = SPEC §10.5–10.6)

- **One record per claim**, with fields: source, source type, date, ticker or topic, stance, time horizon, rationale category, evidence cited, conflicts of interest, verification status and staleness half-life.
  - Conflicts include sponsors, affiliate links, paid platforms (e.g., Sven Carlin's Research Platform) and the creator's own holdings.
  - Half-life is about 180 days for stock calls and about 3 years for general principles.
- **Evidence tier before popularity:** meta-analyses and replications > peer-reviewed work > institutional research (Vanguard, Dimensional, AQR, Morningstar) > books > creators > social media. The academic verdict is shown first and separately; it is never averaged together with creators' views.
- **Collapse opinions that aren't independent**, such as several creators citing one source, into a single cluster. This avoids information cascades (Bikhchandani, Hirshleifer & Welch 1992; Banerjee 1992). The wisdom of crowds requires independent opinions (Chen et al. 2014, RFS **[UNVERIFIED]**), and sell-side analysts lean optimistic (Barber et al. 2001 **[UNVERIFIED]**).
- **Show the distribution and the dissent**, with consensus / lean / contested labels per SPEC §10.6. Sponsored items get zero weight.
- **Consensus has 0% weight in the Buy score.** It is context only.

---

## 5. Reconciliation: original agent proposal vs SPEC (adopted)

| Topic | Agent's original proposal | SPEC (adopted) | Action |
|---|---|---|---|
| Style set | Value/quality, dividend growth, growth, factor | quality_value, dividend_growth, quality_growth, deep_value | SPEC adopted. A "factor" style for stocks is redundant with factor ETFs. |
| Weights | VQ: Q35 V35 H15 G10 M5; DG: D35 Q25 V20 H15 G5; G: G35 Q25 V15 M15 H10; F: V30 Q30 M25 Inv15 | See §B.8 | SPEC adopted: growth ≤ 10% (non-canonical), momentum ≤ 10. |
| Hard gates | Altman Z distress, Beneish M > −1.78, F ≤ 3, coverage < 3, negative FCF in 3 of 5 years | HIGH_LEVERAGE (coverage < 3), NEG_OWNER_EARNINGS (soft), UNPROFITABLE, etc. No Altman or Beneish | **Open question:** add ALTMAN_DISTRESS and BENEISH_FLAG as soft flags in v1.1? |
| Margin of safety | Required MOS of 20/30/40/50% by uncertainty | Worst-case IV ≥ price | SPEC adopted. The bands are unverified. |
| DCF | FCF, r = 9.5% (ERP-based), Gordon terminal | Owner earnings, r = 10%, terminal multiple 15 (10 / 20) | SPEC adopted. The draft example is kept as a teaching example. |
| Satellite | 10–20% of the portfolio; 8–15 names; sector ≤ 30% | 10% of equities (warn), > 20% strong warning | SPEC adopted. Names and sector cap are a user preset. |
| Fee-drag figures | $609,909 / $584,000 / $504,769 | — | Corrected to ≈ $606.4K / $580.7K / $502.3K. |

## 6. Still to verify before the knowledge base ships
Bibliographic volume and page numbers for most papers (only the DOIs resolved); Gray & Vogel 2012; Chan, Karceski & Lakonishok 2003; DeMiguel et al. 2009; Chen et al. 2014; Barber et al. 2001; Jaconetti et al. 2010; the Morningstar moat and MOS bands; the Beneish cutoff; the stress-test inputs; ETF factor loadings and overlap percentages; and the Chowder thresholds, which are community-sourced.
