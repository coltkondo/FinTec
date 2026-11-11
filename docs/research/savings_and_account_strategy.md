# Savings & Account Strategy: US Research Brief (as of 2026-11-10)

> **Purpose.** This is the knowledge-base foundation for the app's "Cash & Savings" and "Account Order of Operations" modules. It covers where to keep cash, how big an emergency fund should be, the US account priority order with 2026 limits, and a parameterized framework for splitting about $20K of savings.
>
> **Not financial advice.** This is an educational research summary. Rates change daily, so the app must show the `as_of` date next to every rate and ask the user to re-check before acting.
>
> **How this was researched (please read).** Live web *search* was unavailable during research. Facts were checked by fetching primary-source pages directly (IRS, TreasuryDirect, Treasury.gov, Federal Reserve, BLS, FDIC, fund sponsors) and extracting text from IRS and Fed PDFs. Market-rate aggregators (Bankrate, Crane Data) and Yahoo Finance quotes were also fetched on 2026-11-10. Creator content was identified from YouTube metadata: titles, publish dates and descriptions. Transcripts could not be retrieved, so any claim about what a creator *says inside* a video is marked **[UNVERIFIED]** unless it appears in the title or description. Paper DOIs were checked by confirming they resolve at doi.org; bibliographic details come from the publisher landing URL or memory, and are marked where uncertain.
>
> **Labels used:** **[V]** = verified against the cited source on the date shown. **[UNVERIFIED]** = from memory or a secondary source, not confirmed live.
>
> **Fact-check pass (2026-11-10).** An adversarial review re-fetched the Fed, BLS A-12, Treasury bill-rate, TreasuryDirect I bond, IRS limits/brackets/HSA-guidance, SGOV, BIL, FDIC and Urban Institute pages, plus paper metadata. It made these corrections:
> - Fed decision vs effective dates.
> - Choi et al. figures.
> - Full bibliographic details for every paper.
> - The CD-vs-T-bill break-even state tax rate.
> - The I bond fixed-rate history claim.
> - The outdated "gates" language for MMFs.
>
> It also added a SECURE 2.0 section (§3.3a). Creator in-video claims remain unverified, because transcripts were not retrieved.

---

## 0. Macro snapshot (drives every cash yield below)

| Item | Value | Source / date |
|---|---|---|
| Fed funds target range | **3.75%–4.00%**. The FOMC raised it by 25 bp at its Sep 15–16, 2026 meeting (effective Sep 17, 2026), after cutting to 3.50–3.75% at its Dec 9–10, 2025 meeting (effective Dec 11, 2025) **[V]** | Federal Reserve, "Open Market Operations" page, https://www.federalreserve.gov/monetarypolicy/openmarket.htm (fetched and re-checked 2026-11-10). That table lists *effective* dates, which fall the day after each announcement, so the page's "last updated Sep 16, 2026" is consistent and not an error. A Graham Stephan video titled "BREAKING: The Fed Just Froze Rates…" (~Oct 28, 2026) suggests the Fed held at its late-October meeting **[UNVERIFIED]** |
| CPI-U, 12-month change | **3.7%** (Sep 2025 → Sep 2026); October 2026 rose +0.4% m/m SA **[V]** | BLS CPI home, https://www.bls.gov/cpi/ (fetched 2026-11-10) |
| T-bill curve (coupon-equivalent), 11/10/2026 | 4-wk 3.94% · 6-wk 4.08% · 8-wk 4.10% · 13-wk 4.22% · 17-wk 4.29% · 26-wk 4.41% · 52-wk 4.54% **[V, re-checked]** | U.S. Treasury Daily Bill Rates, https://home.treasury.gov/resource-center/data-chart-center/interest-rates/TextView?type=daily_treasury_bill_rates&field_tdr_date_value_month=202611 |
| National avg savings APY | 0.56% (Bankrate; FDIC-based) **[V]** | Bankrate, 2026-11-10 |
| Crane 100 Money Fund Index (7-day) | **3.75%** as of 2026-11-09 **[V]** | https://www.cranedata.com/ |

**What this implies.** The yield curve slopes *upward* across bills (4-week 3.94% vs 52-week 4.54%) and inflation is about 3.7%. The market is pricing short rates to stay flat or rise. That makes locking in 6–12-month yields (CDs, 26/52-week bills) reasonable in Nov 2026. It is a reversal of the 2024–25 "lock in before cuts" logic. The app should recompute this from live data rather than hard-code it.

**Real-yield warning.** With CPI at about 3.7%, a 4.2% HYSA earns roughly +0.5% real *before tax*, and is roughly flat or negative after tax. Cash protects stability; it does not build wealth. This is the core of Ben Felix's "Cash is a terrible long-term investment, even at 5% interest" (Ben Felix, 2023-08-29, https://www.youtube.com/watch?v=KdzOlRRHOU8).

---

## 1. Where to keep cash: vehicles, current rates, pros and cons

### 1.1 Comparison table (rates as of 2026-11-09/10)

