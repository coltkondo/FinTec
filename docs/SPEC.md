# Investment Research Assistant: Specification

| | |
|---|---|
| **Document** | `docs/SPEC.md` (spec sheet; implementation plan and workflow live in separate docs) |
| **Version** | 1.0 draft |
| **Date** | 2026-11-10 |
| **Product** | Local Python + Streamlit "Investment Research Assistant" (codename FinTec) |
| **Budget** | $0. Free data only. The optional LLM must use free tiers or run locally |
| **Status** | Draft for user review. Open questions are in §17 |

> **Educational decision-support tool, not financial advice.** The app helps one person research and document their own decisions. It does not execute trades, it is not a registered adviser, and it never guarantees outcomes.

---

## 0. Source briefs and how they are cited

Every key decision in this spec cites one of the research briefs below. The tag in the left column is used throughout as **[tag §section]**.

| Tag | Brief | Covers |
|---|---|---|
| **[ACAD]** | `docs/research/academic_evidence.md` | Peer-reviewed evidence, program rules, default weights, guardrails |
| **[CR-A]** | `docs/research/influencers_ben_felix_plain_bagel_whiteboard_graham.md` | Ben Felix, The Plain Bagel, Whiteboard Finance, Graham Stephan; creator JSON schema; app rule IDs |
| **[CR-B]** | `docs/research/influencers_tilbury_jikh_carlin_carlson.md` | Mark Tilbury, Andrei Jikh, Sven Carlin, Joseph Carlson; principle schema; seed records |
| **[MKT]** | `docs/research/market_context_2026.md` | Valuations, rates, inflation, concentration, themes, institutional CMAs, refresh checklist |
| **[SAV]** | `docs/research/savings_and_account_strategy.md` | Cash vehicles, emergency-fund sizing, account order of operations, 2026 limits, $20K split algorithm |
| **[TECH]** | `docs/research/tech_stack_and_data_sources.md` | Free data APIs, rate limits, LLM layer, Python stack, project layout, caching, testing |

**Missing brief.** The task listed `docs/research/analysis_methodology.md`, but no such file exists as of 2026-11-10. The analysis methodology here (scoring formulas, DCF, dividend safety, factor regression, stress tests) is therefore drawn from [ACAD], [CR-B §3.2, §4.2], [MKT] and [TECH §5.2]. If that brief is written later, §10 (scoring model) should be checked against it.

**Which document wins (consistency rule, added in the 2026-11-10 consistency review).**
- **This SPEC is canonical** for requirement IDs (FR/NFR/AC), formulas, thresholds, weights, caps and red-flag rules (§5, §10).
- **`IMPLEMENTATION_PLAN.md` is canonical** for repository layout, file and page names, module/package names, and phase sequencing. The KB folder is `knowledge_base/` (not `kb/`), and the Streamlit pages are `app/pages/0_Profile.py` … `11_Settings.py` (see §9.1).
- **`WORKFLOW.md` is canonical** for how and when the user runs each flow.
- `docs/README.md` indexes all documents. If two documents disagree, fix the non-canonical one.

**Verification carry-over.** The briefs mark claims **[V]/[VERIFIED]**, **[S]** (secondary) or **[UNVERIFIED]**. The knowledge base (KB) must keep those flags. The app must **never show an [UNVERIFIED] figure as fact**. It shows "approximate, as of <date>" or fetches the value live [ACAD §0].

---

## 1. Purpose, scope and non-goals

### 1.1 Purpose
Help a US-based young professional make **better, documented, evidence-based investment and savings decisions** by:

1. Sizing an emergency fund and placing cash in the best after-tax, insured vehicle.
2. Ordering contributions across 401(k), IRA, HSA and taxable accounts.
3. Picking and comparing low-cost ETFs for a core portfolio, with optional factor tilts.
4. Screening and scoring individual stocks as **"buy candidates" for a capped satellite sleeve**, with transparent reasoning.
5. Showing **what the evidence says** and **what each of 8 creators says**, side by side, with conflicts of interest disclosed.
6. Giving descriptive market context, never market timing.
7. Making the user pause and write a thesis (decision journal, pre-trade checklist) before acting.

### 1.2 In scope
- A single local user on Windows, macOS or Linux, running `uv run streamlit run app/Home.py`.
- US accounts and tax rules (federal plus user-entered state rate).
- US-listed ETFs and US SEC-filing stocks. Foreign ADRs only where EDGAR or yfinance data allows.
- A curated, versioned KB shipped with the app (markdown + JSON).
- An optional LLM explainer grounded in the KB.

### 1.3 Non-goals (explicit)
| Non-goal | Why |
|---|---|
| **Not a broker.** No order routing, no brokerage logins, no account aggregation | Privacy and safety. Portfolio input is manual or CSV [TECH §5.5] |
| **No auto-trading or alerts that say "buy now"** | Frequent trading lowers individual returns (Barber & Odean 2000) [ACAD §6.1] |
| **No real-time ticker or intraday charts by default** | The app is built for low-frequency decisions [ACAD §6.1] |
| **No market-timing signals.** CAPE and valuation never trigger "sell" or "go to cash" | Valuation is weak or useless as a timing signal (Campbell & Shiller; Goyal & Welch 2008) [ACAD §8.2] |
| **No LLM-generated numbers or recommendations** | Prices, ratios and rates come only from the data layer. Scores come from deterministic rules [TECH §4, §6] |
| **Not a registered investment adviser.** No personalized advice claims | Compliance (§16) |
| **Not tax-filing software** | It shows tax estimates and rules with sources. The user verifies with the IRS or a professional |
| **No leverage, options or margin strategies for this profile** | Moderate risk; leverage covered as education only [ACAD §9; CR-A §1.2.9] |
| **No public hosting or data redistribution** | yfinance terms allow personal use only; creator transcripts are copyrighted [TECH §1.1, §3.4] |

---

## 2. User persona

| Attribute | Value | Source / design consequence |
|---|---|---|
| Location / tax | US resident; federal + state income tax (state TBD) | The after-tax yield calculator needs `t_fed`, `t_state` [SAV §1.4] |
| Life stage | Young professional, W-2 income (assumed; confirm) | Human capital is "bond-like" if income is stable, which supports a high equity share [ACAD §9] |
| Current investing | Mostly ETFs | Core-satellite framing; ETF module is primary |
| Accounts | 401(k) with employer match; Roth and/or Traditional IRA; taxable brokerage | Account waterfall + asset location [SAV §3, §3.5] |
| Horizon | 10+ years | Long-run CMAs, not trailing returns [MKT §8] |
| Risk tolerance | **Moderate.** Big drawdowns would cause stress | Defaults: 70-90% equity, bonds for drawdown control, satellite caps, staged entry option, no leverage [ACAD §9, §10; SAV §4.4] |
| Interests | Value/quality stocks, evidence-based factor ETFs, dividend growth, growth stocks | Per-style scorecards (§10.3). Dividend growth framed as a quality screen, never "free income" [ACAD §5.2] |
| Cash | About $20K savings, part emergency fund, part excess. **Monthly expenses unknown** | `M` is a required input with no default; the app must not invent it [SAV §4.1] |
| Skills | Comfortable with Python | Can maintain KB files, run refresh scripts, read code-level formulas |
| Budget | $0 | Free APIs only; optional free LLM [TECH §0] |

**Persona statement:** "I want to keep building wealth mainly through low-cost ETFs, sometimes buy individual stocks I've researched, know what both experts and research say, and make sure my cash and accounts are set up right, without being pushed into trading or hype."

---

## 3. Guiding principles (evidence-derived)

Each principle becomes code: a default, a warning, a guardrail or a score weight.

| # | Principle | Evidence | Implementation hook |
|---|---|---|---|
| P1 | **Evidence first, creators second.** The academic verdict is always shown first; creators are sources, not authorities | Creator ratings A-E [CR-A §0.3]; "academic verdict ranked first" [CR-B §6.1.7] | Expert Lens layout (FR-5xx); `evidence_alignment` on every creator principle |
| P2 | **Core-satellite.** Broad, low-cost index ETFs are the core (default ≥ 90% of equities, never < 80%). Individual stocks, thematic and crypto are a capped satellite | Sharpe 1991; SPIVA; Fama-French 2010; Bessembinder 2018 [ACAD §1-2, §7.1] | `SATELLITE_CAP` default 10% of equities (warn when exceeded), strong warn > 20% [ACAD §12; CR-A §7.5] |
| P3 | **The index owns the 4% by construction.** Single stocks are positively skewed; most underperform T-bills over their lifetime | Bessembinder 2018; Bessembinder et al. 2023 [ACAD §2.1] | Base-rate card on every stock page; single-stock warning > 5% of investable assets, strong > 10% |
| P4 | **Costs matter, monotonically.** Expense ratio is the single most predictive fund variable | Carhart 1997; French 2008; Kinnel/Morningstar [ACAD §1.4, §1.5, §6.4] | ER = 30% of ETF score; fee-drag calculator |
| P5 | **Total return, never yield.** Dividends are not free money; covered-call distributions are not extra return | Miller & Modigliani 1961; Hartzmark & Solomon 2019; Israelov & Nielsen 2015 [ACAD §5; MKT §7.1] | No yield sorting; "Yield ≠ return" badge > 6%; tax-drag display |
| P6 | **Global diversification by default.** Global market-cap weight is the neutral reference (about 64/36 US/ex-US) | French & Poterba 1991; Anarkulova et al. 2022 [ACAD §7.2; MKT §4] | Home-bias flag if ex-US < 20% of equities |
| P7 | **Factor tilts are optional, modest and haircut.** Use canonical factors only; halve historical premiums for planning | Fama-French 1993/2015; Novy-Marx 2013; McLean & Pontiff 2016; Hou-Xue-Zhang 2020; Harvey-Liu-Zhu 2016 [ACAD §3-4] | Tilt 0-30% of equities; `factor_premium_haircut = 0.5`; `evidence_tier` gate |
| P8 | **Valuation sets expectations, not timing.** | Campbell & Shiller 1998; Goyal & Welch 2008; Vanguard VEMO [ACAD §8; MKT §1.2] | CAPE feeds CMAs only; "save more, diversify" message when high |
| P9 | **Behavioral guardrails.** Slow down, write the thesis, limit turnover | Barber & Odean 2000/2001; Morningstar Mind the Gap; Shefrin & Statman 1985; Odean 1998 [ACAD §6] | Decision journal required; 48-72 h cooling-off; turnover > 50% warning; sell checklist |
| P10 | **Liquidity before investing.** An insured emergency fund comes first, and retirement accounts are not emergency funds | Carroll 1997; Kaplan & Violante 2014; Fed SHED; BLS unemployment duration [ACAD §11; SAV §2] | Savings & Cash Optimizer; starter EF; "your 401(k) is not your EF" warning |
| P11 | **Free money and tax shelters first.** Capture the full match, use tax-advantaged room each year | Choi, Laibson & Madrian 2011 [SAV §2.1.5, §3.1] | Account waterfall (FR-2xx) |
| P12 | **Lump sum is the default, staging is a behavioral option.** | Vanguard 2012; Constantinides 1979 [ACAD §10; SAV §4.4] | Lump vs. staged planner with stated expected cost |
| P13 | **Source reliability and conflicts are first-class data.** Sponsored and paid promotions are flagged and excluded from scoring | Plain Bagel finfluencer research; Leite et al. 2025 "Pay to pump" [CR-A §2.2.6] | `sponsored`, `paid_promotion`, `conflicts[]`, trust weights |
| P14 | **Every number has a source and an as-of date; stale data is labeled, never silently shown** | [MKT §9.3; TECH §5.4] | Freshness badges (NFR-04) |
| P15 | **Deterministic and explainable.** Every pass/fail shows *why*; rules live in versioned YAML with citations | [TECH §6] | `knowledge_base/rules/*.yaml`; score breakdown panels |


---

## 4. System overview

```
+------------------------ Streamlit UI (app/) -------------------------+
| Home | Profile/IPS | Savings | ETF | Stocks | Experts | Library |    |
| Market | Portfolio | Journal | Ask-the-KB (optional) | Settings      |
+---------------+-----------------------------------+------------------+
                | pure functions                    | read-only
+---------------v--------------+     +--------------v-------------------+
| analytics/ (deterministic)   |<----| knowledge_base/ (shipped, in git)|
| savings, returns, risk,      |rules| papers.json, creators/*.json,    |
| factors, valuation, quality, |     | rules/*.yaml, topics/*.md,       |
| scoring, stress              |     | etfs/etf_universe.json,          |
+---------------+--------------+     | accounts/limits_2026.json, cma   |
                |                    +----------------------------------+
+---------------v-----------------------------------------------------+
| DuckDB cache (data/fintec.duckdb): prices, facts, macro, factors,    |
| etf_holdings, papers, videos, user tables, llm_cache                 |
+---------------+-----------------------------------------------------+
                | adapters: retry, rate limit, validation, fallback
+---------------v-----------------------------------------------------+
| yfinance, Tiingo, Alpha Vantage, SEC EDGAR, FRED, Treasury CSV /     |
| Fiscal Data, French, AQR, issuer holdings / N-PORT, Finnhub,         |
| OpenAlex, Semantic Scholar, YouTube RSS, Rational Reminder RSS       |
+---------------------------------------------------------------------+
        optional: llm/ router -> OpenRouter :free -> Ollama -> none
```

**Stack** [TECH §5.1]:
- Runtime and packaging: Python 3.12, uv.
- UI and charts: Streamlit, plotly.
- Data: pandas (plus polars for screening), DuckDB, pydantic v2.
- Stats: statsmodels.
- Networking: httpx, tenacity, feedparser.
- Config: pydantic-settings.
- Quality: pytest, vcrpy, hypothesis, ruff, mypy.

Project layout follows [TECH §5.3].

---

## 5. Functional requirements

Conventions:
- **Priority:** **M** = must (MVP), **S** = should (v1.0), **C** = could (later).
- Every FR maps to an acceptance criterion (AC) in §13.
- **Warn** = non-blocking amber banner.
- **Strong warn** = red banner. The user must tick an acknowledgement checkbox, and the acknowledgement is logged in the journal.

### 5.1 Module 1: Profile & Investment Policy Statement (FR-1xx)

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-101 | The onboarding wizard captures the user's situation (fields below). **Monthly essential expenses `M` is required and has no default.** Liquid savings `S` defaults to $20,000 | M | [SAV §4.1, §6] |
| FR-102 | A risk questionnaire (8-10 items) separates **risk tolerance** (feelings; e.g., "If your portfolio fell 20% the week after you invested, would you keep going?") from **risk capacity** (horizon, income stability, EF status). Output: risk score 1-10, profile label (Conservative/Moderate/Aggressive), recommended equity range | M | [ACAD §9, §10; CR-A §1.4] |
| FR-103 | The app generates an **Investment Policy Statement (IPS)** from the profile and questionnaire (contents below). The IPS is editable, versioned, and exportable to Markdown/PDF | M | [ACAD §12] |
| FR-104 | IPS defaults for the moderate profile are listed below | M | [ACAD §7.2, §9, §12; MKT §4, §7.4, §7.5; CR-A §7.5] |
| FR-105 | Every IPS guardrail is a named rule (`SATELLITE_CAP`, `SINGLE_STOCK_CAP`, `INTL_MIN`, `FACTOR_TILT_MAX`, `BLOCK_LEVERAGED_FOR_MODERATE`, `CRYPTO_CAP`, `THEMATIC_CAP`) that other modules reference. Setting a value outside the evidence-backed range requires a written reason, which is logged | M | [CR-A §7.5] |
| FR-106 | Profile data is stored only in local DuckDB. User tables can optionally be encrypted with a passphrase (C) | M/C | NFR-06 |
| FR-107 | Annual "IPS review" reminder with a diff of changes. Reminder when new IRS limits are published (each November) | S | [SAV §3.2] |
| FR-108 | **First-run acknowledgement** (text in §16.2) must be accepted before any personalized output is shown; stored with a timestamp and re-shown when the disclaimer text version changes | M | §16 |
| FR-109 | **Freshness gate for decisions:** a decision record (FR-901) cannot reach "Ready" while any input it uses (price, rate, fundamental, limit file, creator item) carries a red (expired) freshness badge; amber inputs require a tick "I verified this at the source" | M | NFR-04; [MKT §9.3] |

**FR-101 onboarding fields:**
- Age, filing status, state.
- Federal and state marginal tax rates. If the user doesn't know them, estimate from income using `knowledge_base/accounts/tax_brackets_2026.json`.
- MAGI band.
- Employment type (W-2 / 1099 / mixed), job stability (1-5), single or dual income, dependents, homeowner flag.
- HDHP/HSA eligibility.
- 401(k) match formula and vesting schedule.
- Contributions so far this year: 401(k), 2026 IRA, HSA.
- High-interest debts (balance, APR).
- Known expenses within 36 months.
- Monthly essential expenses `M` (required).
- Liquid savings `S` (default $20,000).

**FR-103 IPS contents:** goals, horizon, target allocation (equity/bond/cash; US/ex-US), satellite cap, factor-tilt cap, rebalancing rule, contribution plan, prohibited items (leverage, single-stock option-income ETFs, crypto above the cap), review cadence.

**FR-104 moderate-profile defaults:**

