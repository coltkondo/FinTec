# My Savings and Starter Investing Plan

**Prepared:** 2026-11-10 | **For:** a US-based young professional with a 10+ year horizon and moderate risk tolerance who invests mostly in ETFs. Accounts: a 401(k) with employer match, a Roth/Traditional IRA and a taxable brokerage. About $20K in savings.

> **Disclaimer (please read).** This is an educational document, not individualized financial, tax or legal advice. It was written from research briefs prepared on 2026-11-10. Every rate and yield below is a snapshot that will change, sometimes within days. Check each one at its source before you act. Your monthly expenses, state, income and plan details aren't known yet, so the numbers are shown as scenarios. Section 10 lists the questions that will turn this into a specific plan. Nothing here recommends a particular security. The tickers are examples of fund *types*, so check each fund's expense ratio, holdings and tax notices on the issuer's site.

### Source key (used throughout)
| Tag | Brief (in `docs/research/`) |
|---|---|
| **[SAV]** | `savings_and_account_strategy.md` (cash vehicles, emergency fund research, 2026 IRS limits, $20K split algorithm) |
| **[ACAD]** | `academic_evidence.md` (peer-reviewed evidence: index vs. active, factors, DCA, diversification, emergency savings) |
| **[MKT]** | `market_context_2026.md` (valuations, rates, CMAs, popular products, as of Nov 2026) |
| **[CR1]** | `influencers_ben_felix_plain_bagel_whiteboard_graham.md` |
| **[CR2]** | `influencers_tilbury_jikh_carlin_carlson.md` |
| **[TECH]** | `tech_stack_and_data_sources.md` (used only for data-quality notes) |

Note: the task list named `analysis_methodology.md`, but that file doesn't exist in `docs/research/`. Nothing here depends on it.

Labels: **[V]** means the brief verified it against a primary source. **[UNVERIFIED]** means the brief flagged it as from memory or unconfirmed. Treat those items as "check before relying on it."

---

## 1. The short version (TL;DR)

1. **Split the $20K into jobs:** (a) an emergency fund (EF) sized to *your* essential monthly expenses, (b) money for any known expense in the next 1 to 3 years, and (c) the "excess", which should be invested for the long term. Cash is for stability. Over 10+ years it doesn't build wealth [SAV §2.4; CR1 Felix].
2. **Emergency fund:** start at **4.5 months** of essential expenses (the app's default). Go to **6 months** if your pay is variable, your industry is shaky, you have dependents, or a thinner cushion would make you anxious (§2; the app's "lean conservative" switch). Hold it in tiers: a little in checking, up to about 3 months in an **FDIC-insured HYSA held directly at a bank**, and the rest in **T-bills, SGOV or a Treasury money market fund**. Treasury interest is exempt from state income tax [SAV §1, §4.3].
3. **Don't leave money at a big bank.** The national average savings rate is **0.38%** (FDIC, 2026-10-19). Top HYSAs pay about **4.0–4.3%**. On $20K that gap is about **$760 a year** before tax [MKT §10; SAV §1.2].
4. **Excess cash goes down this ladder:** get the full 401(k) match (raise your deferral **before Dec 31**). Pay off any debt above about 8% APR. Finish the EF. Fund an HSA if you're eligible. Make a **2026 Roth IRA contribution (up to $7,500, deadline April 15, 2027)**. Make the 2027 IRA contribution from January 2027. Then raise your 401(k) or invest in the taxable account [SAV §3; ACAD §11].
5. **Long-term portfolio (moderate risk):** about **80% stocks / 20% bonds**. The stock part is a **global total-market core**, roughly 60–65% US and 35–40% international. An optional factor tilt (small-cap value / profitability) can be up to about 15–30% of stocks. Individual stock picks go in a **satellite capped at 10% of your stock allocation** (strong warning above 20%), with no single stock above 5% of investable assets [ACAD §2, §3, §7, §9; MKT §4]. These are the same caps the app enforces (SPEC FR-104, FR-413).
6. **Lump sum vs. DCA:** a lump sum wins about two-thirds of the time. Drawdowns stress you, though, so it's reasonable to invest the excess in **3 to 6 monthly installments**. Payroll contributions should always go in right away [ACAD §10; SAV §4.4].
7. **Ignore the noise.** CAPE is near 40, and plenty of creators are posting "urgent warnings." Valuations lower *expected* returns, which argues for saving a bit more and diversifying globally. They don't tell you when to get in or out [ACAD §8; MKT §1].

---

## 2. Emergency fund: how big?

### 2.1 What the research says
- **3–6 months is a convention, not a precise research result** [ACAD §11]. The real evidence behind it:
  - **BLS Table A-12 (Oct 2026):** the median unemployment spell is **11.9 weeks (~2.7 months)** and the mean is **27.1 weeks (~6.2 months)**. **27.6%** of unemployed people have been out of work 27+ weeks, which is elevated [SAV §2.1, V]. Three months covers a typical job loss, and about six months covers the average one, which is pulled up by long spells.
  - **Buffer-stock theory** (Carroll 1997, *QJE*; Carroll & Samwick 1997): the right buffer grows with how uncertain your income is [ACAD §11].
  - **Even small balances help.** Families with $250–$749 saved were less likely to miss housing or utility payments after an income shock (McKernan et al. 2016, Urban Institute; correlational) [SAV §2.1, V]. Gjertson (2016, *JFEI*) found that emergency savers in a low-income panel had less hardship later [ACAD §11].
  - **Fed SHED 2025:** only **55%** of adults have 3 months of expenses set aside [SAV §2.1, V]. A 3-month fund already puts you ahead of almost half of adults.
  - **Your 401(k) is not your emergency fund.** Withdrawals before 59½ usually cost income tax plus a 10% penalty. This is Kaplan & Violante's (2014) "wealthy hand-to-mouth" trap [ACAD §11].
- **Use *essential* expenses only:** rent/mortgage, utilities, groceries, insurance, minimum debt payments, transportation and expected out-of-pocket health costs. Leave out spending you'd cut in an emergency [SAV §2.2].

### 2.2 Sizing table (3 / 4.5 / 6 months)
"Left over" is what remains of $20,000 after the emergency fund, before any sinking funds for known expenses.

| Essential monthly expenses | 3 months | 4.5 months | 6 months | Left over at 3 mo | Left over at 4.5 mo | Left over at 6 mo |
|---|---|---|---|---|---|---|
| **$2,500** | $7,500 | $11,250 | $15,000 | $12,500 | $8,750 | $5,000 |
| **$3,500** | $10,500 | $15,750 | $21,000 | $9,500 | $4,250 | **−$1,000** |
| **$4,500** | $13,500 | $20,250 | $27,000 | $6,500 | **−$250** | **−$7,000** |
| **$5,500** | $16,500 | $24,750 | $33,000 | $3,500 | **−$4,750** | **−$13,000** |

