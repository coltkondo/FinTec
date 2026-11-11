# Academic Evidence Brief: Index ETFs vs. Individual Stocks for a Young US Investor

**Prepared:** 2026-11-10
**Purpose:** The evidence base behind the Investment Research Assistant's scoring weights, warnings, and default allocations. Each section has: **Finding**, **Strength of evidence**, **Caveats**, and **Program rule/implication**.
**Audience profile:** US-based, 10+ year horizon, moderate risk tolerance, mostly ETFs today; has a 401(k) with match, an IRA, and a taxable brokerage account; about $20K in cash.

> **Not financial advice.** This document summarizes published research to support the user's own decisions. It does not recommend specific securities.

---

## 0. Verification status (read first)

Live web search **was not available** in the session that produced this brief (the search tool returned "unsupported provider", and most publisher sites such as SSRN, ScienceDirect, S&P, and Morningstar returned HTTP 403). As a result:

- **Verified this session against a primary or near-primary source:**
  - Sharpe (1991): citation and argument, from Sharpe's Stanford page.
  - Barber & Odean (2000): abstract figures, from the authors' PDF.
  - Fama-French 1992, 1993, and 2015 citations and DOIs, from a Wikipedia summary that cites the papers.
  - Vanguard 2012 DCA "about two-thirds" finding, from a Wikipedia summary that cites the paper.
- **Fact-check pass (2026-11-10)** confirmed these against abstract/bibliographic records (IDEAS/RePEc, NBER, Oxford Academic, IRS, Federal Reserve, multpl.com):
  - Bibliographic details for Fama & French 2010, Carhart 1997, French 2008, Bessembinder 2018, Bessembinder et al. 2023, Novy-Marx 2013, Asness-Frazzini-Pedersen 2019, Asness et al. 2018, Frazzini & Pedersen 2014, Ang et al. 2006, Baker-Bradley-Wurgler 2011, Daniel & Moskowitz 2016, Harvey-Liu-Zhu 2016, Jensen-Kelly-Pedersen 2023, Hartzmark & Solomon 2019, Barber & Odean 2001, Barber-Lee-Liu-Odean 2009, Anarkulova et al. 2022, Arnott et al. 2021, Asness-Israelov-Liew 2011, Siegel 2016, Welch & Goyal 2008, Domian et al. 2007, Gjertson 2016, Miller & Modigliani 1961, McLean & Pontiff 2016 (volume/pages only), Pedersen 2018.
  - Numeric claims: Hou-Xue-Zhang 65%/82%; Bessembinder et al. 2.4% / $75.7T; Anarkulova 12%; Barber-Odean 2001 45%; Barber-Lee-Liu-Odean 3.8pp; French 0.67%; Fed SHED 63%; CAPE ~39.7 (Nov 10, 2026); 2026 IRS contribution limits.
  - **Corrections made:** Pedersen (2018) title; Hartzmark & Solomon DOI; Welch & Goyal author order; QMJ quality definition; Gjertson dollar thresholds (not in that paper); the t-stat rule-of-thumb assumption; the Vanguard 2016 paper title.
- **Everything else** comes from the author's (Claude's) knowledge of the literature. Bibliographic details (authors, year, journal, volume, DOI) for the classic papers are well established and very likely correct. They are **not marked** individually, but they should be spot-checked with the DOI links before release.
- **Specific numbers that are recent, or that I recall with less confidence**, are marked **[UNVERIFIED]**. The app must not show any [UNVERIFIED] figure as fact. It should fetch or refresh those figures (SPIVA, Morningstar Mind the Gap, CAPE level, savings rates) from the live source, or label them "approximate, as of <date>."
- **TODO before shipping the knowledge base:** run a verification pass with working web access. Resolve every DOI and replace each [UNVERIFIED] figure with a sourced number.

---

## 1. Market efficiency and active vs. passive

### 1.1 Sharpe, "The Arithmetic of Active Management" (1991)
- **Citation:** Sharpe, W. F. (1991). The Arithmetic of Active Management. *Financial Analysts Journal*, 47(1), 7-9. https://web.stanford.edu/~wfsharpe/art/active/active.htm (verified). DOI 10.2469/faj.v47.n1.7
- **Finding:** Passive investors together hold the market, so they earn the market return before costs. Active investors together also hold the market, so their *average* dollar also earns the market return before costs. Active management costs more, so the average actively managed dollar **must** underperform the average passively managed dollar *after costs*. This is an accounting identity, not an empirical estimate.
- **Strength:** **Very strong** (it is logically necessary given the definitions).
- **Caveats:**
  - It applies to the average active dollar, not to every manager.
  - "Passive" in the real world is not perfectly passive: index reconstitution, IPOs, and share issuance all create trading. Pedersen (2018), "Sharpening the Arithmetic of Active Management," *Financial Analysts Journal* 74(1), 21-36 (verified), argues this leaves room for some active value-add.
  - It does not show that *you* cannot pick winners. It shows that the average stock picker, after costs, loses to the index.
- **Program rule/implication:**
  - Default core recommendation = low-cost, broad-market index ETF.
  - Any individual-stock or active-fund screen shows a persistent banner: *"On average, active selection underperforms the index after costs (Sharpe 1991). Treat picks as a satellite allocation."*

### 1.2 SPIVA scorecards (S&P Dow Jones Indices)
- **Source:** S&P Dow Jones Indices, *SPIVA U.S. Scorecard* (published twice a year, year-end and mid-year). https://www.spglobal.com/spdji/en/research-insights/spiva/
- **Finding:**
  - Over long horizons, most active US equity funds underperform their benchmarks. A typical recent long-horizon reading is roughly **85-90%+ of US large-cap funds trailing the S&P 500 over 10-20 years** [UNVERIFIED: exact current figures].
  - In single years, the underperforming share usually falls between about 55% and 80%. For calendar 2024, my recollection is about 65% of large-cap funds [UNVERIFIED].
  - SPIVA also corrects for survivorship bias. A large share of funds are merged or liquidated within 10-20 years, which makes the "pure" fund-database averages look better than reality.
  - The companion *Persistence Scorecard* shows that top-quartile funds rarely stay in the top quartile. The persistence observed is close to, or below, what random chance would produce.
- **Strength:** **Strong.** The data run for 20+ years, are adjusted for survivorship, and hold across categories (large, mid, small, international, bond). The finding replicates across regions (SPIVA Europe, Canada, Japan, and others).
- **Caveats:**
  - The results depend on which benchmark each fund is assigned.
  - Some categories show weaker underperformance in certain years, for example small-cap and some fixed-income categories.
  - SPIVA counts funds equally (with an asset-weighted variant also published), and it is fund-level, not investor-level.
  - The 2025 and mid-2026 figures were not checked here.
- **Program rule/implication:**
  - Maintain a `spiva_latest.json` data file (category, horizon, % underperforming, report date) that the user refreshes by hand, or that the app scrapes if allowed.
  - Show it on the "ETF vs. stock-picking" explainer page.
  - When a user marks an active mutual fund or active ETF as a candidate, show the matching SPIVA category figure.
  - Default scoring penalty for active funds with no **≥10-year** record of net-of-fee outperformance.

### 1.3 Fama & French (2010), "Luck versus Skill in the Cross-Section of Mutual Fund Returns"
- **Citation:** Fama, E. F., & French, K. R. (2010). *Journal of Finance*, 65(5), 1915-1947. https://doi.org/10.1111/j.1540-6261.2010.01598.x
- **Finding:**
  - The authors bootstrap simulated "zero-alpha" fund histories and compare them with the actual distribution of US equity mutual fund alphas (1984-2006).
  - After fees, the actual distribution looks *worse* than pure luck would produce. Few funds show net alpha large enough to cover their costs.
  - Before fees there is some evidence of skill in both tails, both positive and negative, but it is economically small.
  - In aggregate, the mutual fund sector holds something close to the market portfolio and underperforms by roughly its costs.
