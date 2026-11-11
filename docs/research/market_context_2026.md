# Market Context and Popular Ideas/Trends: Brief as of 10 November 2026

> **STALENESS WARNING.** This is the most perishable file in the knowledge base. Every number below is a snapshot with an "as of" date. Valuations, yields and fund yields can move a lot within weeks. The app must show each figure's as-of date and display a "stale" badge after the refresh intervals in Section 9. Nothing here is a forecast or a buy/sell recommendation.

**How this was compiled.** The WebSearch tool was unavailable (provider error). Every figure was therefore fetched directly from primary or near-primary pages with WebFetch on 2026-11-10: Federal Reserve, BLS, BEA, U.S. Treasury, FRED, FDIC, TreasuryDirect, EIA, FactSet Insight, J.P. Morgan Asset Management (Guide to the Markets PDF, LTCMA page), Vanguard, BlackRock, AQR, iShares, multpl.com and StockAnalysis.com.
- Figures marked **[UNVERIFIED]** could not be confirmed from a fetched page.
- Figures marked **[PARSE-UNCERTAIN]** came from text extracted out of a chart PDF, where column alignment was ambiguous.
- Fund figures from StockAnalysis.com are third-party aggregations. Before acting, confirm them on the issuer's own page.

---

## 0. One-screen dashboard snapshot (what the app's "Market Context" header card should show)

| Metric | Value | As of | Source |
|---|---|---|---|
| S&P 500 level | 7,353.73 | 2026-11-05 close | FactSet, *Earnings Insight*, 2026-11-09 |
| S&P 500 forward 12-month P/E | **17.7x** (5-yr avg 19.8, 10-yr avg 19.1, 25-yr avg 16.4) | 2026-11-05 | FactSet, 2026-11-09 |
| Shiller CAPE | **39.7** (mean 17.45, median 16.14, record 44.19 in Dec 1999) | 2026-11-10 | multpl.com/shiller-pe |
| Top-10 weight in S&P 500 | **40.6%** of market cap, **36.1%** of earnings | 2026-09-30 | JPM *Guide to the Markets* (GTM) 4Q26, slide 8 |
| Fed funds target range | **3.75%–4.00%** (held 10–2 on 2026-10-28; two dissents favoured a **hike**) | 2026-10-28 | Federal Reserve statement |
| 3-mo / 2-yr / 10-yr / 30-yr Treasury | 4.30% / 4.73% / **4.98%** / 5.35% | 2026-11-10 | U.S. Treasury par yield curve |
| 10-yr TIPS real yield | **2.66%** | 2026-11-09 | FRED DFII10 |
| 10-yr breakeven inflation | 2.20% | 2026-11-10 | FRED T10YIE |
| CPI, October 2026 (m/m, SA) | headline +0.4%, core +0.3% (BLS cannot publish a 12-month change; see 3.3) | released 2026-11-10 | BLS CPI release |
| CPI, 12-month to September 2026 | ≈ **3.7%** (computed from FRED CPIAUCSL 336.298 / 324.245) | Sep 2026 | FRED; own calculation |
| PCE price index, 12-month | 3.6% | Sep 2026 (released 2026-10-29) | BEA |
| Unemployment / payrolls | 4.1% / +165k | Oct 2026 (released 2026-11-06) | BLS Employment Situation |
| High-yield OAS | 3.34% | 2026-11-09 | FRED BAMLH0A0HYM2 |
| Brent oil (EIA forecast) | ≈ $100/bbl through end-2026; 2026 average $97 | STEO, 2026-11-10 | EIA STEO |
| I bond composite / fixed rate | **5.51% / 1.40%** | issued 2026-11-01 to 2027-04-30 | TreasuryDirect |
| FDIC national avg savings rate | 0.38% (rate cap 4.63%) | 2026-10-19 | FDIC |
| Bitcoin | ≈ $73,455, about 42% below its $126,080 all-time high | 2026-11-10 | CoinGecko |

**The regime in one paragraph (Nov 2026).**
- **Earnings vs. prices.** U.S. corporate earnings have grown far faster than prices in 2026. Forward EPS is up **33.6%** year to date against a **7.4%** price gain. That pulled the S&P 500 forward P/E down from **22.0x (2025-12-31)** to **17.7x**, the lowest since Nov 2023 (FactSet, 2026-11-09).
- **Trailing valuations.** Long-lookback measures remain near historic extremes: CAPE is about 40. Analysts' forward EPS is also running at record profit margins (Section 1).
- **Inflation.** Inflation has re-accelerated to about 3.5–4% on headline CPI/PCE. The main driver is a Middle East conflict that disrupted Persian Gulf oil shipping (EIA STEO, Nov 2026).
- **Fed.** After cutting to 3.50–3.75% by Dec 2025, the Fed **hiked 25 bp at its 15–16 Sep 2026 meeting** (decision 2026-09-16, effective 2026-09-17), to 3.75–4.00% (Fed open-market page; statement monetary20260916a). Kevin Warsh took office as Chairman on **2026-05-22** (Fed bio page); Jerome Powell remains a governor.
- **Long yields.** Long rates rose sharply. The 10-yr went from 4.19% (2026-01-02) to 5.29% (2026-09-30), then back to 4.98% (2026-11-10).
- **Bonds and cash.** Starting yields on bonds and cash are among the highest since 2007. They are comparable to the Oct 2023 peak, not clearly above it. J.P. Morgan's 2027 LTCMA calls its global aggregate bond *return assumption* (5.3%) "the highest in two decades."

> **Program implication (global).** The app should show a "Regime" banner built from 4–6 live indicators (forward P/E, CAPE, 10-yr real yield, CPI y/y, Fed funds, HY spread), each with its percentile against history. The banner must stay **descriptive, not prescriptive**. The research base (Vanguard and others, below) shows that valuations are poor short-term timing tools. They mainly shift *long-run expected returns*. So the banner should feed expected-return ranges and savings-vs-invest framing, never "sell now" signals.

---

## 1. Equity valuations