A negative number means the $20K doesn't fully cover that target. If so, keep all $20K as the EF, except the cash needed to capture the 401(k) match (step 1 in §4). Point new monthly savings at the EF until it's full.

### 2.3 Which column is yours?
The [SAV §2.2] scoring model starts at 3 months and adjusts. For a single person who rents and has a stable W-2 job in Nov 2026, it gives **4.5 months**: +1.0 for a single income and +0.5 because long-term unemployment is above 25% [SAV §4.5].

| Situation | Suggested months |
|---|---|
| Dual stable incomes, no dependents, government or high-demand role | **3** |
| Single stable W-2 income, renter (the default until you answer §10) | **4.5** |
| Single income **and** any of: bonus- or commission-heavy pay, layoff-prone industry, dependents, homeowner/older car, HDHP without HSA cash | **6** |
| Self-employed / 1099 / gig income | **6–9+** |

- **Moderate-risk tilt:** [ACAD §11] defaults a moderate-risk user to 6 months until more is known. Mark Tilbury uses 5× core expenses and calls that "on the more cautious side" [CR2 §1.4, V]. If a thinner cushion would make you anxious, 6 months is a legitimate choice. The cost of holding 1.5 extra months of $3,500 in cash instead of stocks is roughly a few hundred dollars a year in expected (not guaranteed) return.
- **Always include** your highest insurance deductible (health or auto) in the target [ACAD §11].
- **Backstop, not a first layer:** money you've *contributed* to a Roth IRA (not the earnings) can be withdrawn tax- and penalty-free at any time. That's why the scoring model lets you shave about 0.5 months once you have a Roth balance [SAV §2.2, §3.1].

---

## 3. Where to hold the emergency fund (rates as of 2026-11-09/10)