| Vehicle | Current yield (example) | Liquidity | Principal protection | Federal tax | State/local tax | Best use |
|---|---|---|---|---|---|---|
| **HYSA** (online bank) | Top ~4.0–4.35% APY; many reputable banks 3.9–4.2% **[V]** | Same day to 1–3 business days by ACH; some banks limit to ~6 withdrawals/month | FDIC up to $250K per depositor, per bank, per ownership category | Ordinary income | **Taxable** | Emergency fund tier 1–2; short-term goals |
| **Money market fund** (gov't) | VMFXX ~3.96% (7-day) **[V, Yahoo, low confidence]**; SPAXX ~3.33% **[V, Yahoo, low confidence]**; SWVXX (prime) 3.75% **[V]** | T+0/T+1 inside a brokerage; next day to bank | **Not FDIC-insured**. Stable-NAV $1 target, holds gov't securities and repo | Ordinary income (dividends) | Partly exempt: only the share from direct US Treasury/agency obligations, varies by fund and year | Brokerage "sweep" / dry powder; EF tier 2 |
| **Treasury money market** (e.g., VUSXX, FDLXX, SNSXX) | Similar to gov't MMFs **[UNVERIFIED current yield]** | Same as MMFs | Not FDIC; holds Treasuries | Ordinary | Usually *mostly* exempt; check the fund's annual "US government obligations %" | High-state-tax residents |
| **T-bills (direct)** | 13-wk 4.22%, 26-wk 4.41%, 52-wk 4.54% (coupon-equiv.) **[V]** | At brokerage: sell anytime at market price, small price risk. At TreasuryDirect: hold to maturity or transfer out first | Full faith & credit of US | Ordinary (taxed in year of maturity/sale) | **Exempt** **[V]** (TreasuryDirect) | EF tier 3, ladders, sinking funds |
| **T-bill ETFs** SGOV / BIL | SGOV 30-day SEC 3.85%, ER 0.09% **[V]**; BIL 3.86%, ER 0.1352% **[V]** | Trades intraday; T+1 settlement | US Treasuries via fund; tiny price variance | Ordinary (dividends; not "qualified") | Generally exempt in most states as Treasury interest; CA/CT/NY apply 50%-asset tests **[UNVERIFIED specifics]** | Simple "T-bill ladder in one ticker" |
| **CDs** | 6-mo up to 4.40%; 1-yr up to 4.65%; 3-yr up to 4.85%; 5-yr up to 5.00% **[V]** (Bankrate) | Locked; early withdrawal penalty is typically months of interest | FDIC (bank) / NCUA (credit union) | Ordinary (taxed as accrued) | Taxable | Known-date goals; rate lock |
| **No-penalty CD** | Typically below top HYSA; rates not retrieved **[UNVERIFIED]** | Withdraw after ~7 days, no penalty | FDIC | Ordinary | Taxable | Rate lock with an exit option |
| **I bonds** | **5.51% composite (1.40% fixed)** for issues Nov 1, 2026–Apr 30, 2027 **[V]** | **Locked 12 months**; 3-month interest penalty if redeemed before 5 years **[V]** | US gov't | Ordinary, **deferrable** until redemption | **Exempt** **[V]** | Deep-reserve EF after year 1; inflation hedge |

### 1.2 HYSAs

**Top listed APYs (Bankrate, "as of 11/10/2026") [V]**
- CIT Bank 4.35%. This is promotional: a 3.75% base plus a 0.60% six-month bonus code expiring 12/15/2026, and requires a $5K minimum for the base tier.
- Peak Bank 4.27%.
- Happen Bank 4.20%, which requires $250 per month in deposits.
- EverBank 4.20%.
- Western State Bank 4.20%.
- Accordia 4.10%, best rates for Utah residents only.
- Vio Bank 4.06%.
- Bread Savings 4.05%.
- Live Oak 4.00%.
- TAB 3.95%.
- Bask 3.90%.
- Popular Direct 3.90%.

Source: https://www.bankrate.com/banking/savings/best-high-yield-interests-savings-accounts/

**Typical reliable large online providers** (rates not retrieved live; usually 0.2–0.6 pp below the top of the table) **[UNVERIFIED rates]**: Ally, Marcus by Goldman Sachs, Capital One 360 Performance Savings, American Express Personal Savings, Discover, SoFi, Synchrony, Barclays, Axos. Mark Tilbury's US picks in May 2025 were Axos 4.66% ($1,500 min), Openbank 4.40%, and Bread 4.35% (video description, "How To Manage Your Money Like The 1%", 2025-05-12, https://www.youtube.com/watch?v=QThz1B8SHmc). Those rates are stale but show his preference for online HYSAs.

**FDIC insurance facts [V]** (FDIC, "Understanding Deposit Insurance", https://www.fdic.gov/resources/deposit-insurance/understanding-deposit-insurance)
- Coverage is $250,000 per depositor, per insured bank, per ownership category. Categories include single, joint, certain retirement accounts (IRAs), trust, employee benefit, business, and government.
- Joint accounts give each co-owner $250K, separate from their single-account coverage.
- Not covered: stocks, bonds, **mutual funds (including money market *funds*)**, annuities, Treasuries, crypto, and safe-deposit contents. Money market *deposit accounts* (MMDAs) at banks **are** covered.
- "No depositor has lost a penny of FDIC-insured funds" since 1933.
- Trust accounts: since April 1, 2024, the rule is $250K per beneficiary, up to 5 beneficiaries ($1.25M per owner per bank). This comes from the FDIC's January 2022 final rule. **[From memory; the FDIC pages fetched did not detail it, and the trust-accounts brochure URL returned 404. Confirm with FDIC EDIE before relying on it.]**
- **Fintech caveat.** FDIC does not cover the default or bankruptcy of a *non-bank* intermediary such as an app or a BaaS middleware firm **[V, re-checked: FDIC insurance "doesn't cover default or bankruptcy of any non-FDIC-insured institution"]**. The middleware firm Synapse Financial filed for bankruptcy in April 2024, and customers of fintech apps built on it (e.g., Yotta, Juno) had their funds frozen for months, with a reported shortfall in the tens of millions of dollars **[from memory; exact figures UNVERIFIED]**. The app should recommend accounts held *directly* at an FDIC-insured bank, and should check the bank on FDIC BankFind (https://banks.data.fdic.gov/bankfind-suite/).

**HYSA pros:** FDIC-insured, no price volatility, instant to near-instant access, simple.
**HYSA cons:** variable rate (can be cut at any time), interest fully taxable at state level, promotional "teaser" rates, tiered balance requirements, withdrawal limits at some banks.

**App rule:** rank HYSAs by *sustainable base APY*, not promotional APY. Flag `promo=True`, `min_balance`, `deposit_requirement` and `residency_restriction`.

### 1.3 Money market funds (MMFs)

| Fund | Type | 7-day yield | ER | Min | Notes |
|---|---|---|---|---|---|
| VMFXX, Vanguard Federal MMF | Government | ~3.96% (Yahoo quote, date implied ~11/10/2026) **[V-low]** | ~0.11% **[UNVERIFIED]** | $3,000 at Vanguard **[UNVERIFIED]** | Default Vanguard settlement fund; average maturity 25 days (Yahoo) |
| SPAXX, Fidelity Government MMF | Government | ~3.33% (Yahoo, ~11/10/2026) **[V-low]** | ~0.42% **[UNVERIFIED]** | $0 | Default Fidelity core position. The higher ER explains much of the gap vs VMFXX. Fidelity users can often buy a higher-yield MMF (e.g., FDLXX Treasury Only, SPRXX) **[UNVERIFIED yields]** |
| SWVXX, Schwab Prime Advantage | **Prime** (corporate paper) | **3.75%** as of 11/10/2026 **[V]** | 0.34% net / 0.35% gross **[V]** | No minimum **[V]** | Prime funds carry slightly more credit risk than government funds. The SEC's July 2023 MMF reforms *removed* redemption gates. A *retail* prime fund like SWVXX can still impose a discretionary liquidity fee (up to 2%) if its board decides that is in shareholders' interest; mandatory fees apply only to *institutional* prime funds **[from memory of SEC Release 33-11211; not re-fetched]** |
| Crane 100 average | — | 3.75% (11/9/2026) **[V]** | — | — | Top retail funds ~3.94–3.98% (e.g., MWMXX 3.98%, IPPXX 3.97%) **[V]** |

**Data-quality note.** The VMFXX vs SPAXX spread (0.63 pp) is about twice the expense-ratio difference (~0.31 pp, if the remembered ERs are right), and Yahoo gave no explicit date for either yield. The sponsor pages could not be scraped on 2026-11-10 because their data loads dynamically. **The app must pull yields from the sponsor pages** (vanguard.com, fidelity.com, schwab.com) or the SEC's monthly N-MFP filings, and display the as-of date.

**State-tax treatment.** Government MMF dividends are partly state-exempt, in proportion to the income from direct US obligations (Treasuries, some agencies; *not* repo). Each sponsor publishes an annual "% of income from US government obligations" table. CA, CT and NY allow the exemption only if the fund met a 50% asset threshold at each quarter-end **[UNVERIFIED]**. Treasury-only funds usually have a much higher exempt percentage.

**Pros:** often yields more than big-bank HYSAs, lives inside the brokerage (instant to invest), possible state-tax benefit.
**Cons:** not FDIC-insured (though government MMFs have an extremely strong record), the yield floats with the Fed, and a prime fund adds credit risk.

### 1.4 Treasury bills and ladders

**Mechanics [V]** (TreasuryDirect, https://www.treasurydirect.gov/marketable-securities/treasury-bills/)
- Terms are 4, 6, 8, 13, 17, 26 and 52 weeks. The 52-week bill is auctioned every 4 weeks; all others weekly.
- Minimum purchase is $100, in $100 increments. Non-competitive bids up to $10M.
- Bills are sold at a discount and pay face value at maturity. The discount is the interest.
- Interest is subject to federal income tax, with **"No state or local taxes."**
- Cash Management Bills are only available through banks or brokers.

**TreasuryDirect vs brokerage**

| | TreasuryDirect | Brokerage (Fidelity/Schwab/Vanguard) |
|---|---|---|
| Fees | None | $0 commission at auction at major brokers **[UNVERIFIED for each]** |
| Selling before maturity | Not possible inside TD. You must transfer to a bank or broker, and TD requires the security to have been held in TD for at least 45 days before transfer **[from memory of TD's long-standing rule; TD FAQ and "selling" pages returned 404 on 2026-11-10]**. For a 4- or 6-week bill, that effectively means holding to maturity | Sell anytime on the secondary market (small bid/ask cost and price risk) |
| Auto-roll | Yes ("reinvest") | Yes (auto-roll at auction) |
| UX | Dated, separate login | Integrated with your investments; proceeds land in the MMF/sweep |
| Verdict for app | OK for I bonds (only source) | **Preferred for T-bills** |

**Ladder pattern for the emergency fund's deep tier.** Split the amount into N rungs. Example: 4 rungs of 13-week bills, one bought every ~3–4 weeks, or 4 rungs of 26-week bills bought ~6 weeks apart. Each rung auto-rolls. Something matures every few weeks, giving liquidity without needing to sell. The simpler equivalent is one ticker (SGOV/BIL), at a cost of 0.09–0.14% ER.

**Tax-equivalent yield (TEY).** This formula is required in the app. Treasury interest is state-exempt; bank interest is not. Ignoring the SALT-deduction interaction (most young filers take the standard deduction):

```
HYSA_equivalent_of_Tbill = y_tbill × (1 − t_fed) / (1 − t_fed − t_state)
after_tax_HYSA          = y_hysa × (1 − t_fed − t_state)
after_tax_Tbill         = y_tbill × (1 − t_fed)
after_tax_MMF           = y_mmf × (1 − t_fed − t_state × (1 − exempt_share))
after_tax_Ibond         = y_ibond × (1 − t_fed)        # plus deferral benefit
```

Worked check: 13-week bill at 4.22% for a California resident (22% federal, 9.3% CA) gives 4.22 × 0.78 / 0.687 = **4.79% HYSA-equivalent**. That beats every non-promotional HYSA. In a no-income-tax state (TX, FL, WA, etc.) the HYSA-equivalent is just 4.22%, so a 4.20% HYSA is about equal.

### 1.5 CDs and no-penalty CDs

**Top CD APYs (Bankrate, updated Nov 3–9, 2026) [V]**
- 3-month: 4.30% (Bank5 Connect, Popular Direct).
- 6-month: 4.40% (Quorum FCU, Happen, Accordia).
- 1-year: 4.65% (BTG Pactual, CFG Bank), Sallie Mae 4.60%.
- 3-year: 4.85% (BTG Pactual, Popular Direct).
- 5-year: 5.00% (Quorum FCU, Sallie Mae, Popular Direct).
- National 2-year average: 1.89%.

Source: https://www.bankrate.com/banking/cds/cd-rates/

The page's own commentary ("top rates around 4%") contradicts its tables, so treat this as indicative and re-check.

- **Early-withdrawal penalties** are bank-specific, commonly 90 days to 12 months of interest **[UNVERIFIED typical range]**. The app should compute break-even:

  ```
  net_yield_if_broken_at_m_months = (APY × m/12 − penalty_months × APY/12) / (m/12)
  ```
- **No-penalty CDs** (e.g., Ally, Marcus, CIT; rates not retrieved **[UNVERIFIED]**) lock a rate but allow withdrawal after about 7 days. They are useful if you expect HYSA rates to fall.
- **Brokered CDs** (bought at a brokerage): FDIC-insured, sellable on the secondary market (with price risk) instead of paying a penalty, and they consolidate with investments.
- **Nov 2026 context:** 1-year CD at 4.65% APY vs 52-week T-bill at 4.54% (state-exempt). With a 22% federal rate, the break-even state rate is about **1.9%**: 4.65 × (0.78 − s) = 4.54 × 0.78 gives s ≈ 1.85%. So in almost any state with an income tax, the T-bill wins after tax. Two caveats: CD APY and bill coupon-equivalent yields are not perfectly comparable, and the bill must be rolled at an unknown future rate if your horizon is longer. Compare with the TEY formula.

### 1.6 I bonds

**Current terms [V]** (https://www.treasurydirect.gov/savings-bonds/i-bonds/ and https://www.treasurydirect.gov/savings-bonds/i-bonds/i-bonds-interest-rates/, fetched 2026-11-10)
- **Composite 5.51%** for bonds issued **Nov 1, 2026 – Apr 30, 2027**: fixed rate **1.40%** plus semiannual inflation **2.04%**.
- Formula: `composite = fixed + 2×semiannual_inflation + fixed×semiannual_inflation`, giving 0.0140 + 0.0408 + 0.000286 ≈ 5.51%.
- **Purchase limit:** $10,000 per SSN per calendar year, electronic only. Paper I bonds via tax refund were discontinued Jan 1, 2025.
- **Lock:** cannot redeem in the first 12 months.
- **Penalty:** redeem before 5 years and you lose the last 3 months of interest.
- **Taxes:** federal only (deferrable until redemption or 30-year maturity); state/local exempt; possible education exclusion.

**Rate history [V, composites computed from stated components]**

| Issue period | Fixed | Semiannual infl. | Composite at issue |
|---|---|---|---|
| May–Oct 2023 | 0.90% | 1.69% | 4.30% |
| Nov 2023–Apr 2024 | 1.30% | 1.97% | 5.27% |
| May–Oct 2024 | 1.30% | 1.48% | 4.28% |
| Nov 2024–Apr 2025 | 1.20% | 0.95% | 3.11% |
| May–Oct 2025 | 1.10% | 1.43% | 3.98% |
| Nov 2025–Apr 2026 | 0.90% | 1.56% | 4.03% |
| May–Oct 2026 | 0.90% | 1.67% | 4.26% |
| **Nov 2026–Apr 2027** | **1.40%** | **2.04%** | **5.51%** |

A 1.40% fixed rate **ties the highest fixed rate since 2006**. TreasuryDirect's history shows 1.40% for both May 2006 and Nov 2006 issues, followed by 1.30% (May 2007) and 1.20% (Nov 2007); no fixed rate since then has exceeded 1.30% **[V, TreasuryDirect rate table, re-checked 2026-11-10]**. The fixed rate locks for 30 years, so this is a genuinely attractive real-return floor for a *deep* reserve.

*Note on the table above:* the "composite at issue" column is the rate each issue earned in its *first* six months. The variable part resets every six months for all bonds. TreasuryDirect shows that every bond is currently earning `its fixed rate + the 2.04% inflation component` (e.g., the 0.90%-fixed issues earn 5.00% for Nov 2026–Apr 2027).

**How the app should treat I bonds**
- Never use them for the first 12 months of emergency-fund needs.
- Count them as EF capacity only after month 12, valued at `balance − 3 months interest`.
- Penalty-adjusted effective yield when redeemed at month n (12 ≤ n < 60): `≈ r × (n − 3)/n`.

**Pros:** inflation-indexed, state-exempt, tax-deferred, zero default and price risk.
**Cons:** 12-month lock, $10K cap, TreasuryDirect UX, and the variable part resets every 6 months (it can fall).

### 1.7 Treasury ETFs (SGOV, BIL)

**SGOV, iShares 0–3 Month Treasury Bond ETF [V]** (https://www.ishares.com/us/products/314116/)
- 30-day SEC yield 3.85% (11/09/2026); 12-month trailing yield 3.63%.
- ER 0.09%; effective duration 0.11 years; monthly distributions; about 99.5% Treasury debt.

**BIL, SPDR Bloomberg 1–3 Month T-Bill ETF [V]** (https://www.ssga.com/us/en/intermediary/etfs/spdr-bloomberg-1-3-month-t-bill-etf-bil)
- 30-day SEC yield 3.86% (11/09/2026).
- ER 0.1352%; average maturity 0.13 years.

**Pros:** one-click T-bill exposure, intraday liquidity, no ladder management, Treasury interest that is generally state-exempt.
**Cons:**
- Not FDIC-insured.
- Distributions are ordinary income (not qualified dividends).
- The price drifts up during the month and drops at the ex-dividend date, which confuses beginners.
- Yield lags new-issue T-bills slightly. On 11/10 the 13-week bill was at 4.22% vs SGOV's 3.85% SEC yield, because rates rose recently and the fund's SEC yield is backward-looking over 30 days.

### 1.8 Decision summary: which cash vehicle for which job

| Job | Horizon | Recommended vehicle(s) | Why |
|---|---|---|---|
| Bill-paying buffer | 0–30 days | Checking (0.5–1× monthly expenses) | Avoid overdrafts; instant |
| EF "first responder" | Instant to 3 days | HYSA at an FDIC bank (direct, not via fintech middleware) | Insured, same-day/next-day access |
| EF deep tier | 1 week+ | T-bill ladder / SGOV / Treasury MMF in brokerage; I bonds only after 12 months | State-tax-exempt, higher after-tax yield |
| Known expense within 6–36 months (car, wedding, move) | Fixed date | CD or T-bill maturing *before* the date; brokered CD | Rate lock, no market risk |
| Brokerage dry powder | Days | Brokerage MMF (VMFXX/SPAXX/SWVXX) | Auto-sweep, immediate investing |
| Long-term wealth (10+ yrs) | 10+ years | **Not cash.** Diversified ETFs in tax-advantaged accounts | Real cash returns ≈ 0% (§2.4) |

---

## 2. Emergency fund sizing: research and rules

### 2.1 Evidence base

1. **Federal Reserve SHED 2025** (*Economic Well-Being of U.S. Households in 2025*, published May 13, 2026; survey of about 13,000 adults, October 2025) **[V, extracted from report PDF]**. https://www.federalreserve.gov/publications/files/2025-report-economic-well-being-us-households-202605.pdf
   - **63%** of adults would cover a $400 emergency expense entirely with "cash or its equivalent". This is unchanged for 3 years and down from 68% in 2021.
   - **55%** had a rainy-day fund covering **3 months** of expenses. This is unchanged from 2024 and down from 59% at the peak.
   - *Implication:* having a 3-month fund already puts the user ahead of about 45% of adults.

2. **BLS duration of unemployment** (Employment Situation Table A-12, October 2026, SA) **[V, re-checked 2026-11-10]**. https://www.bls.gov/news.release/empsit.t12.htm Note: the October 2025 figures were never collected because of the federal government shutdown, so year-over-year comparisons for that month are unavailable.
   - Median 11.9 weeks (about **2.7 months**); mean 27.1 weeks (about **6.2 months**).
   - **27.6%** of the unemployed had been out of work for 27+ weeks.
   - *Implication:* 3 months covers a median job-loss spell. About 6 months covers the mean, which is skewed by long spells. That is the empirical basis for the 3–6-month rule. Long-term unemployment is elevated at about 28%, which argues for leaning toward the upper half of the range in late 2026.

3. **Gjertson, L. M. (2016).** "Emergency Saving and Household Hardship." *Journal of Family and Economic Issues*, 37(1), 1–17 (published online 2014). https://doi.org/10.1007/s10834-014-9434-z (title, sole author, journal, volume and pages **[V via Semantic Scholar]**; closed access).
   - Studies the link between liquid emergency savings and material hardship after an income or expense shock. The claimed effects are that even modest liquid savings meaningfully reduce hardship, with a "first dollars matter most" pattern, and that the data are SIPP panel data. **[Data source, effect sizes and the "small balances" pattern are UNVERIFIED; abstract withheld by publisher. Do not quote numbers from this paper in the app.]**

4. **McKernan, S.-M., Ratcliffe, C., Braga, B., & Kalish, E. C. (2016, April 21).** *Thriving Residents, Thriving Cities: Family Financial Security Matters for Cities.* Urban Institute brief. https://www.urban.org/research/publication/thriving-residents-thriving-cities-family-financial-security-matters-cities
   - Families with even $250–$749 of nonretirement savings were less likely than families with no savings to be evicted, miss a housing or utility payment, or receive public benefits after an income disruption **[V, Urban Institute page]**.
   - The brief also reports that low-income families with $2,000–$4,999 saved were more financially resilient than middle-income families with no savings **[V]**. This is correlational evidence, not causal.

5. **Choi, J. J., Laibson, D., & Madrian, B. C. (2011).** "$100 Bills on the Sidewalk: Suboptimal Investment in 401(k) Plans." *Review of Economics and Statistics*, 93(3), 748–763. https://doi.org/10.1162/REST_a_00100 **[V]**.
   - Studies employees at seven firms who were over 59½ (so they could withdraw without penalty) and fully vested in the match.
   - At the typical firm, **36%** of these employees failed to contribute enough to get the full match. The forgone match averaged **1.6% of annual salary (about $507/yr)**. An educational survey telling them about the forgone money had no statistically significant effect **[V, abstract via Semantic Scholar]**.
   - *Relevance:* this is why "capture the full match" sits *above* even a full emergency fund in the order of operations.

6. **Vanguard research on emergency savings** (2022–23).
   - Vanguard has reported that having about $2,000 of emergency savings is associated with materially higher self-reported financial well-being. It has also reported that workers with emergency savings are less likely to take 401(k) hardship withdrawals or loans.
   - **[UNVERIFIED: two Vanguard URLs returned 404 on 2026-11-10; figures from memory. The app must not display a specific "% higher well-being" number from this study until a working source is found.]**

7. **Ben Felix: cash as a long-term asset.** "Cash is a terrible long-term investment, even at 5% interest" (2023-08-29), https://www.youtube.com/watch?v=KdzOlRRHOU8.
   - Cites Campbell & Viceira (2001), "Who Should Buy Long-Term Bonds?", *American Economic Review* 91(1):99–127, https://doi.org/10.1257/aer.91.1.99 (resolves **[V]**).
   - Also cites "Long-Horizon Losses in Stocks, Bonds, and Bills" (SSRN 3964908), "Crash Beliefs from Investor Surveys" (NBER w22143), and "Reaching for Yield: Evidence from Households" (SSRN 4283008). Source: video description **[V]**.
   - *Takeaway:* cash's stable nominal value hides real-return risk over long horizons. Keep cash for liquidity, not as an investment.

### 2.2 Standard rule and adjustments

**Baseline:** 3–6 months of *essential* expenses. Essentials are rent/mortgage, utilities, groceries, insurance premiums, minimum debt payments, transportation, and healthcare out-of-pocket. Exclude discretionary spending, which can be cut in an emergency.

**Recommended scoring model for the app.** These are heuristic weights synthesized from the BLS duration data and common planner practice, not from a single paper. They should be shown to the user as adjustable.

| Factor | Adjustment (months) |
|---|---|
| Base | 3.0 |
| Single-income household | +1.0 |
| Variable/commission/bonus-heavy pay, gig or 1099 income | +1.5 (self-employed: +3.0) |
| Low job security (layoff-prone industry, recent layoffs at employer, contract role) | +1.0 |
| Specialized or senior role (longer job searches) | +0.5 |
| Dependents (children, supporting parents) | +1.0 |
| Homeowner (repairs) or older car with no car fund | +0.5 |
| HDHP without HSA cash to cover the deductible | +0.5 |
| Chronic health condition / high expected medical costs | +0.5 |
| Dual, stable, uncorrelated incomes | −1.0 |
| Government/tenured/very-high-demand role | −0.5 |
| Reliable non-credit liquidity backstop (e.g., Roth IRA contribution basis) | −0.5 (combined cap with prior row: −1.0) |
| Macro overlay: long-term unemployment share > 25% (BLS A-12) | +0.5 (Nov 2026: 27.6%, so applies) |

```
target_months = clamp(3.0 + sum(adjustments), min=3.0, max=12.0)
EF_target     = target_months * M_essential
```

### 2.3 What the listed creators say about emergency funds and HYSAs

| Creator | Content found (title, date, URL) | What can be stated |
|---|---|---|
| **Marko (Whiteboard Finance)** | "The Exact Order To Invest Your Money (Most People Get This Wrong)", 2026-03-31, https://www.youtube.com/watch?v=VdLfZJVfLkY | Description **[V]**: "the emergency fund is the foundation that everything else depends on"; paying off high-interest debt is a "guaranteed return"; employer match "comes before almost everything else"; when the IRA should take priority over the 401(k); the HSA "triple tax advantage"; "taxable accounts are the last stop." The exact ranking is **[UNVERIFIED]**; the description lists EF, high-interest debt, match, IRA vs 401(k), HSA, taxable. He keeps a "favorite high yield savings accounts" affiliate page (whiteboardfinance.com/go/savings), so he endorses HYSAs for the EF. Also "The Only Money Plan You Should Follow For The Next 3 Months" (2026-10-26, https://www.youtube.com/watch?v=xxoqWfPAhhY); content **[UNVERIFIED]**. |
| **Graham Stephan** | "WHY YOU NEED AN EMERGENCY FUND" (2020-03-31, https://www.youtube.com/watch?v=0HS_O009unM), a caller who lost his job right after buying a house **[V, description]**. "I FOUND THE 5 BEST BANK ACCOUNTS OF 2023" (2023-04-17, https://www.youtube.com/watch?v=jeq2JaJqbqM). "Why you should have $5M in T-Bills" (2026-05-13, https://www.youtube.com/watch?v=K7mR2Psti3o). "Why EVERYTHING Changes After You Save $10K" (2025-04-28, https://www.youtube.com/watch?v=ZAPhN6o3e3o) | A long-time HYSA advocate who in 2026 also promotes T-bills for cash. His $10K video description says at $100K "you have enough to protect yourself against most major financial emergencies" **[V]**. He commonly recommends about 6 months of expenses in a HYSA **[UNVERIFIED; widely attributed, not confirmed from transcript]**. His channels carry substantial affiliate and sponsor links. |
| **Ben Felix** | KdzOlRRHOU8 (above). "The Biggest Myths in Personal Finance" (2026-07-26, https://www.youtube.com/watch?v=3u-mP3C26mg), including "Myth #7: Bonds and Cash Are Safe Investments" and "Myth #1: Saving As Much As You Can Early" (life-cycle model) **[V, timestamps in description]** | Cash is safe only for short horizons; its long-term real return is poor and risky. The life-cycle critique implies you should not over-hoard cash or over-save at the expense of current wellbeing. A specific EF month count is **[UNVERIFIED]**. |
| **Andrei Jikh** | "My 5 BEST Savings Accounts - RECESSION PROOF" (2022-11-07, https://www.youtube.com/watch?v=OMGHRrYE0qA). "Do This NOW - Once You Save $1,000!" (2020-08-14, https://www.youtube.com/watch?v=dOHjKLmDiqg) | The 2020 description lists step #1 as "invest in yourself" **[V]**. The 2022 video covers his preferred savings vehicles in a recession; specific products **[UNVERIFIED]**. |
| **Mark Tilbury** | "How To Manage Your Money Like The 1%" (2025-05-12, https://www.youtube.com/watch?v=QThz1B8SHmc) | A "25/15/50/10 rule" for income allocation **[V, description]**; category mapping **[UNVERIFIED]**. US HYSA picks: Axos 4.66%, Openbank 4.40%, Bread 4.35% **[V, May 2025 rates]**. |
| **Sven Carlin** | "How Much CASH To Hold In Portfolio? My Framework" (2024-10-11, https://www.youtube.com/watch?v=AN9iDrtp1NI). "What are safe investments? Cash is safe, but only short term!" (2020-04-02, https://www.youtube.com/watch?v=NQIVKRQNl7Y) | Description **[V]**: there is no correct answer on cash holdings, because "it all depends on the future." "Currencies are made to lose their purchasing power over time, so the safest investment are real assets despite their volatile prices." This concerns *portfolio* cash (an opportunity reserve), separate from the EF. |
| **The Plain Bagel (Richard Coffin)** | "Cash Equivalents & Alternatives - Non Traditional Investments" (2017-11-10, https://www.youtube.com/watch?v=mtcBKGbyE70) | An educational overview of cash equivalents **[V]**. No dedicated EF/HYSA video found **[UNVERIFIED that none exists]**. |
| **Joseph Carlson** | No dedicated EF/HYSA video found. "How To Build Wealth In 3 Steps" (2022-03-25, https://www.youtube.com/watch?v=0aGAhPQIpU4) discusses household cash flows **[V, description]** | No verified EF guidance. The "Carlson Financial – Emergency Funds" video is a *different* creator. |

**Consensus across creators and research:**
- (a) Keep an EF in an insured, liquid, interest-bearing account (HYSA, MMF, T-bills).
- (b) Capture the employer match and kill high-interest debt early.
- (c) Do not treat cash as a long-term investment.

The main disagreement is sizing and ordering: whether the full EF comes before or after the match and debt payoff. The app resolves this with a "starter EF" step (§3.1).

### 2.4 Opportunity-cost check (for user education)

Holding cash for 10 years versus a diversified portfolio:
- Cash at about 4.2% nominal minus an assumed long-run inflation of about 3% gives about 1.2% real before tax. At today's 3.7% CPI, it is only about 0.5%, and roughly zero after tax.
- Diversified equities have historically earned about 5% real or more over long periods (US data). They also had decade-long periods of negative real returns, e.g., 2000–2009 **[long-run estimate; UNVERIFIED for any future period]**.

On $6,000 over 10 years, this is roughly 6,000 × (1.05^10 − 1.012^10), or about $3,000 of real growth forgone *in expectation, not guaranteed*. That is why "excess" cash should be invested. The app should show this with user-adjustable assumptions, not as a forecast.

---

## 3. US "order of operations" for a young professional (2026)

### 3.1 The priority ladder

| Step | Action | Rationale / evidence |
|---|---|---|
| 0 | **Starter EF:** 1 month of essentials (or $2,000, whichever is larger) in a HYSA | Even small liquid balances ($250–$749) are associated with lower hardship after a shock (McKernan et al. 2016 **[V]**; correlational). See also Gjertson 2016 and Vanguard's ~$2K finding **[both UNVERIFIED in detail]**. Prevents new credit-card debt while you climb the ladder |
| 1 | **Contribute enough to get the full 401(k) employer match** | Typically an instant 50–100% return. Choi, Laibson & Madrian (2011) found that 36% of penalty-free-eligible employees at the typical firm left an average of 1.6% of salary on the table **[V]**. Check vesting: a match that vests on a schedule is worth less if you may leave soon |
| 2 | **Pay off high-interest debt** (APR > ~8%; credit cards first, by avalanche: highest APR first) | Repaying a 22% card is a risk-free 22% return (Whiteboard Finance: "guaranteed return" **[V]**). For 4–8% APR debt, compare to expected after-tax returns; it can be paid off at the normal pace while investing |
| 3 | **Full emergency fund:** target_months × M (§2.2) | Covers median to mean unemployment spell (BLS) |
| 4 | **HSA (if enrolled in an HSA-eligible plan):** max it, invest it, pay current medical costs out of pocket if possible, keep receipts | Triple tax advantage at the federal level: deductible, tax-free growth, tax-free for qualified medical expenses. CA and NJ do not follow federal HSA treatment, so contributions are not state-deductible and earnings are state-taxable there **[from memory; widely documented; confirm with state tax agency]** |
| 5 | **IRA: Roth vs Traditional** (decision rules in §3.4); max $7,500 | IRA offers wider fund choice and lower costs than many 401(k) plans. Under the Roth IRA ordering rules (IRS Pub 590-B), regular contributions come out first and can be withdrawn anytime tax- and penalty-free. That makes them a secondary liquidity backstop. Earnings, and converted amounts within 5 years of conversion if under 59½, can be taxed or penalized **[ordering rule well established; Pub 590-B not re-fetched]** |
| 6 | **Increase 401(k) toward the $24,500 maximum** | Tax-advantaged space is "use it or lose it" annually |
| 7 | **Mega-backdoor Roth** (after-tax 401(k) contributions + in-plan Roth conversion) if the plan allows. The room is $72,000 (§415(c)) *minus* your deferrals and employer contributions | Only some plans permit it |
| 8 | **Taxable brokerage:** tax-efficient broad ETFs | Last stop (Whiteboard Finance **[V]**). Use it also for goals before 59½ |

**Parallel buckets.** Saving for a home down payment within about 5 years, or other mid-term goals, belongs in cash/T-bills/CDs (not stocks) and can run alongside steps 5–8. The user sets a priority weight.

### 3.2 2026 contribution limits **[V]**

Sources: IRS IR-2025-111 (Nov 13, 2025), https://www.irs.gov/newsroom/401k-limit-increases-to-24500-for-2026-ira-limit-increases-to-7500; IRS Notice 2025-67 (text extracted from PDF), https://www.irs.gov/pub/irs-drop/n-25-67.pdf; Rev. Proc. 2025-19 (HSA), https://www.irs.gov/pub/irs-drop/rp-25-19.pdf.

| Item | 2026 | 2025 |
|---|---|---|
| 401(k)/403(b)/gov't 457/TSP employee deferral (§402(g)) | **$24,500** | $23,500 |
| Catch-up, age 50+ | **$8,000** (total $32,500) | $7,500 |
| "Super" catch-up, age 60–63 (SECURE 2.0) | **$11,250** (in place of $8,000) | $11,250 |
| Total defined-contribution limit (§415(c), employee + employer + after-tax) | **$72,000** | $70,000 |
| Compensation limit (§401(a)(17)) | **$360,000** | $350,000 |
| HCE threshold (§414(q)) | **$160,000** | $160,000 |
| **Mandatory Roth catch-up** if prior-year FICA wages exceed (§414(v)(7)) | **$150,000** (2025 wages) | $145,000 |
| IRA (Traditional + Roth combined) | **$7,500** | $7,000 |
| IRA catch-up, 50+ (now inflation-indexed) | **$1,100** | $1,000 |
| SIMPLE deferral | $17,000 ($18,100 "applicable" plans) | $16,500 |
| **HSA, self-only** | **$4,400** | $4,300 (Rev. Proc. 2024-25; from memory, not re-fetched) |
| **HSA, family** | **$8,750** | $8,550 (Rev. Proc. 2024-25; from memory, not re-fetched) |
| HSA catch-up, 55+ | $1,000 (fixed by statute in IRC §223(b)(3)(B), not inflation-indexed) | $1,000 |
| HDHP minimum deductible | $1,700 self / $3,400 family | — |
| HDHP max out-of-pocket | $8,500 self / $17,000 family | — |
| Saver's Credit AGI ceilings | $40,250 single / $60,375 HoH / $80,500 MFJ | $39,500 / $59,250 / $79,000 |

**Deadlines.** 401(k) deferrals are counted by calendar year through payroll, so they must happen by Dec 31, 2026. **2026 IRA and HSA contributions can be made until April 15, 2027.** In Nov 2026 the user can still fund *both* a 2026 IRA (now) and a 2027 IRA (from Jan 1, 2027).

**HSA changes under the One Big Beautiful Bill Act** (IRS Notice 2026-05, Dec 9, 2025) **[V]** (https://www.irs.gov/newsroom/treasury-irs-provide-guidance-on-new-tax-benefits-for-health-savings-account-participants-under-the-one-big-beautiful-bill)
- From Jan 1, 2026, **bronze and catastrophic plans are HSA-compatible** even if they do not meet the HDHP definition, whether or not they are bought on an Exchange.
- **Direct primary care** arrangements no longer disqualify HSA eligibility, and DPC fees can be paid from the HSA. This applies only to arrangements under a monthly fee cap (about $150 self-only / $300 family, indexed) **[fee cap from memory of OBBBA text; UNVERIFIED]**.
- **Telehealth before the deductible** is permanently allowed (plan years beginning on or after Jan 1, 2025).

### 3.3 Roth IRA income limits and backdoor Roth (2026) **[V]**

| Filing status | Roth IRA contribution phase-out (MAGI) |
|---|---|
| Single / HoH | **$153,000 – $168,000** |
| MFJ | **$242,000 – $252,000** |
| MFS | $0 – $10,000 |

**Traditional IRA *deduction* phase-out when covered by a workplace plan [V]**
- Single: **$81,000 – $91,000**.
- MFJ, contributor covered: $129,000 – $149,000.
- MFJ, contributor not covered but spouse is: $242,000 – $252,000.

*Practical consequence:* a single young professional with a 401(k) who earns over $91K **cannot deduct** a Traditional IRA contribution. Their choice is Roth IRA (if under $153K) or backdoor Roth (if over).

**Backdoor Roth procedure**
1. Make a non-deductible Traditional IRA contribution (up to $7,500) and file Form 8606.
2. Convert to Roth, typically soon after, to minimize taxable growth.
3. **Pro-rata rule [V]** (Form 8606 instructions, https://www.irs.gov/instructions/i8606): all traditional, SEP and SIMPLE IRAs are aggregated. Their combined value **on December 31** of the conversion year determines what fraction of the conversion is taxable. Any pre-tax IRA balance, such as an old 401(k) rolled into an IRA, makes part of the conversion taxable.
   - *Fix:* roll pre-tax IRA money *into* the current 401(k) (if the plan accepts roll-ins) before Dec 31.
4. The app should compute:

   ```
   taxable_fraction = 1 − basis / (Dec31_total_trad_IRA_value + conversion_amount)
   ```

   This is simplified; actual Form 8606 lines 6–13 also include distributions and outstanding rollovers.
5. **Other caveats.**
   - Each conversion starts its own 5-year clock for the 10% penalty if you are under 59½. Converted amounts are therefore *not* as liquid as regular Roth contributions.
   - The pro-rata test uses the Dec 31 balance. Rolling an old 401(k) into an IRA *later in the same year* still contaminates that year's conversion.

### 3.3a SECURE 2.0 features a young saver should know (missing from earlier drafts)

These are from memory of SECURE 2.0 (Dec 2022) and are **not re-fetched**. Confirm each one with the plan administrator, because most are optional for employers.
- **Emergency personal expense distribution (from 2024):** up to $1,000 per year from a 401(k)/IRA without the 10% penalty (still taxable). After one, you cannot take another for 3 years unless it is repaid. This is a last-resort backstop, *not* an EF substitute.
- **Pension-linked emergency savings accounts (PLESA):** if the employer offers one, Roth after-tax contributions up to $2,500 and liquid on demand. Can count toward EF tier 2.
- **Student-loan "match":** employers *may* match qualified student-loan payments as if they were 401(k) deferrals. If offered, paying the loan can capture the match without contributing (affects step 1).
- **Saver's Credit becomes the "Saver's Match" from tax year 2027:** a federal matching deposit (50% of up to $2,000) into a retirement account for lower-income savers. The app should flag eligibility from 2027 onward.
- **Roth 401(k)s have no RMDs** (from 2024), which makes the Roth 401(k) more comparable to a Roth IRA.

### 3.4 Roth vs Traditional decision rules

The core principle: **contribute pre-tax when your marginal rate today is above your expected marginal rate in retirement; contribute Roth when it is below.** Equal rates make them mathematically equivalent if you contribute equivalent amounts. Roth effectively shelters *more* when you max out the account, because a $7,500 Roth holds more after-tax dollars than a $7,500 Traditional.

**2026 federal brackets [V]** (IRS IR-2025-103, Oct 9, 2025; Rev. Proc. 2025-32), https://www.irs.gov/newsroom/irs-releases-tax-inflation-adjustments-for-tax-year-2026-including-amendments-from-the-one-big-beautiful-bill

| Rate | Single taxable income over | MFJ over |
|---|---|---|
| 10% | $0 | $0 |
| 12% | $12,400 | $24,800 |
| 22% | $50,400 | $100,800 |
| 24% | $105,700 | $211,400 |
| 32% | $201,775 | $403,550 |
| 35% | $256,225 | $512,450 |
| 37% | $640,600 | $768,700 |

Standard deduction: $16,100 single / $32,200 MFJ / $24,150 HoH.

**App heuristic** (single filer; t = federal marginal rate + state marginal rate):

| Current federal bracket | Default recommendation |
|---|---|
| 10–12% | **Roth** (Roth IRA + Roth 401(k) if offered) |
| 22% | **Roth-leaning or split.** Roth IRA; 401(k) split ~50/50 or Roth if you expect income growth. Traditional if you live in a high-tax state and plan to retire in a no-tax state |
| 24% | **Split / Traditional-leaning** 401(k); backdoor or direct Roth IRA |
| 32%+ | **Traditional 401(k)**; backdoor Roth IRA |

**Modifiers**
- Expected large income growth: shift toward Roth.
- Plan to retire early (Roth-conversion ladder opportunity): shift toward Traditional.
- Want tax diversification: hold some of each.
- Employer match is always pre-tax unless the plan offers SECURE 2.0 Roth matching **[UNVERIFIED plan-by-plan]**.

**Research support for tax-diversification and Roth reasoning [UNVERIFIED in detail]:** Kitces and Reichenstein practitioner literature; Dammon, Spatt & Zhang (2004), below.

### 3.5 Tax-efficient asset location

Principle: put the *least tax-efficient* assets in tax-advantaged accounts and the *most tax-efficient* in taxable.

- **Dammon, R. M., Spatt, C. S., & Zhang, H. H. (2004).** "Optimal Asset Location and Allocation with Taxable and Tax-Deferred Investing." *Journal of Finance*, 59(3), 999–1037. https://doi.org/10.1111/j.1540-6261.2004.00655.x (authors, title, volume and pages **[V via Semantic Scholar]**).
  - Finds a strong preference for holding taxable bonds in tax-deferred accounts and equities in taxable accounts.
- **Huang, J. (2008).** "Taxable and Tax-Deferred Investing: A Tax-Arbitrage Approach." *Review of Financial Studies*, 21(5), 2173–2207. https://doi.org/10.1093/rfs/hhm082 (sole author Jennifer Huang; title, issue and pages **[V, OUP article page]**).
  - Reaches a similar bonds-in-tax-deferred conclusion via tax arbitrage.
- **Shoven, J. B., & Sialm, C. (2004).** "Asset Location in Tax-Deferred and Conventional Savings Accounts." *Journal of Public Economics*, 88(1–2), 23–38. https://doi.org/10.1016/S0047-2727(02)00083-X (journal, volume, pages and ScienceDirect PII **[V via RePEc/IDEAS]**; the DOI resolves to Elsevier's linking hub for that PII). An earlier version was NBER Working Paper 7192.
  - Same direction, with nuance for tax-efficient equity funds.
- **Evidence caveat:** these are *model-based* results (optimal portfolio models under stylized tax rules), not empirical studies of investor outcomes. The expected gain from location is real but modest at small balances.

**Location table for an all-ETF moderate-risk investor**

| Asset | Best location | Why |
|---|---|---|
| Taxable bonds / bond funds, TIPS | 401(k) / Traditional IRA | Interest taxed as ordinary income |
| REITs | Tax-deferred or Roth | Non-qualified dividends |
| Highest expected-return / higher-volatility equity (small-cap value, factor ETFs) | Roth IRA / HSA | Tax-free growth on the biggest expected gains |
| Broad US total-market / S&P 500 ETF | Taxable (or anywhere) | Low turnover, qualified dividends, ETF in-kind redemption |
| International equity ETF | Taxable preferred | Foreign tax credit is only usable in taxable accounts |
| Cash / T-bills (EF) | Taxable (HYSA/brokerage) | Must be liquid; T-bills keep a state-tax edge |

**Caveat:** at small balances, simplicity beats optimization. With under ~$50K invested, holding the *same* target-allocation ETF mix in every account is acceptable. The app should only suggest location tweaks when the estimated tax drag saved exceeds ~0.1%/yr of the portfolio.

---

## 4. Worked decision framework: splitting ~$20K of savings

### 4.1 Inputs (app form)

| Symbol | Input | Default |
|---|---|---|
| `S` | Total liquid savings | 20,000 |
| `M` | Monthly **essential** expenses | *required, no default* |
| `adj` | EF adjustment flags (§2.2) | none |
| `D_hi` | High-interest debt balance (APR > 8%) | 0 |
| `K[i], date[i]` | Known expenses in the next 36 months (amount, date) | [] |
| `match_gap_annual` | Extra yearly 401(k) contribution needed to get the full match | 0 |
| `months_left_in_year` | For match / IRA timing | computed |
| `ira_room_2026`, `ira_room_2027` | Unused IRA room (7,500 − contributed) | 7,500 each |
| `hsa_eligible`, `hsa_room` | HSA eligibility and room | False / 4,400 |
| `t_fed`, `t_state` | Marginal tax rates | 0.22 / user state |
| `risk_profile` | Conservative / Moderate / Aggressive | Moderate |
| `horizon_years` | Investing horizon | 10 |

### 4.2 Algorithm

```python
def split_savings(S, M, adj_months, D_hi, known_expenses, match_gap_annual,
                  ira_room, hsa_room, hsa_eligible, today):
    # 1. Emergency fund target
    target_months = min(max(3.0 + adj_months, 3.0), 12.0)
    EF = target_months * M

    # 2. Sinking funds: known expenses within 36 months
    #    (discount optional; keep nominal for safety)
    SF = sum(k.amount for k in known_expenses if months_until(k.date, today) <= 36)

    # 3. High-interest debt: pay off from cash, but never drop below the
    #    starter EF (max(1*M, 2000))
    starter = max(1.0 * M, 2000)
    debt_pay = min(D_hi, max(0, S - starter))
    remaining = S - debt_pay

    # 4. Fill the EF, then sinking funds
    ef_alloc = min(EF, remaining); remaining -= ef_alloc
    sf_alloc = min(SF, remaining); remaining -= sf_alloc
    excess = remaining                       # investable

    # 5. Deploy the excess in priority order
    plan = {}
    # 5a. Match-gap bridge: hold cash to cover the paycheck shortfall from
    #     raising the 401(k) deferral to the match level for the rest of the year
    match_bridge = min(excess, match_gap_annual * months_left_in_year(today) / 12)
    plan["401k_match_bridge_cash"] = match_bridge; excess -= match_bridge
    # 5b. HSA
    if hsa_eligible:
        h = min(excess, hsa_room); plan["HSA"] = h; excess -= h
    # 5c. IRA (current year first, then next year after Jan 1)
    i = min(excess, ira_room); plan["IRA"] = i; excess -= i
    # 5d. Remaining: 401(k) increase bridge or taxable brokerage
    plan["taxable_or_401k_increase"] = excess
    return EF, ef_alloc, SF, sf_alloc, debt_pay, plan
```

### 4.3 EF tiering inside the allocation

```
tier1_checking = 0.5 * M                        # in checking, outside EF math (optional)
tier2_hysa     = min(ef_alloc, 3 * M)            # instant access
tier3_tbills   = max(0, ef_alloc - tier2_hysa)   # SGOV / T-bill ladder / Treasury MMF
# I bonds: only for money beyond 12 months of need; count as EF after 12 months
```

- **Tier choice rule.** Choose tier 3 in T-bills instead of HYSA when `after_tax_Tbill > after_tax_HYSA + 0.10%`, using the TEY formula in §1.4.
- **Moderate-risk investor note.** Do *not* hold the EF in a stock or bond fund. A 2022-style drawdown hit stocks and bonds together, and that is exactly when job-loss risk rises.

### 4.4 Deploying the excess: lump sum vs DCA

- Lump-sum investing has historically beaten dollar-cost averaging about two-thirds of the time, because markets rise more often than they fall. Source: Vanguard (2012), "Dollar-cost averaging just means taking risk later," Shtekhman, Tasopoulos & Wimmer. This is a Vanguard practitioner research note, not peer-reviewed; it covered US, UK and Australian data over rolling 12-month DCA periods **[UNVERIFIED URL; title, authors and ~2/3 finding from memory]**.
- **For a moderate-risk user who would be stressed by drawdowns,** the app default is: invest the excess over **3–6 equal monthly installments** if it exceeds 25% of the existing portfolio; otherwise invest it as a lump sum. This trades a small expected cost for behavioral adherence.
- **Investing target.** The investment mix should come from the separate allocation module (e.g., a 70–80% equity global ETF mix for moderate risk over 10+ years). That is outside this document.

### 4.5 Worked examples (S = $20,000, single, renter, stable W-2 job, no high-interest debt, match already captured, not HSA-eligible, Nov 2026)

Macro overlay applies (+0.5 months, BLS long-term unemployment 27.6%). Adjustments: single income +1.0, macro +0.5. **target_months = 4.5.**

| | M = $2,500 | M = $3,500 | M = $4,500 |
|---|---|---|---|
| EF target (4.5 × M) | $11,250 | $15,750 | $20,250 |
| Sinking funds (example: $1,500 car insurance + travel in 2027) | $1,500 | $1,500 | $0 (EF not yet full) |
| Tier 2 HYSA (≤ 3 × M) | $7,500 | $10,500 | $13,500 |
| Tier 3 T-bills / SGOV | $3,750 | $5,250 | $6,500 (EF $250 short) |
| **Excess** | **$7,250** | **$2,750** | **$0** |
| 2026 Roth IRA (fund by 4/15/2027) | $7,250 | $2,750 | $0 |
| 2027 Roth IRA / taxable | Then route new monthly savings | Monthly savings | Monthly savings → finish EF first |

**Interpretation for the user's stated situation** (about $20K, "part EF + part excess"):
- If essential spending is about $3,000/month, the 4.5-month EF is $13,500 and, with no sinking funds, roughly **$6,500 is investable.**
- The best home for that excess is a **2026 Roth IRA contribution ($7,500 room)** if eligible (MAGI < $153K single). Then contribute the 2027 IRA in January from ongoing savings.
- The EF sits in a HYSA (about $9,000) plus SGOV/T-bills (about $4,500). The T-bills are state-tax-advantaged.

**Sensitivity:** each $500/month change in M shifts the investable excess by $2,250 at 4.5 months.

### 4.6 Guardrails the app must enforce

- Never allocate below the starter EF to anything other than high-interest debt.
- Warn if `excess > 0` but the match is not captured (step 1 not done).
- Warn if a known expense within 24 months is allocated to equities.
- Warn if HYSA APY is under 3.0% when top-tier APYs are 4%+ (the bank is underpaying).
- Warn if more than $250K is at one bank in one ownership category (FDIC limit).
- I bonds: block counting them as EF before 12 months of holding.
- Show as-of dates for every rate; mark rates older than 7 days as stale.
- Disclaimer: educational, not individualized advice. Taxes vary by state.

---

## 5. Data sources the app should refresh (all free)

| Data | Source | Refresh |
|---|---|---|
| T-bill daily rates | Treasury "Daily Treasury Bill Rates" (XML/CSV) and Fiscal Data API https://fiscaldata.treasury.gov/api-documentation/ ; FRED series DTB4WK, DTB3, DTB6, DTB1YR (free API key) | Daily |
| Fed funds range | FRED DFEDTARU / DFEDTARL | On FOMC days |
| CPI-U | BLS API v2 (CUUR0000SA0), FRED CPIAUCSL | Monthly |
| Unemployment duration | BLS LNS13008275 (median weeks), LNS13008276 (mean) **[UNVERIFIED series IDs]**; FRED UEMPMED | Monthly |
| I bond rate | TreasuryDirect I bond page (scrape, May 1 / Nov 1) | Semiannual |
| HYSA / CD rates | Manual curated JSON (Bankrate/DepositAccounts pages, user verification); no free official API | Weekly, user-confirmed |
| MMF / ETF yields | Sponsor pages (Vanguard, Fidelity, Schwab, iShares, SSGA); `yfinance` as fallback (low confidence) | Weekly |
| IRS limits | Static JSON per tax year from IRS news releases/notices | Annually (Oct–Nov) |

Store each rate as `{value, unit, as_of, source_url, confidence: high|medium|low}`.

---

## 6. Open questions to ask the user

1. Monthly essential expenses (M), and whether the $20K is already split across accounts.
2. Any high-interest debt (credit cards, personal loans) or student loans (and their APRs)?
3. Does the 401(k) match vest immediately? What percent of salary is the match, and is it currently captured?
4. Approximate MAGI (for Roth eligibility and bracket) and state of residence (for T-bill vs HYSA tax math).
5. Is the health plan HSA-eligible (HDHP, or a bronze/catastrophic plan under the 2026 rules)?
6. Any known large expenses within 3 years (car, wedding, move, home down payment)?
7. Job stability: industry, single vs dual income, dependents.
8. Any pre-tax IRA balances (they affect backdoor Roth pro-rata)?
9. Does the employer offer a PLESA, student-loan matching, Roth matching, or after-tax (mega-backdoor) contributions?
10. How much has already been contributed to a 2026 IRA/401(k)/HSA (sets `ira_room_2026` etc.)?

---

## 7. Source list (fetched 2026-11-10 unless noted)

- Federal Reserve, Open Market Operations: https://www.federalreserve.gov/monetarypolicy/openmarket.htm
- Federal Reserve, SHED 2025 report (May 13, 2026): https://www.federalreserve.gov/publications/files/2025-report-economic-well-being-us-households-202605.pdf
- BLS CPI: https://www.bls.gov/cpi/ · BLS Table A-12: https://www.bls.gov/news.release/empsit.t12.htm
- U.S. Treasury Daily Bill Rates (Nov 2026): https://home.treasury.gov/resource-center/data-chart-center/interest-rates/TextView?type=daily_treasury_bill_rates&field_tdr_date_value_month=202611
- TreasuryDirect T-bills: https://www.treasurydirect.gov/marketable-securities/treasury-bills/
- TreasuryDirect I bonds: https://www.treasurydirect.gov/savings-bonds/i-bonds/ · rates: https://www.treasurydirect.gov/savings-bonds/i-bonds/i-bonds-interest-rates/
- IRS IR-2025-111 (2026 retirement limits): https://www.irs.gov/newsroom/401k-limit-increases-to-24500-for-2026-ira-limit-increases-to-7500
- IRS Notice 2025-67: https://www.irs.gov/pub/irs-drop/n-25-67.pdf
- IRS Rev. Proc. 2025-19 (HSA 2026): https://www.irs.gov/pub/irs-drop/rp-25-19.pdf
- IRS Notice 2026-05 / OBBBA HSA guidance: https://www.irs.gov/newsroom/treasury-irs-provide-guidance-on-new-tax-benefits-for-health-savings-account-participants-under-the-one-big-beautiful-bill
- IRS IR-2025-103 (2026 brackets): https://www.irs.gov/newsroom/irs-releases-tax-inflation-adjustments-for-tax-year-2026-including-amendments-from-the-one-big-beautiful-bill
- IRS Form 8606 instructions: https://www.irs.gov/instructions/i8606
- FDIC deposit insurance: https://www.fdic.gov/resources/deposit-insurance/understanding-deposit-insurance
- Bankrate HYSA: https://www.bankrate.com/banking/savings/best-high-yield-interests-savings-accounts/ · CDs: https://www.bankrate.com/banking/cds/cd-rates/
- Crane Data: https://www.cranedata.com/
- Schwab SWVXX: https://www.schwabassetmanagement.com/products/swvxx
- iShares SGOV: https://www.ishares.com/us/products/314116/ishares-0-3-month-treasury-bond-etf
- SSGA BIL: https://www.ssga.com/us/en/intermediary/etfs/spdr-bloomberg-1-3-month-t-bill-etf-bil
- Yahoo Finance VMFXX / SPAXX (low confidence): https://finance.yahoo.com/quote/VMFXX/ · https://finance.yahoo.com/quote/SPAXX/
- Papers (metadata re-checked 2026-11-10 via Semantic Scholar, OUP, RePEc and Urban Institute): Gjertson 2016, JFEI 37(1):1–17 (doi:10.1007/s10834-014-9434-z); Choi, Laibson & Madrian 2011, REStat 93(3):748–763 (doi:10.1162/REST_a_00100); Dammon, Spatt & Zhang 2004, JF 59(3):999–1037 (doi:10.1111/j.1540-6261.2004.00655.x); Huang 2008, RFS 21(5):2173–2207 (doi:10.1093/rfs/hhm082); Campbell & Viceira 2001, AER 91(1):99–127 (doi:10.1257/aer.91.1.99; pages not re-checked); Shoven & Sialm 2004, JPubE 88(1–2):23–38 (doi:10.1016/S0047-2727(02)00083-X); McKernan et al. 2016, Urban Institute brief **[V]**; Vanguard DCA 2012 **[UNVERIFIED]**; Vanguard emergency-savings research **[UNVERIFIED]**.
- Creator videos: URLs inline in §2.3.