### 1.1 The data
- **Forward P/E.**
  - FactSet: 17.7x on 2026-11-05, below the 5-yr (19.8) and 10-yr (19.1) averages and roughly at the 15-yr average (17.6).
  - Two sectors trade below their own 25-yr averages. Information Technology is at 17.3x vs. a 20.0 average, its lowest since 2020-03-23. Energy is at 11.7x vs. 13.9 (FactSet, "Lowest Forward 12-Month P/E Ratio For the S&P 500 in More Than 3 Years," J. Butters, 2026-11-09, https://insight.factset.com/lowest-forward-12-month-p/e-ratio-for-the-sp-500-in-more-than-3-years).
  - J.P. Morgan GTM (data as of 2026-09-30, index 7,652): 19.0x forward P/E vs. a 30-yr average of 17.2x, +1 std dev = 20.5x. CAPE 40.9x vs. 28.9x. Dividend yield 1.4% vs. 2.0%. Earnings yield minus Baa yield −1.0% vs. +0.7% average (GTM 4Q26 slide 5, https://am.jpmorgan.com/us/en/asset-management/adv/insights/market-insights/guide-to-the-markets/).
- **Earnings.**
  - Q3 2026 blended EPS growth is **31.4%** and revenue growth **14.3%**. 84% of companies beat estimates, but the aggregate surprise was only 2.7%, the smallest since Q4 2022.
  - CY2026 EPS growth estimate: 33.3%. Q4 2026: 28.4% (FactSet, "S&P 500 Earnings Season Update: November 6, 2026").
  - GTM: 2026 EPS growth of about 31.6%, of which margin expansion contributed 19.5 pts (vs. a 2.4-pt average for 2001–25). Consensus EPS: $362 (2026F), $417 (2027F), $488 (2028F). Quarterly net margin about 15.9% (3Q26F), a record (GTM slide 7).
  - FactSet (2026-10-26) reported "second-highest net profit margin in more than 15 years" for Q3 2026 (headline only).
- **Equity risk premium.** The Fed's May 2026 *Financial Stability Report* says P/E ratios are "in the upper range of its historical distribution" and that the estimated equity premium "remained well below its historical average" (https://www.federalreserve.gov/publications/2026-may-financial-stability-report-overview.htm). With a 10-yr TIPS yield of 2.66%, the forward earnings yield (1/17.7 ≈ 5.65%) leaves a thin cushion over real bonds.

### 1.2 Proponents vs. skeptics
| View | Argument | Evidence / citation |
|---|---|---|
| **"Valuations are fine / E is catching up"** | Earnings growth, not multiple expansion, has driven 2026. P/E compression while prices rise is the healthy kind. AI capex and productivity support durable margins. | FactSet 2026-11-09. JPM 2027 LTCMA raised U.S. large-cap to 7.5% "driven by profit margins above 15%." |
| **"Expensive on any normalised measure"** | CAPE about 40 is second only to 1999–2000. Forward P/E relies on record margins and +33% EPS growth that may be cyclical (energy windfall from the oil shock, AI capex boom). Forward estimates usually get revised down. | Shiller CAPE (multpl). Campbell & Shiller (1998), "Valuation Ratios and the Long-Run Stock Market Outlook," *J. Portfolio Management* 24(2):11–26, DOI 10.3905/jpm.24.2.11 (corrected; the DOI previously listed belongs to a different paper). |
| **"Valuation doesn't time markets"** | Starting valuation explains a meaningful share of *10-yr* returns but very little of 1-yr returns. | Vanguard VEMO, Oct 2026: valuations are "poor predictors of performance over the short or even intermediate term." GTM slide 6 (P/E vs. 1-yr and 5-yr returns). Asness, "An Old Friend: The Stock Market's Shiller P/E" (AQR white paper, 2012-11-01; also published in *J. Portfolio Management*) [title/date verified on aqr.com]. |
| **Cautionary research** | CAPE's long-run averages may be biased by accounting changes and lower rates (Siegel critique). Even so, high CAPE has historically been followed by below-average 10-yr real returns. | Siegel (2016), "The Shiller CAPE Ratio: A New Look," *Financial Analysts Journal* 72(3):41–50, DOI 10.2469/faj.v72.n3.1 [verified via OpenAlex]. |

> **Program implication.**
> - Show **both** forward P/E and CAPE with percentile bands and a one-line plain-English note: "Forward P/E depends on analyst forecasts. CAPE uses 10 years of actual inflation-adjusted earnings. They currently disagree because margins and earnings are unusually high."
> - Feed the institutional 10-yr expected-return *ranges* (Section 8) into the planning calculator. Do **not** use trailing 10-yr S&P returns (about 15% a year) as the default.
> - For a moderate-risk user, the app's default U.S.-equity planning assumption should be the median of the CMAs (about 5–7.5% nominal), with a stress case.

---

## 2. Index concentration, mega-caps and AI

### 2.1 The data (GTM 4Q26, as of 2026-09-30, unless noted)
- **Top-10 weight and earnings.** The top 10 make up **40.6%** of S&P 500 market cap and **36.1%** of earnings (slide 8). In Oct 2026 they were, by weight: Nvidia, Apple, Microsoft, Alphabet, Amazon, Broadcom, Meta, Micron, Tesla, AMD. Combined: 41%, $26.8tn (slide 9). [Ordering PARSE-UNCERTAIN; membership read from slide.]
- **Top-10 valuation.** Forward P/E of the top 10 is 20.9x, 100% of its own average. The remaining ~490 companies trade at 17.9x vs. a 16.0x average, 112% (slide 8). Concentration is high, but the mega-caps are **not** priced above their own history. The "rest of the market" is relatively more stretched against its history.
- **Mag 7 vs. the rest.**
  - 2026 YTD price return: Mag 7 12% vs. S&P 493 12%. Mag 7 contributed 34% of the index return, down from 63% in 2023 (slide 10).
  - FactSet (2026-11-03): "'Mag 7' Now Reporting Lower Earnings Growth Than the Other 493 S&P 500 Companies for Q3" (headline; figures not captured).
- **AI capex.**
  - Hyperscaler capex (Alphabet, Amazon, Meta, Microsoft, Oracle): $416bn in 2025 → **$799bn 2026F** → $1,093bn 2027F. That is growth of 92% in 2026F and 37% in 2027F (slide 22). [Values PARSE-UNCERTAIN on year alignment.]
  - J.P. Morgan Global Research projects about $5.5tn of cumulative 2026–2030 AI-buildout funding from mixed sources, including credit and private capital (GTM slide 57).
- **AI-linked industries.** These were 53.2% of the S&P 500 (hyperscalers, semis, hardware, power, software; slide 11) [PARSE-UNCERTAIN].
- **Sectors.** Technology is about 39.7% of S&P 500 weight [PARSE-UNCERTAIN] (slide 15).
- **Vanguard.** In Q3 2026, "AI-linked technology, growth, and large-cap U.S. stocks" kept outperforming, while value and small caps weakened (VEMO return forecasts page, published 2026-10-21).

### 2.2 Proponents vs. skeptics
- **Proponents (AI supercycle).**
  - Earnings are real: Q3 AI-linked earnings growth was extraordinary, and hyperscalers fund much of their capex from cash flow.
  - JPM's 2027 LTCMA sees AI as "a meaningful, durable uplift to productivity over 10 to 15 years," "most significant in the 2030s and beyond."
  - BlackRock (CMA, 2026-08-11) moved to overweight DM equities on AI-driven earnings upgrades. It includes an "AI productivity boom" scenario.
- **Skeptics.**
  - Capex is increasingly debt- and private-credit-funded (GTM slides 37 and 57).
  - The capex-to-revenue payback is unproven.
  - Concentration means a cap-weighted "diversified" index is about 40% in ten correlated names.
  - Historically, the largest companies at each decade's start (GTM slide 9: 1985, 1995, 2005, 2015 lists) have mostly not stayed on top.
  - Bessembinder (2018), "Do Stocks Outperform Treasury Bills?", *Journal of Financial Economics* 129(3):440–457, DOI 10.1016/j.jfineco.2018.06.004: a small minority of stocks generate most wealth creation. This supports owning the whole market rather than guessing winners, and it cuts both ways for concentration.
  - Infrastructure booms (railroads, telecom/fibre in 1999–2002) created useful assets but poor investor returns in the capex-heavy firms. [General historical claim; cite Ben Felix / Plain Bagel episodes on the dot-com analogy in the creator file. UNVERIFIED specific episode.]

> **Program implication.**
> - Add a **"Look-through concentration"** widget. For any ETF mix the user enters, show the effective weight in the top 10 U.S. stocks and in AI-linked industries. Example: VTI + QQQ + a "tech" ETF can push single-name exposure to Nvidia/Microsoft/Apple above 20%.
> - Warn when a single stock exceeds 10% of total portfolio exposure, or AI-linked exposure exceeds 1.5× its market weight.
> - Offer diversifiers neutrally: equal-weight (e.g., RSP), value and small-cap value, and international. Show their costs and history, not predictions.
> - AI-theme ETFs: show the theme-ETF evidence from Section 7.4.

---

## 3. Interest rates, Fed policy, inflation and bond yields

### 3.1 Fed policy
- **Path to today.**
  - Cuts in 2024 (decision dates): Sep 18 −50 bp, Nov 7 −25 bp, Dec 18 −25 bp.
  - Cuts in 2025 (decision dates): Sep 17, Oct 29, Dec 10, each −25 bp, reaching 3.50–3.75%. The open-market page lists the *effective* dates, one day later (Sep 18, Oct 30, Dec 11).
  - **Hike at the 15–16 Sep 2026 meeting** (+25 bp; decided 2026-09-16, effective 2026-09-17), to **3.75–4.00%**.
  - Source: https://www.federalreserve.gov/monetarypolicy/openmarket.htm. That page lists effective dates, which explains why it shows "Last Update: September 16, 2026" next to a Sep 17 entry. The statement is https://www.federalreserve.gov/newsevents/pressreleases/monetary20260916a.htm. Also corroborated by the Oct 28 statement range.
- **Oct 27–28, 2026 meeting.**
  - Rate held at 3.75–4.00%. Vote 10–2: Lisa Cook and Beth Hammack dissented in favour of a **25 bp hike**.
  - Statement language: "Inflation remains elevated," activity "expanding at a strong pace," and "The Committee will deliver price stability" (https://www.federalreserve.gov/newsevents/pressreleases/monetary20261028a.htm).
  - Next meeting: **Dec 8–9, 2026**, with a new SEP (https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm).
- **September 2026 SEP (via GTM slide 31).** 2026 Q4/Q4 headline PCE 3.7%, core PCE 3.4%, unemployment 4.1%. Inflation projected back to 2.3% (headline) in 2027. [Fed-funds dot medians PARSE-UNCERTAIN; read the SEP directly.]
- **JPM GTM 4Q26 key takeaway:** the Fed "could hike one more time in 2026."
- **Leadership.** The Fed Board page lists **Kevin Warsh, Chairman**. His bio page says he "took office as chairman ... on May 22, 2026," for a four-year term.

### 3.2 Treasury yields (U.S. Treasury par yield curve)
| Date | 3-mo | 2-yr | 10-yr | 30-yr |
|---|---|---|---|---|
| 2026-01-02 | 3.65% | 3.47% | 4.19% | 4.86% |
| 2026-09-30 | — | — | 5.29% (month high) | 5.64% |
| 2026-11-10 | 4.30% | 4.73% | **4.98%** | 5.35% (20-yr 5.39%) |

- **Real and breakeven.** 10-yr TIPS 2.66% (2026-11-09). 10-yr breakeven 2.20% (2026-11-10, FRED).
- **The breakeven puzzle.** The 2.20% breakeven is well below realized inflation of about 3.5–4%. Markets expect the oil shock to be transitory, and the EIA forecasts Brent easing to $86 in 2027.
- **Aggregate bonds.** Bloomberg U.S. Aggregate yield **5.56%** on 2026-09-30, vs. a 1976–present average of 6.17%. Real Agg yield 3.12%. GTM's regression implies a forward 5-yr Agg return of about 5.74% (slides 32 and 41). The Agg returned −2.91% YTD to Sep 30 as yields rose (slide 42).
- **Term premium.** BlackRock expects "a persistently higher term premium on long-term bonds," from competition for capital (governments, AI hyperscalers) plus inflation uncertainty (BlackRock CMA, 2026-08-11). Syzygy (formerly Research Affiliates) argues "Resurgent inflation, not bond vigilantes, is driving steeper sovereign yield curves" (C. Suvak, Oct 2026).

### 3.3 Inflation
- **October 2026 CPI** (released 2026-11-10): headline +0.4% m/m SA, core +0.3% (third straight +0.3%). Energy +0.7% after +5.2% in September. Gasoline +37.4% y/y.
- **No official 12-month figures.** BLS **cannot publish 12-month changes for October 2026** for most indexes, because October 2025 data were never collected during the 2025 government shutdown (https://www.bls.gov/news.release/cpi.nr0.htm).
- **Computed estimates.**
  - September 2026 y/y ≈ 3.7% from FRED CPIAUCSL (336.298 / 324.245).
  - October 2026 vs. an interpolated October 2025 gives ≈ 3.9–4.0%. [Own estimate; label "estimate" in the app.]
- **GTM slide 20:** headline CPI 3.4% and core CPI 2.4% for Aug '26; headline CPI peaked near 4.2% in May '26 [PARSE-UNCERTAIN]. The September 2026 CPI-U jump (+0.6% m/m) is consistent with ≈3.7%.
- **PCE:** 3.6% y/y (Sep 2026, BEA, released 2026-10-29). Next release 2026-11-25. Core PCE not captured.
- **Oil.** Brent 2026 average forecast $97/bbl vs. $69 in 2025. Drivers: "attacks on oil tankers in the Persian Gulf," depleted inventories and tight shipping. U.S. commercial crude stocks were 410 mn bbl, the lowest end-October level since 2014 (EIA STEO, 2026-11-10, https://www.eia.gov/outlooks/steo/). WTI peaked at $113 on 2026-04-07 and was $90 on 2026-09-30 (GTM slide 28).
- **Labor:** unemployment 4.1%, October payrolls +165k (BLS, 2026-11-06).

### 3.4 Proponents vs. skeptics (bonds and duration)
- **Pro-bonds.**
  - Starting yields are the best predictor of bond returns; GTM shows R² ≈ 89% for 5-yr Agg returns on starting yield.
  - Vanguard's 10-yr U.S. aggregate bond forecast is 5.1–6.1%, overlapping its U.S. equity range of 4.8–6.8% (VCMM, 2026-09-30 run).
  - Bonds now offer equity-like *expected* returns with lower volatility. A similar overlap last appeared in late 2023, so this is not unprecedented.
- **Skeptics.**
  - The stock-bond correlation turns positive when inflation is the dominant shock, as in 2022 and again in 2026. Bonds then diversify less: JPM LTCMA notes higher stock-bond correlation, and the Agg fell YTD alongside rising yields.
  - Long duration is exposed to fiscal or term-premium shocks.
  - Research: Ilmanen, *Expected Returns* (Wiley, 2011). AQR on the stock-bond correlation and inflation regimes, e.g. Brixton, Brooks, Hecht, Ilmanen, Maloney & McQuinn (2023), "A Changing Stock–Bond Correlation: Drivers and Implications," *J. Portfolio Management* 49(4):64–80, DOI 10.3905/jpm.2023.1.459 (corrected via OpenAlex; the previously listed …460 resolves to a different paper).

> **Program implication.**
> - **Savings and cash.** Cash is not "trash" in this regime: 3-mo T-bills yield about 4.3%. The savings module (Section 10) should rank HYSA vs. money-market fund vs. T-bills / SGOV vs. I bonds vs. CDs by after-tax yield.
> - **Bonds in the allocation.** In the 401(k)/IRA builder, explain *why* bonds belong even with big equity expectations: drawdown control for a moderate-risk user. Default to intermediate core bonds (BND/AGG-type) plus optional TIPS. Avoid long-duration bets.
> - **Inflation display.** Show y/y CPI with a footnote on the October 2025 data gap. Use FRED series, not hard-coded values.

---

## 4. International vs. U.S. valuations

### 4.1 The data
- **Returns (GTM slide 43, MSCI gross total return, USD).**
  - 2026 YTD to Sep 30: S&P 500 12.7%, ACWI ex-US 14.7%, EAFE 10.8%, EM 23.7%, Japan 23.0%, Taiwan 70.7%, Korea 98.3%, China −11.7%, India −14.7%.
  - 2025: S&P 500 17.9% vs. ACWI ex-US 33.1%.
  - 15-yr annualized: U.S. 15.7% vs. non-U.S. 8.7%.
  - International has outperformed for **about two years running**, after more than a decade of underperformance.
- **Valuations (GTM slide 46, forward P/E).** U.S. 19.0x (22.0x on Jan 1, 2026). Japan ≈16.1x, Eurozone ≈14.9x, ACWI ex-US ≈14.8x, EM ≈13.3x [PARSE-UNCERTAIN on current vs. 20-yr-average columns]. The ACWI ex-US discount to the S&P 500 reads as −32% and −20% for current vs. 20-yr average [PARSE-UNCERTAIN which is which]. Roughly a 20–30% discount either way.
- **Concentration abroad.** EM is also concentrated: top 10 ≈ 40% of the index (TSMC, Samsung, SK Hynix and others) (GTM slide 49).
- **Institutional views.**
  - JPM GTM 4Q26: international equities "attractive relative to the U.S. market"; U.S. large caps "expensive and overconcentrated."
  - Vanguard 10-yr: developed ex-U.S. 5.3–7.3% vs. U.S. 4.8–6.8%; EM 3.7–5.7%.
  - JPM 2027 LTCMA: DM forecasts now exceed EM.

### 4.2 Proponents vs. skeptics
- **Pro-international.**
  - Lower starting valuations, higher dividend yields, and diversification of currency and policy regime.
  - Global market-cap weighting is the theoretically neutral portfolio. U.S. home bias is a bet.
  - Ben Felix (PWL Capital) has argued this repeatedly, citing the Dimson–Marsh–Staunton *UBS Global Investment Returns Yearbook* and Anarkulova, Cederburg & O'Doherty (2022), "Stocks for the long run? Evidence from a broad sample of developed markets," *Journal of Financial Economics* 143(1):409–433, DOI 10.1016/j.jfineco.2021.06.040 (corrected; the previously listed …06.009 is an unrelated paper). That paper shows single-country stock-market disappointments are common, which supports global diversification.
- **Skeptics (U.S.-centric view; historically Jack Bogle, and frequently U.S.-focused YouTube creators).**
  - U.S. firms have higher margins, ROE and earnings growth (GTM slide 47). The valuation gap partly reflects sector mix (more tech in the U.S.).
  - S&P 500 companies already earn about 42% of sales abroad (GTM slide 15).
  - Counterpoint: GTM's sector-relative chart still shows ex-U.S. discounts in most sectors.

> **Program implication.**
> - The ETF builder should default to a **global market-cap reference**: about 64% U.S. / 36% ex-U.S. (U.S. share of MSCI ACWI ≈ 64% per GTM slide 43).
> - Show the user their deviation as an explicit "home-bias bet."
> - Present both sides with attributed creator views from the creator knowledge base.
> - Never present two years of international outperformance as a trend to chase.

---

## 5. Small-cap value spread and factor context

### 5.1 The data
- **Value vs. growth (GTM slide 12).** Russell 1000 Value trades at 16.9x forward vs. Growth at 22.2x. The value P/E discount is **−24% vs. a long-term average of −30%** (since 1997). Long-term average P/Es: Value 14.3x, Growth 21.5x.
  - In **large caps**, value is *not* unusually cheap relative to growth right now. Both trade above their own averages.
  - Slide 14 suggests large growth at about 135% of its 20-yr average P/E vs. large value near about 100–119% [PARSE-UNCERTAIN cell alignment].
- **Small caps (Russell 2000; GTM slide 13).** Lower interest coverage than large caps; a large share of companies unprofitable (≈ 38% [PARSE-UNCERTAIN]). Profitability screens matter a lot in small caps, which is why Avantis and Dimensional small-value funds screen for profitability as well as price.
- **10-yr annualized style returns to 2026-09-30** [PARSE-UNCERTAIN cells]: large growth ≈ 18% vs. large value ≈ 11%; small value ≈ 9–10%.
- **Recent fund data (StockAnalysis.com, 2026-11-10 close; confirm on issuer pages).**
  - **AVUV** (Avantis U.S. Small Cap Value): 1Y total return 26.65%, ER 0.25%, yield 1.24%, AUM $31.9bn.
  - **SCHD** (Schwab U.S. Dividend Equity): 1Y total return **38.79%**, yield 2.94%, ER 0.06%, AUM $120.2bn. Likely helped by energy and value exposure during the oil shock [interpretation, not verified attribution].
  - **SPY**: 1Y 12.97%.
  - A one-year snapshot like this is noise, not evidence.
- **Current AQR value-spread reading:** not retrieved [UNVERIFIED for Nov 2026].

### 5.2 Evidence base and debate
- **Factor premia.**
  - Fama & French (1993), "Common risk factors in the returns on stocks and bonds," *JFE* 33(1):3–56, DOI 10.1016/0304-405X(93)90023-5.
  - Fama & French (2015), "A five-factor asset pricing model," *JFE* 116(1):1–22, DOI 10.1016/j.jfineco.2014.10.010.
  - Novy-Marx (2013), "The other side of value: The gross profitability premium," *JFE* 108(1):1–28, DOI 10.1016/j.jfineco.2013.01.003.
  - Asness, Frazzini & Pedersen (2019), "Quality minus junk," *Review of Accounting Studies* 24:34–112, DOI 10.1007/s11142-018-9470-2.
- **Value's lost decade.**
  - Fama & French (2021), "The Value Premium," *Review of Asset Pricing Studies* 11(1):105–121, DOI 10.1093/rapstu/raaa021.
  - Arnott, Harvey, Kalesnik & Linnainmaa (2021), "Reports of Value's Death May Be Greatly Exaggerated," *Financial Analysts Journal* 77(1):44–67, DOI 10.1080/0015198X.2020.1842704.
  - Asness, "Is (Systematic) Value Investing Dead?" (AQR, Cliff's Perspectives, 2020-05-08) [verified on aqr.com]. This is practitioner commentary, not peer-reviewed.
- **Skeptics.**
  - Factor premia shrink after publication: McLean & Pontiff (2016), "Does Academic Research Destroy Stock Return Predictability?", *Journal of Finance* 71(1):5–32, DOI 10.1111/jofi.12365.
  - Many factors fail replication: Harvey, Liu & Zhu (2016), "…and the Cross-Section of Expected Returns," *Review of Financial Studies* 29(1):5–68, DOI 10.1093/rfs/hhv059.
  - A small-cap value tilt can lag the market for 10+ years (tracking-error regret).
- **Recent practitioner writing.** Syzygy Asset Management (formerly Research Affiliates) is publishing on combining value, quality and momentum and on value traps (Arnott, "Value Traps Are Painful … Read the Footnotes" [date UNVERIFIED]).

> **Program implication.**
> - For each factor ETF (AVUV, AVDV, DFAC, QUAL, VLUE, etc.), show:
>   - (a) the papers above;
>   - (b) the *expected premium net of fees*, which is modest and uncertain;
>   - (c) the longest historical stretch of underperformance vs. the market;
>   - (d) a "tracking-error tolerance" question for the user.
> - Size any tilt so the user could hold it through a decade of lagging, e.g. 10–30% of equity.
> - Show the GTM value/growth P/E discount with its quarter date as the live "spread" proxy.

---

## 6. Dividends and dividend growth (theme)

- **Data.** S&P 500 forward dividend yield 1.4% vs. a 2.0% 30-yr average (GTM slide 5). SCHD yield 2.94% (2026-11-10).
- **Proponents.** Dividend-growth channels (e.g., Joseph Carlson) argue for cash-flow discipline, "getting paid to wait," behavioural staying power and quality signalling.
- **Evidence and skeptics.**
  - Miller & Modigliani (1961), "Dividend Policy, Growth, and the Valuation of Shares," *Journal of Business* 34(4):411–433, DOI 10.1086/294442: dividends do not change total value absent frictions.
  - Hartzmark & Solomon (2019), "The Dividend Disconnect," *Journal of Finance* 74(5):2153–2199, DOI 10.1111/jofi.12785: investors mistakenly treat dividends as "free income."
  - Dividend-strategy returns are largely explained by value, profitability and investment factor exposures. Ben Felix has a well-known video on dividend irrelevance [title/date UNVERIFIED].
  - In taxable accounts, dividends create annual tax drag.

> **Program implication.**
> - Score dividend ETFs and stocks on total return and factor exposure, not yield.
> - Add a tax-location note: prefer IRA/401(k) for high-yield holdings.
> - Add a "yield trap" flag: yield above about 2× the sector median, or payout ratio above 80% of earnings (or above 100% of FCF).

---

## 7. Popular themes and products

### 7.1 Covered-call / option-income ETFs (JEPI, JEPQ, QYLD, single-stock "YieldMax"-type)
- **Data (StockAnalysis.com, 2026-11-10 close; confirm on the issuer site).**

  | ETF | Distribution yield | AUM | ER | 1Y total return | Since-inception annualized |
  |---|---|---|---|---|---|
  | JEPI | 7.92% | $45.2bn | 0.35% | 10.62% | 11.31% (May 2020–) |
  | JEPQ | 12.45% | $40.15bn | 0.35% | 8.84% | 14.52% (May 2022–) |
  | SPY (reference) | ≈1.0% trailing | — | — | 12.97% | — |

  5-yr annualized total return: **JEPI 7.63% vs. SPY 11.40%** (StockAnalysis comparison page).
- **Proponents.** High monthly income, lower volatility and beta, and income-investor appeal.
- **Evidence and skeptics.**
  - Covered calls sell the right tail. You give up much of the upside in rallies while keeping most of the downside. Distributions largely repackage option premium. They are not "extra" return.
  - Israelov & Nielsen (2015), "Covered Calls Uncovered," *Financial Analysts Journal* 71(6):44–57, DOI 10.2469/faj.v71.n6.1 (corrected via OpenAlex; …n6.6 does not resolve): a covered call equals long equity + short volatility + an uncompensated negative equity-timing exposure.
  - Distributions from ELN-based funds such as JEPI/JEPQ are largely taxed as ordinary income [verify per the fund's annual tax notice].
  - Ben Felix and The Plain Bagel have published critiques of yield-chasing [specific episodes UNVERIFIED; link from the creator file].

> **Program implication (mandatory warnings).**
> 1. Never sort or rank funds by yield. Always show total return vs. the underlying index (SPY/QQQ) for 1, 3 and 5 years.
> 2. Show a "Yield ≠ return" badge on any fund yielding above 6%.
> 3. Show an upside-cap illustration: fund vs. underlying in the underlying's best 12-month window.
> 4. Add a tax note: inefficient in taxable accounts.
> 5. For this user (10+ yr accumulator), label these "not aligned with accumulation goal."
> 6. Show a red warning for single-stock option-income ETFs (very high yields, NAV erosion, return of capital).

### 7.2 Buffered / defined-outcome ETFs
- **Data.** Category AUM for 2026 not retrieved [UNVERIFIED; check Innovator, First Trust and Morningstar].
- **Proponents.** A downside buffer (e.g., the first 10–15% of losses over a 12-month outcome period) in exchange for an upside cap. Appeals to drawdown-averse investors, which is relevant to this user.
- **Skeptics.**
  - Price-return based, so you lose dividends.
  - Caps can give up a lot of upside, and fees are higher than plain index funds (typically about 0.75–0.80% [UNVERIFIED]).
  - Buying mid-period changes the effective buffer and cap.
  - The same drawdown profile can often be approximated more cheaply with a lower stock allocation plus T-bills or bonds. Israelov, "Pathetic Protection: The Elusive Benefits of Protective Puts," *Journal of Alternative Investments* 21(3):6–33 (2018/19), DOI 10.3905/jai.2018.1.066. The 2017 working paper is SSRN 2934538. The paper is about protective puts, so applying it to buffer ETFs is an analogy, not a direct test.

> **Program implication.** Include a calculator comparing a buffer ETF with a "simple equivalent" (e.g., 70% equity ETF + 30% T-bills). Show the remaining cap and buffer for the current outcome period, and warn about mid-period entry.

### 7.3 Active ETFs
- **Trend.** Active ETFs have taken a growing share of launches and flows since SEC Rule 6c-11 (2019) and mutual-fund-to-ETF conversions [2026 share-of-flows figures UNVERIFIED]. Vanguard's Oct 2026 commentary argues for active managers as AI moves "from hype to economic reality."
- **Evidence.**
  - S&P SPIVA scorecards consistently show most active U.S. large-cap funds underperform over 10–15 years (https://www.spglobal.com/spdji/en/research-insights/spiva/) [2026 figures UNVERIFIED].
  - Fama & French (2010), "Luck versus Skill in the Cross-Section of Mutual Fund Returns," *Journal of Finance* 65(5):1915–1947, DOI 10.1111/j.1540-6261.2010.01598.x.

> **Program implication.** Flag "active." Show the ER gap vs. the cheapest index equivalent and the 10-yr compounded cost on $10k. Link SPIVA.

### 7.4 Thematic ETFs (AI, defense, etc.)
- Ben-David, Franzoni, Kim & Moussawi (2023), "Competition for Attention in the ETF Space," *Review of Financial Studies* 36(3):987–1042, DOI 10.1093/rfs/hhac048: specialized and thematic ETFs tend to launch after hot performance and then underperform. Per the abstract, "specialized ETFs lose about 30% (risk-adjusted)" in their first five years, roughly −7% a year. The authors attribute this to overvalued holdings at launch, not fees.
- Current themes: Goldman Sachs, "The Security Supercycle: Investing in Innovation, Resilience, and Rearmament" (2026-10-29). GTM slide 48 shows Asian semiconductors and Europe aerospace & defense as the top themes since Oct 2022.

> **Program implication.** Show a thematic warning citing Ben-David et al., overlap with existing holdings, and a suggested cap of ≤5% of the portfolio.

### 7.5 Crypto ETFs
- **Data.**
  - IBIT: AUM ≈ $58.3bn (2026-11-10). Fee 0.25%. NAV YTD −14.57% (to 2026-11-09). 1-yr NAV −26.80% and since-inception +26.42% annualized, both to 2026-09-30 (https://www.ishares.com/us/products/333011/ishares-bitcoin-trust-etf).
  - Bitcoin ≈ $73,455, about 42% below its $126,080 ATH (CoinGecko, 2026-11-10; ATH date ≈ 6 Oct 2025 [from memory; CoinGecko page did not show the date; UNVERIFIED]).
  - JPM 2027 LTCMA publishes a bitcoin return range for the first time but excludes it from the main matrices.
- **Proponents.** Scarcity, adoption and ETF accessibility. Some creators (e.g., Andrei Jikh) discuss small allocations [specific videos UNVERIFIED].
- **Skeptics.** No cash flows to anchor expected returns. Repeated 50–80% drawdowns. Liu & Tsyvinski (2021), "Risks and Returns of Cryptocurrency," *Review of Financial Studies* 34(6):2689–2727, DOI 10.1093/rfs/hhaa113: crypto returns are driven by crypto-specific momentum and attention, not stock factors.

> **Program implication.** Treat crypto as a "speculative satellite": default 0%, opt-in cap of ≤5% of investable assets, and always show the max drawdown. Exclude it from the core builder.

### 7.6 Private credit, nontraded BDCs, interval funds
- **Data.**
  - GTM slide 56 (Preqin): global private credit AUM in the trillions (as of 3Q25).
  - JPM 2027 LTCMA: 30% in diversified alternatives lifts balanced returns to 7.5% and Sharpe by 21%. Note that JPM sells alternatives.
  - BlackRock (2026-08-11) prefers "selected private credit" over duration and downgraded public HY to underweight on tight spreads (HY OAS 3.34%, 2026-11-09).
- **Skeptics.**
  - Fed FSR (May 2026): firms on floating-rate debt, including "leveraged loans and private credit," showed weaker debt service, and private credit was a top-cited risk. **Some nontraded BDCs saw rising redemption requests "because of asset-quality concerns," and several limited redemptions.**
  - High fees and gated liquidity. Appraisal-based marks understate volatility ("volatility laundering," a term popularized by AQR's Cliff Asness [citation UNVERIFIED]).
  - Private credit is part of the AI-capex funding chain (GTM slide 57).

> **Program implication.** Label these "not recommended by default" for this user. Explain gates, and show fees vs. a public bond ETF. Never show a yield without default-loss and liquidity context.

---

## 8. Institutional long-term capital market assumptions (CMAs) and outlooks

All figures are nominal USD, annualized and before fees and taxes unless noted. CMAs are **model outputs with wide error bands**, not promises. Note each publication date.

| Institution | Publication / as-of | U.S. equity | Dev. ex-U.S. equity | EM equity | U.S. bonds | Other notes |
|---|---|---|---|---|---|---|
| **Vanguard** (VCMM, 10-yr) | Published 2026-10-21; model run 2026-09-30 | **4.8–6.8%** (prior 4.2–6.2%) | **5.3–7.3%** | 3.7–5.7% | U.S. Agg **5.1–6.1%** | Global ex-U.S. bonds (hedged) 5.2–6.2%; U.S. HY 5.4–6.4%. U.S. "remain highly valued"; higher long rates a "growing discount-rate headwind." https://corporate.vanguard.com/content/corporatesite/us/en/corp/vemo/vemo-return-forecasts.html |
| **J.P. Morgan AM** (2027 LTCMA, 31st ed., 10–15 yr) | Data as of Sep 2026; published ≈ Oct 2026 [exact date UNVERIFIED] | U.S. large cap **7.5%** (+80 bp; margins >15%) | Above EM (figure not captured) | Below DM | Global agg bonds **5.3%** (vs. 2.1% in 2021; "highest in two decades") | Global 60/40-type balanced **7.0%**; U.S. real GDP 2.1%; AI as durable productivity uplift; bitcoin range shown, excluded from matrix. https://am.jpmorgan.com/us/en/asset-management/adv/insights/portfolio-insights/ltcma/ |
| **J.P. Morgan GTM** (quarterly) | 4Q26, data as of 2026-09-30 | Fwd P/E 19.0x; "expensive and overconcentrated" | "attractive relative to U.S." | — | Agg yield 5.56% → implied 5-yr ≈ 5.74% | Fed "could hike one more time in 2026"; neutral bonds, "without duration or credit bets." |
| **BlackRock** (BII CMAs; 5-yr main, 10-yr strategic) | Published 2026-08-11, data 2026-06-30 | Figures in downloadable data [UNVERIFIED numbers] | Overweight DM | Overweight EM | Prefer private credit over long govt/IG duration; HY underweight | Scenarios: base, "AI productivity boom," "Global risk premia rise" (stagflationary). https://www.blackrock.com/institutions/en-us/insights/charts/capital-market-assumptions |
| **AQR** (5–10 yr, **real**) | Published 2026-01-14, as of 2025-12-31 | Not captured | Not captured | Not captured | Not captured | **Global 60/40 expected real return 3.4%**, about 1.5 pp above the 2021 low, below the ~5% U.S. long-run average since 1900; "risk premia are compressed." Special topic: currency hedging. https://www.aqr.com/Insights/Research/Alternative-Thinking/2026-Capital-Market-Assumptions-for-Major-Asset-Classes |
| **Research Affiliates → Syzygy Asset Management** | Ongoing (AAI tool) | Not retrieved (dynamic tool) | — | — | — | Research Affiliates renamed its advisory business **Syzygy Asset Management**. The RAFI index business was sold to **VettaFi (TMX)** (researchaffiliates.com landing page, 2026) [sale date UNVERIFIED]. AAI tool: https://interactive.syzygyassetmanagement.com/asset-allocation |
| **Morningstar** | — | [UNVERIFIED: site returned 403] | — | — | — | Check the "Markets Outlook" and price/fair-value data at morningstar.com. |
| **Goldman Sachs** | Insights through 2026-10-30 | S&P 500 targets / 10-yr forecast **[UNVERIFIED]** | — | — | Pieces: "Are Bonds Becoming a Better Investment for Portfolios?" (2026-10-08); "Will 30-Year US Treasury Yields Climb Higher?" (2026-10-06) | Theme: "The Security Supercycle" (2026-10-29). https://www.goldmansachs.com/insights |

**Where they agree (Nov 2026).**
1. Expected U.S. equity returns are positive but **below the trailing 10–15 years**: Vanguard 4.8–6.8% vs. a trailing 10-yr S&P 500 total return of about 15%.
2. **Bonds are much more attractive than in 2020–2021.** Vanguard has bonds roughly at parity with U.S. equities.
3. **Non-U.S. developed equities have equal or higher expected returns** than U.S. in Vanguard's model and per GTM commentary.
4. **Inflation and the term premium are the key risks.**

**Where they differ.**
- JPM (7.5% U.S. large cap) is notably more optimistic than Vanguard (4.8–6.8%). JPM puts more weight on persistent high margins and AI productivity.
- JPM and BlackRock promote alternatives and private credit. Vanguard and AQR emphasise public-market diversification and compressed premia.

> **Program implication.**
> - Store CMAs as structured data, e.g. `cma.json` with fields: institution, as_of, published, horizon, asset_class, low, high, point, nominal/real, url.
> - The planner shows a "consensus band" (min–max across institutions) and lets the user pick conservative / median / optimistic.
> - **Default for a moderate-risk user: the conservative end.**
> - Show the publication date and a "stale after 12 months" flag.

---

## 9. "How to refresh this" checklist (free sources)

Use **R** (fetch automatically via API or CSV) or **L** (link only, manual review). Cadence is the recommended refresh interval. The app should mark a value stale after 2× cadence.

### 9.1 Automatable (free APIs / CSV)
| Item | Source | Endpoint / series | Cadence |
|---|---|---|---|
| Treasury yields (3m, 2y, 10y, 30y) | U.S. Treasury | https://home.treasury.gov/resource-center/data-chart-center/interest-rates/TextView?type=daily_treasury_yield_curve (CSV/XML feeds available) or FRED `DGS3MO`, `DGS2`, `DGS10`, `DGS30` | Daily (R) |
| Real yield / breakeven | FRED | `DFII10`, `T10YIE`, `T5YIFR` | Daily (R) |
| Fed funds target | FRED | `DFEDTARU`, `DFEDTARL`, `EFFR` | After each FOMC (R) |
| CPI | FRED / BLS API | `CPIAUCSL`, `CPILFESL`; BLS API v2 https://api.bls.gov/publicAPI/v2/timeseries/data/ (free key) | Monthly (R). **Handle the missing Oct 2025 value**: compute y/y only when both months exist; otherwise show "n/a (2025 shutdown gap)." |
| PCE / core PCE | FRED | `PCEPI`, `PCEPILFE` | Monthly (R) |
| Unemployment, payrolls | FRED | `UNRATE`, `PAYEMS` | Monthly (R) |
| HY / IG spreads | FRED | `BAMLH0A0HYM2`, `BAMLC0A0CM` | Daily (R) |
| Oil | FRED / EIA | `DCOILBRENTEU`, `DCOILWTICO`; EIA API v2 (free key) | Daily (R) |
| Recession / financial-conditions context | FRED | `NFCI`, `T10Y2Y`, `T10Y3M` | Weekly (R) |
| CAPE | Shiller data (Yale) | http://www.econ.yale.edu/~shiller/data.htm (ie_data.xls); multpl.com/shiller-pe as display check | Monthly (R; parse XLS) |
| ETF prices / total return / yield | yfinance (unofficial, free) | `yfinance.Ticker("JEPI").history(auto_adjust=True)`. Compute total return yourself; trailing yield = sum of last-12-month distributions / price | Daily (R). Treat as best-effort; may break. |
| Fama-French factors (value spread, SMB/HML returns) | Ken French Data Library | https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html (CSV zips) | Monthly (R) |
| AQR factor data (HML Devil, QMJ, BAB) | AQR datasets | https://www.aqr.com/Insights/Datasets | Monthly (R) |
| I bond rates | TreasuryDirect | https://www.treasurydirect.gov/savings-bonds/i-bonds/i-bonds-interest-rates/ | Each May 1 / Nov 1 (scrape or L) |
| FDIC national avg rates | FDIC | https://www.fdic.gov/national-rates-and-rate-caps | Monthly, third Monday (L/R) |
| T-bill auction results | TreasuryDirect / Fiscal Data API | https://api.fiscaldata.treasury.gov/services/api/fiscal_service/v1/accounting/od/auctions_query | Weekly (R) |
| Fund holdings (look-through concentration) | Issuer CSVs (iShares, Vanguard, SPDR, Schwab, Avantis) | e.g., iShares "Detailed Holdings" CSV link on each product page; SEC N-PORT via EDGAR | Monthly / quarterly (R) |

### 9.2 Link-and-review (manual, publication-driven)
| Item | URL | Cadence |
|---|---|---|
| FactSet *Earnings Insight* (forward P/E, EPS growth, Mag 7 vs. 493) | https://insight.factset.com/topic/earnings | Weekly (L) |
| J.P. Morgan *Guide to the Markets* (U.S.) PDF | https://am.jpmorgan.com/us/en/asset-management/adv/insights/market-insights/guide-to-the-markets/ | Quarterly (L; PDF text extractable with `pdftotext -layout`; values may be misaligned, so check manually) |
| J.P. Morgan LTCMA | https://am.jpmorgan.com/us/en/asset-management/adv/insights/portfolio-insights/ltcma/ | Annually (≈ Sep–Oct) |
| Vanguard VEMO return forecasts | https://corporate.vanguard.com/content/corporatesite/us/en/corp/vemo/vemo-return-forecasts.html | Quarterly |
| BlackRock CMAs | https://www.blackrock.com/institutions/en-us/insights/charts/capital-market-assumptions | Quarterly |
| AQR Capital Market Assumptions | https://www.aqr.com/Insights/Research/Alternative-Thinking | Annually (January) |
| Syzygy (ex-Research Affiliates) Asset Allocation Interactive | https://interactive.syzygyassetmanagement.com/asset-allocation | Monthly |
| Morningstar market fair value / outlooks | https://www.morningstar.com/markets | Quarterly |
| Goldman Sachs insights / outlooks | https://www.goldmansachs.com/insights | Ad hoc |
| FOMC statements, SEP, minutes | https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm | 8×/yr (next: **2026-12-08/09**, with SEP) |
| Fed Financial Stability Report | https://www.federalreserve.gov/publications/financial-stability-report.htm | Semiannual (≈ May / Nov) |
| EIA Short-Term Energy Outlook | https://www.eia.gov/outlooks/steo/ | Monthly |
| BLS release schedule (CPI next: **2026-12-10**; jobs next: **2026-12-04**) | https://www.bls.gov/schedule/news_release/ | Monthly |
| BEA PCE (next: **2026-11-25**) | https://www.bea.gov/data/personal-consumption-expenditures-price-index | Monthly |
| SPIVA scorecards (active vs. passive) | https://www.spglobal.com/spdji/en/research-insights/spiva/ | Semiannual |
| Issuer pages for JEPI/JEPQ/SCHD/AVUV/IBIT etc. (official yield, SEC yield, ER, distributions' tax character) | e.g., https://www.ishares.com/us/products/333011/ishares-bitcoin-trust-etf | Monthly |
| Creator channels (Ben Felix, The Plain Bagel, Whiteboard Finance, Andrei Jikh, Graham Stephan, Sven Carlin, Joseph Carlson, Mark Tilbury) | YouTube channel RSS: `https://www.youtube.com/feeds/videos.xml?channel_id=<ID>` (free, no key) | Weekly (R: titles/dates only; human or LLM summary optional) |

### 9.3 Refresh procedure (for maintainer or optional LLM)
1. Run `scripts/refresh_market_data.py`. It pulls all "R" series into `data/market_snapshot.json` with `as_of` per field.
2. Open each "L" source whose cadence has elapsed. Update `knowledge/market_context/*.json` (CMAs, GTM stats, FactSet P/E) with value, as_of, published, URL.
3. Diff old vs. new. If any **regime indicator crosses a percentile band** (e.g., CAPE >90th pct, 10-yr real yield >2.5%, HY OAS <3% or >6%, CPI y/y >4%), regenerate the narrative paragraph for Section 0. This can be done by an LLM given the JSON plus this template; without an LLM, use templated sentences.
4. Re-verify every figure that has a [PARSE-UNCERTAIN] or [UNVERIFIED] tag in this document.
5. Bump `last_reviewed` and show it in the app footer.

> **Program implication.** The app should never display a market number without its `as_of`, and should colour stale values amber or red. The narrative text (this file) is versioned. The numbers come from the snapshot JSON so they can be refreshed without rewriting prose.

---

## 10. Savings context for the ~$20K (rates as of Nov 2026)

The full savings framework lives in a separate knowledge file. These are the market-dependent inputs:

| Vehicle | Current reference rate | As of | Notes |
|---|---|---|---|
| Big-bank savings (national average) | **0.38%** | FDIC, 2026-10-19 | Avoid for emergency fund; opportunity cost ≈ 3.5–4 pp. |
| FDIC rate cap (proxy for competitive deposit rates) | 4.63% (savings/MM) | FDIC, 2026-10-19 | The cap is the greater of the national rate + 75 bp or the fed funds rate (EFFR 3.88%) + 75 bp; here it is 4.63%. It is a regulatory ceiling for less-than-well-capitalized banks, not a market rate. Top online HYSAs are likely ≈ 3.75–4.5% [UNVERIFIED; check bankrate/depositaccounts]. FDIC insurance covers $250k per depositor, per bank, per ownership category. Confirm that a fintech "HYSA" places funds at an FDIC-insured partner bank. |
| 3-month T-bill | **4.30%** | Treasury, 2026-11-10 | State-tax exempt. Buy via TreasuryDirect or a brokerage. |
| 0–3 mo T-bill ETF (SGOV) | **3.85% 30-day SEC yield** (2026-11-09); 3.63% 12-mo trailing; ER 0.09%; AUM ≈ $116.5bn | iShares product page | The SEC yield is the better forward-looking number. Both lag the 4.30% 3-mo bill because rates rose recently. Distributions are federal-taxable. Most are usually state-tax-exempt as U.S. Treasury interest; confirm the fund's annual state-tax percentage. Not FDIC-insured. |
| 12-month Treasury | 4.54% | FDIC calc input (end-Sep 2026) | CD alternative; state-tax exempt. |
| National avg 12-mo CD | 1.76% (cap 6.20% = 120% × 12-mo Treasury 4.54% + 75 bp) | FDIC, 2026-10-19 | Shop online banks / brokered CDs. CD interest is state-taxable, unlike Treasuries. |
| **I bonds** | **5.51% composite; 1.40% fixed** | TreasuryDirect, issued 2026-11-01 to 2027-04-30 | $10k/person/yr electronic limit; 12-month lockup; 3-month interest penalty if redeemed before 5 years. With realized inflation ≈ 3.5–4% and a 1.40% real floor, these are unusually attractive for the *non-emergency* portion of cash. Not for the first months of the emergency fund because of the 12-month lock. |
| 10-yr TIPS (real) | 2.66% | FRED, 2026-11-09 | For longer-horizon safe money; best held in IRA. |

> **Program implication.** The savings module takes **monthly expenses** as user input and computes the emergency fund target (3–6 months; 6+ if income is volatile). It then allocates in this order:
> 1. Emergency fund → HYSA or government money-market fund (same-day liquidity).
> 2. Remainder → a T-bill ladder, I bonds (≤$10k, 12-month lock) or investing, depending on the user's goals and timeline.
>
> It refreshes all rates from Section 9 sources and shows after-tax yield by the user's state tax rate, since T-bills and I bonds are state-tax exempt.

### 10.1 Account and contribution facts a young U.S. investor needs (added in fact-check pass)
| Item | 2026 value | Source / status |
|---|---|---|
| 401(k)/403(b)/457 employee deferral limit | **$24,500** | IRS COLA page (verified 2026-11-10) |
| 401(k) catch-up (age 50+) / ages 60–63 | $8,000 / $11,250 | IRS COLA page (verified) |
| IRA (Traditional + Roth combined) limit | **$7,500** (+$1,100 catch-up at 50+) | IRS COLA page (verified) |
| **2027 limits** | Not yet posted. The IRS COLA page covers 2023–2026 only. IR-2026-133 (2026-11-05) covered 2027 tax brackets and the standard deduction, not plan limits. | Re-check irs.gov in Nov 2026 |
| Roth IRA income phase-outs (2026) | ≈ $153k–$168k single; $242k–$252k MFJ | [UNVERIFIED in this pass; from IRS Notice 2025-67 as remembered; confirm] |
| HSA limits (2026) | ≈ $4,400 self-only / $8,750 family | [UNVERIFIED in this pass; IRS Rev. Proc. 2025-19 as remembered; confirm] |

- **Typical priority order** (the app should show it as a default the user can override, not as advice): capture the full employer match → build the emergency fund → pay off high-interest debt (above ~8%) → HSA if eligible → IRA (Roth vs. Traditional by current vs. expected future tax rate) → more 401(k) → taxable brokerage.
- **I bonds tax note.** Interest is federally taxable, but tax can be deferred until redemption. It is state/local-tax exempt. I bonds can only be bought at TreasuryDirect, not at a brokerage.
- **Money-market note.** Government and Treasury money-market funds are not FDIC-insured, though they are low risk. State-tax exemption depends on the fund's share of U.S. Treasury income. Funds holding mainly repo get little or no state exemption.

---

## 11. Key risks to monitor (what would change this picture)
1. **Earnings revisions.** The P/E compression assumes forward EPS holds. A margin reversal (oil normalising, AI capex digestion) would raise the effective P/E quickly. Watch FactSet weekly.
2. **Further Fed hikes.** The Dec 8–9 SEP is the next marker. Two members already voted to hike in October.
3. **Long-end yields and term premium.** The 10-yr hit 5.29% on Sep 30. A move above that would pressure both stocks and bonds (positive correlation).
4. **Middle East and oil.** EIA assumes "most Middle East production outside Iran" returns to pre-conflict averages by mid-2027. It forecasts Brent averaging $86 in 2027.
5. **Private credit and nontraded BDC redemptions** (Fed FSR, May 2026). As of 2026-11-10 the November 2026 FSR had not been posted; the latest is dated 2026-05-08. The prior November edition was published 2025-11-07, so expect one in roughly mid-November.
6. **Concentration unwind.** About 40% of the index sits in 10 names.

---

## 12. Verification log and limitations
- **Verified by direct fetch on 2026-11-10:**
  - Fed statement, open-market history, FOMC calendar and Board list.
  - Treasury yields (Jan, Sep and Nov 2026 pages).
  - FRED series: DFII10, T10YIE, BAMLH0A0HYM2, CPIAUCSL.
  - BLS CPI and jobs releases; BEA PCE; EIA STEO; FDIC rates; TreasuryDirect I bonds.
  - FactSet Nov 6 and Nov 9 articles.
  - JPM LTCMA landing page and GTM 4Q26 PDF (text-extracted).
  - Vanguard VCMM page; BlackRock CMA page; AQR CMA page (partial); Syzygy/Research Affiliates landing pages.
  - iShares IBIT page; StockAnalysis.com pages for JEPI, JEPQ, SCHD, AVUV, SGOV and JEPI-vs-SPY; CoinGecko BTC.
- **Not available:** the WebSearch tool (provider error). Morningstar (HTTP 403) and Goldman Sachs targets were not retrievable. ICI/etf.com industry statistics (404/403) were also not retrievable, so buffered-ETF, active-ETF and covered-call category AUM totals are **[UNVERIFIED]**.
- **Academic citations.** All DOIs in this file were checked against OpenAlex or doi.org in the 2026-11-10 fact-check pass. Three were wrong and have been corrected: Campbell & Shiller 1998, Anarkulova et al. 2022, and Brixton et al. 2023. The Israelov & Nielsen 2015 DOI was also corrected. `scripts/verify_citations.py` should still resolve each DOI at build time.
- **Creator attributions** in this file are generic. Specific video titles and dates belong in the creator knowledge file, where each must be verified.
- **Disclaimer for app display:** "Educational research summary, not individualized investment advice. Figures are as of the dates shown and will change."