### 3.1 The options compared
| Vehicle | Current example yield | Access | Protection | State income tax | Best job |
|---|---|---|---|---|---|
| **Big-bank savings** | **0.38%** national average (FDIC) / 0.56% (Bankrate) | Instant | FDIC | Taxable | Avoid for the EF |
| **HYSA (online bank)** | Top ~4.0–4.35%. Examples [V, Bankrate 11/10]: CIT 4.35% (*promo*: 3.75% base + 0.60% bonus code through 12/15/2026; $5K min), Peak 4.27%, EverBank 4.20%, Vio 4.06%, Bread 4.05%, Live Oak 4.00% | Same day to 3 business days | **FDIC, $250K** per depositor, per bank, per ownership category | **Taxable** | EF tier 2 (instant access) |
| **Government money market fund** | VMFXX ~3.96%, SPAXX ~3.33% (both *low-confidence* Yahoo quotes); SWVXX (prime) 3.75% [V]; Crane 100 average 3.75% | Same or next day inside a brokerage | **Not FDIC**; holds government securities and repo | Partly exempt (only the share from direct Treasury income) | Brokerage "dry powder"; EF tier 3 |
| **Treasury-only MMF** (VUSXX, FDLXX, SNSXX) | Similar to government MMFs [current yield UNVERIFIED] | Same as above | Not FDIC; holds Treasuries | **Mostly exempt** (check the fund's annual "% US government obligations") | Tier 3 in a high-tax state |
| **T-bills (direct, at a broker)** | 4-wk 3.94%, **13-wk 4.22%**, **26-wk 4.41%**, **52-wk 4.54%** (coupon-equivalent, 11/10/2026) [V] | Sell any day at market price, or hold to maturity; auto-roll available | US government | **Exempt** [V] | Tier 3, ladders, sinking funds |
| **SGOV / BIL (T-bill ETFs)** | SGOV 30-day SEC yield **3.85%**, ER 0.09%; BIL 3.86%, ER 0.135% [V] | Trades intraday, T+1 | US Treasuries via a fund; not FDIC | Generally exempt (CA/CT/NY have 50%-asset tests [UNVERIFIED specifics]) | The simplest "one-ticker T-bill ladder" |
| **CDs** | 6-mo up to 4.40%; **1-yr up to 4.65%**; 3-yr 4.85%; 5-yr 5.00% [V, Bankrate] | Locked; early-withdrawal penalty | FDIC/NCUA | Taxable | Known-date expenses only |
| **I bonds** | **5.51% composite = 1.40% fixed + inflation**, for issues Nov 1, 2026–Apr 30, 2027 [V] | **Locked 12 months**; lose the last 3 months of interest if redeemed before 5 years | US government | **Exempt**; federal tax can be deferred | *Not* the EF's first 12 months; a deep reserve or medium-term savings |

Sources: [SAV §1.1–1.8], [MKT §10], [CR2 §6.5].

**Data notes.**
- Treasury publishes the 3-month bill in two ways. The par-yield-curve figure is **4.30%** [MKT §0]. The daily bill-rate (coupon-equivalent) figure is **4.22%** [SAV §0]. They measure slightly differently, so use one consistently.
- FRED's `DTB3` is quoted on a *discount* basis and understates the yield, so don't compare it to an APY [TECH].
- SGOV's SEC yield lags new bills (3.85% vs. 4.22%) because rates rose recently [SAV §1.7].

### 3.2 After-tax comparison (assumes a 22% federal bracket)
Formulas [SAV §1.4]:
- HYSA or CD after tax = yield × (1 − fed − state)
- T-bill, SGOV or I bond after tax = yield × (1 − fed)
- MMF after tax = yield × (1 − fed − state × (1 − exempt share))

| Vehicle (yield) | No-income-tax state (TX, FL, WA…) | 5% state | 9.3% state (e.g., CA bracket) |
|---|---|---|---|
| Big bank 0.38% | 0.30% | 0.28% | 0.26% |
| HYSA 4.00% | 3.12% | 2.92% | 2.75% |
| HYSA 4.20% | 3.28% | 3.07% | 2.89% |
| Gov MMF 3.96% (worst case: 0% state-exempt) | 3.09% | 2.89% | 2.72% |
| SGOV 3.85% (state-exempt) | 3.00% | 3.00% | 3.00% |
| 13-wk T-bill 4.22% | 3.29% | 3.29% | 3.29% |
| 26-wk T-bill 4.41% | 3.44% | 3.44% | 3.44% |
| 52-wk T-bill 4.54% | 3.54% | 3.54% | 3.54% |
| 1-yr CD 4.65% | 3.63% | 3.39% | 3.19% |
| I bond 5.51% (locked) | 4.30% | 4.30% | 4.30% |

How to read this:
- **In a no-tax state,** a good HYSA and a 13-week bill are about equal (3.28% vs. 3.29%). Pick whichever is more convenient.
- **In a high-tax state,** the 13-week bill is worth a **4.79% HYSA-equivalent** at 9.3% state tax (4.22 × 0.78 / 0.687) [SAV §1.4]. At 5% state tax it's worth 4.51%. Both beat every non-promotional HYSA.
- **A 1-year CD at 4.65% beats a 52-week bill only if your state tax is below about 1.9%** [SAV §1.5].
- **Keep the stakes in proportion.** On a $15,000 EF, a 0.4-point after-tax gap is about $60 a year. Moving off a 0.38% big-bank account is worth about **ten times more** than fine-tuning HYSA vs. T-bill.
- **Real return check (Felix's point).** CPI is about 3.7% y/y (Sep 2026) [MKT §0]. Every option except I bonds earns **zero or a negative real return after tax**. That's acceptable for insurance money and a bad deal for long-term money [SAV §0; CR1 §1.5].

### 3.3 Recommended tier structure [SAV §4.3]
| Tier | Amount | Where | Why |
|---|---|---|---|
| 0: Bill buffer | 0.5–1 × monthly expenses (in addition to the EF) | Checking | Avoid overdrafts |
| 1–2: First responder | Up to **3 × monthly essentials** | HYSA at an FDIC-insured bank, account held **directly** in your name | Insured, cash in 0–3 days |
| 3: Deep reserve | The rest of the EF | **SGOV/BIL**, a **13- or 26-week T-bill ladder** (auto-roll), or a **Treasury-only MMF**, all inside your brokerage | State-tax-exempt; next-day liquidity |
| Optional: I bonds | Only money you won't need for 12+ months (e.g., medium-term savings, or EF months beyond 6 after year 1) | TreasuryDirect, up to $10K per person per year | The 1.40% real floor **ties the highest fixed rate since 2006** [SAV §1.6, V] and is locked for 30 years. Buy by **Apr 30, 2027** to get it |

**Example ($3,500/month, 4.5 months = $15,750):** $10,500 in a HYSA plus $5,250 in SGOV or a 26-week bill, with $1,750–$3,500 of normal float in checking [SAV §4.5].

### 3.4 Safety rules
- **Check the bank on FDIC BankFind** (https://banks.data.fdic.gov/bankfind-suite/). Prefer an account held directly at the bank over a fintech app.
- **Why:** in 2024 the middleware firm Synapse went bankrupt and froze customer funds for months at apps built on it, including Yotta, which Graham Stephan had invested in and promoted. FDIC insurance doesn't cover a non-bank intermediary's failure [SAV §1.2; CR1 §4.5].
- **Crypto "yield" accounts are not savings accounts.** Andrei Jikh called BlockFi "basically a glorified high yield savings account" in 2021. BlockFi and Voyager both failed in 2022 [CR2 §2.4, V].
- **Rank HYSAs by their sustainable *base* rate, not a teaser.** Watch for minimums, monthly deposit requirements and residency limits [SAV §1.2].
- **Creator HYSA "favorites" pages are usually affiliate links** (Whiteboard Finance, Graham Stephan, Mark Tilbury). Compare rates yourself [CR1 §3.5; CR2 §1.6].
- **Jikh's rule of thumb:** don't switch banks for less than ~1 percentage point. Account for fees with a break-even balance [CR2 §2.4, V].
- **Never hold the EF in stocks or bond funds.** In 2022 stocks and bonds fell together, and job-loss risk tends to rise in exactly those periods [SAV §4.3].

### 3.5 Taxes on your cash interest (don't forget this part)
These are general federal rules; check IRS Topic 403 and Publication 550, and your state's rules, before filing.
- **HYSA, savings-account, CD and money-market interest is ordinary income.** It's taxed at your marginal federal rate and, except for the Treasury share of a money market fund, by your state too. Example: $20,000 earning 4.0% is about **$800 a year of interest, roughly $176 of federal tax at 22%**, plus state tax. That's why the after-tax table in §3.2 matters.
- **T-bill interest** is federally taxable but **state-tax-exempt**. Direct T-bill interest is reported on a **1099-INT**. SGOV/BIL and Treasury MMF payouts come on a **1099-DIV** as ordinary (non-qualified) dividends; claiming the state exemption depends on your state and the fund's published "% from US government obligations".
- **I bond interest** is federally taxable when you redeem (or at final maturity) unless you elect yearly reporting, and it's state-exempt.
- **CD interest is taxable in the year it's credited,** even if the CD hasn't matured yet. An early-withdrawal penalty is deductible (it shows on the 1099-INT).
- **Interest is taxable even when no form arrives.** Banks only have to send a 1099-INT at $10 or more.
- **Withholding:** if your total interest (plus other non-wage income) gets large, add a little extra withholding on your W-4 so you don't owe an underpayment penalty. For most people with a $20K EF this isn't needed.
- **Tax-advantaged accounts change everything.** Interest inside a 401(k), IRA or HSA isn't taxed each year. That's one more reason bonds and TIPS go in the 401(k)/IRA first (§5.3).

---

## 4. What to do with the excess: order of operations

### 4.1 The ladder (2026 limits verified against IRS IR-2025-111, Notice 2025-67, Rev. Proc. 2025-19 [SAV §3.2, V])
| Step | Action | Why / evidence |
|---|---|---|
| 0 | **Starter EF:** 1 month of essentials or $2,000, whichever is larger, in a HYSA | Small liquid buffers lower hardship (McKernan 2016). It also stops new credit-card debt while you work through the steps [SAV §3.1] |
| 1 | **Contribute enough for the full 401(k) match** | Usually an instant 50–100% return. Choi, Laibson & Madrian (2011, *REStat*) found **36%** of employees who could withdraw penalty-free still didn't take the full match, leaving about **1.6% of salary** on the table, and an educational nudge didn't change that [SAV §2.1, V]. Check **vesting** and whether the plan has a **true-up** |
| 2 | **Pay off debt above ~8% APR**, highest APR first (avalanche) | Paying off a 22% card is a guaranteed 22% return. Whiteboard Finance calls it a "guaranteed return" [SAV §3.1; CR1 §3.3]. For 4–8% debt, pay the normal schedule and invest alongside it |
| 3 | **Finish the full EF** (§2) | Covers the median-to-mean unemployment spell [SAV §2.1] |
| 4 | **HSA, if your health plan qualifies:** max it ($4,400 self / $8,750 family for 2026), invest it, and keep receipts | Triple federal tax advantage. Since 2026, **bronze and catastrophic plans count as HSA-compatible** (OBBBA, IRS Notice 2026-05) [SAV §3.2, V]. CA and NJ don't follow the federal treatment [from memory; confirm] |
| 5 | **IRA: $7,500 for 2026.** Roth if your MAGI is below **$153K single / $242K MFJ** (phase-out ends at $168K / $252K) | Wider, cheaper fund choice than most 401(k)s. Roth contributions can be withdrawn later. If you're above the limit, use a **backdoor Roth**, but watch the **pro-rata rule** if you have any pre-tax IRA balance on Dec 31 [SAV §3.3, V] |
| 6 | **Raise the 401(k) toward $24,500** | Unused annual tax-advantaged space is lost for good [SAV §3.1] |
| 7 | **Mega-backdoor Roth,** if the plan allows after-tax contributions plus in-plan conversion | Total room is $72,000 minus your deferrals and the employer's contributions [SAV §3.1] |
| 8 | **Taxable brokerage** (tax-efficient broad ETFs) | "Taxable accounts are the last stop, not the first" (Whiteboard Finance) [CR1 §3.3, V]. Also the right home for goals before age 59½ |
| Parallel | **Sinking funds** for known expenses within 1–3 years (car, move, wedding, down payment within ~5 years) | Use HYSA, T-bills or CDs that mature before the date. **Never stocks** [SAV §1.8, §4.6] |

Creators and planners disagree slightly on ordering:
- Whiteboard puts the full EF before the match.
- Graham's Sept 2026 post goes EF, then match, then debt above 7–8%.
- [MKT] puts the match first.

The **starter-EF-then-match** order above settles it [CR1 §3.3; SAV §2.3].

### 4.2 Timing traps specific to November 2026
- **401(k) changes must go through payroll by Dec 31.** Only a handful of 2026 paychecks are left. If you aren't getting the full match now, raise your deferral **this week**. Use some of the excess cash as a "match bridge" to cover the drop in take-home pay [SAV §3.2, §4.2]. If your plan has an annual **true-up**, the match may be fixed up at year end anyway, so ask HR.
- **The 2026 IRA deadline is April 15, 2027,** and you can make 2027 contributions from **Jan 1, 2027**. That means you can fund **both** a 2026 and a 2027 Roth IRA within the next few months, up to $15,000 of space [SAV §3.2].
- **Same deadline for the HSA:** 2026 HSA contributions are allowed until April 15, 2027.
- **2027 limits weren't published as of 2026-11-10.** The IRS usually announces them in November, so re-check [CR1 §6.1; MKT §10.1].

### 4.3 Roth vs. Traditional (2026 single brackets [SAV §3.4, V])
| Federal bracket (single taxable income) | Default |
|---|---|
| 10–12% (up to $50,400) | **Roth** IRA and Roth 401(k) |
| 22% ($50,400–$105,700) | **Roth IRA**, and Roth or a ~50/50 split in the 401(k). Lean Traditional if you live in a high-tax state and expect to retire somewhere with no income tax |
| 24% ($105,700–$201,775) | Split or Traditional-leaning 401(k); Roth or backdoor Roth IRA |
| 32%+ | Traditional 401(k); backdoor Roth IRA |

If you're single, covered by a workplace plan and earn over **$91K**, you **can't deduct** a Traditional IRA contribution (the phase-out is $81K–$91K). In practice that means Roth IRA or backdoor Roth [SAV §3.3].

### 4.4 Worked splits of the $20K
Assumptions: single, renter, stable W-2 job, no high-interest debt, **match already captured**, not HSA-eligible, no known expenses, **4.5-month EF** [SAV §4.5 method].

| | $2,500/mo | $3,500/mo | $4,500/mo | $5,500/mo |
|---|---|---|---|---|
| EF target (4.5 mo) | $11,250 | $15,750 | $20,250 | $24,750 |
| HYSA (≤ 3 mo) | $7,500 | $10,500 | $13,500 | $16,500 |
| T-bills / SGOV | $3,750 | $5,250 | $6,500 (EF $250 short) | $3,500 (EF $4,750 short) |
| **Excess to invest** | **$8,750** | **$4,250** | **$0** | **$0** |
| Where the excess goes | $7,500 to the **2026 Roth IRA**; $1,250 to a taxable account or kept toward the Jan 2027 Roth IRA | $4,250 to the 2026 Roth IRA; top up the remaining $3,250 of 2026 room from paychecks before 4/15/2027 | Finish the EF from new savings; still fund the Roth IRA from cash flow when possible | Fill the EF gap first (~$400/mo for ~12 months), plus match |

- **Sensitivity:** every $500/month change in expenses moves the investable excess by **$2,250** at 4.5 months [SAV §4.5].
- **If you're not getting the full match today:** set aside the match bridge first. That's roughly (extra % of salary needed) × salary × (months left ÷ 12). Make the change now.
- **If you have a card balance:** pay it off from the excess right away, but never drop below the starter EF [SAV §4.2, §4.6].

---

## 5. Sample long-term allocation (moderate risk, 10+ years)

This covers only long-term money in your 401(k), IRA and taxable account. The EF and sinking funds sit outside it.

### 5.1 The evidence behind the design
| Design choice | Evidence | Strength |
|---|---|---|
| **A low-cost index core** | Sharpe (1991): after costs, the average active dollar must trail the passive dollar (an accounting identity). SPIVA: roughly 85–90% of US large-cap funds trail the S&P 500 over 10–20 years [exact current figures UNVERIFIED]. Fama & French (2010): fund alpha after fees looks like luck. Carhart (1997): only the losers keep losing persistently, and fees reduce returns one-for-one [ACAD §1] | Very strong / strong |
| **Fees are the first filter** | Low fees predict fund success better than star ratings (Morningstar/Kinnel) [ACAD §6.4]. French (2008): investors spent about 0.67% a year trying to beat the market [ACAD §1.5] | Strong |
| **Hold the whole market, not a few stocks** | Bessembinder (2018, *JFE*): about 4% of US stocks produced all net wealth creation over T-bills from 1926 to 2016, and most stocks lost to T-bills over their lifetimes. Globally, 2.4% of 64,000 stocks did (Bessembinder et al. 2023) [ACAD §2] | Very strong |
| **Diversify globally** | Anarkulova, Cederburg & O'Doherty (2022, *JFE*): across 39 developed markets there's about a **12%** chance of a real loss even over 30 years, and the US record is a lucky outlier. Home bias is well documented (French & Poterba 1991) [ACAD §7.2]. Vanguard's 10-year forecast has developed ex-US at **5.3–7.3%** vs. US at **4.8–6.8%** [MKT §8] | Strong (fact) / moderate (size of the benefit) |
| **High equity share while young** | Your future earnings work like a large, bond-like asset (Ayres & Nalebuff 2013). The default for a *moderate* profile is 80–90% equity / 10–20% bonds, and **never leverage** [ACAD §9] | Moderate |
| **Bonds for drawdown control** | Starting yields predict bond returns well. The US Agg yielded **5.56%** (9/30/2026), and Vanguard's 10-year bond forecast is **5.1–6.1%**, about equal to US stocks [MKT §3.4, §8]. Caveat: stocks and bonds can fall together when inflation is the main shock, as in 2022 and 2026 | Strong (starting yield → return) |
| **Optional factor tilt** | Value, profitability and investment premiums (Fama & French 1993/2015; Novy-Marx 2013; Asness et al. "Quality Minus Junk" 2019). Size works only when combined with quality. Premiums shrank about 58% after publication (McLean & Pontiff 2016), so plan on about **half** the historical premium. Value lagged from about 2007 to 2020 [ACAD §3, §4] | Moderate to strong; *optional* |
| **Keep it simple and automated** | Morningstar's *Mind the Gap* puts the behavior gap at about 1.1–1.2 points a year. It's largest in narrow and thematic funds and smallest in allocation and target-date funds [ACAD §6.2] | Moderate to strong |

### 5.2 Three versions to choose from
**Option A: "One decision" (simplest; good inside a 401(k)).** A **target-date index fund** from your plan, if its expense ratio is low. Or a two-fund setup: an all-world stock index fund (e.g., VT-type) plus a total-bond fund (e.g., BND/AGG-type) at 80/20.

**Option B: Three-fund core (the default).**
| Sleeve | Weight of long-term portfolio | Example fund type (verify ER and holdings) |
|---|---|---|
| US total stock market | 51% | VTI / ITOT / FZROX-type, or the 401(k) plan's total-market or S&P 500 index fund |
| International total market (developed + EM) | 29% | VXUS / IXUS-type |
| US core bonds (intermediate) | 15% | BND / AGG-type, or the plan's bond index fund |
| TIPS (optional) | 5% | TIPS index fund (best held in a 401(k) or IRA) |
| **Total** | **80% stocks / 20% bonds**; stocks split about 64% US / 36% international (global market-cap weight, the app's default; anything from 50/50 to 80/20 is defensible) | |

**Option C: Core + factor tilt + stock-pick satellite (closest to your interests).**
| Sleeve | Weight | Notes |
|---|---|---|
| US total market | 38% | Core |
| International total market | 24% | Core |
| **Factor tilt:** US small-cap value with a profitability screen | 8% | e.g., AVUV-type (ER 0.25% [MKT §5.1]). In small caps, filtering out unprofitable companies matters |
| **Factor tilt:** international small-cap value | 4% | e.g., AVDV-type. Alternative: replace both tilt sleeves *and* the core with one global factor-tilted fund (e.g., AVGE/DFAW-type, the US analog of Ben Felix's own Dimensional global fund [CR1 §1.6]; this is the brief's inference, not his endorsement) |
| **Satellite: individual stocks** | **6%** (= 7.5% of stocks; cap 10% of stocks = 8% of this portfolio) | Quality/value picks; rules in §5.4 |
| Core bonds + TIPS | 20% | 15% + 5% |
| **Check** | Stocks 80%. US ≈ 65% / ex-US ≈ 35% of stocks, close to global market cap (~64/36 [MKT §4]). The tilt is 15% of stocks, inside the 0–30% evidence band | |

**If you'd panic-sell after a 20% drop, use 70/30 instead** (same shape, bonds raised to 30%). The best portfolio is the one you'll actually hold through a bad decade.
- **What a crash looks like:** if stocks fall 50% and bonds are flat, an 80/20 mix drops about **40%**, and 70/30 drops about **35%**.
- **This has happened.** From 2000 to 2009, $10,000 in the S&P 500 fell to about $9,089 including dividends (quoted by Graham Stephan [CR1 §4.2]). That's why the plan includes international stocks, bonds and an EF.

### 5.3 Which fund in which account (keep it simple under ~$50K) [SAV §3.5]
- **401(k):** bonds and TIPS go here first (their interest is taxed as ordinary income), plus the plan's cheapest broad index funds.
- **Roth IRA:** the highest-expected-return sleeves, meaning the factor tilt and any stock picks. Tax-free growth on the biggest expected gains.
- **Taxable:** US and international total-market ETFs. International funds belong here because the foreign tax credit only works in taxable accounts. Keep high-yield, dividend-heavy funds out of taxable when possible.
- Under about $50K, holding the **same mix in every account is fine**. Only optimize asset location when it saves at least 0.1% a year.

### 5.4 Rules for the stock-pick satellite
1. **Caps:** all individual stocks ≤ **10% of your stocks** (warning above 10%, strong warning above 20%; the evidence range for moderate risk is 10–20% of equities [ACAD §2.1, §7.1]). **Single stock ≤ 5% of investable assets** (strong warning at 10%), counting what you already own through ETFs. Thematic ETFs ≤ 5% [MKT §7.4]. Crypto defaults to 0%, with an opt-in cap of 5% [MKT §7.5].
2. **Count overlap.** Buying NVDA, MSFT or AAPL on top of VTI raises concentration, and the top 10 are already **40.6%** of the S&P 500 [MKT §2].
3. **Use a written process:**
   - The Plain Bagel's screen → business → finances → strategy → valuation → thesis [CR1 §2.4].
   - Joseph Carlson's moat checklist: barriers to entry, market share, balance sheet, organic growth, recession survival [CR2 §4.2].
   - Sven Carlin's owner-earnings DCF at a 10% hurdle, buying only if the *worst case* still works [CR2 §3.2].
   - Score quality the QMJ way: profitability, growth, safety [ACAD §3.4].
4. **Use the behavior guards:**
   - Wait 48–72 hours before buying any new idea.
   - Keep a decision journal.
   - Before every sale, ask "Would I buy it today?" (disposition effect).
   - Keep annual turnover under 50% (Barber & Odean 2000: the most active traders earned 11.4% vs. the market's 17.9%) [ACAD §6].
5. **Judge yourself on total return against VT/VTI over 5+ years,** not on dollar gains. Dividends aren't "free income" (Miller & Modigliani 1961; Hartzmark & Solomon 2019) [ACAD §5].
6. **Never buy because of a paid promotion.** Sponsored stock promos jump about 8–12% and then reverse within about 30 days (Leite, Moraes & Moura 2025, *Applied Economics Letters*) [CR1 §2.2].
7. **Products to avoid as core holdings:**
   - Covered-call "income" ETFs (JEPI's 5-year total return was 7.63% vs. SPY's 11.40% [MKT §7.1]; Israelov & Nielsen 2015)
   - Leveraged ETFs
   - Buffered ETFs (often replicable more cheaply with less stock plus T-bills)
   - Nontraded BDCs and private credit [MKT §7]

### 5.5 Tax traps once you start investing in taxable
- **Wash-sale rule.** If you sell at a loss and buy the same or a "substantially identical" security within **30 days before or after** (a 61-day window), the loss is disallowed. This applies across **all** your accounts, including IRA/Roth purchases, 401(k) auto-buys and dividend reinvestment. If the repurchase happens in an IRA, the loss is **gone for good** (Rev. Rul. 2008-5) [ACAD §6.3; CR2 §2.5]. Swapping between funds that track different indexes (e.g., a total-market fund for an S&P 500 fund) is the usual approach, but whether two funds are "substantially identical" is a gray area. Turn off DRIP and pause scheduled buys in that fund during the window.
- **Harvested losses** offset gains first, then up to $3,000 a year of ordinary income; the rest carries forward.
- **Qualified dividends and long-term gains** are taxed at 0% up to $49,450 of taxable income (single, 2026) [CR2 §6.5]. Covered-call ETF payouts and REIT dividends are mostly ordinary income, another reason to keep them out of taxable.
- **Hold more than one year** before selling a winner in taxable if you can. Short-term gains are taxed as ordinary income.
- **Roth IRA rules:** contributions can come out any time tax- and penalty-free; earnings generally need age 59½ **and** a 5-year-old Roth. You need earned income at least equal to your contribution.

---

## 6. Lump sum vs. dollar-cost averaging (DCA)

- **Evidence:** investing a lump sum right away beat a 12-month phased plan **about two-thirds of the time** in US, UK and Australian data (Vanguard 2012; ~68% in a 2023 update [UNVERIFIED]; theory in Constantinides 1979) [ACAD §10]. Stocks are expected to beat cash, so waiting costs you expected return.
- **Investing each paycheck as it arrives is different.** That's just investing money when you get it, and it's optimal. Keep 401(k) and IRA payroll contributions automatic.
- **Your case.** You have moderate tolerance, and drawdowns stress you. Ask yourself: ***"If the market fell 20% the week after I invested, would I keep going?"***
  - **Yes:** invest the excess as a lump sum.
  - **Not sure or no:** invest it in **3–6 equal monthly installments** (the savings brief's default when the sum is more than 25% of your existing portfolio [SAV §4.4]), or up to 6 months [ACAD §10]. You accept a small expected cost for the comfort of knowing you'll stick with it.
- **Roth IRA trick:** you can *contribute* the full 2026 amount now, before the April 15, 2027 deadline, and leave it in the IRA's money market fund. Then *buy* your ETFs in monthly tranches inside the IRA. The contribution still counts for 2026.
- **Don't let valuations decide.** CAPE is 39.7 (near the 1999 record of 44), while the forward P/E is 17.7 because earnings grew 33% [MKT §1]. CAPE helps forecast 10-year returns and fails at timing (Goyal & Welch 2008). People who waited for CAPE to normalize missed decades of gains [ACAD §8]. High valuations argue for **saving more and diversifying**, not for waiting.

---

## 7. What "the experts" (institutions) are saying (Nov 2026 snapshot [MKT §8])

| Source | 10-year US stocks | International | US bonds | Takeaway |
|---|---|---|---|---|
| Vanguard (VCMM, 9/30/2026) | 4.8–6.8% | Developed ex-US 5.3–7.3%; EM 3.7–5.7% | 5.1–6.1% | US stocks "remain highly valued"; bonds about equal to US stocks |
| J.P. Morgan 2027 LTCMA | 7.5% US large cap | Developed above EM | Global aggregate 5.3% ("highest in two decades") | More optimistic because of margins and AI |
| AQR (real) | — | — | — | Global 60/40 real return **3.4%**; "risk premia are compressed" |
| JPM Guide to the Markets 4Q26 | US large caps "expensive and overconcentrated" | International "attractive relative to the US" | Neutral on bonds | — |

**Plan with the conservative end:** about **5% nominal** for US stocks, 5–6% for international, and 5% for bonds. Don't use the ~15% trailing 10-year S&P 500 return [MKT §1.2]. Lower expected returns mean you need a higher savings rate, not a riskier portfolio.

Market regime on 2026-11-10 [MKT §0]:
- Fed funds 3.75–4.00%, after a hike in Sep 2026. Next FOMC meeting is Dec 8–9.
- 10-year Treasury 4.98%; 10-year TIPS real yield 2.66%.
- CPI about 3.7%; unemployment 4.1%.
- These are context only, not signals.

---

## 8. What each of the 8 creators would likely say about your situation

This section is an *inference* from each creator's documented, dated positions. They haven't commented on your situation. **[V]** marks a position verified from a title, description, transcript or the creator's own site. **[UNVERIFIED]** marks a widely attributed position that wasn't confirmed. The grade is the brief's rating of how well the creator's views align with the evidence (A = strong … E = contradicted).

### Ben Felix (CFA, CFP; CIO of PWL Capital). Grade A [CR1 §1]
- **Cash:** keep only what you need for liquidity. "Cash is a terrible long-term investment, even at 5% interest" (2023-08-29) [V]. He holds about 5% of his liquid portfolio in cash and calls it "more than I probably need" [V]. He'd approve of an EF and would push you to invest the excess. His "Biggest Myths" video (2026-07-26) also warns against over-saving at the expense of living [V].
- **Portfolio:** a global total market with a small/value/profitability tilt, which is what he holds himself (100% equity in one Dimensional global fund) [V]. Modest home bias is fine; extreme home bias isn't [V]. He'd be against stock picking (Bessembinder), dividend-chasing, covered calls and leveraged ETFs for you [V].
- **Caveat:** his account advice is Canadian (TFSA/RRSP), and his firm uses Dimensional funds, which is a mild conflict.

### The Plain Bagel (Richard Coffin, CFA; portfolio manager). Grade A/B [CR1 §2]
- He gives **no picks, by policy** [V]. He'd likely say a diversified index fund suits most people, and if you pick stocks, use a disciplined five-step research process and write the thesis down [V].
- Dividends are "irrelevant (sort of)" [V]. His most useful contribution for you is skepticism of finfluencers: check registrations (FINRA BrokerCheck), notice sponsor incentives, and treat paid microcap promotions as red flags [V]. He'd tell you to verify anything an AI tool says, including this document ("AI Is Giving Financial Advice Now", 2026-05-29) [V].
- **Emergency fund:** standard getting-started sequencing (debt → EF → accounts → allocation) [UNVERIFIED].

### Whiteboard Finance (Marko Zlatic; "not a financial adviser"). Grade B/C [CR1 §3]
- **Order of operations** ("The Exact Order To Invest Your Money", 2026-03-31) [V]: EF ("the foundation") → high-interest debt ("guaranteed return") → employer match → IRA before more 401(k) → HSA ("triple tax advantage") → more 401(k) → taxable ("the last stop"). For you: build the EF in a HYSA, then fill the Roth IRA. He'd also suggest a 70/20/10 budget [V] and a simple ETF core built on VTI [V].
- **Caveats:** heavy financial sponsors and affiliate HYSA links. He was paid $5,000 to promote a microcap (GRML, Oct 2026) [V]. He has promoted covered-call income funds (JEPI/DIVO/NUSI, 2021) [V], and his examples assume ~10% returns. Use his sequencing; ignore the income and crypto products.

### Graham Stephan (former real-estate agent). Grade B/C [CR1 §4]
- **Priority order** (Substack, 2026-09-14) [V]: EF → full 401(k) match → pay off debt above 7–8% → only then optionally prepay cheap debt. "Maintain a cash buffer — ALWAYS" [V]. Long-time HYSA advocate. In 2026 he also promotes T-bills for cash ("Why you should have $5M in T-Bills") [V]. "About 6 months of expenses" is commonly attributed to him [UNVERIFIED].
- **Portfolio:** index funds as the core (US + international + emerging markets), dollar-cost averaging monthly, "Time in the market beats timing the market," citing Bessembinder [V]. Suggests an 80% safe / 20% risky barbell [V]. His Oct 2026 "Urgent Warning" recommends diversifying (international, bonds, cash) to survive a lost decade, not market timing [V].
- **Caveats:** FTX promoter lawsuit, the Yotta/Synapse episode, many sponsors and alarmist titles. A 20% "risky" sleeve is more than this plan's 10% cap.

### Mark Tilbury (entrepreneur; no finance credentials). Grade: high on basics [CR2 §1]
- **EF:** **5 × core monthly expenses**, reachable within 24 hours, "zero risk" ("not even the safe stuff… crypto, long-term bonds"), in an FDIC-insured HYSA (SoFi, Ally, Marcus) [V].
- **Budget:** split each paycheck **25% growth / 15% stability / 50% essentials / 10% rewards**, automated on payday. Once the EF is full, redirect the 15% to growth [V].
- **Portfolio:** a Bogleheads three-fund portfolio with ERs under 0.2%. Individual stocks "a small, optional, and higher risk portion" [V]. In 2026 he added international exposure because of S&P concentration (top 10 ≈ 40%) [V].
- **Caveats:** UK products (Trading 212/ISA/UCITS tickers like IWDA, which can create PFIC problems for US persons), affiliate links, unverifiable wealth claims, examples that assume 10% returns, and "start a business" survivorship bias.

### Andrei Jikh (YouTuber, former magician). Grade: high on savings and indexing, low on crypto [CR2 §2]
- **Order** ("Do This NOW – Once You Save $1,000!", 2020) [V]: invest in yourself → **3–6 month EF** → pay off debt at 4%+ → HYSA → CDs, T-bills, I bonds → VTI/VOO in a **Roth IRA**.
- **Comparing accounts:** compare net of fees and taxes, find the break-even balance, don't switch for less than ~1 point, and check FDIC coverage [V]. On dividends: "If you want the best overall return… buy the broad market index funds and chill for 20 years" (2023) [V].
- **Caveats:** he promoted BlockFi as a "glorified HYSA" (2021) and lost large sums in the Voyager and BlockFi collapses. His 2026 content leans toward alarmist macro, which is commentary, not principles.

### Sven Carlin, Ph.D. (value investor). Grade: mixed [CR2 §3]
- **Savings:** "We are always talking investing the money you don't need. That's the alpha and omega of investing." Near-term money belongs in **3-month T-bills**, not stocks (2026-10-24) [V]. "First you have to solve your life" before investing [V]. He'd approve of a T-bill tier for the EF.
- **Portfolio:** skeptical of cap-weighted and US "value" ETFs ("buy the most expensive stocks") [V]. Bearish on US valuations (an "AI bubble") [V]. Calls Bitcoin gambling and gold non-productive [V].
- **What to take from him:** his worst-case-positive DCF and risk/reward quadrant are useful *filters for the satellite*. His concentration (down to "one business") and anti-ETF stance don't suit a moderate-risk investor.
- **Caveats:** he sells a $499/yr research platform, and his return claims are self-reported and inconsistent.

### Joseph Carlson (dividend growth turned quality compounders). Grade: mixed [CR2 §4]
- **Portfolio:** "If you want a simple way to grow wealth, start buying ETFs… a hybrid approach, which I think is the best for most people" (2026-08-20) [V]. He'd likely endorse your core-plus-satellite structure, with picks chosen by moat, profitability, balance sheet and organic growth, held 5+ years, and bought during panics rather than sold [V].
- **Savings:** **no verified EF or HYSA guidance.** His stated early priorities are budgeting, depositing often, getting raises and investing the extra cash flow [V]. Don't attribute a savings rule to him.
- **Caveats:**
  - His claim that individuals can outperform is graded D against Bessembinder and Barber & Odean.
  - He promotes Qualtrim ($10/mo).
  - His ~14 mega-cap holdings overlap heavily with the S&P 500.
  - He reports dollar gains, not audited returns vs. a benchmark.
  - His Nov 2026 fair-value targets are dated picks, not buy signals.

### Where all 8 agree, and where they don't
- **Consensus:** (1) build an EF in an insured, liquid account; (2) capture the match and kill high-interest debt; (3) use low-cost broad index funds as the core (Carlin is the exception); (4) fees matter; (5) don't time the market or chase hype [CR1 §5].
- **Disagreements:**
  - EF size: 3–6 months (Jikh), 5× (Tilbury), "solve your life" (Carlin).
  - Dividends and covered calls: Felix and Bagel against; Whiteboard and early Jikh for.
  - Stock picking: Felix against; Carlson and Carlin for.
  - Satellite size: Graham's 20% vs. the evidence default of ≤10–20% of equities.
  - Factor tilts: Felix for; most others silent.

---

## 9. 30-day action checklist (starting 2026-11-10)

**Before acting on any number in this document, re-check it.** Snapshot dates: Treasury bills 2026-11-10; HYSA and CD rates (Bankrate) 2026-11-10; SGOV/BIL yields 2026-11-09; FDIC national rate 2026-10-19; I bond rate valid for purchases 2026-11-01 to 2027-04-30; BLS unemployment data Oct 2026; 2026 IRS limits (final). HYSA and CD rates can change weekly, and the app marks them stale after 7 days.

### Days 1–3: get the facts
- [ ] Add up your **essential** monthly expenses from the last 2–3 months of statements. Graham Stephan suggests tracking everything for 60 days, so keep tracking after this [CR1 §4.4].
- [ ] Write down where the $20K sits today and its current APY. If any of it earns under 3%, it's underpaying (top APYs are 4%+) [SAV §4.6].
- [ ] Get your 401(k) plan details: match formula, **vesting schedule**, **true-up** yes/no, Roth 401(k) option, after-tax/mega-backdoor option, fund list with **expense ratios**, and your current deferral %.
- [ ] List any debts with balances and APRs.
- [ ] Estimate your 2026 MAGI and note your state's marginal income-tax rate.

### Days 4–7: fix the cash
- [ ] **Raise your 401(k) deferral to at least the full match** if you're below it. December paychecks are the last ones for 2026.
- [ ] Pay off any card or loan balance above ~8% APR, keeping at least the starter EF (the larger of 1 month or $2,000).
- [ ] Pick your EF months from §2.3 and calculate the target.
- [ ] Open (or confirm) a HYSA **held directly at an FDIC-insured bank**. Check it on BankFind, choose based on the *base* rate, and note any promo end date. Move up to 3 months of essentials there.

### Days 8–14: the deep tier and accounts
- [ ] In your brokerage, put the rest of the EF into **SGOV/BIL**, a **13- or 26-week T-bill on auto-roll**, or a **Treasury-only MMF**. Use the after-tax table in §3.2 with your state rate.
- [ ] Optional: if you have money you won't need for 12+ months, consider **I bonds** at TreasuryDirect ($10K limit; 1.40% fixed through Apr 30, 2027). This shouldn't come out of the EF.
- [ ] Decide **Roth vs. Traditional** (§4.3) and open a Roth IRA if you don't have one. If your MAGI is above $153K, read up on the backdoor Roth and check for any pre-tax IRA balance (pro-rata rule).
- [ ] If you're HSA-eligible, open or fund the HSA and choose its investment option.

### Days 15–21: write your investment policy (one page)
- [ ] Answer the drawdown question: "If the market fell 20% right after I invested, would I keep going?" Choose 80/20 or 70/30.
- [ ] Pick Option A, B or C from §5.2. Choose specific funds by **expense ratio, tracking and diversification**, not by past 1-year returns or yield [ACAD §1.4, §6.4]. Check each fund on the issuer's site.
- [ ] Write down your satellite rules: ≤10% of your stocks in total, ≤5% of investable assets per stock, a 48–72 hour wait, a written thesis, and benchmarking against VT/VTI.
- [ ] Set rebalancing rules: glance at drift during each monthly check-in, act only when a sleeve drifts more than 5 percentage points (or 25% of its target for small sleeves), and do a full review once a year. Rebalance with new contributions first.

### Days 22–30: invest and automate
- [ ] Contribute the excess to your **2026 Roth IRA**. Invest it as a lump sum or in 3–6 monthly tranches (§6).
- [ ] Set up automatic monthly transfers: paycheck → IRA/taxable auto-invest, plus an EF top-up until it's full.
- [ ] Add calendar reminders:
  - **Jan 2, 2027:** start 2027 IRA contributions, and check the newly published 2027 IRS limits.
  - **Apr 15, 2027:** deadline for any remaining 2026 IRA/HSA room.
  - **Apr 30, 2027:** last day for the 1.40% I-bond fixed rate.
  - **Every quarter:** re-check HYSA and T-bill rates.
  - **Late Jan / Feb 2027:** collect your 1099-INT and 1099-DIV forms (bank, brokerage, TreasuryDirect) and check that the cash interest matches what you expected (§3.5).
  - **Once a year:** rebalance.
- [ ] Turn off price alerts and daily ticker watching. The evidence favors infrequent decisions (Barber & Odean) [ACAD §6.1].

---

## 10. Questions to answer so this plan can be made specific

1. **Monthly essential expenses** (rent, utilities, groceries, insurance, minimum debt payments, transportation, health). This sets the EF and the investable excess.
2. **Job and income stability:** industry, W-2 vs. variable/1099 pay, bonus share of income, recent layoffs at your employer, how long a job search in your field takes.
3. **Household:** single or dual income, dependents, renter or owner, age of your car.
4. **401(k) match details:** formula (e.g., 100% up to 4%, or 50% up to 6%), vesting schedule, true-up, current deferral %, Roth 401(k) option, after-tax/in-plan conversion (mega-backdoor), student-loan match, PLESA (emergency savings account), and the fund menu with expense ratios.
5. **Income for Roth eligibility:** estimated 2026 MAGI and filing status. Also any **pre-tax IRA balances** (rollovers), which affect a backdoor Roth.
6. **HSA eligibility:** is your health plan an HDHP, or a bronze/catastrophic plan (eligible from 2026)? Self-only or family coverage? Your deductible?
7. **Debts:** credit cards, personal, auto and student loans, with balances and APRs.
8. **State of residence:** this decides whether T-bills beat a HYSA after tax.
9. **Known expenses in the next 1–5 years:** car, move, wedding, travel, home down payment (amount and date).
10. **Current balances and 2026 contributions to date** in your 401(k), IRA, HSA and taxable account, and what you hold in them now (to measure overlap and home bias).
11. **Behavior:** your honest answer to the 20%-drop question, and how much of your money you *want* in individual stocks for interest or learning.
12. **Is the $20K all in one place,** and does it include your checking float?

Once you've answered these, §2–§5 can be rerun with your numbers, either by hand or with the app's savings module [TECH].

---

*Educational research summary prepared 2026-11-10 from the FinTec research briefs. Not individualized investment, tax or legal advice. Rates, yields, limits and creator positions are as of the dates shown and will change. Verify at the primary source before acting.*
