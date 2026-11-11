# Tech Stack & Free Data Sources: Technical Brief

**Project:** Local Python + Streamlit "Investment Research Assistant"
**Budget:** $0 (free data only; optional free LLM)
**Date of research:** 2026-11-10
**Status legend:** facts marked **[VERIFIED 2026-11-10]** were checked live against the primary source on that date (HTTP probe or official doc page). Facts marked **[UNVERIFIED]** come from prior knowledge or secondary sources and could not be confirmed. Some were blocked by the research machine's corporate proxy (Zscaler), which returned HTTP 403 for openrouter.ai, opencode.ai, ollama.com and the FMP docs. **Re-check every [UNVERIFIED] limit before you rely on it.** Free tiers change often.

**Fact-check pass (2026-11-10):** an adversarial review re-checked citations (via OpenAlex), PyPI versions, SEC/FRED/Treasury/FDIC/TreasuryDirect/IRS pages, YouTube quota docs, channel IDs, arXiv ToU, and Ollama/opencode docs (via GitHub). Items it confirmed are now marked **[VERIFIED 2026-11-10, fact-check]**. Corrections are inline. OpenRouter, FMP and the Finnhub pricing page were still unreachable (403 / JS-rendered), so they stay [UNVERIFIED].

> **Disclaimer for the app:** this tool supports research and education. It is not personalized financial advice. Every number the app shows must carry its source and as-of date.

---

## 0. Executive summary: recommended architecture at $0

| Need | Primary | Fallback 1 | Fallback 2 | Notes |
|---|---|---|---|---|
| Daily prices (stocks/ETFs), adjusted | **yfinance** (unofficial Yahoo) | **Tiingo** free (official API, key) | Alpha Vantage (25 req/day) | Stooq CSV now sits behind a JS proof-of-work wall (see 1.9) |
| Fundamentals (US filers) | **SEC EDGAR XBRL APIs** (official, no key) | yfinance `.financials` | FMP free / Finnhub `basic-financials` | EDGAR is the source of truth for 10-K/10-Q numbers |
| Macro (rates, CPI, unemployment) | **FRED API** (free key) | US Treasury Fiscal Data API (no key) | — | |
| HYSA / T-bill / I-bond comparisons | **FRED** (DGS3MO, DTB3, DGS1...) + **Treasury daily yield-curve CSV** + **FDIC national rates page** (typical bank rates) | TreasuryDirect pages (manual/semi-manual) | — | No official TreasuryDirect rates API found (none located; absence not provable). HYSA APYs: hand-curated |
| Factor returns for regressions | **Kenneth French Data Library** (ZIP/CSV) | **AQR datasets** (QMJ, BAB, HML Devil, VME) (XLSX) | — | Both free; cite the authors |
| ETF holdings / expense ratios | **Issuer files** (iShares CSV, SSGA XLSX, Vanguard/Schwab pages) | SEC N-PORT filings via EDGAR | yfinance `funds_data` | Issuer endpoints are undocumented and fragile |
| News / analyst recs | **Finnhub free** (key) | yfinance `.recommendations`, `.news` | Alpha Vantage NEWS_SENTIMENT | |
| Research papers | **OpenAlex** (free key) | Semantic Scholar | arXiv q-fin, CORE | SSRN: link only, do not scrape |
| Creator content | **YouTube channel RSS** (no key) | yt-dlp metadata | youtube-transcript-api (local only) | Store summaries + links, not full transcripts |
| LLM (optional) | **OpenRouter `:free` models** | Ollama local | none (rule-based templates) | App must fully work with `LLM_PROVIDER=none` |

**Design principle:** the app's intellectual core is a **curated, versioned knowledge base** (markdown + JSON). Live data only feeds numbers (prices, ratios, rates) into deterministic, testable calculators. The LLM is an optional explainer, never a source of facts.

---

## 1. Free market & fundamental data

### 1.1 yfinance (unofficial Yahoo Finance wrapper)