- **Strength:** **Strong.** The paper is widely cited and robust to the choice of factor model (3- and 4-factor).
- **Caveats:**
  - Bootstrap tests have limited power to detect a small number of truly skilled managers.
  - Even if skill exists, identifying it *in advance* is the practical problem (see Carhart 1997).
- **Program rule/implication:**
  - The "manager track record" field never by itself raises a fund's score.
  - Require an expense-ratio-adjusted, factor-adjusted alpha and display a "could be luck" note. Rule of thumb: t ≈ (alpha / tracking error) x √years. A manager with a true alpha of 2.5%/yr and 5% tracking error needs about 16 years to reach t ≈ 2. A 1% alpha would need about 100 years. Show the user that calculation.

### 1.4 Carhart (1997), "On Persistence in Mutual Fund Performance"
- **Citation:** Carhart, M. M. (1997). *Journal of Finance*, 52(1), 57-82. https://doi.org/10.1111/j.1540-6261.1997.tb03808.x
- **Finding:**
  - The paper introduces the 4-factor model (market, size, value, momentum).
  - Apparent "hot hands" persistence in fund returns is explained mostly by common momentum exposure and by differences in expenses and turnover.
  - The only strong persistence is among the **worst** funds, which keep underperforming.
  - Expenses and turnover reduce net returns roughly one-for-one, or worse.
- **Strength:** **Strong.** It is a foundational paper and its findings have been repeated many times.
- **Caveats:**
  - The sample covers 1962-1993.
  - Later work finds some short-lived persistence, for example Bollen & Busse (2005, *Review of Financial Studies*), but it is too short to exploit after costs.
- **Program rule/implication:**
  - Expense ratio and turnover are **first-class scoring inputs**. Lower is better, and the effect is monotone.
  - Past 1-3 year returns get **zero or near-zero weight** in fund selection.
  - The app warns when a user sorts candidates by trailing 1-year return.

### 1.5 The cost of active investing
- **Citation:** French, K. R. (2008). Presidential Address: The Cost of Active Investing. *Journal of Finance*, 63(4), 1537-1573. https://doi.org/10.1111/j.1540-6261.2008.01368.x
- **Finding:** French estimated that US investors spent about **0.67% of aggregate market value per year** (1980-2006) trying to beat the market. That spending is a deadweight loss to investors as a whole.
- **Strength:** **Strong** (careful accounting).
- **Caveats:** Costs have fallen sharply since 2006 because of zero-commission trading and ETF fee wars.
- **Program rule/implication:** The fee calculator shows the 10-, 20-, and 30-year dollar drag of an expense-ratio gap. For example, 0.03% vs. 0.50% on $X compounding at r%.

---

## 2. Skewness of individual stock returns

### 2.1 Bessembinder (2018), "Do Stocks Outperform Treasury Bills?"
- **Citation:** Bessembinder, H. (2018). *Journal of Financial Economics*, 129(3), 440-457. https://doi.org/10.1016/j.jfineco.2018.06.004
- **Finding:**
  - The paper covers CRSP US common stocks from 1926 to 2016 (about 26,000 stocks).
  - Slightly more than **four out of seven** (about 57%) had lifetime buy-and-hold returns *below* one-month Treasury bills. The abstract confirms "most"; the exact share comes from the paper body and should be spot-checked.
  - The **best-performing ~4% of companies accounted for all of the net wealth creation** (abstract verified; the paper body gives about $35 trillion) in the US stock market over T-bills. The other 96% together matched T-bills.
  - The modal (most common) lifetime outcome is a loss. Long-horizon single-stock returns are strongly **positively skewed**.
- **Follow-up:** Bessembinder, Chen, Choi & Wei (2023), "Long-Term Shareholder Returns: Evidence from 64,000 Global Stocks," *Financial Analysts Journal*, 79(3), 33-63, https://doi.org/10.1080/0015198X.2023.2178858. The global result is even more concentrated. Among more than 64,000 stocks from January 1990 to December 2020, the top **2.4% of firms** accounted for all **US$75.7 trillion** of net global wealth creation. Outside the US, just 1.41% of firms accounted for US$30.7 trillion. Most stocks trailed one-month T-bills over their full lifetimes: **55.2% of US stocks and 57.4% of non-US stocks** (verified from abstract).
- **Strength:** **Very strong.** It is a census-level dataset, and the result follows mathematically from compounding volatile returns.
- **Caveats:**
  - The result is about *lifetime* returns of *all* listed stocks, including many tiny and short-lived ones. Large, established companies have better odds.
  - It does not say stock picking is impossible. It says that a randomly chosen, concentrated portfolio is likely to miss the few big winners and trail the index.
  - Skewness makes the *median* outcome of a concentrated portfolio worse than the *mean*.
- **Program rule/implication:**
  - **Core principle: the index owns the 4% by construction.**
  - Concentration warnings: if a single stock is more than **5%** of total investable assets, show a "single-stock risk" warning. Above **10%**, show a strong warning.
  - If individual stocks *in total* exceed a user-set "satellite" limit (default **10-20%** of equity holdings for a moderate-risk profile), warn.
  - The stock-screener result page always shows a "base rate" card: *"Historically, most individual stocks underperformed T-bills over their lifetime (Bessembinder 2018)."*
  - The Monte Carlo simulator for concentrated portfolios should use a skewed (for example lognormal or bootstrapped) single-stock return distribution, not a normal one. That way it shows the median-vs-mean gap.

---

## 3. Factor investing

### 3.1 Fama-French three- and five-factor models
- **Citations:**
  - Fama, E. F., & French, K. R. (1992). The Cross-Section of Expected Stock Returns. *Journal of Finance*, 47(2), 427-465. https://doi.org/10.1111/j.1540-6261.1992.tb04398.x (verified via secondary source)
  - Fama & French (1993). Common Risk Factors in the Returns on Stocks and Bonds. *Journal of Financial Economics*, 33(1), 3-56. https://doi.org/10.1016/0304-405X(93)90023-5 (verified via secondary source)
  - Fama & French (2015). A Five-Factor Asset Pricing Model. *Journal of Financial Economics*, 116(1), 1-22. https://doi.org/10.1016/j.jfineco.2014.10.010 (verified via secondary source)
  - Fama & French (2012). Size, Value, and Momentum in International Stock Returns. *Journal of Financial Economics*, 105(3), 457-472. https://doi.org/10.1016/j.jfineco.2012.05.011
- **Finding:**
  - Beyond market beta, **size (SMB)** and **value (HML, high book-to-market)** explain much of the cross-section of diversified portfolio returns (1963-1990s).
  - The 5-factor model adds **profitability (RMW, robust minus weak)** and **investment (CMA, conservative minus aggressive)**. In the 1963-2013 US data, HML becomes largely redundant once RMW and CMA are included (verified via secondary source).
  - Value and profitability premiums appear in international developed markets as well (2012 paper).