| Setting | Default | Notes |
|---|---|---|
| Equity | 80% (range 70-90%) | Of long-term assets outside the EF |
| Bonds | 15% | |
| Cash | 5% | Outside the EF |
| Ex-US share | 36% of equity | Global cap weight; editable 20-50% |
| Satellite (individual stocks etc.) | ≤ 10% of equities | Warn above 10%; strong warn above 20% (Graham's 80/20 barbell is the upper bound) |
| Factor tilt | 0% | Opt-in up to 30% of equities |
| Leverage | 0 | |
| Crypto | 0% | Opt-in, ≤ 5% of investable assets |
| Thematic | ≤ 5% | |

### 5.2 Module 2: Savings & Cash Optimizer (FR-2xx)

**5.2.1 Emergency-fund calculator**

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-201 | Compute `target_months = clamp(3.0 + sum(adjustments), 3, 12)` from the adjustment table in §10.1. Show each adjustment applied and why. The user can override any of them | M | [SAV §2.2] |
| FR-202 | Show reference markers next to the computed target: 3 × M (the median unemployment spell is about 2.7 months), 6 × M (the mean is about 6.2 months), Tilbury's cautious 5 × M, and Jikh's 3-6 month band | M | [SAV §2.1; CR-B §1.4, §6.4] |
| FR-203 | Macro overlay: add 0.5 months when the BLS long-term unemployment share (27+ weeks) is above 25%. Value comes from BLS/FRED with its as-of date. In Nov 2026 it is 27.6%, so the overlay applies | S | [SAV §2.2] |
| FR-204 | The EF target must cover at least the highest insurance deductible (e.g., the HDHP deductible) | M | [ACAD §11] |
| FR-205 | **Tiering.** See the tier rules below | M | [SAV §4.3, §1.6] |
| FR-206 | **EF safety warnings.** See the warning list below | M | [ACAD §11; SAV §4.3; CR-B §2.4] |

**FR-205 tier rules:**
- Tier 1: checking, 0.5 × M. Optional and outside the EF math.
- Tier 2: HYSA, min(EF, 3 × M).
- Tier 3: the remainder, in T-bills / SGOV / a Treasury MMF, when its after-tax yield beats the HYSA by more than 0.10%.
- I bonds never count toward the EF in their first 12 months. After that they count at `balance − 3 months of interest`.

**FR-206 EF safety warnings:**
- EF held in stocks, bond funds, crypto or "yield" apps → **strong warn**: "Your emergency fund should not fall when the job market does."
- 401(k)/IRA counted as EF → **warn**: "Your 401(k) is not your emergency fund" (Kaplan & Violante 2014).
- Roth IRA *contribution basis* may be shown as a backstop layer (−0.5 month adjustment).

**5.2.2 Cash vehicle comparison**

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-210 | Compare these vehicles: HYSA, government MMF, Treasury MMF, T-bills (4/8/13/26/52-week), T-bill ETFs (SGOV/BIL), CDs (3 months to 5 years), no-penalty CD, brokered CD, I bonds. The FDIC national average savings rate is shown as a benchmark | M | [SAV §1.1] |
| FR-211 | Compute the after-tax yield of each vehicle with the formulas in §10.2. Treasuries and I bonds are state-exempt. MMFs use a user-entered `exempt_share`. HYSA and CD interest is fully taxable. Also show the HYSA-equivalent yield of a T-bill (TEY) | M | [SAV §1.4] |
| FR-212 | **Live rates.** See the rate sources below | M | [TECH §1.3, §1.4; SAV §5] |
| FR-213 | The HYSA table is **user-curated**, with the fields below. Rank by **sustainable base APY**, never by promo APY | M | [SAV §1.2] |
| FR-214 | FDIC check: link to FDIC BankFind for each bank (optional API lookup). Flag `fintech_intermediated=True` accounts with the Synapse/Yotta lesson. Flag any crypto or non-bank "yield" product as **not a HYSA** (the BlockFi/Voyager lesson) | M | [SAV §1.2; CR-A §4.5; CR-B §2.4] |
| FR-215 | Jikh's break-even model for accounts with fees: `break_even_balance = annual_fee_pretax / (apy_a − apy_b)`, where `annual_fee_pretax = fee / (1 − t)`. Add a note that switching for less than ~1 pp is rarely worth it, and check for teaser-rate caps | S | [CR-B §2.4] |
| FR-216 | CD tools: early-withdrawal break-even `net_yield(m) = (APY·m/12 − penalty_months·APY/12) / (m/12)`, and the state tax rate at which a T-bill beats a CD | S | [SAV §1.5] |
| FR-217 | I bonds: penalty-adjusted yield at month n (12 ≤ n < 60) = `r × (n − 3)/n`. Show the $10K per SSN per year electronic limit and that purchase is through TreasuryDirect only | S | [SAV §1.6] |
| FR-218 | Guardrails: warn if the HYSA APY is below 3.0% while top-tier HYSAs pay 4% or more. Warn if more than $250K sits at one bank in one ownership category. Rates older than 7 days get a stale badge | M | [SAV §4.6] |
| FR-219 | Opportunity-cost explainer: "Holding $X of excess cash for 10 years gives up about $Y of real growth *in expectation*." Assumptions are adjustable. Include Felix's "cash is a terrible long-term investment" context | S | [SAV §2.4; CR-A §1.2] |
| FR-220 | Sinking funds: expenses known within 36 months go into a CD or T-bill that matures before the due date. Warn if an expense less than 24 months away is allocated to equities | M | [SAV §1.8, §4.6] |
| FR-221 | **Tax on cash interest.** For every cash holding, show the estimated annual interest in dollars and the estimated tax on it: HYSA/CD/MMF interest is ordinary income (federal + state, except the MMF's `exempt_share` and Treasuries/I bonds, which are state-exempt). Explain the forms (1099-INT for bank and T-bill interest; 1099-DIV for MMF and T-bill-ETF distributions; I bond interest is federally taxable at redemption unless deferred). Note that interest is taxable even when no 1099 is issued (under $10), and add a "consider withholding or a safe-harbor estimated payment if interest plus other non-wage income is large" note. Example on the persona: $20,000 × 4.0% = $800 interest → ≈ $176 federal tax at 22% | M | [SAV §1.4]; IRS Topic 403 / Pub. 550 (general rule; verify) |
| FR-222 | **Tax-year cash summary** (Settings → Export): projected interest by vehicle and by form type for the current tax year, for the user's tax preparer. Labelled "estimate" | S | FR-221 |

**FR-212 rate sources:**
- T-bills: the Treasury daily par yield curve CSV and bill rates (primary), FRED `DGS3MO` / `DGS1` (fallback). **Never compare `DTB3` with an APY.** It is quoted on a discount basis and understates the yield. For the cash-vehicle table use the **Daily Treasury Bill Rates "coupon equivalent"** column (13-week = 4.22% on 2026-11-10); use the par yield curve (3-month = 4.30% that day) only for the curve chart and regime cards. Label which one is shown so the two never get mixed.
- SGOV/BIL: SEC yield from the issuer pages (manual refresh).
- MMFs: 7-day yield from sponsor pages, or user entry.
- I bonds: `knowledge_base/savings/ibond_rates.json`.

**FR-213 HYSA table fields:** bank name, base APY, promo APY and expiry, minimum balance, deposit requirement, residency restriction, withdrawal limits, `direct_bank` vs `fintech_intermediated`, FDIC certificate number, as-of date.

**5.2.3 Account order-of-operations waterfall**

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-230 | **Default waterfall.** The steps are listed below. The user may reorder them, with a logged reason | M | [SAV §3.1] |
| FR-231 | Show the **creator variants** next to the default (listed below) and highlight where they agree | M | [CR-A §3.3, §4.2; CR-B §1.2, §2.4] |
| FR-232 | `split_savings()` allocates `S` using the [SAV §4.2] algorithm. A waterfall/Sankey chart shows where the money goes: debt payoff, EF tiers, sinking funds, match bridge, HSA, IRA, taxable. The IRA step shows 2026 room (open until 2027-04-15) and 2027 room (from 2027-01-01) | M | [SAV §4.2, §4.5] |
| FR-233 | 2026 limits load from `knowledge_base/accounts/limits_2026.json` (values below), each with its IRS URL. **Never hard-coded** | M | [SAV §3.2, §3.3; ACAD §11] |
| FR-234 | Roth eligibility check. Above the phase-out, show the backdoor Roth explainer with a **pro-rata** calculator, `taxable_fraction = 1 − basis / (Dec31_trad_IRA + conversion)`, and a warning about each conversion's 5-year clock | M | [SAV §3.3] |
| FR-235 | Match-capture check: warn if there is excess cash but the match is not fully captured. Choi, Laibson & Madrian (2011) found 36% of eligible employees left about 1.6% of salary on the table. Toggles for SECURE 2.0 options: student-loan match, PLESA, Roth match | M | [SAV §2.1, §3.3a] |
| FR-236 | Lump-sum vs. staged deployment of the excess (see FR-806) | M | [SAV §4.4] |
| FR-237 | Calculators for mortgage prepay vs. invest (`PREPAY_VS_INVEST_CALC`) and rent vs. buy (`RENT_VS_BUY_CALC`). Ownership costs are computed from the user's inputs, not a fixed 5% | C | [CR-A §1.4, §3.4, §4.2] |
| FR-238 | Optional budget views: 70/20/10 (Whiteboard) and 25/15/50/10 (Tilbury), labelled "heuristic (evidence B/C)" | C | [CR-A §3.4; CR-B §1.2] |

**FR-230 default waterfall:**
- **0.** Starter EF = max(1 × M, $2,000).
- **1.** Full 401(k) match, adjusted for vesting.
- **2.** High-interest debt above 8% APR, highest rate first (avalanche).
- **3.** Full EF.
- **4.** HSA, if eligible.
- **5.** IRA, Roth or Traditional per §10.2.4, up to $7,500.
- **6.** More 401(k), toward $24,500.
- **7.** Mega-backdoor Roth, if the plan allows it.
- **8.** Taxable brokerage.
- **Parallel bucket:** goals within 5 years stay in cash/T-bills/CDs.

**FR-231 creator variants:**
- **Whiteboard:** EF → debt → match → IRA → HSA → 401(k) → taxable.
- **Graham:** EF → match → debt above 7-8% → optional prepayment of cheap debt.
- **Jikh:** invest in yourself → 3-6 month EF → debt at 4% or more → HYSA → CDs/T-bills → invest.
- **Tilbury:** 25/15/50/10 paycheck split.

**FR-233 2026 limits:**

| Limit | 2026 value |
|---|---|
| 401(k) employee deferral | $24,500 |
| 401(k) catch-up | $8,000 (50+); $11,250 (ages 60-63) |
| Total additions, §415(c) | $72,000 |
| IRA | $7,500 |
| HSA | $4,400 self / $8,750 family |
| Roth IRA phase-out | $153-168K single; $242-252K MFJ |
| Traditional IRA deduction phase-out (covered by a workplace plan) | $81-91K single |

### 5.3 Module 3: ETF Research & Comparison (FR-3xx)

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-301 | Curated ETF universe in `knowledge_base/etfs/etf_universe.json`, about 40 funds to start (listed below; fields in §11). The user can add tickers | M | [MKT §5, §7; CR-A; CR-B] |
| FR-302 | ETF profile page with the fields listed below | M | [ACAD §6.4; MKT §7] |
| FR-303 | **Total-return comparison** from adjusted closes: 1/3/5/10 years and since common inception, growth of $10K, rolling 3-year returns, max drawdown, volatility. **Funds are never ranked by yield.** Sorting by trailing 1-year return shows a warning (Carhart 1997) | M | [ACAD §1.4; MKT §7.1] |
| FR-304 | **Holdings overlap** between any 2-5 ETFs: weighted overlap `sum(min(w_A,i, w_B,i))`, count overlap, Venn-style chart, and a look-through table of combined exposure per stock | M | [MKT §2.2] |
| FR-305 | **Look-through concentration** for the whole portfolio (ETFs plus stocks): effective weight in the top-10 US names and in AI-linked industries. Warn if any single name is above 10% of total exposure, or AI-linked exposure is above 1.5× its market weight | M | [MKT §2.2; ACAD §7.1] |
| FR-306 | **Factor regression** (CAPM, FF3, FF5+MOM) on monthly excess returns against Ken French factors. See the method notes below | M | [TECH §5.2; ACAD §3.1] |
| FR-307 | Plain-English factor interpretation, e.g., "This 'value' ETF's HML loading is 0.12 (CI 0.05-0.19): weak value exposure." For dividend ETFs, show how much of the return is explained by value, profitability and investment loadings | M | [ACAD §5.1] |
| FR-308 | **Cost calculator:** dollar drag of an ER gap over 10/20/30 years for a user-entered balance and return. Also the drag of a 1% advisory fee (Whiteboard's "the 1% fee that costs you $186,000") | M | [ACAD §1.5; CR-A §3.4] |
| FR-309 | ETF score (§10.4) shown as a breakdown bar. Funds are ranked only within a comparable category (never SGOV against AVUV) | M | [ACAD §6.4] |
| FR-310 | **Mandatory badges and warnings** by fund type (listed below) | M | [MKT §7.1-7.5; ACAD §1.2, §6.2; CR-A §1.2] |
| FR-311 | Factor-tilt page. For each factor ETF show: the supporting papers; the expected premium **net of fees, after a 50% haircut**; the longest historical stretch of underperformance vs. the market; and a "tracking-error tolerance" question. Suggested tilt size is 10-30% of equity | S | [MKT §5.2; ACAD §4.1] |
| FR-312 | Asset-location helper: bonds, REITs, high-yield and TIPS go in tax-deferred accounts; the highest expected-return factor equity goes in Roth/HSA; broad US equity and international (to use the foreign tax credit) go in taxable. Suggest changes only when the estimated tax drag saved exceeds 0.1% a year. Below about $50K invested, recommend the same mix in every account | S | [SAV §3.5; ACAD §5.1, §7.2] |
| FR-313 | Tax-loss-harvesting partner suggestions (e.g., VTI↔ITOT/SCHB), labelled "whether these are substantially identical is a gray area." The **wash-sale check covers all accounts, including IRA/Roth purchases and dividend reinvestment.** Under Rev. Rul. 2008-5, a repurchase in an IRA permanently disallows the loss | S | [ACAD §6.3; CR-B §2.5] |
| FR-314 | Cross-source validation: if yfinance and Tiingo adjusted closes differ by more than 1% on any day, show a "data discrepancy" flag | M | [TECH §1.13] |
| FR-315 | Tickers and ERs are validated against issuer data, never creator descriptions. Example: Graham's "FXIAX" is a typo for FXAIX | M | [CR-A §4.3] |

**FR-301 starting universe:**

| Group | Funds |
|---|---|
| Core | VTI, VOO, ITOT, VXUS, IXUS, VT, BND, AGG, BNDX, SCHB; FZROX/FZILX as mutual-fund references |
| Factor | AVUV, AVDV, AVLV, AVUS, DFAC, DFAW, AVGE, QUAL, VLUE, MTUM, USMV |
| Dividend | SCHD, VIG, DGRO, VYM |
| Growth | SCHG, QQQM |
| Income | JEPI, JEPQ, DIVO, QQQH |
| Cash | SGOV, BIL |
| Equal-weight | RSP |
| TIPS | SCHP, VTIP |

**FR-302 ETF profile fields:**
- ER, AUM, inception date, index tracked, holdings count, top-10 weight, sector and country weights.
- 30-day SEC yield and distribution yield, each clearly labelled.
- Tracking difference vs. the index; median bid-ask spread.
- Tax notes: qualified-dividend share, return of capital, ordinary-income share for ELN funds.
- Category flags: core / factor / dividend / thematic / active / leveraged / covered-call / buffer / crypto.

**FR-306 regression method:**
- Newey-West HAC standard errors (6 lags).
- Report loadings with 95% CIs, annualized alpha, R² and n_months.
- Require at least 36 months of data.
- US funds use US factors. International funds use the Developed ex-US or Emerging factor files.
- AQR QMJ/BAB factors are optional.
- Warn if R² is below 0.8 for a fund labelled "broad index."

**FR-310 badges and warnings:**

| Fund type | Required treatment |
|---|---|
| Covered-call | "Yield ≠ return" badge; upside-cap illustration over the underlying's best 12-month window; tax-inefficiency note; label "not aligned with accumulation goal" |
| Single-stock option-income | Red warning |
| Leveraged / inverse | Blocked for the moderate profile; volatility-decay explainer |
| Thematic | Ben-David et al. (2023): about 30% risk-adjusted underperformance in the first 5 years; cap at 5% |
| Active | SPIVA category card; ER gap vs. the index equivalent |
| Buffer | Calculator vs. a simple 70/30 equity/T-bill equivalent; mid-period entry warning |
| Crypto | "Speculative satellite"; max drawdown shown |
| Sector / thematic / leveraged | "Behavior-gap risk: high" badge |

### 5.4 Module 4: Stock Research & Buy-Candidate Scoring (FR-4xx)

**Framing (always visible):** individual stocks belong only in the satellite sleeve. Every stock page has a **base-rate card**: *"Historically, most individual US stocks underperformed one-month T-bills over their lifetime. The best ~4% created all net wealth (Bessembinder 2018). On average, active selection underperforms the index after costs (Sharpe 1991)."* [ACAD §1.1, §2.1]

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-401 | Fundamentals come from **SEC EDGAR companyfacts** (bulk ZIP loaded weekly into DuckDB; per-company API on demand). EDGAR uses a tag-fallback map per metric, dedupes on (end, fp, form) keeping the latest `filed`, and builds TTM from 4 quarters. Prices come from yfinance with a Tiingo fallback. yfinance/Finnhub values are labelled "provider estimate" | M | [TECH §1.2, §1.13] |
| FR-402 | Metrics library (definitions in §10.3.1) | M | [ACAD §3.3, §3.4; CR-B §3.2] |
| FR-403 | **Screener** over a user universe (default: S&P 500 + S&P 400 constituents, or user CSV). Filter by any metric, sector, market cap, and style scorecard score. Results show the composite score, sub-scores, flags and data as-of date. A **multiple-testing banner** tracks how many screen variants were tried this session: "You tested N variants; the best result is expected to look good by chance" (Harvey, Liu & Zhu 2016) | M | [ACAD §4.3] |
| FR-404 | **Per-style scorecards** (§10.3.3): `quality_value` (default), `dividend_growth`, `quality_growth` (Carlson-style compounder), `deep_value` (Carlin-style absolute value). Each is a weighting of the same normalized sub-scores plus style-specific gates. The UI shows which style a stock is scored under and lets the user compare styles side by side | M | [ACAD §12; CR-B §3.2, §4.2] |
| FR-405 | **Red-flag rules** cap or block a candidate regardless of score (§10.3.4): value trap (cheap and unprofitable), falling knife (very negative 12-1 momentum on a cheap stock), lottery stock (top-decile 1-year volatility), yield trap, high leverage, negative owner earnings, heavy dilution, paid-promotion mention, microcap / low liquidity | M | [ACAD §3.2, §3.3, §3.5, §5.1; CR-A §2.2] |
| FR-406 | **DCF (owner-earnings, Carlin-style)**: owner earnings = CFO − capex − SBC (per share). Stage 1 (years 1-5) grows at a user rate (default = 5-year revenue CAGR, capped at 15%). Stage 2 (years 6-10) at a sector-average rate (default 5%). Terminal value = year-10 owner earnings × terminal multiple (default 15). Discount rate is user-set (default **10%** hurdle; alternative: CAPM-implied). Three scenarios (worst/normal/best, default probabilities 0.3/0.4/0.3); probability-weighted intrinsic value; **margin-of-safety test**: is the worst case ≥ price? | M | [CR-B §3.2] |
| FR-407 | **Reverse DCF:** solve for the stage-1 growth rate that equates the DCF to the current price (with the same discount rate, terminal multiple and stage-2 rate). Show "The market price implies X% owner-earnings growth for 5 years," compared with the company's historical 5-year and 10-year growth and its sector median. This is a plausibility check, not a price target | M | Valuation practice; [CR-B §4.2 "DCF + judgment"] |
| FR-408 | DCF sensitivity grid (discount rate × terminal multiple; growth × discount rate) as a heatmap. The UI warns that "the hurdle-rate choice drives the conclusion" | M | [CR-B §3.7] |
| FR-409 | **Dividend safety score** (§10.3.5): FCF payout, EPS payout, net debt/EBITDA, interest coverage, dividend growth streak and 5-year CAGR, earnings stability, yield vs. sector median. Output 0-100 and a label (Safe/Borderline/Unsafe). Always shown next to **total return**, and with the "free dividends fallacy" explainer and the taxable-account tax drag | M | [ACAD §5.1, §5.2; MKT §6] |
| FR-410 | **Moat / qualitative checklist (Carlson)**, recorded by the user: barriers to entry, majority market share, entrenched brand/distribution, "top of the food chain," dependence on high-risk R&D, acquisition-only growth, high leverage, recession survival. Answers are stored with the thesis. They don't feed the numeric score, but they gate the pre-trade checklist | M | [CR-B §4.2] |
| FR-411 | **Plain Bagel research workflow** as a guided form: screen → understand the business → finances → strategy → valuation → written notes. The completed form becomes the thesis record (Module 9) | M | [CR-A §2.4] |
| FR-412 | **Risk & reward quadrant (Carlin)**: plot candidates on x = expected return (DCF implied return: `(intrinsic/price)^(1/10)·(1+g_term) − 1`, simplified) and y = permanent-loss risk score (inverse of the safety sub-score plus red flags) | S | [CR-B §3.2] |
| FR-413 | **Portfolio-fit panel** for any candidate: overlap with existing ETFs (e.g., "AAPL is already 6.8% of your VOO"), its new look-through weight, satellite-cap headroom, single-stock cap check (warn > 5%, strong warn > 10% of investable assets), sector concentration, and "stocks held / sectors" toward the ≥ 30 stocks across ≥ 8 GICS sectors needed to call a stock-only sleeve "diversified" | M | [ACAD §2.1, §7.1] |
| FR-414 | "Wall Street view" panel (optional): Finnhub recommendation trends, earnings surprises, earnings calendar; labelled "consensus opinions, not evidence" | S | [TECH §1.5] |
| FR-415 | "Mentioned by creators" panel: dated, expiring creator positions/picks on this ticker (e.g., Carlson fair values 2026-11-02), with conflict flags. **Never used in the score** | M | [CR-A §7.1; CR-B §6.1] |
| FR-416 | Momentum is shown as 12-1 month return and percentile. It counts for at most 10% of any score, and only as a secondary factor | M | [ACAD §3.2] |
| FR-417 | Watchlist with target "research again" dates and a price-alert-free design (alerts only on thesis-review dates or on data such as a dividend cut or an earnings miss) | S | [ACAD §6.1] |
| FR-418 | **"Buy candidate" label** requires: composite ≥ 70 under the chosen style, no blocking red flags, DCF margin-of-safety test passed or the user overrides it with a reason, a completed thesis, and the cooling-off period elapsed. Otherwise the label is "Watch" (55-69) or "Pass" (< 55). The label always reads "Buy candidate (satellite)," never "Buy" | M | [ACAD §6.1, §12] |

### 5.5 Module 5: Expert Lens (FR-5xx)

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-501 | Creator profiles for the 8 creators: Ben Felix, The Plain Bagel (Richard Coffin), Whiteboard Finance (Marko Zlatic), Graham Stephan, Mark Tilbury, Andrei Jikh, Sven Carlin, Joseph Carlson. Each profile has verified credentials, role and incentives, jurisdiction focus (CA/US/UK/EU), trust weight, overall evidence alignment, "best use in this app," and `last_reviewed` | M | [CR-A §5; CR-B §5] |
| FR-502 | **Topic lens.** For a topic (e.g., "Should I buy SCHD?", "emergency fund size," "factor tilts," "covered calls," "individual stocks," "international," "crypto," "mortgage prepay"), show: **(1) the academic verdict first** (from `papers.json` + `rules`), then **(2) each creator's relevant principle** in a card: statement, evidence alignment grade, source link with timestamp/date, sponsored/paid flags, stale badge | M | [CR-B §6.1.7] |
| FR-503 | **Consensus/dissent view** (computed; §10.6): a matrix of creators × topics with stance (`for / against / mixed / silent`). Shows a consensus statement where ≥ 6 of 8 creators agree, and lists the dissenters with their evidence grades. Seeded consensus: EF first, capture the match, kill high-interest debt, low-cost index core, don't time the market, fees matter. Seeded dissent: dividends/covered calls (Felix/Bagel/Jikh vs. Whiteboard), factor tilts (Felix vs. most others silent), stock picking (Carlin/Carlson vs. Felix/Bagel), ETFs (Carlin vs. all), crypto (Jikh/Whiteboard/Graham vs. Carlin/Felix) | M | [CR-A §5; CR-B §5] |
| FR-504 | **"What would X emphasize?"** For any decision context (an ETF, a stock, a savings choice), the app generates, without an LLM, each creator's 2-3 most relevant principles via category matching. Example for a stock candidate: Felix → single-stock risk and base rate; Bagel → structured research process and finfluencer red flags; Carlin → owner-earnings DCF and margin of safety; Carlson → moat checklist and holding period; Graham → 80/20 barbell cap; Jikh → index beats dividend ETFs on total return; Tilbury → keep stocks a small optional slice; Whiteboard → order of operations first | M | [CR-A §7.5; CR-B §6.4] |
| FR-505 | **Conflict-of-interest disclosure** on every creator card: employer, financial sponsors, affiliate links, courses/memberships, creator-owned products, paid stock promotions, legal matters (with severity). Examples: Felix (PWL/OneDigital, DFA alignment), Whiteboard (GRML $5,000 paid promotion, Oct 2026), Graham (FTX class action, Yotta/Synapse), Jikh (BlockFi/Voyager promotion), Carlin ($499/yr platform, self-reported returns), Carlson (Qualtrim), Tilbury (Trading 212/eToro/Webull affiliates) | M | [CR-A §7.3; CR-B §1.6, §2.5, §3.6, §4.6] |
| FR-506 | **Jurisdiction translation**: CA/UK-focused content gets a "translate for the US" note (TFSA/RRSP → Roth/401k; ISA → Roth IRA; UCITS tickers like IWDA → US-domiciled equivalents, with a PFIC warning; DFA607 → DFAW/AVGE as an *app inference, not an endorsement*) | M | [CR-A §1.1, §1.6; CR-B §1.6] |
| FR-507 | **Recent-video ingestion**: a weekly job polls the 8 YouTube RSS feeds (allowlisted channel IDs; Carlson After Hours optional) and the Rational Reminder RSS. It stores video_id, title, published date, description and URL; parses sponsor/affiliate lines from descriptions; and runs a rule-based topic tagger. Retries with backoff. A single 404 never marks a channel deleted. **Impersonator channels (e.g., `UC67NIxAks8wXAmGPoyY7r5A`) are blocked** | M | [TECH §3.1, §3.5] |
| FR-508 | Video classification: each new video gets `type` ∈ {principle, framework, pick, commentary, product_mention}, heuristically from title/description keywords (e.g., "BREAKING," "Warning," "Crash" → commentary; "I bought," "price target" → pick; "sponsored by"/"paid promotion"/"compensated" → product_mention or paid_promotion). Commentary expires after 60 days and picks after 120 days | M | [CR-B §6.1] |
| FR-509 | Optional transcript fetch (youtube-transcript-api; ≤ 6/hour, cached forever in git-ignored `data/cache/transcripts/`, 24 h back-off on `RequestBlocked`). With an LLM enabled, it drafts a summary + candidate principles as **"pending review"**; nothing enters the curated KB without user approval (diff view) | S | [TECH §3.3, §3.4] |
| FR-510 | "New since last visit" feed on the Expert Lens page, grouped by creator and topic, with paid-promotion videos highlighted in red | S | [CR-A §7.1] |
| FR-511 | Creator performance claims are always labelled `self_reported` (e.g., Carlin "72x", Carlson "$1.47M"). The app may compute testable facts, e.g., the **active share** of Carlson's disclosed holdings vs. SPY/QQQ (the critics' "closet indexing" claim) | S | [CR-B §3.5, §4.6] |
| FR-512 | Source-reliability lens (Plain Bagel): a "before trusting a finfluencer" checklist (registration via FINRA BrokerCheck, disclosures, incentives, promoted-stock red flags) and the "Pay to pump" evidence (promoted stocks +11.8% over 5 days, reverting within ~30 days) | M | [CR-A §2.2.6, §2.4] |

### 5.6 Module 6: Research Library (FR-6xx)

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-601 | Curated `knowledge_base/research/papers.json` seeded with every paper cited in the briefs, at least: Sharpe 1991; Pedersen 2018; SPIVA; Fama-French 1992/1993/2010/2012/2015/2021; Carhart 1997; French 2008; Bessembinder 2018; Bessembinder et al. 2023; Jegadeesh-Titman 1993; Asness-Moskowitz-Pedersen 2013; Daniel-Moskowitz 2016; Novy-Marx 2013; Asness-Frazzini-Pedersen 2019 (QMJ); Asness et al. 2018 (Size Matters); Ang et al. 2006; Frazzini-Pedersen 2014; Baker-Bradley-Wurgler 2011; McLean-Pontiff 2016; Hou-Xue-Zhang 2020; Jensen-Kelly-Pedersen 2023; Harvey-Liu-Zhu 2016; Bailey-Lopez de Prado 2014; Miller-Modigliani 1961; Hartzmark-Solomon 2013/2019; Shefrin-Statman 1984/1985; Barber-Odean 2000/2001; Barber-Lee-Liu-Odean 2009; Barber et al. 2022; Odean 1998; Evans-Archer 1968; Statman 1987; Campbell et al. 2001; Domian et al. 2007; Goetzmann-Kumar 2008; French-Poterba 1991; Anarkulova et al. 2022; Asness-Israelov-Liew 2011; Campbell-Shiller 1988/1998; Welch-Goyal 2008; Siegel 2016; Ayres-Nalebuff 2013; Constantinides 1979; Vanguard DCA 2012; Carroll 1997; Carroll-Samwick 1997; Gjertson 2016; Kaplan-Violante 2014; Choi-Laibson-Madrian 2011; McKernan et al. 2016; Dammon-Spatt-Zhang 2004; Huang 2008; Shoven-Sialm 2004; Campbell-Viceira 2001; Israelov-Nielsen 2015; Israelov 2019; Ben-David et al. 2023; Liu-Tsyvinski 2021; Lakonishok-Shleifer-Vishny 1994; Piotroski 2000; Israel-Laursen-Richardson 2021; Arnott et al. 2021; Dichev 2007; DeMiguel et al. 2009; Thaler-Benartzi 2004; Amromin-Huang-Sialm 2007; Leite-Moraes-Moura 2025; Brixton et al. 2023; Newey-West 1987; Choi-Liu-Liu 2025 (WP); Fed SHED 2025; Morningstar Mind the Gap; Cembalest 2014 | M | [ACAD; CR-A §7.4; CR-B §8; MKT; SAV §7; TECH §1.10, §2.6] |
| FR-602 | Each record carries a **plain-English summary** (≤ 120 words), "what it found," evidence strength (very strong/strong/moderate/weak/convention), `evidence_tier` (canonical/contested/speculative), `peer_reviewed`, sample period and markets, caveats/criticisms, counterpoint papers, verification status, and **"how it's used in this app"**: a list of `rule_ids` and FR IDs with deep links to those screens | M | [ACAD §12 legend] |
| FR-603 | Search and filter by topic (index vs active, skewness, factors, decay/replication, dividends, behavior, diversification, international, valuation, lifecycle, DCA, emergency savings, asset location, covered calls, thematic ETFs, crypto, finfluencers), strength, peer-review status, and year | M | — |
| FR-604 | Topic explainer pages (`knowledge_base/topics/*.md`): ETF vs stock-picking, factor investing, dividends myths, covered calls, HYSA vs T-bills, emergency fund, Roth vs Traditional, asset location, lump sum vs DCA, CAPE and expected returns, concentration and AI, international diversification, finfluencer reliability. Each cites papers by ID and lists contrary evidence | M | [MKT; SAV] |
| FR-605 | SPIVA data file `knowledge_base/etfs/spiva_latest.json` (category, horizon, % underperforming, report date), refreshed by hand semiannually; [UNVERIFIED] values display as "approximate" | S | [ACAD §1.2] |
| FR-606 | "Find more research": OpenAlex search (with key; `select=` to save budget) plus Semantic Scholar `tldr`. Results are labelled **"not reviewed by KB"** until the user promotes them. arXiv preprints are labelled "preprint," and SSRN is linked, never scraped | S | [TECH §2] |
| FR-607 | Weekly metadata refresh (OpenAlex `works/doi:`): cited_by_count and open-access URL. The **build-time DOI check** fails CI on a title mismatch. Author names are not auto-imported (OpenAlex lists Harvey-Liu-Zhu's third author wrongly) | M | [CR-A §7.4; TECH §2.1, §1.10] |
| FR-608 | Rational Reminder show notes are parsed for DOIs/SSRN links to build a "papers discussed by Ben Felix" list | C | [TECH §3.2] |

### 5.7 Module 7: Market Context Dashboard (FR-7xx)

**Banner rule:** *descriptive, not prescriptive.* Valuation measures shift long-run expected returns; they are poor short-term timing tools [MKT §0; ACAD §8].

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-701 | Header snapshot cards, each with value, as-of date, source link and historical percentile: S&P 500 level; forward P/E (manual, FactSet); CAPE (Shiller XLS; multpl display check); top-10 weight (GTM/holdings); fed funds range; 3m/2y/10y/30y Treasury; 10y TIPS real yield; 10y breakeven; CPI y/y; PCE y/y; unemployment; HY OAS; Brent; I bond composite/fixed; FDIC national savings rate | M | [MKT §0, §9.1] |
| FR-702 | **Regime banner** built from 4-6 indicators with percentile bands; regenerated when a threshold is crossed (CAPE > 90th pct, 10y real > 2.5%, HY OAS < 3% or > 6%, CPI y/y > 4%). Without an LLM, use templated sentences | M | [MKT §0, §9.3] |
| FR-703 | **CPI y/y handles the Oct 2025 BLS gap**: compute y/y only when both months exist; otherwise show "n/a (2025 shutdown gap)" or a labelled estimate | M | [MKT §3.3, §9.1] |
| FR-704 | Forward P/E vs CAPE explainer: "Forward P/E depends on analyst forecasts; CAPE uses 10 years of actual inflation-adjusted earnings. They currently disagree because margins and earnings are unusually high." | M | [MKT §1.2] |
| FR-705 | **Institutional CMA table** from `knowledge_base/market/cma.json` (Vanguard, JPM LTCMA, GTM, BlackRock, AQR, Syzygy, Morningstar, GS): institution, as_of, published, horizon, asset class, low/high/point, nominal/real, URL. "Consensus band" chart (min-max per asset class). Stale after 12 months. Note conflicts (JPM/BlackRock sell alternatives) | M | [MKT §8] |
| FR-706 | Concentration and AI widget: top-10 weight vs earnings share; Mag 7 vs 493; the user's own look-through exposure (links to FR-305) | S | [MKT §2] |
| FR-707 | International vs US: forward P/E by region, YTD/15-y returns, global cap weights (≈ 64/36), "home-bias bet" display for the user's allocation; "never chase two years of outperformance" note | S | [MKT §4] |
| FR-708 | Factor context: value/growth P/E discount (GTM, quarterly), French factor trailing returns (rolling 1/5/10 y), factor drawdown chart | S | [MKT §5] |
| FR-709 | Popular-themes panel: covered-call, buffered, active, thematic, crypto, private credit; each with data, proponents, skeptics, evidence and the app's mandatory treatment (links to FR-310) | S | [MKT §7] |
| FR-710 | Calendar of upcoming data/events (FOMC 2026-12-08/09, CPI 2026-12-10, jobs 2026-12-04, PCE 2026-11-25, Fed FSR), refreshed from official schedules | C | [MKT §9.2] |
| FR-711 | "What this means for you" box: only mapped implications are allowed: lower expected returns → save more / diversify internationally; high cash yields → use T-bills/HYSA for the EF; high bond yields → bonds earn their place for drawdown control. **Never** "sell"/"go to cash" | M | [ACAD §8.2; MKT §3.4] |

### 5.8 Module 8: Portfolio & Allocation Planner (FR-8xx)

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-801 | Holdings input per account (401k, Roth IRA, Traditional IRA, HSA, taxable, cash): manual entry or broker CSV import (Fidelity/Schwab/Vanguard templates + generic mapper); lots with cost basis and date for taxable | M | [TECH §5.5] |
| FR-802 | Household view: total and per-account allocation by asset class, region, factor (via regressions), sector, top look-through names; drift vs IPS targets | M | — |
| FR-803 | **Model portfolios** for the moderate profile, labelled examples not advice: (a) three-fund (US total / ex-US total / US aggregate bond), e.g., 51/29/20 at 80/20 with 36% ex-US; (b) global one-fund + bonds (VT + BND); (c) three-fund + 10-20% factor tilt (AVUV/AVDV/AVLV); (d) target-date fund reference; (e) core + 10% satellite. Each shows ER, expected-return band (CMA conservative/median/optimistic), historical/stress drawdowns | M | [ACAD §6.2, §9; CR-B §1.3; MKT §8] |
| FR-804 | **Rebalancing engine**: threshold bands (default ±5 percentage points absolute, or ±25% relative for sleeves < 20%) checked at a set cadence (default: drift is checked at every monthly review per WORKFLOW §3.4; trades are proposed only when a band is breached; a full rebalance review is forced once a year even inside the bands). Prefers **cash-flow rebalancing** (direct new contributions to underweights), then rebalancing inside tax-advantaged accounts, then taxable sales with an estimated capital-gains cost. Output: a trade list for the user to place manually | M | [ACAD §6.2 automation] |
| FR-805 | Contribution planner: per-paycheck routing following the waterfall + IPS weights; annual limits tracked against YTD | M | [SAV §3] |
| FR-806 | **Lump-sum vs staged planner**: default lump sum; staged option over 3-12 months (6 suggested if the user answers "no" to the 20% drop question; default 3-6 installments if excess > 25% of existing portfolio). Shows historical probability lump sum wins (~2/3) and the expected cost of staging. Payroll contributions are always immediate | M | [ACAD §10; SAV §4.4] |
| FR-807 | **Stress tests** (§10.7): historical scenarios (2000-02 dot-com, 2007-09 GFC, 2020 COVID, 2022 stock-bond drawdown, 1970s stagflation proxy, Japan 1990-2003 lost decade, US 2000-09 lost decade) replayed on current weights by asset-class proxies; show $ and % drawdown, months to recover, and "Would you hold?" prompt tied to the risk questionnaire | M | [ACAD §7.2; CR-A §4.2.4; MKT §11] |
| FR-808 | **Monte Carlo** (10,000 paths, bootstrapped monthly returns or lognormal with CMA means): terminal wealth percentiles (5/25/50/75/95), probability of reaching a goal, max drawdown distribution. Expected returns = 50/50 blend of CAPE-implied and historical; factor premiums × 0.5. A concentrated-stock option uses a skewed single-stock distribution to show the median-vs-mean gap | M | [ACAD §2.1, §4.1, §8.2, §12] |
| FR-809 | Personal **behavior gap**: dollar-weighted (IRR) vs time-weighted returns from logged cash flows | S | [ACAD §6.2] |
| FR-810 | Turnover tracker: annualized turnover from logged trades; warn if > 50% | M | [ACAD §6.1] |
| FR-811 | Guardrail panel: live status of every IPS rule (satellite, single stock, intl min, factor max, thematic, crypto, leverage, EF funded, match captured) | M | [ACAD §12] |
| FR-812 | Glide-path note: equity share review at life events; Anarkulova et al. 2023 all-equity 50/50 idea shown as **contested**, not default | C | [ACAD §7.2] |

### 5.9 Module 9: Decision Journal & Pre-Trade Checklist (FR-9xx)

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-901 | **Decision record** for every buy/sell/allocation change: date, security, action, amount, account, style, thesis (why, what must be true, key risks), valuation snapshot (score, DCF values, price), evidence links (papers/rules), creator influences (and whether sponsored), emotional state (1-5 + free text), expected holding period, **pre-mortem** ("It's 3 years later and this failed; why?"), exit/review criteria, review date | M | [ACAD §6.1, §6.3] |
| FR-902 | **Pre-trade checklist** (blocking for the "ready" state; the app never blocks the user's real-world action, it just won't mark the plan ready). Items listed in §10.8 | M | [ACAD §6; CR-B §4.2] |
| FR-903 | **Cooling-off timer**: new stock ideas get a 72-hour default (configurable 48-72 h) before the checklist can be completed. ETF rebalances and payroll contributions are exempt | M | [ACAD §6.1] |
| FR-904 | **Sell checklist**: "Would you buy this today at this price?"; has the thesis broken (vs pre-set criteria)?; tax impact (ST vs LT, harvestable loss, wash-sale check across all accounts); is this the disposition effect (selling a winner / holding a loser)? Thesis shown next to P/L to reduce anchoring | M | [ACAD §6.3] |
| FR-905 | Scheduled reviews: on review date, compare the outcome to the thesis; record the lesson; classify as skill/luck/process error. Process quality is scored separately from outcome | S | — |
| FR-906 | Journal analytics: hit rate vs SPY/VT over the same holding period, average holding period, turnover, % of decisions influenced by sponsored content, recurring errors | S | [ACAD §6.1, §6.2] |
| FR-907 | Export journal to Markdown/CSV; immutable after "executed" status (edits create amendments) | M | — |

### 5.10 Module 10: Optional AI Assistant (FR-10xx)

| ID | Requirement | Pri | Source |
|---|---|---|---|
| FR-1001 | Provider router: `LLM_PROVIDER = none | openrouter | ollama | opencode`. Default `none`. Order on failure: OpenRouter `:free` → Ollama local → none | M | [TECH §4] |
| FR-1002 | OpenRouter: discover free models at startup via `GET /api/v1/models` (`id` endswith `:free` or zero pricing); never hard-code model IDs; cache list 24 h; show remaining daily quota (assume ~50/day, ~20/min [UNVERIFIED]); fall back on 429 | M | [TECH §4.1] |
| FR-1003 | Ollama via OpenAI-compatible `http://localhost:11434/v1`; **only local models** for anything touching portfolio data (no ollama.com cloud models) | M | [TECH §4.3] |
| FR-1004 | opencode: dev-time KB curation (`opencode run`) and optional experimental `OpencodeProvider` via `opencode serve` (port 4096, basic auth) | C | [TECH §4.2] |
| FR-1005 | **Grounded Q&A ("Ask the KB")**: retrieve top-k KB chunks (DuckDB FTS; embeddings via Ollama `nomic-embed-text` if available) plus any computed app numbers; system prompt: *answer only from provided sources, cite source IDs `[S#]`, say when sources don't cover it, do not recommend specific trades*. Post-check rejects/flags answers with no citations or with numbers not present in the provided context | M | [TECH §4.4] |
| FR-1006 | Allowed LLM tasks: explain a computed metric; summarize a KB topic; draft transcript/paper summaries **as pending review**; rephrase consensus/dissent. Forbidden: producing prices/ratios/rates, buy/sell calls, filling scores | M | [TECH §4, §6] |
| FR-1007 | **Privacy filter**: before any cloud call, strip balances, account names, holdings amounts and PII; send only generic questions + KB excerpts. A toggle "allow portfolio context" is available only for Ollama | M | [TECH §4.1] |
| FR-1008 | Every LLM output is labelled "AI-generated explanation; verify against cited sources" plus the LLM disclaimer (§16.4); cached by `sha256(provider, model, template_ver, prompt)` | M | [CR-A §2.2 AI advice; TECH §5.4] |
| FR-1009 | No-LLM parity: every page must render fully with `LLM_PROVIDER=none`; the Ask page then offers KB keyword search with snippets | M | NFR-01 |

---

## 6. Non-functional requirements

| ID | Requirement | Measure |
|---|---|---|
| NFR-01 | **Works offline / without an LLM.** All modules render from the shipped KB + DuckDB cache with no network and `LLM_PROVIDER=none` | Offline test suite passes with network disabled |
| NFR-02 | **$0 operating cost.** Only free tiers; no paid proxies; optional keys (FRED, Tiingo, Finnhub, OpenAlex, S2, OpenRouter) are free | Dependency/API audit in README |
| NFR-03 | **Caching** per [TECH §5.4]: prices daily incremental (+ monthly full re-pull for split/dividend re-adjust); EDGAR weekly bulk; `.info`/Finnhub 7 days; FRED/Treasury daily; French/AQR monthly with ETag/If-Modified-Since; holdings monthly/manual; papers weekly; RSS weekly; transcripts forever; LLM forever (versioned); UI `st.cache_data(ttl=3600)` | Cache hit-rate logged; repeat page load < 2 s |
| NFR-04 | **Data freshness labels** on every number: `as_of`, `source`, `fetched_at`, `confidence (high/medium/low)`. Badges: green (fresh), amber (> 1× cadence), red (> 2× cadence). Rate data (HYSA/CD/MMF) stale after 7 days; CMAs 12 months; creator picks 120 days, commentary 60 days, positions 365 days | Unit tests on badge logic; UI audit: zero unlabelled numbers |
| NFR-05 | **Stale-while-error**: failed refresh keeps cached data with "stale since X"; never a blank panel | Fault-injection test |
| NFR-06 | **Privacy — local only.** User data in `data/fintec.duckdb` (git-ignored); no telemetry; no brokerage credentials; cloud LLM calls pass the privacy filter (FR-1007); optional encryption of user tables | Network log review; `.gitignore` test |
| NFR-07 | **Secrets** in `.env` via pydantic-settings `SecretStr`; `.env.example` committed; optional gitleaks pre-commit | [TECH §5.5] |
| NFR-08 | **Rate-limit compliance** (config.toml): SEC ≤ 5 req/s with declared User-Agent; FRED ≤ 1 req/s; Tiingo ≤ 45/h; Alpha Vantage ≤ 20/day; Finnhub ≤ 50/min; OpenAlex ≤ 5/s; S2 ≤ 0.9/s; arXiv 1 per 3 s; YouTube transcripts ≤ 6/h; OpenRouter ≤ 15/min | [TECH §5.6]; token-bucket unit tests |
| NFR-09 | **Determinism & explainability**: same inputs + same data snapshot → same scores; every score shows its component breakdown and rule IDs; rules versioned (`rules_version` stored on every journal entry) | Golden tests |
| NFR-10 | **Correctness tests**: parsers (French, AQR, EDGAR dedupe/TTM, issuer CSV), calculators (hypothesis property tests), golden factor regression (VTI: Mkt-RF ≈ 1.0, SMB ≈ 0, R² > 0.95), KB schema tests, DOI resolution in CI, channel allowlist test | [TECH §5.7] |
| NFR-11 | **Performance**: cold start < 10 s; ETF compare (5 funds, 10 y) < 3 s from cache; screener over 1,000 stocks < 5 s (DuckDB/polars); Monte Carlo 10k paths < 5 s | Benchmarks in CI |
| NFR-12 | **Resilience**: every adapter has `available()`; sidebar "Data sources" panel shows status per source; graceful degradation when keys are missing | [TECH §5.5] |
| NFR-13 | **Usability**: plain English, glossary tooltips, all jargon linked to KB topics; accessible colour palette; no dark patterns, no gamified trading | — |
| NFR-14 | **Legal hygiene**: personal use only; no redistribution of yfinance data; creator content stored as paraphrased summaries + links; brief attributed quotes only | [TECH §1.1, §3.4] |
| NFR-15 | **Maintainability**: ruff + mypy clean; adapters isolated; KB editable by hand with JSON Schema validation; `python -m fintec.jobs <job> [targets]` CLI (e.g. `python -m fintec.jobs refresh prices macro feeds`) scheduled via OS scheduler (not in-process) | [TECH §5.1] |
| NFR-16 | **Auditability**: every journal entry snapshots inputs, scores, rules_version, data as-of dates | — |

---

## 7. Data model

Stored in DuckDB (`data/fintec.duckdb`) unless noted. KB entities are files under `knowledge_base/` loaded into read-only tables at startup. `*` = primary key.

### 7.1 User & planning entities

| Entity | Fields |
|---|---|
| **UserProfile** | id*, created_at, updated_at, age, filing_status, state, t_fed, t_state, magi_band, employment_type, job_stability (1-5), income_earners (1/2), dependents, homeowner, hdhp_eligible, monthly_essential_expenses (M), liquid_savings (S), risk_tolerance_score, risk_capacity_score, risk_profile, horizon_years |
| **EmployerPlan** | id*, user_id, plan_type (401k/403b), match_formula (e.g., `[{"match":1.0,"up_to":0.04},{"match":0.5,"up_to":0.06}]`), vesting_schedule, roth_option, after_tax_option, in_plan_conversion, student_loan_match, plesa, fund_menu (ticker, ER) |
| **ContributionYTD** | user_id, tax_year, account_type, amount, as_of |
| **Debt** | id*, user_id, name, balance, apr, min_payment, type |
| **KnownExpense** | id*, user_id, name, amount, due_date, priority |
| **EFAdjustment** | user_id, factor_code, months, applied (bool), user_override |
| **IPS** | id*, user_id, version, effective_date, targets JSON {equity, bond, cash, us_share, intl_share, factor_tilt, satellite_cap, single_stock_cap, thematic_cap, crypto_cap}, rebalance_rule, review_cadence, prohibited [], notes, override_reasons [] |
| **CashAccount** | id*, user_id, institution, product_type (checking/HYSA/MMF/T-bill/ETF/CD/I-bond), balance, apy_base, apy_promo, promo_expiry, min_balance, fdic_cert, direct_bank (bool), fintech_intermediated (bool), maturity_date, purchase_date, exempt_share, as_of |
| **Account** | id*, user_id, type (401k/roth_ira/trad_ira/hsa/taxable/cash), custodian, name |
| **Holding / Lot** | id*, account_id, ticker, shares, cost_basis, acquired_date, lot_method |
| **Transaction** | id*, account_id, date, ticker, action (buy/sell/div/reinvest/contrib/withdraw), shares, price, amount, journal_id |
| **WatchlistItem** | id*, ticker, style, added_date, review_date, notes |
| **DecisionRecord** | id*, created_at, status (draft/ready/executed/abandoned/reviewed), action, ticker, amount, account_id, style, thesis, must_be_true [], risks [], premortem, exit_criteria, review_date, emotion_score, emotion_note, creator_influences [{creator_id, item_id, sponsored}], evidence_refs [], score_snapshot JSON, dcf_snapshot JSON, rules_version, data_asof JSON, checklist JSON, cooling_off_until, acknowledgements [], outcome, lesson, process_grade |

### 7.2 Market & reference data (cache)

| Entity | Fields |
|---|---|
| **Security** | ticker*, name, type (stock/etf/mf/mmf), cik, exchange, sector (GICS), industry, country, currency, is_active |
| **ETFMeta** (kb + cache) | ticker*, issuer, index, inception, expense_ratio, aum, holdings_count, category, flags [core, factor, dividend, thematic, active, leveraged, inverse, covered_call, buffer, crypto, single_stock_income], asset_class, region, tax_notes, issuer_url, as_of, verified |
| **ETFHolding** | etf*, as_of*, holding_ticker*, name, weight, sector, country, source |
| **Price** | ticker*, date*, close, adj_close, volume, dividend, split_factor, source, fetched_at |
| **Fact** (EDGAR) | cik*, taxonomy*, tag*, unit*, end*, fp*, form*, val, start, filed, accn, frame |
| **Fundamentals (derived)** | ticker*, period_end*, period_type (FY/TTM), revenue, gross_profit, op_income, net_income, cfo, capex, sbc, total_assets, equity, total_debt, cash, interest_expense, shares_diluted, dividends_paid, buybacks, ebitda, derived ratios… source, as_of |
| **MacroSeries** | series_id*, date*, value, source, fetched_at |
| **FactorReturn** | dataset* (FF5_US, FF5_DevExUS, FF5_EM, MOM_US, QMJ, BAB…), date*, factor*, value (decimal), frequency |
| **RateQuote** | id*, vehicle, institution/ticker, term, rate, rate_type (APY/SEC/7-day/coupon-equiv/discount), as_of, source_url, confidence |
| **ScoreResult** | ticker*, style*, computed_at*, rules_version, composite, subscores JSON, flags [], label, data_asof JSON |
| **Video** | video_id*, channel_id, creator_id, title, published, description, url, topics [], type, sponsored, paid_promotion, sponsors [], transcript_cached, review_status |
| **PaperMeta** (cache) | doi*, cited_by_count, oa_url, refreshed_at |
| **LLMCache** | key* (sha256), provider, model, template_ver, prompt, response, created_at |

### 7.3 Knowledge-base entities (files; schemas in §11)

`Paper`, `CreatorProfile`, `CreatorItem` (principle/framework/pick/commentary/product_mention/position), `Conflict`, `Rule`, `Topic`, `ETFUniverse`, `Limits` (by year), `IBondRates`, `CMA`, `MarketSnapshot` (narrative stats), `SPIVA`, `Glossary`.

---

## 8. Data sources and fallbacks

| Data need | Primary | Fallback 1 | Fallback 2 | Notes / caveats |
|---|---|---|---|---|
| Adjusted daily prices | yfinance 1.8.x (pinned) | Tiingo free (50/h, 1k/day) | Alpha Vantage (25/day; unadjusted only on free) | Stooq **dropped** (JS proof-of-work); manual CSV import. Cross-validate > 1% diffs [TECH §1.1, §1.6, §1.8, §1.9] |
| US fundamentals | SEC EDGAR companyfacts bulk (~1.4 GB ZIP weekly) + per-company API (no key; ≤ 5 req/s; User-Agent required) | yfinance `.financials` | Finnhub `/stock/metric` / FMP free | EDGAR wins; tag fallback map; dedupe; TTM [TECH §1.2] |
| Ticker ↔ CIK | `sec.gov/files/company_tickers.json` | — | — | |
| Macro (rates, CPI, PCE, unemployment, spreads, oil) | FRED API (free key; 120/min) | FRED keyless `fredgraph.csv` | BLS API v2 / EIA API | `SP500` is price-only; `SAVNRNJ` discontinued [TECH §1.3] |
| T-bill yields | Treasury daily par yield curve CSV / daily bill rates | FRED `DGS3MO`, `DGS1` | Fiscal Data auctions API | Never `DTB3` for APY; `avg_interest_rates` is not an earnable rate [TECH §1.4] |
| HYSA / CD rates | **User-curated JSON** (Bankrate/DepositAccounts as reference, verify on bank site) | FDIC national rates (manual, monthly) | — | No free API [SAV §5] |
| MMF / T-bill ETF yields | Sponsor/issuer pages (manual refresh) | yfinance (low confidence) | SEC N-MFP | [SAV §1.3] |
| I bonds | `knowledge_base/savings/ibond_rates.json` (manual May 1 / Nov 1) | TreasuryDirect page link | — | No API [TECH §1.4] |
| IRS limits & brackets | `knowledge_base/accounts/limits_<year>.json`, `tax_brackets_<year>.json` (manual annually) | — | — | IR-2025-111, Notice 2025-67, Rev. Proc. 2025-19/2025-32 |
| Factor returns | Ken French Data Library ZIPs (US, Dev ex-US, EM, momentum) | AQR datasets (QMJ, BAB, HML Devil) | — | Percent → decimal; −99.99 missing [TECH §1.10, §1.11] |
| ETF holdings | Issuer files (iShares CSV, SSGA XLSX, Vanguard/Schwab/Avantis) via manual-refresh button | User-uploaded CSV | SEC N-PORT (quarterly, 2-5 months lag) | Fragile; schema tests [TECH §1.12] |
| ETF metadata (ER etc.) | `knowledge_base/etfs/etf_universe.json` (hand-verified quarterly) | yfinance `funds_data` | — | |
| Analyst trends / news / earnings calendar | Finnhub free | yfinance `.recommendations`, `.news` | Alpha Vantage NEWS_SENTIMENT | Labelled opinion |
| CAPE | Shiller `ie_data.xls` | multpl.com display check | — | Monthly |
| Forward P/E, GTM stats, CMAs | Manual entry into `knowledge_base/market/*.json` from FactSet, JPM GTM/LTCMA, Vanguard VEMO, BlackRock, AQR | — | — | Link-and-review cadence [MKT §9.2] |
| Papers metadata | OpenAlex (free key, $1/day budget) | Semantic Scholar (1 rps with key) | CORE, arXiv | SSRN link-only [TECH §2] |
| Creator videos | YouTube channel RSS (allowlist) | yt-dlp `--flat-playlist` metadata | YouTube Data API `playlistItems.list` (free key) | Transcripts optional, local, rate-limited [TECH §3] |
| Podcast | Rational Reminder libsyn RSS | — | — | |
| LLM | OpenRouter `:free` | Ollama local | none (templates) | [TECH §4] |
| Bank verification | FDIC BankFind (link / API) | — | — | |

---

## 9. UI page map and wireframes

### 9.1 Navigation (Streamlit `st.navigation`)

Page files are canonical in `IMPLEMENTATION_PLAN.md` §1 and are used verbatim in `WORKFLOW.md`. The module numbers in §5 (Module 1-10) are requirement groupings, not page numbers.

```
Home.py (Dashboard)                     Modules shown
├─ 0_Profile                            M1  Profile & IPS, risk questionnaire, first-run acknowledgement
├─ 1_Savings_and_Emergency_Fund         M2  (tabs: Emergency Fund · Compare Vehicles · Split my $20K · Calculators)
├─ 2_Account_Order_of_Operations        M2  (tabs: Waterfall · Roth vs Traditional · Backdoor/pro-rata · Limits)
├─ 3_ETF_Explorer                       M3  (tabs: Browse · Compare · Overlap · Factor Regression · Costs · Factor Tilts)
├─ 4_Portfolio_and_Accounts             M8  (tabs: Holdings · Allocation · Rebalance · Stress Test · Monte Carlo · Contributions · TLH)
├─ 5_Stock_Research                     M4  (tabs: Screener · Company · Valuation (DCF/Reverse) · Dividend Safety · Fit)
├─ 6_What_Experts_Say                   M5  (tabs: Topic Lens · Consensus Matrix · Creators · New Videos/Digest · "I just saw a pitch")
├─ 7_Research_Library                   M6  (tabs: Papers · Topics · Find More)
├─ 8_Market_Context                     M7
├─ 9_Decision_Journal                   M9  (tabs: New Decision · Checklist · Open · Reviews · Analytics)
├─ 10_Ask_the_KB                        M10 (optional LLM; KB keyword search when disabled)
└─ 11_Settings                          ⚙   (keys, LLM provider, refresh jobs, source status, KB version, export/backup)
```

**Persistent chrome:** sidebar shows profile summary, guardrail status lights, data-source health, LLM status/quota, KB version and `last_reviewed`. Footer on every page: short disclaimer (§16.1) + "Data as of" summary.

### 9.2 Wireframe-level descriptions

**Home (Dashboard)**
```
┌ Disclaimer strip ─────────────────────────────────────────────────────┐
│ [Next best action card]  e.g. "Capture full 401(k) match (+$1,800/yr)"│
│ [EF status gauge: $13.5K / $15.8K target]  [Guardrails: 7✓ 1⚠]        │
│ [Net allocation donut vs IPS]  [Drift bars]                            │
│ [Market regime banner – descriptive]  [Cash yields: HYSA vs 13-wk bill]│
│ [Open decisions / reviews due]  [New creator videos (3, 1 paid promo)] │
└───────────────────────────────────────────────────────────────────────┘
```
"Next best action" is the first unmet step of the waterfall or the first failing guardrail.

**1 Profile & IPS** — left: stepper wizard (About you → Income & taxes → Accounts & match → Debts & goals → Risk questionnaire). Right: live IPS preview with editable targets; out-of-range edits open a "reason" dialog. Buttons: Save version, Export IPS.

**2 Savings & Cash**
- *Emergency Fund:* inputs (M, flags) → adjustment table with toggles → target months & $ with markers (3×, 5× Tilbury, 6×) → tier bar (checking / HYSA / T-bills) → warnings.
- *Compare Vehicles:* table (vehicle, quoted yield & type, after-tax yield, HYSA-equivalent, liquidity, insurance, state-tax, as-of badge) sortable by after-tax yield; HYSA curation editor; FDIC BankFind links; fintech warnings.
- *Account Waterfall:* step list with ✓/✗/partial, $ allocated from `S`, creator-variant toggle chips; Sankey chart; Roth eligibility and backdoor pro-rata calculator.
- *Calculators:* break-even fee, CD penalty, I bond penalty, opportunity cost, lump vs staged, prepay vs invest (C), rent vs buy (C).

**3 ETF Explorer**
- *Browse:* filterable grid (category, ER, AUM, flags) with badges.
- *Compare:* pick 2-5 → stats table, growth-of-$10K, rolling 3-y, drawdown chart, score breakdown bars, warning badges.
- *Overlap:* matrix heatmap of pairwise weighted overlap + top shared holdings + look-through table.
- *Factor Regression:* model selector, date range → loadings bar with 95% CI whiskers, alpha, R², n_months, interpretation text.
- *Costs:* ER-gap and advisory-fee drag sliders → line chart of $ difference.
- *Factor Tilts:* per-fund evidence card, haircut premium, worst relative stretch, tolerance question, suggested size.

**4 Stock Research**
- *Screener:* style selector; filter builder; results table (composite, sub-scores sparkbars, flags, label); "variants tried: N" banner; base-rate card pinned.
- *Company:* header (price, cap, sector, data as-of), 10-y fundamentals charts, metric table with sector percentiles, red flags, creator mentions (dated), Wall Street view.
- *Valuation:* DCF inputs (3 scenarios) → intrinsic values, probability-weighted value, margin-of-safety verdict; reverse-DCF implied growth vs history; sensitivity heatmap.
- *Dividend Safety:* score dial, component table, total-return vs price-return chart, tax-drag box, "free dividends" explainer.
- *Fit:* overlap with user ETFs, new look-through weight, satellite headroom, single-stock cap, sector mix; "Start decision" button → Journal.

**5 Expert Lens**
- *Topic Lens:* topic picker / free text → **Academic verdict card (top)** → 8 creator cards (grade, statement, source link, date, conflict chips, stale badge) → "Agreement summary".
- *Consensus Matrix:* creators × topics grid coloured for/against/mixed/silent; click a cell → evidence.
- *Creators:* profile pages (credentials verified/unverified, incentives, trust weight, best use, positions timeline).
- *New Videos:* feed with type tags, paid-promo red rows, "Draft summary" (LLM) / "Mark reviewed".

**6 Research Library** — search bar + facet filters; paper cards (title, year, journal, strength pill, peer-reviewed pill, summary, "Used in: FR-306, SINGLE_STOCK_CAP" links, DOI link, verification flag); topic pages rendered from markdown; "Find more" results marked "not reviewed".

**7 Market Context** — snapshot card grid with percentile mini-bars and as-of badges; regime banner; P/E vs CAPE chart; CMA consensus band chart; concentration widget; international panel; factor panel; themes accordion; "What this means for you" box; event calendar.

**8 Portfolio & Allocation**
- *Holdings:* per-account editable tables + CSV import mapper.
- *Allocation:* sunburst (account → asset class → fund), region/factor/sector bars, drift vs IPS.
- *Rebalance:* band settings, proposed trades list (cash-flow first), tax estimate, "Log as decision".
- *Stress Test:* scenario selector → drawdown $/%, recovery months, "Would you hold?" prompt.
- *Monte Carlo:* fan chart, percentile table, goal probability, assumption panel (CMA conservative/median/optimistic).
- *Contributions:* per-paycheck routing, YTD vs limits.

**9 Decision Journal** — *New Decision* form (FR-901) → *Checklist* with item states and cooling-off countdown → status "Ready" → user marks "Executed" after acting in their broker; *Reviews* due list; *Analytics* charts.

**10 Ask the KB** — chat box; each answer shows citations `[S#]` expanding to KB snippets; banner with provider/model/quota; privacy toggle (Ollama only); when LLM disabled: keyword search results.

**⚙ Settings** — `.env` key presence (never values), provider choice, refresh buttons per job with last run/next due, source status table, KB version/diff, backup/restore DuckDB, encryption toggle.

---

## 10. Scoring model and calculation specification

All weights and thresholds live in `knowledge_base/rules/*.yaml` (versioned; each carries `rule_id`, `value`, `evidence_refs`, `rationale`). **These weights are design choices informed by the research, not estimates taken from it** [ACAD §6.4]. Any signal with `evidence_tier ≠ canonical` may not carry > 10% weight [ACAD §4.2].

### 10.1 Emergency-fund months (FR-201) [SAV §2.2]

| Code | Factor | Months |
|---|---|---|
| BASE | Base | 3.0 |
| SINGLE_INCOME | Single-income household | +1.0 |
| VARIABLE_PAY | Variable/commission/bonus-heavy, gig or 1099 | +1.5 (self-employed +3.0) |
| LOW_SECURITY | Layoff-prone industry, recent layoffs, contract role | +1.0 |
| SPECIALIZED | Specialized/senior role | +0.5 |
| DEPENDENTS | Dependents | +1.0 |
| HOME_CAR | Homeowner or older car with no car fund | +0.5 |
| HDHP_GAP | HDHP without HSA cash for the deductible | +0.5 |
| HEALTH | Chronic condition / high expected medical | +0.5 |
| DUAL_STABLE | Dual, stable, uncorrelated incomes | −1.0 |
| SECURE_ROLE | Government/tenured/very high-demand | −0.5 |
| ROTH_BACKSTOP | Roth IRA contribution basis available | −0.5 (combined cap with SECURE_ROLE −1.0) |
| MACRO_LTU | BLS 27+ week share > 25% | +0.5 |

```
target_months = clamp(3.0 + Σ adj, 3.0, 12.0)
EF_target     = max(target_months × M, highest_insurance_deductible + 1 × M)
starter_EF    = max(1.0 × M, 2000)
```
Worked default for the persona (single, renter, stable W-2, Nov 2026 macro): 3 + 1.0 + 0.5 = **4.5 months**; with M = $3,000 → $13,500 EF, ≈ $6,500 investable [SAV §4.5]. The UI also shows 6 × M ($18,000) as the "conservative for moderate risk" marker [ACAD §11].

**`lean_conservative` toggle (default `false`).** When the user turns it on, `target_months = max(target_months, 6.0)`. Default off, so the computed model drives the target (SPEC, WORKFLOW §2.3 and the savings plan all use 4.5 months for the persona). The onboarding wizard *suggests* turning it on when the user answers "sell" to the drawdown question or rates their stress about losses at 4-5 out of 5. With M = $3,000 the toggle moves the EF from $13,500 to $18,000 and the investable excess from $6,500 to $2,000.

### 10.2 Cash and account formulas [SAV §1.4–§1.6, §3.3–§3.4]

```
after_tax_HYSA  = y_hysa  × (1 − t_fed − t_state)
after_tax_CD    = y_cd    × (1 − t_fed − t_state)
after_tax_Tbill = y_tbill × (1 − t_fed)
after_tax_MMF   = y_mmf   × (1 − t_fed − t_state × (1 − exempt_share))
after_tax_Ibond = y_ibond × (1 − t_fed)            # + deferral note
HYSA_equiv_Tbill = y_tbill × (1 − t_fed) / (1 − t_fed − t_state)
real_after_tax  = (1 + after_tax) / (1 + CPI_yoy) − 1
fee_break_even_balance = (fee / (1 − t)) / (apy_a − apy_b)
cd_net_yield(m) = (APY·m/12 − penalty_months·APY/12) / (m/12)
ibond_eff(n)    = r × (n − 3) / n          (12 ≤ n < 60)
backdoor_taxable_fraction = 1 − basis / (Dec31_trad_IRA_total + conversion)
match_value_annual = Σ tiers min(deferral%, tier_cap) × tier_rate × salary × vested_fraction
```
Yields compared on a like-for-like basis: T-bill **coupon-equivalent / bond-equivalent** (never discount-basis DTB3), HYSA/CD APY, MMF 7-day, ETF 30-day SEC.

**10.2.4 Roth vs Traditional default** (single filer; IR-2025-103 brackets): 10–12% → Roth; 22% → Roth IRA, 401(k) Roth or 50/50 (Traditional if high-tax state now and no-tax state in retirement); 24% → split/Traditional-leaning 401(k), Roth/backdoor IRA; 32%+ → Traditional 401(k), backdoor Roth IRA. Modifiers: high expected income growth → Roth; early retirement plan → Traditional; tax diversification → some of each.

### 10.3 Stock buy-candidate scoring (FR-402–FR-418)

**10.3.1 Metric definitions** (TTM unless noted; EDGAR-derived; EV = market cap + total debt − cash)

| Block | Metric | Formula | Direction | Evidence |
|---|---|---|---|---|
| Value | Earnings yield | EBIT / EV | ↑ | FF 1992/93; LSV 1994 |
| Value | FCF yield | (CFO − capex) / EV | ↑ | — |
| Value | Owner-earnings yield | (CFO − capex − SBC) / market cap | ↑ | [CR-B §3.2] |
| Value | Book-to-market | equity / market cap | ↑ | FF 1992 (weight ≤ 20% of block) |
| Value | Sales-to-EV | revenue / EV | ↑ | — |
| Quality | Gross profitability | gross profit / total assets | ↑ | Novy-Marx 2013 (≥ 25% of quality block) |
| Quality | Operating profitability | operating income / book equity | ↑ | FF 2015 RMW |
| Quality | ROIC | NOPAT / (debt + equity − cash) | ↑ | QMJ profitability |
| Quality | Profitability growth | 5-y change in GP/A | ↑ | QMJ growth |
| Quality | Margin stability | −stdev(gross margin, 5 y) | ↑ | QMJ safety |
| Quality | Accruals | −(NI − CFO) / total assets | ↑ | — |
| Quality | Net payout | (dividends + net buybacks) / market cap | ↑ | QMJ payout (working-paper variant) |
| Safety | Net debt / EBITDA | (debt − cash) / EBITDA | ↓ | QMJ safety |
| Safety | Interest coverage | EBIT / interest expense | ↑ | — |
| Safety | Beta (60m vs market) | regression | ↓ | Frazzini-Pedersen 2014 |
| Safety | Earnings volatility | stdev(ROE, 10 y) | ↓ | QMJ safety |
| Safety | Piotroski F-score | 9 binary signals | ↑ | Piotroski 2000 |
| Momentum | 12-1 return | P(t−1m)/P(t−12m) − 1 | ↑ | Jegadeesh-Titman 1993 |
| Dividend | Dividend growth consistency | consecutive years of DPS increases; 5-y DPS CAGR; FCF payout in 30–70% band | ↑ | quality screen only [ACAD §5.2] |
| Growth (contested) | Revenue & owner-earnings 5-y CAGR | CAGR | ↑ | not canonical → ≤ 10% weight |
| Risk flag input | 1-y volatility | stdev daily × √252 | — | Ang et al. 2006 |

**10.3.2 Normalization**
1. Winsorize each metric at the 1st/99th percentile of the universe.
2. Convert to **percentile rank within GICS sector** (requires ≥ 20 peers; otherwise rank within the whole universe and flag "thin sector").
3. Flip direction where ↓; percentile 0–100.
4. Block sub-score = weighted mean of available metric percentiles (equal weights inside blocks unless specified). If < 60% of a block's weight has data → sub-score = null and composite labelled "insufficient data".
5. Financials and REITs: EV-based and gross-profit metrics excluded; use P/B, ROE, and (REITs) FFO yield where available; card notes "limited model coverage."

**10.3.3 Style weights** (composite = Σ wₖ·subₖ, 0–100)

| Style | Quality | Value | Safety | Momentum | Dividend growth | Growth | Style gates |
|---|---|---|---|---|---|---|---|
| `quality_value` (default) | 35 | 30 | 15 | 10 | 10* | — | none |
| `dividend_growth` | 40 | 25 | 20 | 5 | 10 | — | yield > 0; ≥ 5-y increase streak; dividend safety ≥ 60 |
| `quality_growth` (Carlson-style) | 45 | 20 | 15 | 10 | — | 10 | revenue 5-y CAGR > 0; GP/A ≥ sector median; moat checklist completed |
| `deep_value` (Carlin-style) | 25 | 45 | 20 | 5 | 5 | — | DCF worst case ≥ price (or user override with reason) |

\* If the user doesn't want dividends, the 10 is reassigned to quality [ACAD §12]. Momentum never exceeds 10 [ACAD §3.2].

**Penalties:** each soft red flag −5 (max −20). Blocking flags set label "Blocked" regardless of score.

**10.3.4 Red flags**

| Flag | Rule | Type | Source |
|---|---|---|---|
| VALUE_TRAP | value pct ≥ 70 AND (GP/A pct ≤ 30 OR TTM FCF < 0 OR TTM net income ≤ 0) | soft | [ACAD §3.3] |
| FALLING_KNIFE | 12-1 momentum pct ≤ 10 AND value pct ≥ 70 | soft | [ACAD §3.2] |
| LOTTERY | 1-y volatility in top decile of universe | soft | [ACAD §3.5] |
| YIELD_TRAP_WARN | yield > 2× sector median OR EPS payout > 80% OR FCF payout > 100% | soft | [MKT §6] (the more conservative "or" rule) |
| YIELD_TRAP_FAIL | yield > 2× sector median AND FCF payout > 100% | **blocking** | [ACAD §5.1] |
| UNPROFITABLE | TTM operating income < 0 | soft (configurable to blocking for the moderate profile) | [MKT §5.1] |
| HIGH_LEVERAGE | net debt/EBITDA > 4 OR interest coverage < 3 (non-financials) | soft | QMJ safety |
| NEG_OWNER_EARNINGS | owner earnings < 0 in ≥ 3 of last 5 years | soft | [CR-B §3.2] |
| DILUTION | diluted shares CAGR > 5%/y over 3 y | soft | — |
| PAID_PROMOTION | any KB/video item with `paid_promotion=true` for ticker in last 365 days | **blocking** (overridable with acknowledgement) | [CR-A §2.2.6, §3.6] |
| MICROCAP_ILLIQUID | market cap < $300M OR 3-month ADV < $1M | **blocking** | [ACAD §3.1 size/junk] |
| STALE_DATA | latest filing > 210 days old | **blocking** until refresh | NFR-04 |
| DATA_DISCREPANCY | EDGAR vs provider value differs by > 10% on any scored metric, or > 30% of metrics missing | soft (score shown greyed) | [TECH §1.13, §6.1] |

**Labels:** composite ≥ 70 and no blocking flags and FR-418 conditions → "Buy candidate (satellite)"; 55–69 → "Watch"; < 55 → "Pass".

**10.3.5 Dividend safety score (0–100)**

| Component | Points |
|---|---|
| FCF payout | < 60%: 25 · 60–80%: 15 · 80–100%: 5 · > 100%: 0 |
| EPS payout | < 60%: 15 · 60–80%: 10 · > 80%: 0 |
| Net debt / EBITDA | < 1.5: 15 · 1.5–3: 10 · 3–4: 5 · > 4: 0 |
| Interest coverage | > 10: 10 · 5–10: 7 · 3–5: 3 · < 3: 0 |
| Increase streak | ≥ 10 y: 10 · 5–9: 6 · 1–4: 2 · 0: 0 |
| DPS growth sustainability | 5-y DPS CAGR ≤ 5-y EPS (or FCF/share) CAGR: 10 · else 3 |
| Earnings stability | 0 loss years in 10: 10 · 1: 5 · ≥ 2: 0 |
| Yield vs sector median | ≤ 1.5×: 5 · 1.5–2×: 2 · > 2×: 0 |

Safe ≥ 70 · Borderline 50–69 · Unsafe < 50. Displayed with total return and annual tax drag `= yield × qualified_rate` (taxable account) [ACAD §5.2].

**10.3.6 DCF and reverse DCF** [CR-B §3.2]
```
OE₀ = (CFO − capex − SBC)_TTM / diluted_shares
OE_t = OE₀·(1+g₁)^t                     t = 1..5
OE_t = OE₅·(1+g₂)^(t−5)                 t = 6..10
TV   = OE₁₀ × terminal_multiple
IV   = Σ_{t=1..10} OE_t/(1+r)^t + TV/(1+r)^10   (+ dividends counted only once; buybacks via per-share growth)
IV_weighted = Σ p_s · IV_s,  s ∈ {worst, normal, best}, default p = (0.3, 0.4, 0.3)
margin_of_safety = IV_weighted / price − 1;   MOS_PASS ⇔ IV_worst ≥ price
Reverse DCF: solve g₁* s.t. IV(g₁*) = price (bisection on [−20%, 50%]); report g₁* vs 5-y/10-y history and sector median
Implied 10-y return ≈ (IV_weighted/price)^(1/10)·(1 + r) − 1   (quadrant x-axis, simplified)
```
Defaults: r = 10% (Carlin hurdle; editable; alternative CAPM r = rf(10y) + β·ERP, ERP from CMA band), g₁ = min(5-y revenue CAGR, 15%), g₂ = 5%, terminal multiple = 15 (worst 10, best 20). If OE₀ ≤ 0, DCF is not computed ("not valuable by this method").

### 10.4 ETF score (FR-309) [ACAD §6.4, §12]

Weights: **expense ratio 30 · factor/goal fit 20 · tracking difference 15 · diversification 15 · liquidity/spread 10 · tax efficiency 10.** Ranked only within comparable categories.

| Component | Formula (0–100) |
|---|---|
| ER | `100 × (ER_max − ER)/(ER_max − ER_min)` within category |
| Factor/goal fit | `100 × (1 − ‖β_fund − β_target‖ / ‖β_max_dist‖)` where β = (Mkt, SMB, HML, RMW, CMA) loadings and `β_target` set by goal (core: (1,0,0,0,0); small value: (1,0.6,0.4,0.2,0); quality: (1,0,0,0.3,0); dividend: (1,0,0.2,0.2,0.1)); n < 36 months → neutral 50 + "insufficient history" |
| Tracking difference | `gap_bp = max(0, |median annual TD| − ER)` in bp; `clamp(100 − 2·gap_bp, 0, 100)` |
| Diversification | `50·min(1, log10(N_holdings)/log10(3000)) + 50·(1 − top10_weight)` |
| Liquidity | `clamp(100 − 10·median_spread_bp, 0, 100)`; −30 if AUM < $100M |
| Tax efficiency | `100 − 20·avg_5y_capgain_dist_%NAV − 30·ordinary_income_share` (floored 0) |

Category adjustments: active without ≥ 10-y net-of-fee outperformance −10; thematic −15; covered-call −20 when goal = accumulation; leveraged/inverse/single-stock option-income → excluded for moderate profile.

### 10.5 Creator trust weights (display ordering and consensus weighting only; never in security scores)

| Creator | Trust weight | Overall alignment | Source |
|---|---|---|---|
| Ben Felix | 0.90 | A | [CR-A §5] |
| The Plain Bagel | 0.85 | A/B | [CR-A §5] |
| Graham Stephan | 0.55 | B/C | [CR-A §5] |
| Whiteboard Finance | 0.50 | B/C | [CR-A §5] |
| Sven Carlin | 0.60 (proposed) | Mixed | [CR-B §5]; weight is a new editorial proposal |
| Joseph Carlson | 0.60 (proposed) | Mixed | [CR-B §5]; proposed |
| Andrei Jikh | 0.55 (proposed) | High on savings/index; low on dividends/crypto | [CR-B §5]; proposed |
| Mark Tilbury | 0.50 (proposed) | High on core; low on "get rich via business" | [CR-B §5]; proposed |

Per-item adjustments: `item_weight = trust × grade_factor × freshness`, grade_factor A=1.0, B=0.8, C=0.5, D=0.2, E=0, NA=0.5; freshness = 1 if not stale else 0.5; sponsored → 0 for scoring, shown with warning.

**Grade-scale harmonization:** [CR-A] uses A–E; [CR-B] uses A/B/C/D/NA with slightly different meanings. KB stores `evidence_alignment` on the [CR-A] A–E scale; [CR-B] D maps to D, NA stays NA.

### 10.6 Consensus / dissent (FR-503)
```
stance_i ∈ {+1 for, −1 against, 0 mixed, null silent}
S = Σ item_weight_i · stance_i / Σ item_weight_i   (non-silent only, n ≥ 4)
consensus  ⇔ |S| ≥ 0.6 and n_nonsilent ≥ 5
contested  ⇔ |S| < 0.3 and n_nonsilent ≥ 4
lean       ⇔ otherwise
```
The **academic verdict** (from the topic's rules/papers: `supports / mixed / contradicts`) is displayed separately and first; it is never averaged with creators.

### 10.7 Stress tests and Monte Carlo (FR-807, FR-808)

**Asset-class proxies** (monthly total return): US equity = French Mkt (RF + Mkt-RF) / VTI after 2001; ex-US = French Developed ex-US market / VXUS; EM = French Emerging; US bonds = AGG/VBMFX; cash = French RF; small value = French small-value portfolio / AVUV; factor sleeves from French factors. Individual stocks: own history if available, else sector proxy + idiosyncratic shock.

**Historical scenarios** (replayed on current weights): Dot-com 2000-03→2002-10; GFC 2007-10→2009-03; COVID 2020-02→2020-03; 2022 stock-bond 2022-01→2022-10; US lost decade 2000-01→2009-12; Japan 1990-01→2003-04 (ex-US proxy applied to US sleeve as "what if"); 1970s stagflation 1973-01→1974-09. Output: peak-to-trough $ and %, months to recover, worst 12-month return.

**Hypothetical shocks:** equities −40% with bonds +5% (deflationary); equities −25% with bonds −10% (inflationary, positive correlation); ex-US −30%/US −10%; top-10 mega-cap −50% (concentration unwind; via look-through).

**Monte Carlo:** 10,000 paths, monthly, horizon = IPS horizon. Mode A: stationary block bootstrap (12-month blocks) of 1970–present proxies, mean-shifted to forward assumptions. Mode B: multivariate lognormal with historical vol/correlation. Forward assumptions per [ACAD §8.2, §12; MKT §8]:
```
E[r_US_eq_real] = 0.5 × (1/CAPE + 0.015) + 0.5 × hist_real_mean
E[r_other]       = CMA band (conservative / median / optimistic; default conservative for moderate)
factor premium   = historical premium × 0.5 (factor_premium_haircut)
bonds            = current aggregate yield (≈ starting-yield predictor)
```
Report terminal-wealth percentiles (5/25/50/75/95), probability of reaching the goal, max-drawdown distribution, and for concentrated-stock mode the median vs mean gap.

### 10.8 Pre-trade checklist items (FR-902)

| # | Item | Auto-check? |
|---|---|---|
| 1 | Emergency fund ≥ target and no debt > 8% APR | auto |
| 2 | Full 401(k) match captured | auto |
| 3 | This purchase keeps satellite ≤ cap, single stock ≤ 5% (or acknowledged ≤ 10%), thematic ≤ 5%, crypto ≤ cap | auto |
| 4 | Money not needed within 5 years (Carlin's "only invest money you don't need") | user |
| 5 | Thesis written: why, what must be true, risks, pre-mortem, exit criteria | auto (non-empty) |
| 6 | Valuation done (DCF/reverse DCF) and MOS reviewed | auto |
| 7 | Moat checklist completed (stocks) | auto |
| 8 | Base-rate card acknowledged (Bessembinder) | user |
| 9 | Overlap with ETFs reviewed | auto (viewed) |
| 10 | Sources checked for sponsorship/paid promotion; no creator-only rationale | auto + user |
| 11 | Account choice and tax location reviewed; wash-sale check passed | auto |
| 12 | Cooling-off period elapsed | auto |
| 13 | "If this fell 30% next month, would I hold or buy more?" answered | user |
| 14 | Position sizing within IPS; amount entered | auto |
| 15 | All inputs fresh (no red badges; amber inputs verified at source), per FR-109 | auto + user |

All items ✓ → status "Ready". Items 1–3, 12 failures require typed acknowledgement to proceed (logged). Item 15 cannot be acknowledged away; refresh the data instead.

---

## 11. Knowledge-base schema

Layout (shipped, git-versioned) per [TECH §5.3], with the folder name and file names fixed by `IMPLEMENTATION_PLAN.md` §1 and §7.1:

```
knowledge_base/
├─ MANIFEST.json                    # kb_version (CalVer), last_reviewed, file hashes
├─ research/papers.json             # Paper[]
├─ research/claims.json             # topic → academic verdict, paper ids, rule ids (feeds FR-502)
├─ creators/{creator_id}.json       # CreatorProfile (principles[], positions[], product_mentions[], conflicts[])
├─ creators/channels.json           # verified channel-ID allowlist + blocked impersonators
├─ rules/stock_scoring.yaml         # §10.3 blocks, styles, flags, thresholds
├─ rules/etf_scoring.yaml           # §10.4 weights and category adjustments
├─ rules/guardrails.yaml            # IPS guardrails & warnings (FR-105)
├─ rules/product_warnings.yaml      # FR-310 badges (covered call, buffer, thematic, crypto, leveraged, private credit)
├─ rules/savings.yaml               # EF adjustments, waterfall, tier rules
├─ topics/*.md                      # explainers (front-matter: id, papers, rules, last_reviewed)
├─ etfs/etf_universe.json, etfs/spiva_latest.json
├─ accounts/limits_2026.json, tax_brackets_2026.json, state_tax_rates.json
├─ savings/cash_vehicles.json, hysa_rates.json, ibond_rates.json, fdic_national_rates.json
├─ market/cma.json, snapshot_manual.json
└─ schemas/*.schema.json            # JSON Schema 2020-12 exported from the pydantic models in fintec/kb/models.py
```

All records share: `id`, `as_of`, `last_reviewed`, `verification` (`V | S | UNVERIFIED`), `source_urls[]`. CI validates every file against its schema.

### 11.1 Paper

```json
{
  "$id": "paper.schema.json",
  "type": "object",
  "required": ["id","title","authors","year","venue","peer_reviewed","evidence_strength","evidence_tier","summary","used_in","verification"],
  "properties": {
    "id": {"type": "string", "pattern": "^[a-z0-9_]+$", "examples": ["bessembinder_2018"]},
    "title": {"type": "string"},
    "authors": {"type": "array", "items": {"type": "string"}},
    "year": {"type": "integer"},
    "venue": {"type": "string"},
    "volume_issue_pages": {"type": "string"},
    "doi": {"type": ["string","null"]},
    "url": {"type": ["string","null"]},
    "type": {"enum": ["journal_article","working_paper","preprint","industry_report","book","government_data"]},
    "peer_reviewed": {"type": "boolean"},
    "evidence_strength": {"enum": ["very_strong","strong","moderate","weak","convention"]},
    "evidence_tier": {"enum": ["canonical","contested","speculative"]},
    "topics": {"type": "array", "items": {"type": "string"}},
    "summary": {"type": "string", "maxLength": 900},
    "key_findings": {"type": "array", "items": {"type": "string"}},
    "numbers": {"type": "array", "items": {"type": "object",
       "properties": {"claim": {"type":"string"}, "value": {"type":"string"}, "verified": {"type":"boolean"}}}},
    "sample": {"type": "string"},
    "caveats": {"type": "array", "items": {"type": "string"}},
    "counterpoints": {"type": "array", "items": {"type": "string"}, "description": "paper ids"},
    "used_in": {"type": "array", "items": {"type": "object",
       "properties": {"rule_id": {"type":"string"}, "fr_id": {"type":"string"}, "how": {"type":"string"}}}},
    "verification": {"enum": ["V","S","UNVERIFIED"]},
    "last_reviewed": {"type": "string", "format": "date"}
  }
}
```

Example:
```json
{
  "id": "bessembinder_2018",
  "title": "Do Stocks Outperform Treasury Bills?",
  "authors": ["Hendrik Bessembinder"], "year": 2018,
  "venue": "Journal of Financial Economics", "volume_issue_pages": "129(3), 440-457",
  "doi": "10.1016/j.jfineco.2018.06.004", "type": "journal_article", "peer_reviewed": true,
  "evidence_strength": "very_strong", "evidence_tier": "canonical",
  "topics": ["skewness","index_vs_active","diversification"],
  "summary": "Across ~26,000 US stocks (1926-2016), most had lifetime returns below one-month T-bills; the best ~4% of companies accounted for all net wealth creation. Concentrated portfolios are likely to miss the few big winners.",
  "numbers": [{"claim": "share of stocks below T-bills", "value": "~57%", "verified": false},
              {"claim": "share of firms creating all net wealth", "value": "~4%", "verified": true}],
  "caveats": ["Includes many tiny, short-lived stocks; large established firms have better odds"],
  "used_in": [{"rule_id": "SINGLE_STOCK_CAP", "fr_id": "FR-413", "how": "5%/10% concentration warnings"},
              {"rule_id": "BASE_RATE_CARD", "fr_id": "FR-4xx", "how": "base-rate card on stock pages"},
              {"rule_id": "MC_SKEWED_STOCK", "fr_id": "FR-808", "how": "skewed single-stock distribution"}],
  "verification": "V", "last_reviewed": "2026-11-10"
}
```

### 11.2 Creator profile and items

Unifies [CR-A §7.2] (profile/principles/positions/conflicts) and [CR-B §6.2] (typed items with expiry).

```json
{
  "$id": "creator.schema.json",
  "type": "object",
  "required": ["id","name","channel_ids","jurisdiction_focus","credentials","trust_weight","overall_alignment","best_use","items","conflicts","last_reviewed"],
  "properties": {
    "id": {"enum": ["ben_felix","plain_bagel","whiteboard_finance","graham_stephan","mark_tilbury","andrei_jikh","sven_carlin","joseph_carlson"]},
    "name": {"type": "string"},
    "aliases": {"type": "array", "items": {"type": "string"}},
    "channel_ids": {"type": "array", "items": {"type": "string", "pattern": "^UC[\\w-]{22}$"}},
    "blocked_channel_ids": {"type": "array", "items": {"type": "string"}},
    "podcast_rss": {"type": ["string","null"]},
    "jurisdiction_focus": {"enum": ["US","CA","UK","EU","Global"]},
    "credentials": {"type": "array", "items": {"type": "object",
       "properties": {"designation": {"type":"string"}, "year": {"type":["integer","null"]},
                      "verified": {"type":"boolean"}, "source_url": {"type":"string"}}}},
    "trust_weight": {"type": "number", "minimum": 0, "maximum": 1},
    "trust_weight_basis": {"enum": ["brief","proposed"]},
    "overall_alignment": {"type": "string"},
    "best_use": {"type": "string"},
    "items": {"type": "array", "items": {"$ref": "#/$defs/item"}},
    "conflicts": {"type": "array", "items": {"$ref": "#/$defs/conflict"}},
    "last_reviewed": {"type": "string", "format": "date"}
  },
  "$defs": {
    "item": {"type": "object",
      "required": ["id","type","statement","category","evidence_alignment","source","as_of"],
      "properties": {
        "id": {"type": "string", "examples": ["carlin.valuation.001"]},
        "type": {"enum": ["principle","framework","pick","position","commentary","product_mention"]},
        "statement": {"type": "string"},
        "detail": {"type": "string"},
        "category": {"enum": ["savings_emergency_fund","order_of_operations","budgeting","debt_mortgage","asset_allocation","index_vs_active","index_etf","factor_investing","dividends_income","stock_selection","valuation","risk_management","international","fixed_income_cash","crypto","real_estate","leverage","behavior","market_timing","fees_taxes","tax","macro","alternatives","source_reliability"]},
        "topics": {"type": "array", "items": {"type": "string"}},
        "stance": {"type": "object", "additionalProperties": {"enum": ["for","against","mixed"]},
                   "description": "topic_id -> stance, feeds consensus matrix"},
        "parameters": {"type": "object"},
        "tickers": {"type": "array", "items": {"type": "string"}},
        "evidence_alignment": {"enum": ["A","B","C","D","E","NA"]},
        "evidence_refs": {"type": "array", "items": {"type": "string"}},
        "evidence_note": {"type": "string"},
        "risk_level": {"enum": ["low","medium","high"]},
        "applies_to_us": {"type": "boolean"},
        "us_translation": {"type": ["string","null"]},
        "app_rule": {"type": ["string","null"]},
        "source": {"type": "object", "required": ["title","url"],
          "properties": {"medium": {"enum": ["video","podcast","article","newsletter","website","transcript","description","interview"]},
                         "title": {"type":"string"}, "date": {"type":"string","format":"date"},
                         "url": {"type":"string"}, "timestamp_s": {"type":["integer","null"]}}},
        "verification": {"enum": ["V","S","UNVERIFIED"]},
        "as_of": {"type": "string", "format": "date"},
        "stale_after_days": {"type": ["integer","null"], "description": "pick 120, commentary 60, position 365, product 180, principle null"},
        "self_reported": {"type": "boolean"},
        "sponsored": {"type": "boolean"},
        "paid_promotion": {"type": "boolean"},
        "creator_owned_product": {"type": "boolean"},
        "superseded_by": {"type": ["string","null"]}
      }},
    "conflict": {"type": "object", "required": ["type","detail","severity"],
      "properties": {"type": {"enum": ["employer","equity_stake","sponsor_financial","sponsor_nonfinancial","affiliate","course_membership","creator_owned_product","paid_stock_promo","legal"]},
                     "detail": {"type":"string"}, "date": {"type":"string"},
                     "severity": {"enum": ["low","medium","high"]},
                     "source": {"type":"object"}}}
  }
}
```

Seed content: the JSON records in [CR-A §7.3] and [CR-B §6.3], migrated to this schema (field renames: `principle` → `statement`, `creator` → file-level id, `source.medium`). Required additions during seeding: `stance` per topic, `topics[]`, US translations for Felix/Bagel/Tilbury/Carlin, and the proposed trust weights flagged `trust_weight_basis: "proposed"`.

### 11.3 Rule

```yaml
# knowledge_base/rules/guardrails.yaml (excerpt)
rules_version: "2026.11.0"
rules:
  - id: SINGLE_STOCK_CAP
    module: [portfolio, stocks, journal]
    kind: guardrail            # guardrail | score_weight | threshold | warning | default
    params: {warn_pct_investable: 0.05, strong_warn_pct_investable: 0.10}
    applies_to_profiles: [conservative, moderate, aggressive]
    message: "This position would be {pct:.0%} of investable assets. Single stocks are highly skewed: most underperform T-bills over their lifetime (Bessembinder 2018)."
    evidence_refs: [bessembinder_2018, bessembinder_2023]
    evidence_tier: canonical
    user_overridable: true
    override_requires_reason: true
  - id: SATELLITE_CAP
    params: {default_pct_equity: 0.10, warn_pct_equity: 0.20}
    evidence_refs: [sharpe_1991, spiva, fama_french_2010, statman_1987]
    sources_note: "[ACAD §7.1] ≤20% warn; [CR-A §7.5] default ≤10% for moderate"
  - id: CAPE_NO_TIMING
    kind: default
    params: {use_for: expected_returns_only}
    message: "High valuations imply lower expected long-run returns: save more and diversify internationally. Valuation is not a timing signal."
    evidence_refs: [campbell_shiller_1998, welch_goyal_2008, siegel_2016]
```

```json
{"$id": "rule.schema.json", "type": "object",
 "required": ["id","kind","params","evidence_refs","evidence_tier"],
 "properties": {
   "id": {"type": "string", "pattern": "^[A-Z0-9_]+$"},
   "module": {"type": "array", "items": {"type": "string"}},
   "kind": {"enum": ["guardrail","score_weight","threshold","warning","default","checklist_item"]},
   "params": {"type": "object"},
   "applies_to_profiles": {"type": "array", "items": {"enum": ["conservative","moderate","aggressive"]}},
   "message": {"type": "string"},
   "evidence_refs": {"type": "array", "items": {"type": "string"}, "minItems": 1},
   "creator_refs": {"type": "array", "items": {"type": "string"}},
   "evidence_tier": {"enum": ["canonical","contested","speculative","convention"]},
   "max_weight": {"type": "number", "description": "validator: <=0.10 unless canonical"},
   "user_overridable": {"type": "boolean"},
   "override_requires_reason": {"type": "boolean"}
 }}
```

**Initial rule catalogue** (from [CR-A §7.5] plus [ACAD §12]): `CORE_INDEX`, `MONEY_WATERFALL`, `STARTER_EF`, `EF_TARGET`, `EF_NOT_IN_RISK_ASSETS`, `RETIREMENT_NOT_EF`, `HYSA_COMPARE`, `FDIC_DIRECT_PREFERRED`, `NOT_A_HYSA_CRYPTO`, `IBOND_LOCK`, `EXCESS_CASH_ALERT`, `MATCH_FIRST`, `HIGH_INTEREST_DEBT_8PCT`, `ROTH_VS_TRAD`, `BACKDOOR_PRORATA`, `INTL_ALLOCATION_RANGE`/`INTL_MIN`, `OPTIONAL_FACTOR_TILT`/`FACTOR_TILT_MAX`, `FACTOR_PREMIUM_HAIRCUT`, `CANONICAL_ONLY`, `SATELLITE_CAP`, `SINGLE_STOCK_CAP`, `DIVERSIFIED_30_8`, `THEMATIC_CAP`, `CRYPTO_CAP`, `BLOCK_LEVERAGED_FOR_MODERATE`, `WARN_COVERED_CALL`, `TOTAL_RETURN_NOT_YIELD`, `YIELD_BADGE_6PCT`, `NO_SORT_BY_1Y_RETURN`, `SPIVA_CARD_ACTIVE`, `BEHAVIOR_GAP_BADGE`, `FEE_DRAG_CALC`, `TAX_LOCATION`, `WASH_SALE_ALL_ACCOUNTS`, `CAPE_NO_TIMING`, `LUMP_SUM_DEFAULT`, `COOLING_OFF`, `TURNOVER_50PCT`, `MULTIPLE_TESTING_WARNING`, `PAID_PROMO_FLAG`, `SOURCE_TRUST_SCORE`, `LLM_DISCLAIMER`, `PREPAY_VS_INVEST_CALC`, `RENT_VS_BUY_CALC`.

### 11.4 Other KB files (key fields)

| File | Fields |
|---|---|
| `etfs/etf_universe.json` | ticker, name, issuer, index, inception, expense_ratio, category, flags[], asset_class, region, goal_tags[], issuer_url, holdings_url_pattern, factor_dataset (US/DevExUS/EM), tax_notes, tlh_partners[], verified, as_of |
| `accounts/limits_2026.json` | tax_year, items[{key, value, unit, source_url, verification}] incl. 401k_deferral 24500, catchup_50 8000, catchup_60_63 11250, dc_total 72000, ira 7500, ira_catchup 1100, hsa_self 4400, hsa_family 8750, hsa_catchup 1000, hdhp_min_ded, roth_phaseout_single [153000,168000], roth_phaseout_mfj [242000,252000], trad_ded_phaseout_single_covered [81000,91000], saver_credit_agi, ltcg_0pct_single 49450 (S) |
| `accounts/brackets_2026.json` | filing_status → [{rate, over}], standard_deduction |
| `savings/ibond_rates.json` | issue_start, issue_end, fixed, semiannual_inflation, composite, source_url |
| `savings/hysa_rates.json` | bank, base_apy, promo_apy, promo_expiry, min_balance, requirements, residency, direct_bank, fdic_cert, as_of, source (Bankrate 2026-11-10) — user must re-verify |
| `market/cma.json` | institution, published, as_of, horizon_years, asset_class, low, high, point, basis (nominal/real), url, conflict_note |
| `market/snapshot_manual.json` | metric, value, as_of, source, url, verification (manual "L" items: forward P/E, GTM stats) |
| `etfs/spiva_latest.json` | category, horizon_years, pct_underperforming, report, report_date, verification |

### 11.5 KB curation workflow (summary; detail in the implementation plan)
1. Claude-researched briefs → seed JSON/MD (this release).
2. CI: schema validation, DOI resolution (OpenAlex) with title match, channel allowlist check, "no [UNVERIFIED] number displayed as fact" lint, `max_weight` validator.
3. Weekly: RSS ingestion → pending items; user approves into `knowledge_base/` via diff view (optionally LLM-drafted).
4. Semiannual: review `positions`/picks; annual: principles, limits, CMAs, SPIVA.

---

## 12. Key design decisions and their evidence

| # | Decision | Alternatives considered | Rationale / source |
|---|---|---|---|
| D1 | The KB is the core. Live data only feeds numbers into deterministic calculators | LLM-first assistant | [TECH §0, §6]; Plain Bagel AI-advice concerns [CR-A §2.2] |
| D2 | Default EF uses the additive months model (persona ≈ 4.5 months), with 6 × M shown as the conservative marker | Fixed 6 × M default [ACAD §11]; Tilbury 5 × [CR-B] | [SAV §2.2] ties it to BLS spell data. [ACAD] notes "3-6 months" is a convention. Showing every marker resolves the conflict between briefs |
| D3 | Waterfall puts a starter EF first, then the match, debt > 8%, full EF, HSA, IRA, 401(k), taxable | Whiteboard (full EF before the match); creator brief (IRA before HSA) | [SAV §3.1]; Choi et al. 2011 on forgone match. Creator variants are shown (FR-231) |
| D4 | Satellite default cap 10% of equities (warn above it), strong warn above 20% | 20% default (Graham's 80/20) | [CR-A §7.5] default ≤ 10% for moderate; [ACAD §7.1, §12] warn > 20% |
| D5 | ER is 30% of the ETF score | Equal weights | Carhart 1997; Kinnel [ACAD §1.4, §6.4] |
| D6 | Stock score: quality 35 / value 30 / safety 15 / momentum 10 / dividend 10 | Value-only; adding growth/momentum weight | QMJ, Novy-Marx, FF5, momentum ≤ 10% [ACAD §3, §12]; canonical-only gate [ACAD §4.2] |
| D7 | Fixed 10% DCF hurdle by default, editable; reverse DCF always shown | WACC only | Carlin framework [CR-B §3.2]. Reverse DCF lowers forecast-error risk; the sensitivity grid exposes how much the hurdle drives the result [CR-B §3.7] |
| D8 | CAPE never times the market; it only feeds expected returns | Valuation-based allocation shifts | Goyal & Welch 2008; Siegel 2016; Vanguard VEMO [ACAD §8.2; MKT §1.2] |
| D9 | Lump sum by default; staging as a labelled behavioral option | DCA default for a moderate-risk user | Vanguard 2012 (~2/3 of the time) [ACAD §10; SAV §4.4] |
| D10 | Creator tickers are never "buy" signals; sponsored items carry zero scoring weight | Weighting creator picks | [CR-A §7.1; CR-B §6.1]; "Pay to pump" [CR-A §2.2] |
| D11 | Global cap weight (~64/36) as the reference, with an ex-US floor of 20% | US-only core | Anarkulova et al. 2022; French & Poterba [ACAD §7.2; MKT §4] |
| D12 | OpenRouter free models discovered at runtime; Ollama for anything private | Hard-coded models; paid APIs | [TECH §4.1, §4.3]; $0 budget |
| D13 | Factor regressions use French data with HAC errors and CIs, and need at least 36 months | Simple correlations | [TECH §5.2]; Newey-West 1987 |
| D14 | Treasury coupon-equivalent yields are used for cash comparisons, never DTB3 | FRED DTB3 | DTB3 is quoted on a discount basis [TECH §1.3] |

---

## 13. Acceptance criteria

Format: **AC-x.y** → the FRs it verifies. All ACs must pass with `LLM_PROVIDER=none` unless stated. Each AC maps to automated tests (unit, golden, or UI via `streamlit.testing.AppTest`) where feasible.

### Module 1: Profile & IPS
- **AC-1.1** (FR-101) The wizard won't finish without `M`. Inputs persist across restarts. `S` defaults to 20,000.
- **AC-1.2** (FR-102) Fixtures map to the expected profile. Answering "would sell" to the 20% drop question caps equity at ≤ 80% and sets a staged-entry suggestion.
- **AC-1.3** (FR-103/104) For a moderate fixture, the IPS shows equity 80%, ex-US 36%, satellite 10%, tilt 0%, leverage 0, crypto 0%. Export produces valid Markdown.
- **AC-1.4** (FR-105) Setting the satellite cap to 30% opens the reason dialog, the reason is stored, and every page shows the guardrail as "user override."

### Module 2: Savings & Cash
- **AC-2.1** (FR-201/203) Persona fixture (single, renter, stable, LTU 27.6%) → target 4.5 months. M = 3,000 → EF $13,500. Each toggle changes the result by its table value. The result is clamped to [3, 12].
- **AC-2.2** (FR-205) M = 3,000 and EF = 13,500 → HYSA tier 9,000 and T-bill tier 4,500 (when the T-bill after-tax yield is more than 0.10% higher). I bonds held < 12 months contribute $0 to the EF.
- **AC-2.3** (FR-211) A 4.22% T-bill at 22% federal + 9.3% state gives a HYSA-equivalent of 4.79% (±0.01). With a 0% state rate it equals 4.22%.
- **AC-2.4** (FR-212) The comparison table never uses DTB3. Each rate shows a type and as-of date. Rates older than 7 days show an amber badge.
- **AC-2.5** (FR-214) A HYSA marked `fintech_intermediated` shows the Synapse warning. A product of type crypto/"yield app" can't be assigned to the EF.
- **AC-2.6** (FR-232) [SAV §4.5] worked examples reproduce exactly: M = 2,500 with a $1,500 sinking fund → excess $7,250; M = 3,500 with a $1,500 sinking fund → $2,750; M = 4,500 (no sinking fund) → $0 with the EF $250 short. Without sinking funds, M = 3,000 → EF $13,500 and excess $6,500 (default `lean_conservative = false`).
- **AC-2.7** (FR-233) Changing `limits_2026.json` changes the outputs with no code change. A grep test finds no literal 24500/7500 outside the KB.
- **AC-2.8** (FR-234) Pro-rata: basis 7,500, Dec 31 pre-tax IRA 0, conversion 7,500 → 0% taxable. A $67,500 pre-tax IRA → 90% taxable.
- **AC-2.9** (FR-235) Excess > 0 with an uncaptured match → the warning appears and the match becomes the dashboard's "next best action."

### Module 3: ETF
- **AC-3.1** (FR-303) A yield sort control doesn't exist. Sorting by 1-year return shows the Carhart warning.
- **AC-3.2** (FR-304) With synthetic holdings A = {X: .5, Y: .5} and B = {X: .2, Z: .8}, weighted overlap = 0.2.
- **AC-3.3** (FR-306) Golden test: VTI vs FF5+MOM over ≥ 60 months gives Mkt-RF in [0.95, 1.05], |SMB| < 0.1, R² > 0.95. Under 36 months, the regression is refused with a message.
- **AC-3.4** (FR-308) An ER gap of 0.47% on $10,000 at 7% over 30 years matches the closed form within $1.
- **AC-3.5** (FR-310) JEPI shows the "Yield ≠ return" and covered-call badges and the upside-cap chart. TQQQ is blocked for the moderate profile. A thematic ETF shows the Ben-David card and the 5% cap.
- **AC-3.6** (FR-313) A taxable sale of VTI at a loss plus a VTI purchase in the Roth IRA within 30 days → wash-sale strong warn citing Rev. Rul. 2008-5.
- **AC-3.7** (FR-314) An injected 2% discrepancy between sources produces the discrepancy flag.

### Module 4: Stocks
- **AC-4.1** (FR-401) For 5 golden companies, EDGAR TTM revenue and net income match hand-computed values from the 10-K/10-Q (tolerance 0.5%). Duplicated facts resolve to the latest `filed`.
- **AC-4.2** (FR-403) Every screener result page shows the base-rate card and the "variants tried" count, which increments when filters change.
- **AC-4.3** (FR-404/10.3.3) Weight vectors sum to 100. A validator rejects any non-canonical block weight above 10. Recomputing with the same snapshot gives identical scores.
- **AC-4.4** (FR-405) Synthetic fixtures trigger each red flag exactly. PAID_PROMOTION blocks the "Buy candidate" label until acknowledged.
- **AC-4.5** (FR-406/407) DCF matches a spreadsheet reference within 0.1%. Reverse DCF recovers the input g₁ within 0.01 pp when the price is set to the DCF value. OE₀ ≤ 0 → "not computed."
- **AC-4.6** (FR-409) Dividend safety fixtures give the documented scores (e.g., FCF payout 110% and net debt/EBITDA 4.5 → Unsafe).
- **AC-4.7** (FR-413) Buying AAPL at 8% of investable assets while holding VOO shows the combined look-through weight and the single-stock warn. 12% → strong warn.
- **AC-4.8** (FR-418) The label "Buy" without "(satellite)" never appears anywhere in the UI (string test).

### Module 5: Expert Lens
- **AC-5.1** (FR-501) All 8 creators load and pass schema validation. Credentials marked unverified display as such. Marko is never labelled CFP.
- **AC-5.2** (FR-502) For the topic "covered calls," the academic verdict card renders above the creator cards. Felix/Bagel show "against" and Whiteboard "for (D)."
- **AC-5.3** (FR-503) The consensus matrix marks "low-cost index core" as consensus and "dividend/covered-call income" as contested, from the seed data.
- **AC-5.4** (FR-505) Whiteboard's card shows the GRML paid-promotion conflict (high). Graham's shows FTX and Yotta/Synapse (high).
- **AC-5.5** (FR-507) With recorded RSS cassettes, 8 feeds ingest and duplicate video_ids upsert. The impersonator channel ID is rejected. A simulated 404 triggers a retry and doesn't delete the channel.
- **AC-5.6** (FR-508) A description containing "has been compensated" sets `paid_promotion=true`. Commentary older than 60 days shows a stale badge.
- **AC-5.7** (FR-509) No LLM-drafted item appears in curated views until approved.

### Module 6: Research Library
- **AC-6.1** (FR-601/602) ≥ 60 papers load. Every paper has a non-empty `used_in`, and each `rule_id` it names exists.
- **AC-6.2** (FR-607) The CI DOI check passes for every DOI. A deliberately wrong DOI fixture fails CI.
- **AC-6.3** (FR-606) "Find more" results carry the "not reviewed by KB" badge. arXiv items carry "preprint."

### Module 7: Market Context
- **AC-7.1** (FR-701) Every card shows value, as-of and source. With the network off, cards show cached values with stale badges and are never blank.
- **AC-7.2** (FR-703) CPI y/y for October 2026 shows "n/a (2025 shutdown gap)" or a labelled estimate.
- **AC-7.3** (FR-711) A string test confirms no Market Context text contains "sell," "go to cash," or "exit the market" as advice.
- **AC-7.4** (FR-705) CMAs older than 12 months are flagged. The consensus band computes min-max correctly.

### Module 8: Portfolio
- **AC-8.1** (FR-801) Sample Fidelity/Schwab/Vanguard CSVs import with correct share counts.
- **AC-8.2** (FR-804) A 70/30 portfolio drifted to 78/22 with ±5 pp bands produces trades back to 70/30. With a pending contribution, the trades use cash flow first.
- **AC-8.3** (FR-807) A 100% US-equity GFC replay gives a drawdown within 2 pp of the proxy's historical drawdown.
- **AC-8.4** (FR-808) A fixed seed reproduces the percentiles. Factor premium inputs show the 0.5 haircut. The default CMA mode for moderate is "conservative."
- **AC-8.5** (FR-810) Logged trades implying 60% turnover trigger the warning.

### Module 9: Journal
- **AC-9.1** (FR-901/902) A decision can't reach "Ready" with an empty thesis/pre-mortem or failing checklist items without a typed acknowledgement.
- **AC-9.2** (FR-903) A new stock idea created at T can't be marked Ready before T + 72 h (freezegun test). An ETF rebalance is exempt.
- **AC-9.3** (FR-904) The sell flow shows "Would you buy this today?", the thesis next to P/L, and the wash-sale check.
- **AC-9.4** (FR-907) Edits to an executed record create amendments. The original is unchanged.

### Module 10: AI Assistant
- **AC-10.1** (FR-1009) With `LLM_PROVIDER=none`, every page renders and the Ask page performs keyword search.
- **AC-10.2** (FR-1002) Model discovery selects only `:free` / zero-price models from a recorded `/models` response. On a 429, it falls back to Ollama, then none.
- **AC-10.3** (FR-1005) An answer without `[S#]` citations is flagged. An answer containing a number not present in the provided context is flagged.
- **AC-10.4** (FR-1007) The request payload captured for a cloud provider contains no balances, account names, or holdings amounts (test with seeded PII).
- **AC-10.5** (FR-1008) Every LLM output displays the AI disclaimer and is served from cache on repeat.

### Non-functional
- **AC-NFR-1** The full offline test suite passes with sockets disabled (`pytest-socket`).
- **AC-NFR-2** A UI audit script finds zero numeric widgets without an `as_of` label.
- **AC-NFR-3** gitleaks finds no secrets; `data/` and `.env` are git-ignored.
- **AC-NFR-4** The performance targets in NFR-11 are met on a reference laptop (16 GB RAM).

---

## 14. Risks and mitigations

| # | Risk | Likelihood / impact | Mitigation |
|---|---|---|---|
| R1 | **The user treats the output as advice** and over-trusts scores | M / H | Persistent disclaimers; "Buy candidate (satellite)" wording; base-rate cards; checklist and cooling-off; uncertainty (CIs, sensitivity grids) shown everywhere |
| R2 | **yfinance breaks** (endpoint or schema changes, 429s) | H / M | Pinned version; adapter with retry; Tiingo/Alpha Vantage fallback; cache-first; nightly live smoke test; manual CSV import [TECH §1.1] |
| R3 | **Stale or incorrect KB facts** (rates, limits, creator stances, [UNVERIFIED] items) | H / H | as_of on every record; stale badges; the [UNVERIFIED] lint; annual and semiannual review calendar; DOI CI; "verify at source" links |
| R4 | **EDGAR tag inconsistencies** produce wrong fundamentals | M / H | Tag-fallback map; dedupe rules; golden-company tests; cross-check against yfinance and flag > 10% gaps; financials/REITs marked "limited coverage" |
| R5 | **Overfitting the screener** (data mining) | M / M | Canonical-only weights; the multiple-testing banner; no in-app optimizer of weights; changes to weights logged with a reason [ACAD §4] |
| R6 | **Behavioral misuse** (frequent tinkering, chasing hot themes) | M / H | Turnover tracker; behavior-gap badge; thematic caps; no real-time prices; journal analytics |
| R7 | **Creator content risk** (paid promotions, impersonators, copyright) | M / M | Channel allowlist; paid-promo detection; summaries plus links only; transcripts local and git-ignored [TECH §3.4] |
| R8 | **LLM hallucination or privacy leak** | M / H | Off by default; grounded RAG with citation check; numeric post-check; privacy filter; Ollama-only for portfolio context |
| R9 | **Free-tier limits change** (OpenRouter, OpenAlex budget, Finnhub) | H / L | Runtime discovery; quotas in config; graceful degradation; no hard dependency |
| R10 | **Tax-rule complexity** (state MMF exemption, wash sales, backdoor Roth) | M / M | Conservative defaults; user-entered `exempt_share`; "gray area" labels; IRS links; recommend professional review |
| R11 | **Issuer holdings endpoints fail** (iShares HTML gate) | H / L | Manual refresh plus user CSV upload; N-PORT fallback; holdings as_of shown |
| R12 | **Regime-specific defaults age badly** (e.g., the macro EF overlay, Nov 2026 rates) | M / M | All regime inputs computed live, never hard-coded; narrative regenerated on threshold crossings |
| R13 | **Scope creep / maintenance burden** for one user | M / M | MVP vs v1 split (§15); KB curation cadence kept light; tests guard core calculators |
| R14 | **Data-security loss** (local DB deleted or corrupted) | L / M | Backup/restore button; journal export; optional encryption |

---

## 15. Release scope

| Release | Includes |
|---|---|
| **MVP** | All **M** FRs in Modules 1, 2, 3 (excluding FR-311-313), Module 4 core (FR-401-410, 413, 415-416, 418), Module 5 (FR-501-508, 512), Module 6 (FR-601-604, 607), Module 7 (FR-701-705, 711), Module 8 (FR-801-808, 810-811), Module 9 (FR-901-904, 907), Module 10 FR-1009 only (no-LLM parity) |
| **v1.0** | All **S** FRs, including the optional LLM (FR-1001-1003, 1005-1008), transcripts, factor-tilt page, asset location, TLH, quadrant, Finnhub view, behavior gap, journal analytics |
| **Later** | **C** FRs: rent vs buy, prepay vs invest, budget templates, opencode provider, encryption, event calendar, Rational Reminder paper mining, glide-path notes |

---

## 16. Compliance and disclaimer text

### 16.1 Global footer (every page)
> **Educational research tool, not financial advice.** FinTec Research Assistant provides general information and calculations based on the data and assumptions shown. It does not know your full financial situation, is not a registered investment adviser, broker-dealer, or tax professional, and does not recommend that you buy or sell any security. Investing involves risk, including loss of principal. Past performance does not guarantee future results. Verify all figures at the original source before acting, and consider consulting a qualified, fee-only fiduciary adviser or tax professional.

### 16.2 First-run acknowledgement (must be accepted; stored with timestamp)
> I understand that:
> 1. This app is a personal research and education tool that I run on my own computer. It is not personalized investment, tax, or legal advice.
> 2. Scores, labels such as "Buy candidate (satellite)," model portfolios, and plans are rule-based outputs from public research and my own inputs. They can be wrong, and the data can be stale or incorrect.
> 3. I am solely responsible for my investment decisions and for checking rates, limits, and tax rules with official sources (IRS, FDIC, TreasuryDirect, fund issuers, my plan administrator).
> 4. Creator content is summarized for education. Creators may have conflicts of interest, and their inclusion is not an endorsement.
> 5. No trades are executed by this app.

### 16.3 Contextual disclaimers
- **Stock pages:** "A 'Buy candidate (satellite)' label means the stock passed this app's rules under the selected style. It is not a recommendation. Most individual stocks have historically underperformed Treasury bills over their lifetime (Bessembinder 2018). Keep individual stocks to a small part of your portfolio."
- **ETF pages:** "Expense ratios, yields and holdings come from the sources and dates shown. Confirm them on the issuer's website. Distribution yield is not total return."
- **Savings pages:** "Rates change often and are shown with their as-of dates. FDIC/NCUA insurance covers deposits up to $250,000 per depositor, per insured institution, per ownership category. It does not cover money-market funds, Treasuries, crypto, or the failure of non-bank apps. Tax calculations are estimates. State rules vary."
- **Account waterfall:** "This order is a common default based on published research and practitioner guidance. Your situation may justify a different order. Contribution limits are from IRS publications for the tax year shown."
- **Market context:** "Market indicators describe current conditions. They are not forecasts or timing signals. Institutional return assumptions are model estimates with wide error bands."
- **Expert Lens:** "Creator views are summarized from public videos and posts as of the dates shown, and may have changed. Sponsored or paid content is flagged. Inclusion is not endorsement. FinTec is not affiliated with any creator."
- **Monte Carlo / stress tests:** "Simulations use assumptions and historical data. Actual results will differ, and losses can exceed any scenario shown."
- **Tax tools (TLH, backdoor Roth, location):** "Simplified estimates. Wash-sale and 'substantially identical' determinations depend on facts and circumstances. Consult IRS Publication 550, Form 8606 instructions, or a tax professional."

### 16.4 AI assistant disclaimer
> "AI-generated explanation based only on the cited knowledge-base sources. It may be incomplete or wrong. It is not financial advice and will not recommend specific trades. Verify against the cited sources. [Provider/model, date]"
> Cloud providers: "Your question and the cited knowledge-base excerpts are sent to <provider>. Personal balances and holdings are removed. Free models may log prompts."

### 16.5 Data and licensing notices (Settings → About)
- Yahoo Finance data via yfinance is for personal use only and is not redistributed.
- Fama/French factors: Kenneth R. French Data Library. AQR datasets: AQR Capital Management. Both used with attribution.
- SEC EDGAR data accessed under SEC fair-access rules with a declared User-Agent.
- YouTube content is linked and summarized. Transcripts are cached locally only and not redistributed.
- Creator names and channel names belong to their owners.

---

## 17. Open questions for the user

These fill gaps in the spec. Each answer changes a default, and the parts affected are listed.

**Savings & cash (Module 2)**
1. What are your **monthly essential expenses** (rent, utilities, groceries, insurance, minimum debt payments, transport, healthcare)? *Sets the EF target and how much of the $20K is investable.*
2. Is the $20K all in one account today? Which bank, and what APY? Is it a direct bank or a fintech app?
3. Do you have **any debt** (credit cards, car, student loans), and at what APRs?
4. Any **large expenses in the next 3 years** (car, wedding, move, home down payment)?
5. **Which state** do you live in? *This drives the T-bill vs. HYSA after-tax comparison.*

**Accounts & taxes (Module 2 waterfall)**
6. Your 401(k) **match formula**, vesting schedule, and whether you capture the full match now. Does the plan offer Roth 401(k), after-tax contributions/mega-backdoor, or a student-loan match?
7. Approximate **income/MAGI** and filing status. *Sets Roth eligibility, bracket, and Roth vs. Traditional.*
8. Is your health plan **HSA-eligible**?
9. How much have you already contributed to your **2026 IRA, 401(k), and HSA**? Do you hold any **pre-tax IRA balances** (relevant to the backdoor Roth pro-rata rule)?
10. How stable is your job (industry, contract vs. permanent)? Single or dual income? Dependents?

**Investing (Modules 3, 4, 8)**
11. Which ETFs do you currently hold, in which accounts, and roughly how much? (A CSV export is fine.)
12. How large should the **individual-stock satellite** be: 0%, 5%, 10%, or up to 20% of equities?
13. Do you want an optional **factor tilt** (e.g., small-cap value or profitability), and could you live with it lagging the S&P 500 for 10 years?
14. Among your interests (value/quality, dividend growth, growth), which should be the **default scoring style**?
15. Bonds: are you comfortable holding 10-20% bonds in retirement accounts for drawdown control, or do you prefer all-equity plus a larger cash buffer?
16. Any exclusions (sectors, ESG preferences, crypto entirely off)?

**App behavior (Modules 5, 9, 10)**
17. Should the **72-hour cooling-off** be mandatory, or shortened to 48 hours?
18. Do you want the optional **AI assistant**, and if so, which: OpenRouter free (cloud) or Ollama (local; needs about 8-16 GB RAM)? Do you have an opencode setup you want integrated?
19. Should the app fetch **YouTube transcripts** locally (slower, gray-area ToS), or use titles/descriptions plus curated summaries only?
20. How often will you use the app (weekly check-in vs. monthly review)? *Sets refresh schedules and review reminders.*
21. Do you want the four proposed trust weights for Tilbury, Jikh, Carlin and Carlson (§10.5) adjusted?

---

*End of specification. The companion documents (implementation plan and workflow) will reference FR/AC IDs from this file.*