- **Package:** `yfinance` latest **1.8.0** on PyPI **[VERIFIED 2026-11-10]** (https://pypi.org/project/yfinance/). Docs: https://ranaroussi.github.io/yfinance
- **Auth:** none. It scrapes or calls undocumented Yahoo endpoints.
- **ToS caveat:** the README says yfinance is "not affiliated, endorsed, or vetted by Yahoo". It also says: *"Remember - the Yahoo! finance API is intended for personal use only."* It points users to Yahoo's terms (https://legal.yahoo.com/us/en/yahoo/terms/otos/index.html) **[VERIFIED 2026-11-10, README text]**. **Implication:** this works for a personal, local, non-redistributed tool. Do not host it publicly or resell the data.
- **Reliability:** historically breaks whenever Yahoo changes endpoints, cookies or crumbs (many GitHub issues across 2023-2025 about 401/429 "Too Many Requests") **[UNVERIFIED, general track record]**. There is no published rate limit. Community experience suggests keeping bursts small (a few requests per second at most) and batching with `yf.download([...])`.
- **Key calls:**
  ```python
  import yfinance as yf
  px = yf.download(["VTI","VXUS","AVUV"], start="2010-01-01", auto_adjust=True,
                   group_by="ticker", threads=False, progress=False)
  t = yf.Ticker("MSFT")
  t.info                    # dict: marketCap, trailingPE, dividendYield, ... (field names drift)
  t.financials, t.balance_sheet, t.cashflow   # annual; .quarterly_* variants
  t.dividends, t.splits
  t.recommendations, t.upgrades_downgrades
  t.funds_data              # for ETFs: top holdings, sector weights, expense info (newer versions)
  ```
- **Hardening:**
  - Pin the version (`yfinance==1.8.*`).
  - Wrap every call in an adapter with tenacity retry and exponential backoff on HTTP 429.
  - Cache aggressively: prices daily, `.info` weekly.
  - Validate with pydantic. Treat missing keys as `None`, not as errors.
  - **Unit sanity checks on `.info`:** Yahoo has changed field units before. For example, `dividendYield` has appeared both as a decimal and as a percent in different periods [UNVERIFIED which form is current]. Cross-check yield against `trailingAnnualDividendRate / price` and reject values outside plausible bounds before they reach a screen.
  - Run a nightly smoke test (`pytest -m live`) that alerts you when the schema drifts.

### 1.2 SEC EDGAR APIs (official, primary for fundamentals)

Source: https://www.sec.gov/search-filings/edgar-application-programming-interfaces **[VERIFIED 2026-11-10]**

- **Auth:** "do not require any authentication or API keys". CORS is not supported, which is fine for server-side Python.
- **Endpoints** (base `https://data.sec.gov`). The CIK is zero-padded to 10 digits:
  - Submissions (filing history, tickers, SIC): `GET /submissions/CIK0000789019.json`
  - Company concept (one tag across time): `GET /api/xbrl/companyconcept/CIK0000789019/us-gaap/Revenues.json`
  - Company facts (all XBRL facts for one company): `GET /api/xbrl/companyfacts/CIK0000789019.json`
  - Frames (one tag across **all** companies for one period, ideal for screening): `GET /api/xbrl/frames/us-gaap/NetIncomeLoss/USD/CY2024.json`
    - Period formats: `CY####` (annual), `CY####Q#` (quarterly), `CY####Q#I` (instantaneous, i.e. balance-sheet items). Compound units use `-per-`, e.g. `USD-per-shares`.
- **Bulk ZIPs**, rebuilt nightly around 3:00 a.m. ET:
  - `https://www.sec.gov/Archives/edgar/daily-index/xbrl/companyfacts.zip`
  - `https://www.sec.gov/Archives/edgar/daily-index/bulkdata/submissions.zip`
  - **Recommendation:** for a screener, download `companyfacts.zip` weekly (it is large: the ZIP itself was about **1.42 GB compressed** on 2026-11-10, Last-Modified 04:14 GMT **[VERIFIED 2026-11-10, fact-check HEAD request]**, and several times that uncompressed) and load it into DuckDB. Do not hit the API thousands of times.
- **Ticker to CIK map:** `https://www.sec.gov/files/company_tickers.json` (also `company_tickers_exchange.json`). `company_tickers.json` returned `200 application/json` with a declared User-Agent **[VERIFIED 2026-11-10, fact-check]**.
- **Fair access** (https://www.sec.gov/search-filings/edgar-search-assistance/accessing-edgar-data) **[VERIFIED 2026-11-10]**:
  - "Current max request rate: **10 requests/second**."
  - The SEC reserves the right to limit rates further.
  - **You must declare a User-Agent.** The sample is `User-Agent: Sample Company Name AdminContact@<sample company domain>.com`, plus `Accept-Encoding: gzip, deflate` and the `Host` header.
  - Implementation: `User-Agent: "FinTec Research Assistant your.email@example.com"`, stored in `.env` as `SEC_USER_AGENT`. Throttle to **at most 5 req/s** to stay well under the cap.
- **Data quirks to handle:**
  - Companies use different tags for the same concept (`Revenues` vs `RevenueFromContractWithCustomerExcludingAssessedTax` vs `SalesRevenueNet`). Maintain a **tag-fallback map** for each metric.
  - Facts are duplicated across filings: deduplicate on `(end, fp, form)` and prefer the latest `filed`.
  - Separate 10-K (`fp=FY`) from 10-Q values. Build TTM as the sum of the last 4 quarterly flows, or as FY plus YTD deltas.
  - Foreign filers use `ifrs-full`, and some only file 20-F or 40-F.
- **Helper library (optional):** `edgartools` **6.0.0** on PyPI **[VERIFIED version 2026-11-10]** wraps these APIs. Raw `httpx` is still recommended for the core path so you control the schema.

### 1.3 FRED API (St. Louis Fed)

- Docs: https://fred.stlouisfed.org/docs/api/fred/ **[VERIFIED page exists]**.
- **Auth:** a free API key is required. Calling without one returns `400 Bad Request. Variable api_key is not set.` **[VERIFIED 2026-11-10 via live call]**. Get a key at https://fred.stlouisfed.org/docs/api/api_key.html.
- **Rate limit:** **120 requests/minute**. The FRED errors page says: "429 Too Many Requests (Up to 120 requests per minute are allowed before being served a 429 error code." (https://fred.stlouisfed.org/docs/api/fred/errors.html) **[VERIFIED 2026-11-10, fact-check]**. The Terms of Use let the St. Louis Fed change or impose limits at any time without notice. Throttle to 1 req/s and cache daily.
- **Endpoints:** `https://api.stlouisfed.org/fred/series/observations?series_id=DGS10&api_key=...&file_type=json`, and `/fred/series`, `/fred/series/search`, `/fred/release/...`. The docs mention a "Version 2" API for bulk release observations **[VERIFIED mention]**.
- **Useful series IDs** [series IDs UNVERIFIED individually, all standard]:

  | Series | Description |
  |---|---|
  | `DGS3MO`, `DGS1`, `DGS2`, `DGS10` | Treasury constant-maturity yields |
  | `DTB3` | 3-month T-bill secondary market rate, **quoted on a discount basis**. This understates the investment yield, so do not compare it directly to an HYSA APY. Use `DGS3MO` (bond-equivalent) or the Treasury yield-curve CSV for APY comparisons. On 2026-11-09, DTB3 was 4.06 and DGS3MO was 4.24 **[VERIFIED 2026-11-10, fact-check]** |
  | `T10Y2Y`, `T10Y3M` | Yield-curve spreads |
  | `CPIAUCSL`, `CPILFESL` | CPI, core CPI |
  | `FEDFUNDS`, `DFF` | Fed funds rate |
  | `UNRATE` | Unemployment rate |
  | `SP500` | S&P 500 index level, **price only (no dividends)**. Licensing limits history to about 10 years: the CSV starts 2016-11-11 **[VERIFIED 2026-11-10, fact-check]**. Never use it for total-return comparisons |
  | `BAMLH0A0HYM2` | High-yield spread |
  | `MORTGAGE30US` | 30-year mortgage rate |
  | ~~`SAVNRNJ`~~ | **Discontinued.** The FRED weekly FDIC national savings, money-market and CD series (`SAVNRNJ`, `MMNRNJ`, `CD12NRNJ`) all stop at **2021-03-29** **[VERIFIED 2026-11-10, fact-check]**. Do not use them for current comparisons |

- **"Typical bank rate" benchmark:** use the FDIC National Rates page (https://www.fdic.gov/national-rates-and-rate-caps), which is updated monthly. It offers no CSV, so curate by hand. As of 2026-10-19 the FDIC listed savings at **0.38%**, money market at **0.63%** and the 12-month CD at **1.76%** **[VERIFIED 2026-11-10, fact-check]**. These numbers show how far a big-bank savings account lags a HYSA or T-bills.
- **Alternative client:** `pandas-datareader` **0.11.1** **[VERIFIED version]**. The keyless CSV path (`https://fred.stlouisfed.org/graph/fredgraph.csv?id=DGS10`) still works and returns `observation_date,DGS10` **[VERIFIED 2026-11-10, fact-check]**. Keep it as a fallback only.

### 1.4 US Treasury Fiscal Data API & TreasuryDirect

- **Fiscal Data API:** base `https://api.fiscaldata.treasury.gov/services/api/fiscal_service/`. **No key** is needed.
- Live call **[VERIFIED 2026-11-10]**: `v2/accounting/od/avg_interest_rates?sort=-record_date&page[size]=1` returned `record_date 2026-10-31, Treasury Bills, avg_interest_rate_amt 4.014`.
- Query params: `fields=`, `filter=record_date:gte:2025-01-01`, `sort=-record_date`, `page[size]=`, `page[number]=`, `format=json|csv`.
- Useful datasets (all three paths returned data **[VERIFIED 2026-11-10, fact-check]**):
  - `v2/accounting/od/avg_interest_rates`: average interest rates on *outstanding* Treasury securities. This is a debt-cost statistic, **not** the yield a new buyer gets. Do not show it as a "T-bill rate you can earn".
  - `v1/accounting/od/rates_of_exchange`: FX rates (quarterly)
  - `v2/accounting/od/debt_to_penny`: total debt
- **Daily Treasury par yield curve CSV** (no key): `https://home.treasury.gov/resource-center/data-chart-center/interest-rates/daily-treasury-rates.csv/{YEAR}/all?type=daily_treasury_yield_curve&field_tdr_date_value={YEAR}&page&_format=csv`. The header is `Date,"1 Mo","1.5 Month","2 Mo","3 Mo","4 Mo","6 Mo","1 Yr",...,"30 Yr"`. The 2026-11-10 row showed 3 Mo = 4.30 and 6 Mo = 4.38 **[VERIFIED 2026-11-10, fact-check]**. This is the best free source for "what can I earn on a T-bill today".
- **TreasuryDirect** (I bonds / EE bonds): no official public JSON API for current I-bond composite rates was found (none located).
  - Rates are set each May 1 and November 1. **As of 2026-11-10:** new I bonds issued 2026-11-01 to 2027-04-30 earn a **5.51% composite** (1.40% fixed + 2.04% semiannual inflation) **[VERIFIED 2026-11-10, fact-check, TreasuryDirect rates page]**. Older bonds keep their own fixed rate. For example, bonds bought May-Oct 2026 (0.90% fixed) earn 5.00% for this period.
  - **Recommendation:** store them in the knowledge base (`kb/savings/ibond_rates.json`) and update by hand twice a year, with a source URL (https://www.treasurydirect.gov/savings-bonds/i-bonds/i-bonds-interest-rates/) and an as-of date.
  - Rules **[VERIFIED 2026-11-10, fact-check, treasurydirect.gov]**:
    - Up to **$10,000 per SSN per calendar year**, electronic only.
    - **Paper I bonds (including the old extra $5,000 bought through a federal tax refund) ended on 1 January 2025.** "As of January 1, 2025, I bonds are only available electronically." Do not show the old $15K-per-year figure.
    - The bond cannot be redeemed for the first **12 months**, which makes it a poor fit for the liquid emergency-fund core.
    - Redeeming before 5 years forfeits the last 3 months of interest.
    - Interest is exempt from state and local tax. Federal tax can be deferred until redemption.

### 1.5 Finnhub (free tier)

- Base: `https://finnhub.io/api/v1/`. Auth is `?token=KEY` or header `X-Finnhub-Token`.
- **Free limit:** **60 API calls/minute** [UNVERIFIED: pricing page is JS-rendered and could not be parsed]. The extra cap is confirmed: Finnhub's rate-limit doc says "On top of all plan's limit, there is a 30 API calls/ second limit", and over-limit calls return HTTP 429 **[VERIFIED 2026-11-10, fact-check, finnhub.io/docs/api/rate-limit]**.
- Free endpoints commonly available [UNVERIFIED per endpoint]:

  | Endpoint | Data |
  |---|---|
  | `/quote` | Real-time quote |
  | `/stock/profile2` | Company profile |
  | `/stock/metric?metric=all` | Basic financials: P/E, margins, 52-week stats |
  | `/company-news` | Company news |
  | `/stock/recommendation` | Analyst recommendation trends |
  | `/stock/earnings` | Earnings surprises |
  | `/calendar/earnings` | Earnings calendar |
  | `/stock/insider-transactions` | Insider transactions |

  ETF holdings and price targets are typically **premium**.
- **Use for:** news feed, analyst recommendation trends ("what Wall Street says"), and the earnings calendar.

### 1.6 Alpha Vantage (free)

- Source: https://www.alphavantage.co/support/ **[VERIFIED 2026-11-10]**. The free tier covers "the majority of our datasets for **25 API requests per day**".
- Auth is `apikey=` (free key). Base: `https://www.alphavantage.co/query?function=...`.
- Functions: `TIME_SERIES_DAILY` (raw, unadjusted; `TIME_SERIES_DAILY_ADJUSTED` is labeled **Premium** in the docs **[VERIFIED 2026-11-10, fact-check]**, so the free tier cannot provide dividend-adjusted total returns), `OVERVIEW`, `INCOME_STATEMENT`, `BALANCE_SHEET`, `CASH_FLOW`, `EARNINGS`, `NEWS_SENTIMENT`, `TREASURY_YIELD`, `CPI`.
- **Use for:** last-resort fallback and spot checks only. 25 calls per day is too few for screening.

### 1.7 Financial Modeling Prep (free tier)

- The docs and pricing page returned 403 through the proxy. Free "Basic" plan details below are all **[UNVERIFIED]**:
  - About **250 calls/day**
  - US equities only
  - Limited history (about 5 years)
  - Many endpoints moved to paid "stable" API tiers in 2024-2025
- Base `https://financialmodelingprep.com/stable/...` (newer) or `/api/v3/...` (legacy), with `apikey=`.
- **Use for:** optional enrichment such as DCF reference values or ratios. Never make it a primary source.

### 1.8 Tiingo (free "Starter")

- Source: https://www.tiingo.com/about/pricing **[VERIFIED 2026-11-10]**: $0/month.

  | Limit | Starter (free) |
  |---|---|
  | Unique symbols per month | 500 |
  | Requests per hour | 50 |
  | Requests per day | 1,000 |
  | Bandwidth per month | 1 GB |
  | License | "Internal Use Only" |

- Endpoints [UNVERIFIED paths, long-standing]:
  - EOD prices: `https://api.tiingo.com/tiingo/daily/{ticker}/prices?startDate=2010-01-01&token=KEY` (includes `adjClose`, `divCash`, `splitFactor`)
  - Metadata: `/tiingo/daily/{ticker}`
  - News: `/tiingo/news` (may be restricted on the free plan)
- **Use as:** the official **fallback for adjusted daily prices** when yfinance fails. Its split and dividend data also cross-validates Yahoo's.

### 1.9 Stooq

- Historic CSV pattern: `https://stooq.com/q/d/l/?s=spy.us&i=d`.
- **Finding [VERIFIED 2026-11-10]:** the URL returned an HTML page with a JavaScript SHA-256 proof-of-work challenge ("This site requires JavaScript to verify your browser"), **not CSV**. Plain HTTP clients (and `pandas-datareader`'s Stooq reader) will likely fail now.
- **Recommendation:** **drop Stooq from the automated chain.** At most, offer a "manual CSV import" button that accepts files you download in your browser.

### 1.10 Kenneth French Data Library (factor returns)

- Page: https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html **[VERIFIED reachable, file list scraped 2026-11-10]**.
- Direct ZIP files (prefix `https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/ftp/`):
  - `F-F_Research_Data_5_Factors_2x3_CSV.zip` (Mkt-RF, SMB, HML, RMW, CMA, RF; monthly and annual) **[VERIFIED HTTP 200]**
  - `F-F_Research_Data_5_Factors_2x3_daily_CSV.zip`, `F-F_Research_Data_Factors_CSV.zip` (3-factor) **[VERIFIED HTTP 200, 2026-11-10 fact-check]**
  - `F-F_Momentum_Factor_CSV.zip` and `_daily_CSV.zip` **[VERIFIED in listing]**
  - `F-F_LT_Reversal_Factor_CSV.zip` **[VERIFIED in listing]**
  - Industry portfolios (`10_`, `12_`, `17_`, `30_`, `38_Industry_Portfolios_CSV.zip`) **[VERIFIED in listing]**
  - Developed and international factor files: `Developed_5_Factors_CSV.zip`, `Developed_ex_US_5_Factors_CSV.zip`, `Emerging_5_Factors_CSV.zip` **[VERIFIED HTTP 200, 2026-11-10 fact-check]**. Use `Developed_ex_US` for VXUS/AVDV-type funds.
- **Format:** each CSV has header text, then a monthly block (`YYYYMM`), then an annual block. Values are **percent** (divide by 100), and `-99.99`/`-999` mean missing. Write a robust parser that splits on blank lines and detects the first column's width (6 = monthly, 4 = annual, 8 = daily).
- **Methodology citations:**
  - Fama, E. F., & French, K. R. (1993). Common risk factors in the returns on stocks and bonds. *Journal of Financial Economics*, 33(1), 3-56. https://doi.org/10.1016/0304-405X(93)90023-5 **[VERIFIED via OpenAlex]**
  - Fama, E. F., & French, K. R. (2015). A five-factor asset pricing model. *Journal of Financial Economics*, 116(1), 1-22. https://doi.org/10.1016/j.jfineco.2014.10.010 **[VERIFIED; online 2014]**
  - Carhart, M. M. (1997). On persistence in mutual fund performance. *Journal of Finance*, 52(1), 57-82. https://doi.org/10.1111/j.1540-6261.1997.tb03808.x **[VERIFIED]** (momentum factor model)
  - All DOIs in this document were re-resolved through OpenAlex on 2026-11-10 and matched on authors, journal, volume, issue and first page **[VERIFIED, fact-check]**. One caution: OpenAlex lists Harvey-Liu-Zhu's third author as "Caroline Zhu". The correct name is **Heqing Zhu**, so do not auto-import author names from OpenAlex without review.
  - **Evidence caveat for the KB:** the papers above document *historical* premiums. Post-publication, the value premium (HML) had a long weak stretch in US large caps (roughly 2007-2020). Factor ETFs also earn less than paper factors because of costs, long-only construction and implementation. Present factor tilts as "expected, uncertain, possibly decades-long underperformance", never as a reliable edge.
- **Terms:** freely available. Cite the source. Re-hosting is not explicitly licensed, so download at runtime and cache locally [UNVERIFIED license text].

### 1.11 AQR datasets

- Index: https://www.aqr.com/Insights/Datasets **[VERIFIED reachable]**. Datasets found 2026-11-10 (XLSX downloads from each page):
  - **Quality Minus Junk**: Factors Daily/Monthly, 10 quality-sorted portfolios, 6 size×quality portfolios. Asness, C. S., Frazzini, A., & Pedersen, L. H. (2019). Quality minus junk. *Review of Accounting Studies*, 24, 34-112. https://doi.org/10.1007/s11142-018-9470-2 **[VERIFIED; online 2018]**
  - **Betting Against Beta**: Equity Factors Daily/Monthly. Frazzini, A., & Pedersen, L. H. (2014). Betting against beta. *Journal of Financial Economics*, 111(1), 1-25. https://doi.org/10.1016/j.jfineco.2013.10.005 **[VERIFIED]**
  - **The Devil in HML's Details**: Factors Daily/Monthly. Asness, C., & Frazzini, A. (2013). *Journal of Portfolio Management*, 39(4), 49-68. https://doi.org/10.3905/jpm.2013.39.4.049 **[VERIFIED]**
  - **Value and Momentum Everywhere**: Factors/Portfolios Monthly. Asness, C. S., Moskowitz, T. J., & Pedersen, L. H. (2013). *Journal of Finance*, 68(3), 929-985. https://doi.org/10.1111/jofi.12021 **[VERIFIED]**
  - **Momentum Indices Monthly**
- **Format:** Excel with multiple sheets, a header offset of about 18 rows [UNVERIFIED], and decimal returns. Parse with `pandas.read_excel(..., sheet_name=None)`, then locate the header row by searching for "DATE".
- **Terms:** AQR's site terms require attribution and allow non-commercial use [UNVERIFIED]. Download into the local cache only.

### 1.12 ETF holdings: issuer files

All issuer endpoints are **undocumented, unofficial and fragile**. Isolate each one behind an `EtfHoldingsSource` adapter with a schema test.

- **iShares (BlackRock):** pattern `https://www.ishares.com/us/products/{productId}/{slug}/1467271812596.ajax?fileType=csv&fileName={TICKER}_holdings&dataType=fund`.
  - Live test for IVV **[VERIFIED 2026-11-10]**: returned `200 text/csv` whose body was **an HTML page**, probably a disclaimer, cookie or bot gate.
  - Treat it as unreliable. The CSV also has about 9 preamble lines before the header [UNVERIFIED].
- **SPDR (SSGA):** `https://www.ssga.com/us/en/intermediary/library-content/products/fund-data/etfs/us/holdings-daily-us-en-{ticker}.xlsx`. Returned **301** (redirect) in testing **[VERIFIED status]**. Follow the redirect with `httpx(follow_redirects=True)`. XLSX with about 4 header rows [UNVERIFIED].
- **Vanguard:** holdings come from a JSON API used by the investor site (`https://investor.vanguard.com/...` / `eds.ecs.gtm.vanguard.com`) [UNVERIFIED, changes often]. Vanguard publishes full holdings monthly with a lag.
- **Avantis / Dimensional / Schwab / Invesco:** each has a downloadable CSV/XLSX on its product page [UNVERIFIED URLs].
- **Authoritative fallback: SEC Form N-PORT.** Funds file portfolio holdings quarterly. Public N-PORT-P exhibits and the quarterly "Form N-PORT data sets" (https://www.sec.gov/data-research/sec-markets-data/form-n-port-data-sets **[VERIFIED HTTP 200, 2026-11-10 fact-check]**; the old `/dera/data/...` URL returns 404) give holdings. Only the quarter-end month of each quarter is made public, about 60 days after the quarter ends, so holdings can be 2-5 months old.
  - Find the fund's series via `submissions/CIK{trust}.json`.
- **Practical recommendation:** for the 20-40 ETFs the user cares about (VTI, VOO, VXUS, AVUV, AVDV, QUAL, SCHD, VIG, DGRO, QQQM...), use a **"manual refresh" button** that tries the issuer URL. If that fails, accept a user-downloaded CSV. Store top holdings plus sector/country weights in DuckDB with an `as_of` date.
- Expense ratios and other static ETF metadata belong in a curated `kb/etfs/etf_universe.json`, verified by hand against the issuer pages and refreshed quarterly.

### 1.13 Recommended fallback chain (code-level)

```python
# src/fintec/data/prices.py
PRICE_CHAIN = [YFinancePriceSource(), TiingoPriceSource(), AlphaVantagePriceSource()]

def get_prices(tickers, start, end) -> pl.DataFrame:
    cached = cache.read_prices(tickers, start, end)
    missing = cached.missing_ranges()
    for src in PRICE_CHAIN:
        if not missing: break
        if not src.available():          # key present? quota left today?
            continue
        try:
            got = src.fetch(missing)      # tenacity-wrapped, rate-limited
            validate_prices(got)          # pydantic/pandera checks: no neg prices, monotonic dates
            cache.upsert_prices(got, source=src.name)
            missing = missing.minus(got)
        except SourceError as e:
            log.warning("source %s failed: %s", src.name, e)
    return cache.read_prices(tickers, start, end)  # always return from cache, with `source` column
```

**Cross-validation rule:**
- When two sources both have a ticker, compare adjusted closes. If they diverge by more than 1% on any day, flag the ticker in the UI ("data discrepancy: check splits/dividends").
- Fundamentals: EDGAR wins. yfinance and Finnhub values are shown as "provider estimate".

---

## 2. Research ingestion (papers & case studies)

**Goal:** support the curated knowledge base, not replace it. Each KB claim (for example "small-cap value has a premium, but it is noisy and time-varying") links to 1-5 papers with DOI, abstract, citation count and a plain-English summary written ahead of time. Runtime APIs only **refresh metadata** (citation counts, new related papers) and power a "find more research" search box.

### 2.1 OpenAlex (primary)

Source: https://help.openalex.org/how-to-use-the-api/rate-limits-and-authentication **[VERIFIED 2026-11-10]**

- **Big change (Nov 2026):** OpenAlex now uses a **dollar-denominated daily budget**.
  - A live keyless call returned the notice: *"No API key, so this request shares one free daily budget ($0.10) with everyone on your network's IP address. Get a free key with its own $1/day..."* **[VERIFIED]**
  - A free key gives "10x the keyless budget" ($1/day). It requires email verification. Unactivated keys return `403 key_not_activated`.
  - New accounts (created after 3 Nov 2026) are paced at $0.10 every 5 minutes until the first $1 is spent.
  - Budgets reset at midnight UTC.
  - Each call's cost appears in the `X-RateLimit-Credits-Used` header and in `meta`.
- **Rate limits:**

  | Caller | Limit |
  |---|---|
  | No key | 10 req/s |
  | Free key | 30 req/s |
  | Semantic search | 1 req/s |

  `per_page` max is 100. Basic paging stops at 10,000 results, and cursor paging needs a key.
- **Auth:** `api_key=...` query param or `Authorization: Bearer ...`.
- **Endpoints:**
  - Search: `GET https://api.openalex.org/works?search=quality+minus+junk&per_page=25&select=id,doi,title,publication_year,cited_by_count,primary_location,abstract_inverted_index`
  - By DOI: `GET https://api.openalex.org/works/doi:10.1007/s11142-018-9470-2` **[VERIFIED working]**
  - Filters: `filter=from_publication_year:2000,type:article`, plus topic filters
- Abstracts arrive as `abstract_inverted_index`. Rebuild them by sorting `(position, word)` pairs.
- Always use `select=` and `per_page=100` to save budget.

### 2.2 Semantic Scholar Academic Graph API

Source: https://www.semanticscholar.org/product/api **[VERIFIED 2026-11-10]**

- Unauthenticated: "rate-limited to 1000 requests per second **shared among all unauthenticated users**", and further throttled under load. A live keyless call returned **HTTP 429**. In practice, keyless use is unreliable.
- With a free key (request form, delivered by email): "introductory rate limit ... **1 RPS** on all endpoints". Send it as header `x-api-key`.
- Endpoints:
  - Search: `GET https://api.semanticscholar.org/graph/v1/paper/search?query=...&fields=title,year,venue,externalIds,abstract,tldr,citationCount,openAccessPdf`
  - By DOI: `GET /graph/v1/paper/DOI:10.1111/jofi.12021`
  - Batch: `POST /graph/v1/paper/batch` (up to about 500 IDs [UNVERIFIED])
  - Recommendations: `/recommendations/v1/papers/forpaper/{id}`
- **Unique value:** the `tldr` field (auto one-line summary) and citation context. Use it as a fallback for OpenAlex and for "related papers".

### 2.3 arXiv (q-fin)

- API: `https://export.arxiv.org/api/query?search_query=cat:q-fin.PM&sortBy=submittedDate&max_results=50`. Returns an Atom feed **[VERIFIED 2026-11-10; the http:// form redirects 301 to https]**.
- Relevant categories: `q-fin.PM` (portfolio management), `q-fin.ST` (statistical finance), `q-fin.GN` (general), `q-fin.RM` (risk management).
- Terms of use: "make no more than one request every three seconds" and "limit requests to a single connection at a time". The limits apply to all machines you control combined (https://info.arxiv.org/help/api/tou.html) **[VERIFIED 2026-11-10, fact-check]**.
- Parse with `feedparser`.
- **Caveat:** arXiv preprints are **not peer-reviewed**. The KB must label them "preprint" and weight them below journal articles.

### 2.4 SSRN

- SSRN (Elsevier) has **no public API**. Its terms prohibit automated scraping and bulk downloading [UNVERIFIED exact clause].
- Many practitioner finance papers appear on SSRN first.
- **Recommendation:** **do not scrape SSRN.**
  - Store hand-curated SSRN links (`https://papers.ssrn.com/sol3/papers.cfm?abstract_id=NNNNNN`) in the KB.
  - Fetch metadata through OpenAlex or Semantic Scholar, which index many SSRN papers via DOIs of the form `10.2139/ssrn.NNNNNN` [UNVERIFIED coverage].

### 2.5 CORE (open-access aggregator)

- API v3: `https://api.core.ac.uk/v3/search/works?q=...`. Docs at https://api.core.ac.uk/docs/v3.
- Rate limit per https://core.ac.uk/services/api **[VERIFIED 2026-11-10]**: "One batch request or five single requests per 10 seconds". "Free API access without registration (subject to our T&Cs)". "Better performance for registered users".
- A keyless probe returned 429. **Register for a free key** (Bearer token).
- **Use for:** full text of open-access PDFs. Low priority.

### 2.6 Research pipeline design

1. **Offline (Claude-curated, shipped):** `kb/research/papers.json` holds one record per paper:
   - `doi`, `title`, `authors`, `year`, `journal`, `peer_reviewed: bool`
   - `finding_summary`, `practical_implication`
   - `sample_period`, `markets`, `replication_notes`, `criticisms`
   - `kb_topics: ["factor-value","factor-quality",...]`
2. **Runtime refresh:** a weekly job calls OpenAlex `works/doi:` for each DOI and updates `cited_by_count` and the open-access URL. Cost is about 100 calls per refresh.
3. **Discovery:** the "Find more research" box calls OpenAlex search, then Semantic Scholar for the `tldr`. Results are marked **"not reviewed by KB"** until you promote them.
4. **Credibility guardrails** that every factor page should show:
   - **Multiple-testing / p-hacking risk:** Harvey, C. R., Liu, Y., & Zhu, H. (2016). "...and the Cross-Section of Expected Returns". *Review of Financial Studies*, 29(1), 5-68. https://doi.org/10.1093/rfs/hhv059 **[VERIFIED via OpenAlex]**
   - **Post-publication decay:** McLean, R. D., & Pontiff, J. (2016). Does academic research destroy stock return predictability? *Journal of Finance*, 71(1), 5-32. https://doi.org/10.1111/jofi.12365 **[VERIFIED via OpenAlex]**
   - **Regression standard errors:** Newey, W. K., & West, K. D. (1987). A simple, positive semi-definite, heteroskedasticity and autocorrelation consistent covariance matrix. *Econometrica*, 55(3), 703-708. https://doi.org/10.2307/1913610 **[VERIFIED via OpenAlex]**

---

## 3. Creator content ingestion

### 3.1 Channel IDs (verified via live RSS feeds, 2026-11-10)

Every ID below was verified by fetching `https://www.youtube.com/feeds/videos.xml?channel_id=<ID>` and checking the feed `<title>`. Handles were checked through the `externalId` on the channel page. **Fact-check re-run 2026-11-10:** all 8 IDs match their handle pages' `externalId`. However, the RSS endpoint was **flaky**: several valid feeds returned intermittent HTTP 404/500 on repeated tries. The RSS poller must retry with backoff and must never treat one 404 as "channel deleted".

| Creator | Channel (feed title) | Handle URL | Channel ID |
|---|---|---|---|
| Ben Felix | Ben Felix | https://www.youtube.com/@BenFelixCSI | `UCDXTQ8nWmx_EhZ2v-kp7QxA` |
| Richard Coffin | The Plain Bagel | https://www.youtube.com/@ThePlainBagel | `UCFCEuCsyWP0YkP3CZ3Mr01Q` |
| Marko | Marko - WhiteBoard Finance | https://www.youtube.com/@WhiteboardFinance | `UCL_v4tC26PvOFytV1_eEVSg` |
| Graham Stephan | Graham Stephan | https://www.youtube.com/@GrahamStephan | `UCV6KDgJskWaEckne5aPA0aQ` |
| Andrei Jikh | Andrei Jikh | https://www.youtube.com/@AndreiJikh | `UCGy7SkBjcIAgTiwkXEtPnYg` |
| Sven Carlin | Value Investing with Sven Carlin, Ph.D. | https://www.youtube.com/@Value-Investing | `UCrTTBSUr0zhPU56UQljag5A` |
| Joseph Carlson | Joseph Carlson | https://www.youtube.com/@JosephCarlsonShow | `UCbta0n8i6Rljh0obO7HzG9A` |
| Mark Tilbury | Mark Tilbury | https://www.youtube.com/@MarkTilbury | `UCxgAuX3XZROujMmGphN_scA` |

RSS pattern: `https://www.youtube.com/feeds/videos.xml?channel_id={ID}`

**Warnings and notes:**

- **Sven Carlin impersonator risk:** a second channel titled "Sven Carlin" (`UC67NIxAks8wXAmGPoyY7r5A`, handle `@value.investing-SV`, created 2020) also appeared in YouTube search. Its titles ("LISTEN JUST LISTEN. IF YOU CARE ABOUT THE STOCKS YOU'RE HOLDING !!!") do not match the official channel. **Treat it as unofficial and do not ingest it.** The official channel (`UCrTTBSUr0zhPU56UQljag5A`) dates from 2017 and posted "Housing Stocks Look Interesting - Lennar, NVR, Weyerhaeuser..." on 2026-11-10. Hard-code an **allowlist of channel IDs**, because finance impersonation scams are common.
- Joseph Carlson has a second channel, "Joseph Carlson After Hours": handle `@JosephCarlsonAfterHours`, channel ID **`UCfCT7SSFEWyG4th9ZmaGYqQ`**. The handle page `externalId` and `vanityChannelUrl` match **[VERIFIED 2026-11-10, fact-check; its RSS feed returned intermittent 404/500 during the check]**. It is optional, lower-signal content.
- The RSS feed returns the **15** most recent uploads. Feeds that loaded returned exactly 15 `<entry>` items **[VERIFIED 2026-11-10, fact-check]**. For back catalogs, use `yt-dlp --flat-playlist` or the Data API.

### 3.2 Rational Reminder podcast (Ben Felix & Cameron Passmore)

- RSS: `https://rationalreminder.libsyn.com/rss`. This 302-redirects to `https://rss.libsyn.com/shows/127327/destinations/763774.xml`. Feed title is "The Rational Reminder Podcast" **[VERIFIED 2026-11-10]**.
- Show notes usually link the papers discussed, which makes a strong seed list for the research KB. Extract DOIs and SSRN links with a regex.

### 3.3 Tools

- **RSS (primary, no key, sanctioned):** `feedparser.parse(url)`. Entries expose `yt_videoid`, `title`, `published`, `link` [UNVERIFIED exact attribute names; inspect once].
- **yt-dlp** (latest **2026.10.26** **[VERIFIED PyPI]**): metadata only, never media.
  ```bash
  yt-dlp --flat-playlist --dump-json "https://www.youtube.com/channel/UCDXTQ8nWmx_EhZ2v-kp7QxA/videos" > ben_felix.jsonl
  ```
  From Python: `yt_dlp.YoutubeDL({"extract_flat": True, "skip_download": True, "quiet": True})`.
- **youtube-transcript-api** (latest **1.2.5** **[VERIFIED PyPI]**). The 1.x API is instance-based **[VERIFIED README]**:
  ```python
  from youtube_transcript_api import YouTubeTranscriptApi
  ytt = YouTubeTranscriptApi()
  fetched = ytt.fetch(video_id, languages=["en"])  # FetchedTranscript -> snippets (.text, .start, .duration)
  ```
  - The README warns: "YouTube has started blocking most IPs that are known to belong to cloud providers... you will most likely run into `RequestBlocked` or `IpBlocked` exceptions". This "can happen to the IP of your self-hosted solution, if you are doing too many requests" **[VERIFIED]**. The suggested workaround is paid rotating residential proxies, which **violates the $0 budget**. Therefore:
    - Run transcripts locally only, a few per hour at most, cached forever.
    - On `RequestBlocked`, back off for 24 hours.
    - Focus on curated "canonical" videos, not whole catalogs.
- **YouTube Data API v3** (optional, free Google key). **Corrected per the current quota doc** (https://developers.google.com/youtube/v3/determine_quota_cost) **[VERIFIED 2026-11-10, fact-check]**:
  - Projects get "100 search.list calls, 100 videos.insert calls, and 10,000 units per day combined for all other endpoints".
  - `search.list` now has its **own bucket of 100 calls/day**. It is no longer "100 units per call". Each extra results page counts as another call.
  - `playlistItems.list`, `videos.list` and `channels.list` cost 1 unit each.
  - Quotas reset at midnight Pacific Time.
  - Use `playlistItems.list` on the uploads playlist (the channel ID with `UC` replaced by `UU`) and avoid `search.list`. This is the most ToS-clean route to the full upload history.

### 3.4 Legal / ToS considerations (not legal advice)

- **YouTube Terms of Service** restrict automated access other than through public search engines or with permission [UNVERIFIED exact wording; https://www.youtube.com/t/terms]. RSS and the official Data API are sanctioned. yt-dlp and transcript scraping are a **gray area**: acceptable risk for low-volume personal research, not for a public or distributed product.
- **Copyright:** transcripts are the creators' copyrighted works.
  - Ship only **paraphrased summaries, key claims, timestamps and links** in the KB. Keep raw transcripts in the git-ignored `data/cache/transcripts/`. Never redistribute them.
  - Quote only brief excerpts with attribution.
- **UI attribution:** every creator claim shows the creator, video title, publish date and a timestamped URL (`&t=123s`).
- **Conflicts of interest:** creators often have sponsors, affiliate links (brokerages, HYSAs, credit cards), courses or paid communities. Store `sponsor_mentions` parsed from descriptions and show "creator may earn from linked products".

### 3.5 Creator ingestion pipeline

1. A weekly job polls the 8 YouTube RSS feeds plus Rational Reminder, then upserts `videos(video_id, channel_id, title, published, description, url)`.
2. A rule-based topic tagger assigns keyword topics (ETF, index, factor, dividend, valuation, HYSA, Roth, 401k, market-timing, crypto...).
3. **Optional:** fetch the transcript, then summarize it with the LLM (if enabled) or queue it for manual curation.
4. **Curated stances** live in `kb/creators/{slug}.json`, one record per topic: `stance_summary`, `evidence_links`, `date`, `consensus_with_research: agree|mixed|disagree`. The UI view "What the experts say about X" puts creator stances next to the research verdict.

---

## 4. Optional LLM layer

**Hard requirement:** the app works fully with `LLM_PROVIDER=none`. The LLM is used only for:
- summarizing transcripts and papers into **draft** KB entries (a human then reviews them),
- natural-language Q&A over the KB through retrieval (answers must cite KB entries),
- plain-English explanations of computed metrics.

**It must never:**
- produce prices, ratios or rates (those come only from the data layer),
- issue buy or sell recommendations on its own. Scores come from deterministic rules in the KB.

### 4.1 OpenRouter free models

- The research machine's proxy blocked openrouter.ai with 403, so everything in this subsection is **[UNVERIFIED in this session]**, based on OpenRouter docs as of early-to-mid 2026. Re-check https://openrouter.ai/docs/api-reference/limits.
- **Base URL:** `https://openrouter.ai/api/v1`. It is OpenAI-compatible (`/chat/completions`, `/models`). Auth is `Authorization: Bearer $OPENROUTER_API_KEY`. Optional headers: `HTTP-Referer` and `X-Title` for app attribution.
- **Free models:** the model ID ends in `:free`. IDs such as `meta-llama/llama-3.3-70b-instruct:free` and `deepseek/deepseek-chat-v3-0324:free` are **illustrative only and may no longer exist** [UNVERIFIED]. The free roster rotates often.
  - **Do not hard-code model IDs.** At startup, `GET /api/v1/models` and keep models where `id.endswith(":free")` or where `pricing.prompt == "0"` and `pricing.completion == "0"`.
  - Rank them by `context_length`, then by a user preference list in `config.toml`.
  - Cache the list for 24 hours.
- **Rate limits** (documented as of 2025-2026):
  - Free models allow about **20 requests/minute**.
  - Daily cap of about **50 free-model requests/day** if you have bought less than $10 in credits lifetime, and about **1,000/day** once you have bought at least $10. The second option is not $0, so assume 50/day.
  - Failed attempts may count toward the quota.
  - Check remaining quota with `GET /api/v1/key` (formerly `/auth/key`).
  - Free models may log prompts or train on them, depending on the provider. **Never send account numbers, balances or PII.**
- **Implications for the design:**
  - Batch summarization offline, at about 40 per day.
  - Cache every completion by `sha256(model + prompt)`.
  - Show remaining quota in the sidebar.
  - When rate-limited (HTTP 429), fall back to Ollama, then to `none`.

### 4.2 opencode integration

- opencode's site (https://opencode.ai) was blocked by the proxy. The fact-check verified it through GitHub instead. **Correction:** the repo now lives at **`github.com/anomalyco/opencode`** (`sst/opencode` redirects there).
- It is a **developer tool**, not a runtime LLM API. Two practical uses:
  1. **Dev-time KB curation:** run opencode, configured with an OpenRouter free model or Ollama, inside the repo. Use it to draft or refresh `kb/` files from cached transcripts and papers, then review the diffs in git.
  2. **Headless mode** **[VERIFIED 2026-11-10, fact-check, repo docs `services/site/src/docs/v1/content/{cli,server}.mdx`]**:
     - `opencode run "<prompt>"` runs a prompt non-interactively.
     - `opencode serve [--port <number>] [--hostname <string>]` "runs a headless HTTP server that exposes an OpenAPI endpoint". The default port is 4096. It supports optional HTTP basic auth via `OPENCODE_SERVER_PASSWORD`.
     - You could add an `OpencodeProvider` that shells out to it or calls the server, but the CLI is changing fast (a v2 installer exists). Treat it as optional and experimental.

### 4.3 Local fallback: Ollama

- Ollama exposes a native API at `http://localhost:11434/api/chat` and an **OpenAI-compatible endpoint at `http://localhost:11434/v1/`**. The client "requires an API key value, but Ollama ignores it" **[VERIFIED 2026-11-10, fact-check, ollama repo `docs/api/openai-compatibility.mdx`]**. Ollama also offers a hosted `https://ollama.com/v1` with "cloud models" that need an `OLLAMA_API_KEY` and an account. **Do not use cloud models for portfolio data**: the privacy guarantee below applies only to local models.
- Suggested models for a typical laptop (8-16 GB RAM): `llama3.2:3b`, `qwen2.5:7b`, `gemma2:9b` (quantized) [UNVERIFIED current tags].
  - Use them for summaries and explanations, not for complex reasoning.
  - Embeddings: `nomic-embed-text`, for KB semantic search [UNVERIFIED tag].
- **Fully offline, no rate limits, no data leakage.** This is the preferred option for anything touching personal portfolio data.

### 4.4 Provider abstraction

Because OpenRouter and Ollama are both OpenAI-compatible, one client class with different `base_url`s covers both. The `openai` Python SDK is at **3.49.0** on PyPI **[VERIFIED version]**. Alternatively use raw `httpx` to avoid SDK churn.

```python
# src/fintec/llm/base.py
from typing import Protocol
from pydantic import BaseModel

class LLMMessage(BaseModel):
    role: str          # "system" | "user" | "assistant"
    content: str

class LLMResult(BaseModel):
    text: str
    model: str
    provider: str
    cached: bool = False

class LLMProvider(Protocol):
    name: str
    def available(self) -> bool: ...
    def complete(self, messages: list[LLMMessage], *, max_tokens: int = 800,
                 temperature: float = 0.2) -> LLMResult: ...

# src/fintec/llm/openai_compat.py
import httpx
from tenacity import retry, stop_after_attempt, wait_exponential, retry_if_exception_type

class RateLimited(Exception): ...

class OpenAICompatProvider:
    def __init__(self, name: str, base_url: str, api_key: str | None, model: str):
        self.name, self.model = name, model
        headers = {"Authorization": f"Bearer {api_key or 'none'}"}
        self.client = httpx.Client(base_url=base_url, headers=headers, timeout=60)

    def available(self) -> bool:
        try:
            return self.client.get("/models").status_code == 200
        except httpx.HTTPError:
            return False

    @retry(stop=stop_after_attempt(3), wait=wait_exponential(min=2, max=30),
           retry=retry_if_exception_type(httpx.TransportError))
    def complete(self, messages, *, max_tokens=800, temperature=0.2):
        r = self.client.post("/chat/completions", json={
            "model": self.model, "messages": [m.model_dump() for m in messages],
            "max_tokens": max_tokens, "temperature": temperature})
        if r.status_code == 429:
            raise RateLimited(r.text)
        r.raise_for_status()
        return LLMResult(text=r.json()["choices"][0]["message"]["content"],
                         model=self.model, provider=self.name)

# src/fintec/llm/null.py
class NullProvider:
    name = "none"
    def available(self): return True
    def complete(self, messages, **kw):
        return LLMResult(text="(LLM disabled - showing curated KB content only)",
                         model="none", provider="none")

# src/fintec/llm/router.py
class LLMRouter:
    """Tries providers in order (e.g. openrouter -> ollama -> none), caches by prompt hash."""
    def __init__(self, providers: list[LLMProvider], cache): ...
```

- **Grounding pattern (RAG over the KB):**
  1. Retrieve the top-k KB chunks with DuckDB full-text search (`fts` extension), or with embeddings if Ollama is present.
  2. The system prompt says: *"Answer ONLY from the provided sources; cite source IDs; if the sources don't cover it, say so. Do not recommend specific trades."*
  3. Post-check: reject or flag answers that cite no `[S#]` IDs.

---

## 5. Python stack & project layout

### 5.1 Libraries

Versions are PyPI latest as of 2026-11-10 where verified. Otherwise pin to whatever you install.

| Concern | Choice | Notes |
|---|---|---|
| UI | **Streamlit** (1.67.0 **[VERIFIED]**) | Multipage via `st.navigation`/`pages/`. `st.cache_data(ttl=...)` for view-level caching. `st.secrets` or `.env` |
| DataFrames | **pandas** (ecosystem compatibility: yfinance, statsmodels) + **polars** (2.1.0 **[VERIFIED]**) for heavy screening | Convert with `pl.from_pandas` / `.to_pandas()` at the boundaries |
| Storage/cache | **DuckDB** (2.0.0 **[VERIFIED]**) | Single file `data/fintec.duckdb`. Reads Parquet/CSV/JSON directly. Fast analytics over the EDGAR companyfacts dump. Use SQLite only if you need many concurrent writers (you don't) |
| Validation | **pydantic v2** | Models for every external payload and every KB record. Optionally `pandera` for DataFrame schemas |
| Charts | **plotly** | `st.plotly_chart`. Drawdown, rolling returns, factor loadings bar chart with CIs |
| Stats | **statsmodels** | OLS factor regressions with Newey-West HAC errors |
| HTTP | **httpx** (sync client, connection pooling) | One shared client per source with headers and timeout |
| Retry | **tenacity** | Exponential backoff with jitter on 429/5xx/transport errors |
| Rate limit | Small token bucket of your own (about 20 lines) or `aiolimiter`/`pyrate-limiter` | Per-source limits from config |
| Scheduling | **APScheduler** `BackgroundScheduler` *or* OS scheduler (Windows Task Scheduler / cron) running `python -m fintec.jobs.refresh` | Prefer the OS scheduler plus a CLI: Streamlit reruns scripts, so in-process schedulers can double-start. If you use APScheduler, guard it with `st.cache_resource` |
| Feeds | **feedparser** | YouTube RSS, Rational Reminder, arXiv |
| Config/secrets | **python-dotenv** + **pydantic-settings** | `.env` (git-ignored) plus `.env.example` (committed) |
| Testing | **pytest**, `pytest-recording`/`vcrpy` (record HTTP fixtures), `hypothesis` (calculators), `freezegun` | Mark live-network tests `@pytest.mark.live`, skipped by default |
| Lint/format | **ruff**, **mypy** (or pyright) | |
| Env/packaging | **uv** + `pyproject.toml` | `uv run streamlit run app/Home.py` |

### 5.2 Factor regression: code-level recommendation

```python
import statsmodels.api as sm

def factor_regression(fund_ret: pd.Series, factors: pd.DataFrame, model: str = "FF5+MOM",
                      hac_lags: int = 6):
    """fund_ret: monthly simple returns (decimal). factors: French data / 100, incl. RF."""
    cols = {"CAPM": ["Mkt-RF"], "FF3": ["Mkt-RF", "SMB", "HML"],
            "FF5+MOM": ["Mkt-RF", "SMB", "HML", "RMW", "CMA", "Mom"]}[model]
    df = pd.concat([fund_ret.rename("R"), factors], axis=1, join="inner").dropna()
    y = df["R"] - df["RF"]
    X = sm.add_constant(df[cols])
    res = sm.OLS(y, X).fit(cov_type="HAC", cov_kwds={"maxlags": hac_lags})
    return {
        "alpha_annual": (1 + res.params["const"]) ** 12 - 1,
        "loadings": res.params.drop("const"),
        "t_stats": res.tvalues, "r2": res.rsquared, "n_months": int(res.nobs),
        "ci95": res.conf_int(),
    }
```

- Require at least 36 monthly observations and show `n_months` in the UI.
- Warn when R² is below 0.8 for a "broad index" ETF.
- Use the same frequency and dates for returns and factors. Convert prices to month-end total returns from adjusted closes.
- Use US factors for US funds. For international funds, use Developed or Emerging factors from the French library.
- The short-history problem applies to newer ETFs (e.g. AVUV since 2019): regressions over about 5 years have wide confidence intervals. Always plot the CIs.

### 5.3 Project layout

```
FinTec/
├─ pyproject.toml            # uv-managed; pinned deps
├─ .env.example              # SEC_USER_AGENT=, FRED_API_KEY=, TIINGO_API_KEY=, FINNHUB_API_KEY=,
│                            # ALPHAVANTAGE_API_KEY=, OPENALEX_API_KEY=, S2_API_KEY=, CORE_API_KEY=,
│                            # OPENROUTER_API_KEY=, LLM_PROVIDER=none|openrouter|ollama, OLLAMA_BASE_URL=
├─ config.toml               # non-secret: rate limits, TTLs, ticker universe, creator allowlist, model prefs
├─ app/                      # Streamlit UI only (thin)
│  ├─ Home.py
│  └─ pages/
│     ├─ 1_Savings_and_Emergency_Fund.py
│     ├─ 2_ETF_Explorer.py          # costs, holdings overlap, factor loadings
│     ├─ 3_Stock_Research.py        # EDGAR fundamentals, quality/value scores, checklist
│     ├─ 4_What_Experts_Say.py      # creator stances vs research
│     ├─ 5_Research_Library.py
│     ├─ 6_Portfolio_and_Accounts.py # 401k/IRA/taxable placement, allocation, rebalancing
│     └─ 7_Ask_the_KB.py            # optional LLM
├─ src/fintec/
│  ├─ settings.py            # pydantic-settings
│  ├─ http.py                # shared httpx clients, token buckets, tenacity policies
│  ├─ cache/                 # duckdb connection, migrations (sql files), upsert helpers
│  ├─ data/                  # one module per source: yf.py, edgar.py, fred.py, treasury.py, tiingo.py,
│  │                         # finnhub.py, alphavantage.py, fmp.py, french.py, aqr.py, etf_holdings/
│  ├─ research/              # openalex.py, s2.py, arxiv.py, core.py
│  ├─ creators/              # rss.py, ytdlp_meta.py, transcripts.py, tagger.py
│  ├─ analytics/             # returns.py, risk.py (drawdown, vol), factors.py, valuation.py,
│  │                         # quality.py, savings.py (EF sizing, HYSA vs T-bill after-tax yield)
│  ├─ kb/                    # loaders + pydantic models for kb files; retrieval (fts)
│  ├─ llm/                   # base.py, openai_compat.py, null.py, router.py, prompts/
│  └─ jobs/                  # refresh.py CLI: prices | fundamentals | macro | feeds | research
├─ kb/                       # SHIPPED curated knowledge base (versioned in git)
│  ├─ research/papers.json
│  ├─ creators/*.json
│  ├─ topics/*.md            # e.g. factor-investing.md, dividends-myths.md, hysa-vs-tbills.md
│  ├─ etfs/etf_universe.json
│  ├─ savings/ibond_rates.json, savings_options.md
│  └─ rules/scoring.yaml     # deterministic screens/checklists
├─ data/                     # git-ignored: fintec.duckdb, raw/ (zips), cache/transcripts/
├─ tests/                    # unit (offline, cassettes), live (marked), kb schema tests
└─ docs/research/            # this file and siblings
```

### 5.4 Caching strategy

| Data | Store | TTL / refresh | Key |
|---|---|---|---|
| Daily prices | DuckDB `prices(ticker, date, adj_close, close, volume, source, fetched_at)` | Incremental daily after US market close; full re-pull monthly to catch split/dividend re-adjustments | (ticker, date) |
| EDGAR companyfacts | Weekly bulk ZIP to Parquet to DuckDB `facts` | Weekly; per-company API refresh on demand if `filed` > last load | (cik, taxonomy, tag, unit, end, fp, form) |
| yfinance `.info` / Finnhub metrics | DuckDB JSON column | 7 days | (source, ticker) |
| FRED / Treasury | DuckDB `macro(series_id, date, value)` | Daily | (series_id, date) |
| French / AQR factors | Raw ZIP/XLSX in `data/raw/` + parsed Parquet | Monthly (French updates roughly monthly [UNVERIFIED]) | file name + Last-Modified/ETag |
| ETF holdings | DuckDB `etf_holdings(etf, as_of, holding, weight, ...)` | Manual / monthly | (etf, as_of, holding) |
| Papers metadata | DuckDB `papers` | Weekly | doi |
| Videos / feeds | DuckDB `videos` | Weekly (daily is fine for RSS) | video_id |
| Transcripts | Files `data/cache/transcripts/{video_id}.json` | Forever | video_id |
| LLM completions | DuckDB `llm_cache` | Forever (invalidate on prompt-template version bump) | sha256(provider, model, template_ver, prompt) |
| UI views | `st.cache_data(ttl=3600)` on pure functions reading DuckDB | 1 h | function args |

- Use **HTTP conditional requests** (`If-None-Match`/`If-Modified-Since`) for static files (French, AQR, SEC ZIPs).
- Every row stores `source` and `fetched_at`. The UI shows "as of" timestamps everywhere.
- **Stale-while-error:** if a refresh fails, keep serving cached data with a yellow "stale since X" badge. Never show a blank.

### 5.5 Secrets handling

- Keep `.env` in the project root, listed in `.gitignore`. Commit `.env.example` with empty values.
- Load with `pydantic-settings`, using `SecretStr` for keys so they never print in logs or `repr`.
  ```python
  class Settings(BaseSettings):
      model_config = SettingsConfigDict(env_file=".env", extra="ignore")
      sec_user_agent: str            # required: "Name email@example.com"
      fred_api_key: SecretStr | None = None
      tiingo_api_key: SecretStr | None = None
      finnhub_api_key: SecretStr | None = None
      openrouter_api_key: SecretStr | None = None
      llm_provider: Literal["none", "openrouter", "ollama"] = "none"
  ```
- Every source's `available()` returns False when its key is missing. The app degrades gracefully, and a sidebar "Data sources" panel shows the status of each one.
- No personal account credentials: **do not** integrate brokerage logins. Portfolio input is manual or a CSV export.
- Optionally run `pre-commit` with `detect-secrets` or `gitleaks`.

### 5.6 Rate-limit configuration (config.toml)

```toml
[rate_limits]            # requests per second unless noted; set conservatively below published caps
sec = 5                  # published cap 10/s [VERIFIED]
fred = 1                 # cap 120/min [VERIFIED errors page]
tiingo_per_hour = 45     # cap 50/h, 1000/day [VERIFIED]
finnhub_per_min = 50     # cap 60/min [UNVERIFIED]; 30/s all-plan cap [VERIFIED]
alphavantage_per_day = 20 # cap 25/day [VERIFIED]
openalex = 5             # cap 30/s with key, but budget-limited [VERIFIED]
semantic_scholar = 0.9   # 1 RPS with key [VERIFIED]
arxiv = 0.33             # 1 per 3 s, single connection [VERIFIED ToU]
youtube_search_per_day = 0   # search.list has its own 100 calls/day bucket; avoid it [VERIFIED]
core_per_10s = 4         # 5 single req / 10 s [VERIFIED]
yfinance = 1             # unpublished; be gentle
youtube_transcripts_per_hour = 6
openrouter_per_min = 15  # ~20/min, ~50/day [UNVERIFIED]
```

### 5.7 Testing strategy

- **Unit tests (offline):**
  - Parsers (French CSV blocks, AQR XLSX, EDGAR facts dedupe and TTM, iShares CSV preamble), each against fixture files in `tests/fixtures/`.
  - Calculators (CAGR, max drawdown, after-tax yield comparison, emergency-fund months), with property tests via `hypothesis`.
- **Contract tests:** recorded HTTP cassettes (`vcrpy`) for each source adapter. These validate pydantic models.
- **Live smoke tests:** `pytest -m live`, run weekly. They detect yfinance schema drift, issuer URL changes and API quota changes.
- **KB tests:**
  - Every `papers.json` record has a DOI or URL, `peer_reviewed` is set, and every creator stance links to a URL with a date.
  - No unverified claim without the `unverified: true` flag.
  - The channel-ID allowlist matches `kb/creators`.
- **Golden tests:** factor regression on a known fund (e.g. a US total-market ETF should have Mkt-RF ≈ 1.0, SMB ≈ 0, R² > 0.95) to catch data-alignment bugs.

---

## 6. Decision-safety notes for real-money use

1. **Single source of truth per number.** Each displayed metric names its source. Prefer EDGAR for fundamentals and FRED/Treasury for rates.
2. **No LLM-generated numbers.** The LLM only rephrases data the app has already computed.
3. **Every recommendation is a rule.** Screens and checklists live in `kb/rules/scoring.yaml`, are versioned and backed by citations. The UI shows *why* a stock or ETF passed or failed each criterion.
4. **Show uncertainty:** confidence intervals on factor loadings, sample periods, and post-publication decay warnings (McLean & Pontiff, 2016).
5. **Staleness badges** on every data panel, plus a manual "verify at source" link for expense ratios, HYSA APYs and I-bond rates. HYSA APYs have no free API, so curate them by hand with dates and verify on the bank's site before you open an account.
6. **Savings module inputs** (user-entered): monthly essential expenses, job stability, and other liquid assets. Emergency fund target = N months × expenses (N user-selectable, default 3-6). The tool then compares HYSA APY, T-bills, money-market funds, CDs and I-bonds on an **after-tax** basis, using the user's federal and state bracket.
   - **T-bill yields:** use the Treasury daily yield-curve CSV or FRED `DGS3MO`, not the discount-basis `DTB3`. T-bill interest is exempt from state and local tax.
   - **Treasury money-market funds:** their state-tax exemption is only partial and depends on the state. Some states (e.g. CA, NY, CT) exempt nothing unless the fund holds at least 50% US government obligations [UNVERIFIED per-state; check the fund's annual "% from US government obligations" tax letter]. Model it as a user-entered "% state-exempt" field.
   - **Liquidity tiers:** the emergency-fund core must stay same-day or next-day liquid (HYSA or MMF). I bonds are locked for 12 months, so they can only fill a "tier 2" bucket. CDs carry early-withdrawal penalties.
   - **Deposit safety:** HYSAs must be FDIC-insured (or NCUA for credit unions), up to $250,000 per depositor, per insured bank, per ownership category. Warn about fintech "partner bank" or sweep products, where insurance applies only once funds reach the partner bank.
   - **Benchmark:** show the FDIC national savings rate (0.38% as of 2026-10-19) next to the HYSA and T-bill yields.
7. **Account-placement reference numbers** (store in `kb/accounts/limits_{year}.json` with source URL; IRS release IR-2025-111, **[VERIFIED 2026-11-10, fact-check, irs.gov]**). For **2026**:
   - 401(k)/403(b)/TSP employee deferral: **$24,500**. The age-50+ catch-up is $8,000, and ages 60-63 get $11,250.
   - IRA: **$7,500**, with a $1,100 catch-up at 50+.
   - Roth IRA phase-out for single filers: **MAGI $153,000-$168,000**.
   - 2027 limits are usually announced in Oct-Nov. Re-check irs.gov before January.
   - Encode the standard "order of operations" as a rule, not as LLM output: capture the full employer match first, then build the emergency fund and pay off high-interest debt, then fund the IRA or HSA, then max the 401(k), then taxable. Cite the source for each step in the KB.

---

## 7. Open items to re-verify (summary of [UNVERIFIED])

Still unverified after the 2026-11-10 fact-check:

- OpenRouter free-model limits (about 20 RPM; 50/day under $10 of credits vs 1,000/day at $10 or more), the `/api/v1/key` endpoint, and the example `:free` model IDs. openrouter.ai still returns 403 from this network.
- Finnhub free-tier 60/min and the per-endpoint free/premium split. The 30/s cap *is* verified.
- FMP free plan (about 250/day, US-only, ~5-year history). Docs are still 403.
- Exact YouTube ToS wording. The quota numbers are now verified.
- iShares, SSGA and Vanguard holdings URL formats. iShares returned an HTML gate; SSGA returned a 301.
- Ollama model tags (`llama3.2:3b`, `qwen2.5:7b`, `gemma2:9b`, `nomic-embed-text`). Newer families exist (the docs show `gemma4`), so pick from `ollama list` / the library at install time.
- State tax treatment of Treasury money-market fund dividends (per state).

Resolved by the fact-check: FRED 120/min, arXiv 1 req/3 s, YouTube quota, the I-bond rules and current rate, the Treasury yield-curve CSV, the N-PORT URL, the French international file names, all 8 creator channel IDs plus Joseph Carlson After Hours (`UCfCT7SSFEWyG4th9ZmaGYqQ`), opencode `run`/`serve`, the Ollama `/v1` endpoint, and the 2026 IRS contribution limits. TreasuryDirect rates API: none found (cannot prove absence).