- **Strength:**
  - Market: **strong**.
  - Value: **moderate-strong** historically, but with a long, painful drawdown from about 2007 to 2020. There was a partial recovery in 2021-2022, and US value trailed growth again in the AI-led 2023-2025 market.
  - Size: **weak** by itself. It is mostly concentrated in microcaps and junk stocks; it is stronger when combined with quality or profitability (see Asness, Frazzini, Israel, Moskowitz & Pedersen, "Size Matters, If You Control Your Junk," *JFE* 2018, 129(3), 479-509, https://doi.org/10.1016/j.jfineco.2018.05.006, verified).
  - Profitability: **moderate-strong**.
  - Investment: **moderate**.
- **Caveats:**
  - Premiums are expected, not guaranteed. Factors can underperform for 10+ years.
  - Whether premiums are risk compensation or mispricing is still debated.
  - Value's 2007-2020 drawdown led to "is value dead" research. Israel, Laursen & Richardson (2021), "Is (Systematic) Value Investing Dead?", *Journal of Portfolio Management*, 47(2), 38-62, and Arnott, Harvey, Kalesnik & Linnainmaa (2021), "Reports of Value's Death May Be Greatly Exaggerated," *Financial Analysts Journal*, 77(1), 44-67, argue it is not dead. They point to spread widening and intangibles as partial explanations.
- **Program rule/implication:**
  - Factor-tilted ETFs (small-cap value, profitability/quality, multi-factor) are an **optional tilt**, not the default.
  - For a moderate-risk user, the default tilt is **0-30% of equities**, set by the user.
  - The app shows "tracking-error regret" warnings: *"Factor tilts can lag the S&P 500 for a decade or more."*
  - The ETF analyzer runs a factor regression on any ETF against Ken French Data Library factors (free: https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html). It reports loadings so the user can see whether a "value" ETF actually delivers value or profitability exposure.

### 3.2 Momentum: Jegadeesh & Titman (1993)
- **Citation:** Jegadeesh, N., & Titman, S. (1993). Returns to Buying Winners and Selling Losers: Implications for Stock Market Efficiency. *Journal of Finance*, 48(1), 65-91. https://doi.org/10.1111/j.1540-6261.1993.tb04702.x
- **Finding:** Stocks with high returns over the past 3-12 months tend to keep outperforming over the next 3-12 months. The strategy earned roughly 1% per month (1965-1989). The effect has been found across countries and asset classes (Asness, Moskowitz & Pedersen 2013, "Value and Momentum Everywhere," *Journal of Finance*, 68(3), 929-985).
- **Strength:** **Strong** as a pattern. It is among the most robust anomalies.
- **Caveats:**
  - High turnover means high trading costs and taxes.
  - It is subject to severe **momentum crashes**, for example 2009 (Daniel & Moskowitz 2016, "Momentum Crashes," *JFE*, 122(2), 221-247, https://doi.org/10.1016/j.jfineco.2015.12.002). Crashes happen in "panic states," after market declines when volatility is high and the market rebounds sharply.
  - It is hard for individuals to capture efficiently in taxable accounts.
- **Program rule/implication:**
  - Momentum is not a primary "buy" signal for a long-horizon individual.
  - In the stock screener, use 12-1 month momentum only as a **minor secondary score (≤10% weight)** or as a "falling knife" flag, meaning very negative momentum on a "cheap" stock.
  - Prefer holding momentum through diversified funds, if at all.

### 3.3 Profitability: Novy-Marx (2013)
- **Citation:** Novy-Marx, R. (2013). The Other Side of Value: The Gross Profitability Premium. *Journal of Financial Economics*, 108(1), 1-28. https://doi.org/10.1016/j.jfineco.2013.01.003
- **Finding:** Gross profits divided by assets predict returns about as strongly as book-to-market does. Profitable firms earn higher returns despite having higher valuations. Profitability is negatively correlated with value, so combining the two improves diversification.
- **Strength:** **Moderate-strong.** It replicates internationally, and similar ideas were incorporated into FF5 as RMW.
- **Caveats:** The premium is smaller after publication (see section 4). Different definitions (gross vs. operating vs. cash-based) give somewhat different results.
- **Program rule/implication:**
  - Stock-screener "Quality" sub-score includes **gross profitability (GP/Assets)** and **operating profitability (OP/Book equity)**.
  - Default weight in the composite "quality-value" score is about **25-30%**.
  - "Cheap but unprofitable" stocks get a value-trap flag.

### 3.4 Quality Minus Junk: Asness, Frazzini & Pedersen
- **Citation:** Asness, C. S., Frazzini, A., & Pedersen, L. H. (2019). Quality Minus Junk. *Review of Accounting Studies*, 24(1), 34-112. https://doi.org/10.1007/s11142-018-9470-2
- **Finding:**
  - The published abstract defines quality through **profitability, growth, and safety**. Payout appeared as a separate component in working-paper versions, and some implementations still include it.
  - High-quality stocks earn higher risk-adjusted returns. They trade at higher prices, but "not by a large margin."
  - The QMJ factor earns significant risk-adjusted returns in the US and across 24 countries. It tends to do well in market downturns ("flight to quality").
- **Strength:** **Moderate-strong.**
- **Caveats:**
  - The authors are AQR practitioners.
  - The construction is composite and partly data-mined in its components.
  - Quality-labelled ETFs differ widely in methodology.
- **Program rule/implication:** The stock-screener "Quality" score mirrors the QMJ components:
  - profitability (ROE, ROA, GP/A, margins)
  - growth (5-year change in profitability)
  - safety (low leverage, low earnings volatility, low beta)
  - payout (net share buybacks plus dividends)

  This fits the user's stated interest in value/quality and Joseph Carlson-style dividend-growth quality. The quality score doubles as the main **downside-risk mitigant** for a moderate-risk user.

### 3.5 Low volatility / low beta
- **Citations:**
  - Ang, A., Hodrick, R. J., Xing, Y., & Zhang, X. (2006). The Cross-Section of Volatility and Expected Returns. *Journal of Finance*, 61(1), 259-299. https://doi.org/10.1111/j.1540-6261.2006.00836.x
  - Frazzini, A., & Pedersen, L. H. (2014). Betting Against Beta. *Journal of Financial Economics*, 111(1), 1-25. https://doi.org/10.1016/j.jfineco.2013.10.005
  - Baker, M., Bradley, B., & Wurgler, J. (2011). Benchmarks as Limits to Arbitrage: Understanding the Low-Volatility Anomaly. *Financial Analysts Journal*, 67(1), 40-54. https://doi.org/10.2469/faj.v67.n1.4
- **Finding:** Low-volatility and low-beta stocks have historically delivered similar or higher returns than high-volatility stocks, so they have higher risk-adjusted returns. Stocks with high idiosyncratic volatility have had notably poor returns. Proposed explanations include leverage constraints and lottery preferences.
- **Strength:** **Moderate.** The pattern is robust, but much of it overlaps with value, profitability, and quality. Novy-Marx (2014, "Understanding Defensive Equity," NBER working paper, not peer-reviewed) and Fama & French (2016, "Dissecting Anomalies with a Five-Factor Model," *RFS* 29(1)) argue that profitability and investment exposures largely explain it.
- **Caveats:**
  - Low-vol ETFs can carry heavy sector concentration (utilities, staples) and interest-rate sensitivity.
  - They can lag badly in strong bull markets.
  - Popularity has made them more expensive.
- **Program rule/implication:**
  - The default screener **penalizes very high idiosyncratic volatility** ("lottery stock" flag, for example the top decile of 1-year volatility).
  - A low-vol ETF is offered as an *optional* way to cut drawdown for a moderate-risk user. The app explains that bonds or cash are the more reliable way to reduce volatility.

---

## 4. Factor decay, replication, and data mining

### 4.1 McLean & Pontiff (2016), "Does Academic Research Destroy Stock Return Predictability?"
- **Citation:** McLean, R. D., & Pontiff, J. (2016). *Journal of Finance*, 71(1), 5-32. https://doi.org/10.1111/jofi.12365
- **Finding:**
  - The authors study 97 published return predictors. The volume and pages were verified; the publisher abstract was blocked, so the following figures are the widely quoted abstract numbers, not re-checked.
  - Their returns are about **26% lower out-of-sample** (after the original sample ends but before publication). That drop is consistent with statistical bias.
  - Returns are about **58% lower after publication**, which implies about 32 percentage points of decay from investors learning about and trading on the anomaly.
  - The predictors do not disappear entirely.
- **Strength:** **Strong.**
- **Caveats:**
  - Decay is larger for anomalies that are cheap to arbitrage.
  - Results vary by anomaly.
- **Program rule/implication:**
  - When the app quotes a historical factor premium, it **haircuts it by about 50%** for forward-looking expectations.
  - Example: if a historical value premium is X%/year, the planning assumption is ≤0.5X.
  - This haircut is a config parameter: `factor_premium_haircut = 0.5`.

### 4.2 Hou, Xue & Zhang (2020), "Replicating Anomalies"
- **Citation:** Hou, K., Xue, C., & Zhang, L. (2020). *Review of Financial Studies*, 33(5), 2019-2133. https://doi.org/10.1093/rfs/hhy131
- **Finding:**
  - The authors try to replicate 452 anomalies using NYSE breakpoints and value-weighted returns, which limits the influence of microcaps.
  - About **65% fail** the usual |t| >= 1.96 hurdle. With a stricter multiple-testing hurdle of t >= 2.78, about **82% fail** (verified from the RFS abstract). The 2017 NBER working-paper version (w23394) reported 64% of 447 anomalies, and 85% at t >= 3.
  - The worst category is trading frictions: **96%** fail.
  - Even the anomalies that replicate are typically much smaller than originally reported.
  - Value, profitability, investment, and momentum largely survive in some form.
- **Strength:** **Strong.**
- **Caveats:**
  - Methodology choices (value-weighting, microcap exclusion) tend to produce more failures.
  - Jensen, Kelly & Pedersen (2023), "Is There a Replication Crisis in Finance?", *Journal of Finance*, 78(5), 2465-2518, https://doi.org/10.1111/jofi.13249, argue that a Bayesian approach replicates *most* factors. They find factors cluster into **13 themes** and hold up out-of-sample across 93 countries (verified). The two papers disagree mainly over methodology. JKP use a different factor construction and a Bayesian framework, so the "replication crisis" verdict is genuinely contested.
- **Program rule/implication:**
  - Only use **"canonical" factors** (value, profitability/quality, investment, momentum, low-vol) in scoring.
  - **Never** add a signal just because a recent backtest looked good.
  - The knowledge base tags each signal with `evidence_tier: canonical | contested | speculative`. Only `canonical` signals can carry weight above 10%.

### 4.3 Harvey, Liu & Zhu (2016), "...and the Cross-Section of Expected Returns"
- **Citation:** Harvey, C. R., Liu, Y., & Zhu, H. (2016). *Review of Financial Studies*, 29(1), 5-68. https://doi.org/10.1093/rfs/hhv059
- **Finding:**
  - The paper catalogs 316 published factors. That count is from the paper body; the abstract says only "hundreds."
  - Because of multiple testing, a new factor should clear **t > 3.0** rather than 2.0.
  - Many published factors are likely false discoveries.
- **Strength:** **Strong** (methodological).
- **Caveats:** The exact hurdle depends on assumptions about correlation between tests and on unpublished tests.
- **Program rule/implication:**
  - Any user-built screen or backtest in the app shows a **multiple-testing warning**: "You tested N variants; the best result is expected to look good by chance."
  - The backtest module reports the number of variants tried and an approximate deflated Sharpe ratio (Bailey & Lopez de Prado 2014, "The Deflated Sharpe Ratio," *Journal of Portfolio Management*, 40(5), 94-107).

---

## 5. Dividends

### 5.1 Miller & Modigliani (1961): dividend irrelevance
- **Citation:** Miller, M. H., & Modigliani, F. (1961). Dividend Policy, Growth, and the Valuation of Shares. *Journal of Business*, 34(4), 411-433. https://doi.org/10.1086/294442
- **Finding:** In frictionless markets (no taxes, no transaction costs, investment policy held fixed), dividend policy does not affect firm value or total shareholder return. A dividend lowers the share price by the amount paid. Investors who want cash can create "homemade dividends" by selling shares.
- **Strength:** **Very strong** as theory. **Strong** in the sense that real-world frictions mostly work *against* dividends for taxable investors.
- **Caveats:**
  - Taxes, transaction costs, and signaling all matter in practice. Dividend changes carry information (signaling).
  - Payout discipline may limit empire-building by managers (agency theory: Jensen 1986, *American Economic Review* 76(2)).
  - The dividend-growth strategies the user likes (Joseph Carlson style) may work through a **quality or profitability tilt**, not through the dividend itself.
- **Program rule/implication:**
  - Show **total return**, never price return or yield alone.
  - The dividend-growth screener runs a factor regression and reports how much of a dividend ETF's return is explained by value, profitability, and investment loadings.
  - **Tax-location rule:** high-yield holdings are flagged as better placed in IRA/401(k) than in the taxable account. Qualified dividends are taxed at 0/15/20%, plus the 3.8% Net Investment Income Tax above $200K MAGI (single) / $250K (MFJ). Non-qualified dividends are taxed at ordinary rates. REIT dividends are mostly non-qualified at ordinary rates, but may get the 20% Section 199A deduction (made permanent by the 2025 OBBBA [UNVERIFIED]). Qualified status also requires holding the stock more than 60 days in the 121-day window around the ex-dividend date.
  - Yield traps: a yield above about 2x the sector median combined with a payout ratio above 100% of free cash flow triggers a warning.

### 5.2 Hartzmark & Solomon (2019), "The Dividend Disconnect" (free dividends fallacy)
- **Citation:** Hartzmark, S. M., & Solomon, D. H. (2019). The Dividend Disconnect. *Journal of Finance*, 74(5), 2153-2199. https://doi.org/10.1111/jofi.12785 (DOI corrected and verified via RePEc)
- **Finding:**
  - Individual investors, mutual funds, and institutions treat dividends and capital gains as "disconnected attributes." They overlook the fact that a dividend lowers the share price, which the authors call the **"free dividends fallacy."**
  - Investors judge performance by price changes and ignore dividends. They rarely reinvest dividends and treat them as a separate, steady income stream.
  - Analysts' price targets for dividend payers are too optimistic because they miss the price drop.
  - Demand for dividends rises when interest rates are low and markets perform poorly, and this pushes down future returns on dividend payers. (Price pressure around dividend months is documented in the separate 2013 paper below.)
  - A related paper is Hartzmark & Solomon (2013), "The Dividend Month Premium," *JFE*, 108(3), 640-660.
- **Strength:** **Moderate-strong.** It is a top-journal paper with multiple datasets.
- **Caveats:**
  - The behavioral interpretation is debated.
  - Retirees who prefer dividends for self-control reasons may get real psychological value from them (Shefrin & Statman 1984, "Explaining Investor Preference for Cash Dividends," *JFE*, 13(2), 253-282).
- **Program rule/implication:**
  - The dividend screen includes a "Free dividends?" explainer.
  - For a 10+ year accumulating investor with a taxable account, the app shows the **tax drag** of dividend yield, for example a 3% yield taxed at 15%, compared with an equivalent broad-market fund. Ben Felix has argued this point on his channel [video title, URL, and date UNVERIFIED].
  - Dividend growth is **allowed** as a quality screen. It is never justified as "income is free."

---

## 6. Fees, trading, and investor behavior

### 6.1 Barber & Odean (2000), "Trading Is Hazardous to Your Wealth"
- **Citation:** Barber, B. M., & Odean, T. (2000). Trading Is Hazardous to Your Wealth: The Common Stock Investment Performance of Individual Investors. *Journal of Finance*, 55(2), 773-806. https://doi.org/10.1111/0022-1082.00226 (abstract verified from authors' PDF)
- **Finding:**
  - The study covers 66,465 households at a large discount broker, 1991-1996.
  - The **highest-turnover households earned 11.4%/year, versus 17.9% for the market**. The average household earned 16.4%.
  - The average household turned over **more than 75%** of its portfolio each year.
  - The authors' explanation is overconfidence.
- **Related studies:**
  - Barber & Odean (2001), "Boys Will Be Boys," *Quarterly Journal of Economics*, 116(1), 261-292, https://doi.org/10.1162/003355301556400. The study used more than 35,000 households (Feb 1991-Jan 1997). Men traded **45% more** than women. Trading reduced men's net returns by **2.65 pp/yr**, compared with **1.72 pp/yr** for women (verified from abstract).
  - Barber, Lee, Liu & Odean (2009), "Just How Much Do Individual Investors Lose by Trading?", *Review of Financial Studies*, 22(2), 609-632. Using complete Taiwan market data, individuals as a group suffered an annual performance penalty of **3.8 percentage points**, nearly all of it from aggressive orders. Institutions gained about 1.5 pp/yr (verified from abstract).
- **Strength:** **Strong.** The finding replicates across countries.
- **Caveats:**
  - The data come from the commission era. Commissions are now zero, but bid-ask spreads, payment for order flow, taxes, and poor timing remain.
  - Barber, Huang, Odean & Schwarz (2022), "Attention-Induced Trading and Returns: Evidence from Robinhood Users," *Journal of Finance*, 77(6), 3141-3190, find that herding episodes among Robinhood users are followed by negative returns.
- **Program rule/implication:**
  - The app is designed for **low-frequency decisions**. There is no real-time ticker view by default. A "decision journal" is required before any "buy" checklist is marked complete.
  - Turnover tracker: if the user's logged trades imply annual turnover above **50%**, show a warning.
  - The cooling-off feature suggests a 48-72 hour wait before acting on any new stock idea.

### 6.2 The behavior gap: Morningstar "Mind the Gap"
- **Source:** Morningstar, *Mind the Gap* (annual), lead author Jeffrey Ptak. https://www.morningstar.com/lp/mind-the-gap
- **Finding:**
  - The study compares dollar-weighted (investor) returns with time-weighted (fund) returns for US funds and ETFs.
  - The gap has been about **1.1-1.2 percentage points per year**, roughly 15% of the funds' total return.
  - Edition covering the 10 years through 2023: about 6.3% investor return vs. 7.3% fund return.
  - Edition covering the 10 years through 2024: about 7.0% vs. 8.2% [both UNVERIFIED: the Morningstar pages were blocked (403/empty) during the fact-check; display as "approximate" until refreshed].
  - Gaps are **largest in volatile, narrow, or thematic funds** (sector funds, leveraged, crypto-adjacent). They are **smallest in allocation and target-date funds**, which automate rebalancing.
- **Strength:** **Moderate-strong.** The methodology is transparent and repeated every year.
- **Caveats:**
  - Dollar-weighted returns are also affected by the *timing of contributions*, not only behavior. Systematic contributions during a rising market can create a "gap" with no misbehavior.
  - **DALBAR QAIB** figures are frequently quoted with much larger gaps. Their methodology has been widely criticized (for example by Wade Pfau and others), so **do not cite DALBAR as fact**.
- **Program rule/implication:**
  - Default recommendation favors **simple, broad, automated** holdings: a target-date fund or a 2-3 fund portfolio with scheduled rebalancing.
  - When a user adds a thematic or sector ETF, show a "behavior-gap risk: high" badge.
  - The app tracks the user's own dollar-weighted vs. time-weighted return and shows their personal behavior gap.

### 6.3 Disposition effect
- **Citations:**
  - Shefrin, H., & Statman, M. (1985). The Disposition to Sell Winners Too Early and Ride Losers Too Long. *Journal of Finance*, 40(3), 777-790. https://doi.org/10.1111/j.1540-6261.1985.tb05002.x
  - Odean, T. (1998). Are Investors Reluctant to Realize Their Losses? *Journal of Finance*, 53(5), 1775-1798. https://doi.org/10.1111/0022-1082.00072
- **Finding:**
  - Investors sell winners more readily than losers.
  - Odean measured the proportion of gains realized (PGR, about 0.148) against the proportion of losses realized (PLR, about 0.098) over the full year [from memory: the author PDF is encrypted and could not be parsed; spot-check Table 1]. In December the pattern reverses (tax-loss selling).
  - The winners that investors sold went on to outperform the losers they kept. The behavior is also tax-inefficient: in the US it is better to harvest losses.
- **Strength:** **Strong.** The effect replicates widely.
- **Caveats:** Explanations differ (prospect theory, realization utility).
- **Program rule/implication:**
  - Each "sell" checklist asks: *"Would you buy this today at this price?"*
  - Tax-loss harvesting suggestions apply to the taxable account, with a **wash-sale** warning. The window is 30 days before *or* after the sale, 61 days in total. It also covers purchases in the user's IRA/Roth IRA (Rev. Rul. 2008-5), where the disallowed loss is permanently lost, and automatic dividend reinvestment.
  - Holdings display the *thesis* next to P/L to discourage anchoring on the purchase price.

### 6.4 Fees as a predictor
- **Source:** Kinnel, R. (2016). "Predictive Power of Fees: Why Mutual Fund Fees Are So Important." Morningstar [UNVERIFIED: exact title/date].
- **Finding:** Across asset classes, the cheapest-quintile funds have higher success rates (survived and beat their category) than the most expensive quintile. Fees were a better predictor than Morningstar's own star ratings.
- **Strength:** **Strong.** The result is consistent with Carhart (1997) and Sharpe (1991). It is also a practitioner study from a fund-rating firm, not peer-reviewed.
- **Program rule/implication:**
  - ETF scoring: expense ratio is the **highest-weighted single variable** among comparable funds, default **30%** of the ETF score. These weights are design choices informed by the research, not estimates taken from it.
  - The other weights are: tracking difference (15%), diversification/holdings count (15%), liquidity/spread (10%), tax efficiency (10%), factor fit to the user's goal (20%).

---

## 7. Diversification and concentration

### 7.1 How many stocks make a diversified portfolio?
- **Citations:**
  - Evans, J. L., & Archer, S. H. (1968). Diversification and the Reduction of Dispersion: An Empirical Analysis. *Journal of Finance*, 23(5), 761-767. https://doi.org/10.1111/j.1540-6261.1968.tb00315.x
  - Statman, M. (1987). How Many Stocks Make a Diversified Portfolio? *Journal of Financial and Quantitative Analysis*, 22(3), 353-363. https://doi.org/10.2307/2330969
  - Campbell, J. Y., Lettau, M., Malkiel, B. G., & Xu, Y. (2001). Have Individual Stocks Become More Volatile? *Journal of Finance*, 56(1), 1-43. https://doi.org/10.1111/0022-1082.00318
  - Domian, D. L., Louton, D. A., & Racine, M. D. (2007). Diversification in Portfolios of Individual Stocks: 100 Stocks Are Not Enough. *Financial Review*, 42(4), 557-570. https://doi.org/10.1111/j.1540-6288.2007.00185.x
- **Finding:** Estimates of "enough" stocks have grown over time:
  - Evans & Archer (1968) suggested about 10 stocks, measured by standard-deviation reduction.
  - Statman (1987) put it at **30-40** once borrowing costs are considered.
  - Campbell et al. (2001) found idiosyncratic volatility rose from 1962 to 1997, so about **50** stocks were needed for the same reduction.
  - Domian et al. (2007) measured *shortfall risk* of terminal wealth over 20 years and found even **100 stocks** leave meaningful risk compared with the market.
  - With Bessembinder's skewness (section 2), small portfolios also risk missing the few big winners.
- **Strength:** **Strong.**
- **Caveats:** The answer depends on the risk metric (volatility vs. terminal-wealth shortfall), on whether stocks are equal- or cap-weighted, and on sector diversification.
- **Program rule/implication:**
  - If the user wants a stock-only portfolio, the app requires at least **30 stocks across at least 8 GICS sectors** before it labels the portfolio "diversified."
  - The default framing is **core-satellite**: broad ETFs make up at least 80% of equities, and individual stocks are at most 20%. The user can set this, but the app warns above 20% for the moderate-risk profile.
  - The app shows the overlap between individual stocks held and the stocks already inside the user's ETFs. For example, buying AAPL on top of VOO increases concentration.
  - The app also reports index concentration. The top 10 holdings of the S&P 500 made up a historically high share of the index in 2024-2026, roughly 35-40% [UNVERIFIED]. The app computes this live from the ETF holdings file. **Caveat for a "just buy VOO" user:** a cap-weighted S&P 500 fund is itself concentrated in a handful of mega-cap tech/AI names. Index investing protects against single-company failure, but not against a sector or theme repricing.

### 7.2 Home bias and international diversification
- **Citations:**
  - French, K. R., & Poterba, J. M. (1991). Investor Diversification and International Equity Markets. *American Economic Review*, 81(2), 222-226.
  - Anarkulova, A., Cederburg, S., & O'Doherty, M. S. (2022). Stocks for the Long Run? Evidence from a Broad Sample of Developed Markets. *Journal of Financial Economics*, 143(1), 409-433. https://doi.org/10.1016/j.jfineco.2021.05.009
  - Anarkulova, Cederburg & O'Doherty (2023 working paper). Beyond the Status Quo: A Critical Assessment of Lifecycle Investment Advice. SSRN [UNVERIFIED: current publication status].
  - Asness, Israelov & Liew (2011). International Diversification Works (Eventually). *Financial Analysts Journal*, 67(3), 24-38. https://doi.org/10.2469/faj.v67.n3.1
- **Findings:**
  - Investors heavily overweight their home market relative to global market-cap weights. French & Poterba documented this for the US, Japan, and the UK.
  - Anarkulova et al. (2022) examine 39 developed markets from 1841 to 2019. Their bootstrap estimates a **12% chance** that a diversified investor with a 30-year horizon loses purchasing power (verified from abstract). The US's 20th-century record is a lucky outlier among countries ("survivorship of the US"). Caveat: the estimate comes from a block bootstrap that pools countries, and critics argue it overweights disaster periods such as wars and hyperinflations in small markets.
  - International diversification does not help much in short crashes, because correlations rise. It does help over long horizons, when one country's market stagnates (for example Japan after 1989).
  - The 2023 working paper argues that an all-equity mix of about 50% domestic and 50% international beats typical target-date glide paths for wealth accumulation and retirement outcomes. Ben Felix has discussed this paper, including on the Rational Reminder podcast with Scott Cederburg [UNVERIFIED: episode]. It was **not confirmed as peer-reviewed** during the fact-check [UNVERIFIED]. All-equity is also a large behavioral ask for a *moderate* risk-tolerance investor, so the app presents it as a contested idea, not a default.
- **Strength:**
  - Home bias as a fact: **very strong**.
  - The benefits of international diversification: **strong** in theory, **moderate** in recent US-dominated data (US outperformance from 2010 to the mid-2020s).
- **Caveats:**
  - Currency risk.
  - Higher fees and foreign withholding tax on international funds. The foreign tax credit applies in taxable accounts.
  - US multinationals already earn substantial foreign revenue.
- **Program rule/implication:**
  - Default equity mix starts at **global market-cap weight**, roughly 60-65/35-40 US/ex-US in 2025-2026 [UNVERIFIED: current weights; compute from VT holdings]. The user can set it.
  - The app warns if international exposure falls below **20%** of equities ("home bias" flag).
  - In the asset-location helper, international ETFs are suggested for the **taxable** account so the foreign tax credit is usable.

---

## 8. Valuation and expected returns (CAPE)

### 8.1 Campbell & Shiller: valuation ratios predict long-horizon returns
- **Citations:**
  - Campbell, J. Y., & Shiller, R. J. (1988). Stock Prices, Earnings, and Expected Dividends. *Journal of Finance*, 43(3), 661-676. https://doi.org/10.1111/j.1540-6261.1988.tb04598.x
  - Campbell & Shiller (1998). Valuation Ratios and the Long-Run Stock Market Outlook. *Journal of Portfolio Management*, 24(2), 11-26. https://doi.org/10.3905/jpm.24.2.11
  - Shiller data (free): http://www.econ.yale.edu/~shiller/data.htm
- **Finding:** High CAPE (price divided by 10-year average real earnings) is associated with lower subsequent **10-year** real returns. CAPE has explained a meaningful share of the variation in 10-year forward returns in US history; AQR's Cliff Asness (2012, "An Old Friend: The Stock Market's Shiller P/E") put it at roughly 40% [UNVERIFIED]. CAPE has almost no ability to predict 1-year returns.

### 8.2 Limits of CAPE and return prediction
- **Citations:**
  - Welch, I., & Goyal, A. (2008). A Comprehensive Look at the Empirical Performance of Equity Premium Prediction. *Review of Financial Studies*, 21(4), 1455-1508. https://doi.org/10.1093/rfs/hhm014 (the published version lists Welch first; the working paper lists Goyal first)
  - Siegel, J. J. (2016). The Shiller CAPE Ratio: A New Look. *Financial Analysts Journal*, 72(3), 41-50. https://doi.org/10.2469/faj.v72.n3.1
- **Findings:**
  - Goyal & Welch (2008) find that most valuation predictors do poorly **out-of-sample**. They often fail to beat the historical average return.
  - Siegel (2016) argues that changes in accounting standards (FAS 142/144 write-downs) bias GAAP earnings downward, which inflates modern CAPE readings.
  - US CAPE has stayed above its long-run average for most of the past 30 years. The long-run mean is about 17.5 and the median about 16 (multpl.com, Nov 2026). Investors who sat out because of high CAPE lost heavily.
  - On 2026-11-10 the US CAPE was about **39.7** (multpl.com), near the December 1999 record of about 44. The app must still fetch it live.
- **Strength:** **Moderate** for long-horizon expected-return estimates. **Weak to none** as a market-timing signal.
- **Caveats:**
  - Overlapping 10-year windows leave very few independent observations.
  - Regime changes (interest rates, buybacks, sector mix) affect the ratio.
- **Program rule/implication:**
  - CAPE (or earnings yield) feeds **only the long-run expected-return assumptions** in the planning and Monte Carlo module. Example: US expected real return of about 1/CAPE plus a small growth adjustment, blended 50/50 with the historical average.
  - CAPE **never** triggers "sell" or "go to cash" advice.
  - When CAPE is high, the app shows a *"lower expected returns, so save more and diversify internationally"* message, not *"get out."*
  - Stock-level valuation (P/E, EV/EBIT, FCF yield) feeds the value sub-score in the stock screener. It is cross-sectional, comparing stocks with each other, not a timing tool.

---

## 9. Lifecycle investing and leverage (Ayres & Nalebuff), briefly
- **Citations:**
  - Ayres, I., & Nalebuff, B. (2013). Diversification Across Time. *Journal of Portfolio Management*, 39(2), 73-86. https://doi.org/10.3905/jpm.2013.39.2.073
  - Ayres & Nalebuff (2010). *Lifecycle Investing*. Basic Books.
- **Finding:**
  - Young workers have most of their lifetime wealth in future earnings (human capital), so their stock exposure is concentrated in later decades.
  - Moderate leverage early in life (up to about **2:1** in equities), shifting to unlevered stock and then bonds later, spreads market exposure more evenly across time.
  - In historical simulations this produced higher expected retirement wealth with lower or similar risk than standard glide paths.
- **Strength:** **Moderate** (theory plus historical simulation). It is not widely implemented.
- **Caveats:**
  - Margin calls and forced selling.
  - Borrowing costs are well above the risk-free rate for retail investors. The strategy is very sensitive to the borrowing rate (2022-2026 rates are much higher than in the backtests).
  - Behavioral risk: few investors can hold a levered position through a 50%+ drawdown.
  - Leveraged ETFs (daily reset) are **not** the same thing; they suffer volatility decay.
  - Income volatility: if your job is cyclical, your human capital is more "stock-like."
- **Program rule/implication:**
  - The app covers this **as education only**.
  - For a **moderate** risk profile, the app **never recommends leverage**.
  - The takeaway the app *does* use: a young investor with stable income can justify a **high equity allocation** (for example 80-100% of long-term investment assets, *after* the emergency fund). For a moderate profile the default is 80-90% equity / 10-20% bonds in retirement accounts, which the user adjusts.

---

## 10. Dollar-cost averaging vs. lump sum
- **Citations:**
  - Shtekhman, A., Tasopoulos, C., & Wimmer, B. (2012). *Dollar-Cost Averaging Just Means Taking Risk Later*. Vanguard Research. The finding was verified via a secondary source; author names are from memory.
  - Vanguard Research (2016). *Invest Now or Temporarily Hold Your Cash?* This paper calls staged investing a "systematic implementation plan" (title verified via Wikipedia citation).
  - Vanguard (2023). *Cost Averaging: Invest Now or Temporarily Hold Your Cash?*, an update [UNVERIFIED: title/date/68% figure].
  - Constantinides, G. M. (1979). A Note on the Suboptimality of Dollar-Cost Averaging as an Investment Policy. *Journal of Financial and Quantitative Analysis*, 14(2), 443-450. https://doi.org/10.2307/2330513
- **Finding:**
  - Investing a lump sum immediately beat staged investment over 12 months **about two-thirds of the time** in US, UK, and Australian historical data. The 2023 update reported about 68% [UNVERIFIED].
  - The reason: the expected return on stocks exceeds cash, so delaying lowers expected wealth.
  - Staged investing reduces the regret of investing just before a crash.
  - Note that "DCA" in the colloquial sense of investing each paycheck as it arrives is **not** the same thing. That is simply investing money when you have it, and it is optimal.
- **Strength:** **Strong.** The theory is clear and the empirical results are consistent.
- **Caveats:**
  - Lump sum loses about one-third of the time.
  - The behavioral benefit is real for anxious investors (moderate risk tolerance).
- **Program rule/implication:**
  - For excess cash being moved into investments, the default suggestion is **lump sum**.
  - The app offers a "staged plan" alternative over **3-12 months**, labelled with its expected cost (about two-thirds historical underperformance odds).
  - The user's moderate risk tolerance pre-selects a choice prompt: *"If the market fell 20% the week after you invested, would you keep going?"* If not, suggest a 6-month stage.
  - Payroll contributions (401(k) and IRA) always go in immediately.

---

## 11. Emergency fund, liquidity, and precautionary savings
- **Citations:**
  - Carroll, C. D. (1997). Buffer-Stock Saving and the Life Cycle/Permanent Income Hypothesis. *Quarterly Journal of Economics*, 112(1), 1-55. https://doi.org/10.1162/003355397555109
  - Carroll, C. D., & Samwick, A. A. (1997). The Nature of Precautionary Wealth. *Journal of Monetary Economics*, 40(1), 41-71. https://doi.org/10.1016/S0304-3932(97)00036-6
  - Gjertson, L. (2016). Emergency Saving and Household Hardship. *Journal of Family and Economic Issues*, 37(1), 1-17. https://doi.org/10.1007/s10834-014-9434-z
  - Kaplan, G., & Violante, G. L. (2014). A Model of the Consumption Response to Fiscal Stimulus Payments. *Econometrica*, 82(4), 1199-1239. https://doi.org/10.3982/ECTA10528
  - Federal Reserve Board, *Economic Well-Being of U.S. Households* (SHED, annual). https://www.federalreserve.gov/consumerscommunities/shed.htm
  - CFPB, financial well-being research. https://www.consumerfinance.gov/data-research/research-reports/
  - Vanguard Research, emergency savings and financial well-being, about 2021-2022 [UNVERIFIED: title/date and the exact well-being figure].
- **Findings:**
  - Buffer-stock theory (Carroll 1997) holds that households facing income uncertainty optimally hold a target liquid buffer. Saving is above the buffer target only when the buffer is depleted. The size of the buffer rises with income volatility.
  - Carroll & Samwick (1997) estimate that a substantial share of household wealth is precautionary, driven by income uncertainty.
  - Gjertson (2016) uses the Annie E. Casey *Making Connections* panel of **low-income** households. Households that *reported saving for emergencies* had somewhat less hardship three years later (food insecurity, phone disconnection). The author concludes that "small, unrestricted savings may play a protective role" (verified from abstract). **Correction:** the $250-$750 dollar thresholds are **not** in this paper. Similar thresholds appear in Urban Institute work (e.g., McKernan, Ratcliffe and coauthors) [UNVERIFIED: exact source and figures]. The sample is low-income, so effect sizes may not transfer to a salaried professional.
  - Kaplan & Violante (2014) describe the "wealthy hand-to-mouth": households with substantial illiquid wealth (retirement accounts, home equity) but little liquid cash. They behave as if liquidity-constrained when shocks hit. **Relevant here:** a 401(k) is a poor emergency fund. Before age 59½, withdrawals usually face income tax plus a 10% penalty. Exceptions exist: SECURE 2.0 allows one penalty-free emergency personal-expense distribution of up to $1,000 per year, if the plan offers it. By contrast, **Roth IRA *contributions*** (not earnings) can be withdrawn at any time tax- and penalty-free. That makes a Roth IRA a reasonable *backstop* layer, though not the first layer.
  - The SHED 2025 survey (fielded October 2025, published May 2026) found **63%** of adults would cover a hypothetical $400 emergency expense with cash or its equivalent. That share has been unchanged for four years and is below the 2021 peak of 68% (verified from the Fed executive summary).
  - Vanguard's research reports that people with an emergency fund (even about $2,000) report meaningfully higher financial well-being [UNVERIFIED].
- **Strength:**
  - The precautionary-saving theory is **strong**.
  - The link between liquid savings and lower hardship is **moderate-strong** (correlational, but the direction is consistent).
  - The "3-6 months of expenses" rule is **convention, not a precise research result**.
- **Caveats:**
  - The optimal buffer depends on job stability, number of earners, insurance deductibles, and access to credit.
  - Holding too much cash has an opportunity cost of about the equity premium.
- **Program rule/implication:**
  1. **Input:** monthly essential expenses E, job-stability rating, single or dual income, insurance deductibles, and any high-interest debt.
  2. **Emergency-fund target:**
     - Stable job and dual income: 3 months.
     - Stable job and single income: about 4-6 months.
     - Variable income or cyclical industry: 6-9 months.
     - The target always includes the highest insurance deductible.
     - The **default for this user is 6 x E** (moderate risk tolerance, so lean conservative) until they provide more information.
  3. **Allocation of the ~$20K:**
     - (a) Pay off any debt with interest **above about 7-8%**.
     - (b) Fill the emergency fund in liquid, FDIC/NCUA-insured or Treasury-backed vehicles.
     - (c) Capture the full 401(k) match if not already doing so (a 50-100% instant return). Many planners put this *before* fully funding the emergency fund, once a starter buffer of about 1 month is in place. The app shows both orderings.
     - (d) Fill the Roth or Traditional IRA for the year. The **2026 limit is $7,500** (verified, IRS IR-2025-111). Roth eligibility phases out at MAGI **$153K-$168K single / $242K-$252K MFJ**. Above that, show the "backdoor Roth" explainer with a pro-rata-rule warning.
     - (d2) For reference, the **2026 401(k) employee deferral limit is $24,500**, with a $8,000 catch-up at 50+ and $11,250 at ages 60-63 (verified). If the user has an HSA-eligible high-deductible health plan, the HSA is often ranked just after the match because it is triple tax-advantaged [UNVERIFIED: 2026 HSA limits of about $4,400 self / $8,750 family; fetch from IRS].
     - (e) Put remaining excess cash in the taxable brokerage account, or hold it for known 1-3 year goals in T-bills, CDs, or a HYSA.
     - Limits change every year. Store them in `limits_<year>.json` with the IRS source URL, and never hard-code them.
  4. **Vehicles for emergency and short-term cash:**
     - HYSA (FDIC-insured up to $250K per depositor, per insured bank, per ownership category). **Caveat:** fintech "savings" apps that are not banks rely on *pass-through* insurance through partner banks. That coverage only protects against a bank failure, not a fintech or middleware failure; the 2024 Synapse collapse froze customer funds. The app prefers accounts held directly at an FDIC-insured bank or an NCUA-insured credit union, and links to FDIC BankFind for verification.
     - Treasury money-market funds or T-bills. T-bill interest is exempt from state and local income tax. Money-market fund dividends are exempt only for the share that comes from US Treasury obligations, and some states (notably CA, CT, NY) require the fund to meet a 50% threshold. Repo-heavy "government" funds may get little exemption. Money-market funds are **not FDIC-insured** (SIPC protects against broker failure, not losses).
     - CD ladders for known future expenses.
     - I bonds: 12-month lockup and a 3-month interest penalty if redeemed within 5 years, so they are not ideal for the first layer of the emergency fund. There is a $10K-per-person annual electronic purchase limit through TreasuryDirect.
     - The app compares **after-tax yield**, using the user's federal and state bracket inputs. HYSA and CD yield is fully taxable; T-bill yield is federal-only.
  5. **Live rates:** HYSA and T-bill rates change frequently. The app pulls the 3-month T-bill yield from FRED (series DTB3, free API) and lets the user input HYSA quotes. It never hard-codes a rate.
  6. **Warnings:**
     - "Your 401(k)/IRA is not your emergency fund" (Kaplan-Violante).
     - The app flags any plan that invests the emergency-fund portion in stocks.

---

## 12. Evidence-strength summary table

| # | Topic | Key source(s) | Evidence strength | Default program use |
|---|---|---|---|---|
| 1.1 | Average active manager loses after costs | Sharpe 1991 (FAJ) | Very strong (identity) | Index ETF as core; satellite cap on picks |
| 1.2 | Most active funds trail benchmarks long-term | SPIVA (S&P DJI) | Strong | SPIVA card on active fund candidates |
| 1.3 | Fund alpha mostly luck after fees | Fama & French 2010 (JF) | Strong | Track record never raises score alone |
| 1.4 | No persistence except losers; fees matter | Carhart 1997 (JF) | Strong | Expense ratio and turnover first-class inputs |
| 1.5 | Aggregate cost of active investing | French 2008 (JF) | Strong | Fee-drag calculator |
| 2.1 | Stock returns highly skewed; 4% create all wealth | Bessembinder 2018 (JFE); BCCW 2023 (FAJ) | Very strong | Concentration warnings (5%/10%); base-rate card |
| 3.1 | Value, size, profitability, investment premiums | Fama-French 1992/93/2015 (JF/JFE) | Market strong; value mod-strong; size weak alone; profitability mod-strong | Optional 0-30% factor tilt; factor regression of ETFs |
| 3.2 | Momentum | Jegadeesh & Titman 1993 (JF); Daniel & Moskowitz 2016 | Strong pattern, hard to capture | <=10% screener weight; falling-knife flag |
| 3.3 | Gross profitability premium | Novy-Marx 2013 (JFE) | Moderate-strong | 25-30% of quality-value score |
| 3.4 | Quality minus junk | Asness, Frazzini, Pedersen 2019 (RAS) | Moderate-strong | Quality sub-score components |
| 3.5 | Low volatility / low beta anomaly | Ang et al. 2006; Frazzini & Pedersen 2014; Baker et al. 2011 | Moderate | Lottery-stock flag; optional low-vol ETF |
| 4.1 | Anomalies decay after publication | McLean & Pontiff 2016 (JF) | Strong | 50% haircut on historical premiums |
| 4.2 | Many anomalies fail replication | Hou, Xue, Zhang 2020 (RFS); counterpoint JKP 2023 (JF) | Strong (contested magnitude) | Canonical factors only |
| 4.3 | Multiple testing; t>3 hurdle | Harvey, Liu, Zhu 2016 (RFS) | Strong (methodological) | Backtest overfitting warning |
| 5.1 | Dividend irrelevance (frictionless) | Miller & Modigliani 1961 (J. Business) | Very strong (theory) | Total-return display; tax-location of yield |
| 5.2 | Free dividends fallacy | Hartzmark & Solomon 2019 (JF) | Moderate-strong | Dividend explainer; tax-drag calc |
| 6.1 | Active trading hurts individuals | Barber & Odean 2000 (JF); BLLO 2009 (RFS) | Strong | Decision journal; turnover warning; cooling-off |
| 6.2 | Investor behavior gap about 1%+/yr | Morningstar Mind the Gap | Moderate-strong | Simple/automated defaults; personal gap tracker |
| 6.3 | Disposition effect | Shefrin & Statman 1985; Odean 1998 (JF) | Strong | Thesis-based sell checklist; TLH prompts |
| 6.4 | Low fees predict fund success | Morningstar (Kinnel) | Strong | Expense ratio = 30% of ETF score |
| 7.1 | 30-100+ stocks for diversification | Statman 1987; Campbell et al. 2001; Domian et al. 2007 | Strong | >=30 stocks / >=8 sectors to call "diversified"; overlap check |
| 7.2 | Home bias; single-country risk | French & Poterba 1991; Anarkulova et al. 2022 (JFE) | Strong (fact); moderate (benefit magnitude) | Global-cap default; <20% intl flag |
| 8 | CAPE predicts 10-yr returns, not timing | Campbell & Shiller 1988/98; Goyal & Welch 2008 | Moderate (long-run); weak (timing) | Expected-return inputs only; never a sell signal |
| 9 | Lifecycle leverage | Ayres & Nalebuff 2013 (JPM) | Moderate | Education only; supports high equity share |
| 10 | Lump sum beats staged about 2/3 of the time | Vanguard 2012/2023; Constantinides 1979 | Strong | Default lump sum; optional 3-12 month stage |
| 11 | Precautionary savings / emergency fund | Carroll 1997; Gjertson 2016; Kaplan & Violante 2014; Fed SHED | Strong (theory); moderate (hardship link, low-income samples); 3-6 month rule = convention | 6xE default target; after-tax yield comparison; savings waterfall |

### Evidence-tier legend (for the knowledge-base JSON `evidence_tier` field)
- **Very strong:** logical identity or census-level data, replicated many times.
- **Strong:** multiple top-journal studies, out-of-sample and international replication.
- **Moderate:** robust pattern, but contested explanation, overlap with other factors, or sensitivity to implementation.
- **Weak:** inconsistent out-of-sample, or mostly in microcaps.
- **Convention:** practitioner rule of thumb without precise empirical derivation.

### Default weights summary (config seeds derived from this brief)
- **ETF score:** expense ratio 30, factor/goal fit 20, tracking difference 15, diversification 15, liquidity/spread 10, tax efficiency 10.
- **Stock "buy-candidate" score (satellite only):** quality (QMJ-style) 35, value (cross-sectional valuation) 30, profitability is included in quality (GP/A minimum 25% of the quality block), balance-sheet safety 15, momentum 10, dividend-growth consistency 10 (optional; reassign to quality if the user does not want dividends).
- **Portfolio guardrails (moderate profile):**
  - Individual stocks: at most 20% of equities (warn).
  - Single stock: warn above 5% of investable assets, strong warning above 10%.
  - International: at least 20% of equities.
  - Factor tilt: at most 30%.
  - Leverage: 0.
  - Emergency fund: at least 6 x monthly expenses (default), held in insured or Treasury cash.
- **Forward-return assumptions:** historical factor premiums x 0.5; equity expected return blends a CAPE-implied estimate with the historical average, 50/50.

