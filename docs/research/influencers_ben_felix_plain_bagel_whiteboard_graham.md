# Creator Profiles: Ben Felix, The Plain Bagel, Whiteboard Finance, Graham Stephan

**Research date:** 2026-11-10
**Purpose:** Background knowledge base for the FinTec investment research assistant (local Python + Streamlit). This file describes what these four creators say, how well their views match the academic evidence, and how the app should store their views as data.
**Audience:** A US-based young professional who mostly holds ETFs, has a 10+ year horizon and moderate risk tolerance, and holds about $20K in savings.

---

## 0. Method, source quality, and caveats (read first)

### 0.1 How this was researched
- **Primary sources were used wherever possible.** These include YouTube video metadata and descriptions (titles, exact publish timestamps, timestamps/chapters, sponsor disclosures), the creators' own websites and newsletters, and the Rational Reminder podcast site, including episode transcripts.
- **Exact publish dates come from YouTube page metadata** (`publishDate`), which I pulled directly on 2026-11-10.
- **Limitations in this session:**
  - The general web-search tool was unavailable.
  - The corporate network proxy blocked Crossref/doi.org lookups, YouTube transcript downloads, and some sites (pwlcapital.com returned 403). *Update: OpenAlex, NBER, IRS, FRED and YouTube page metadata were reachable in the fact-check pass (§0.1a).*
  - So claims about what a creator **says inside a video** (rather than in its title/description) come from memory and are marked **[UNVERIFIED]** unless a transcript or description confirmed them.
  - **Paper DOIs** are standard references written from memory. Each one should be checked automatically during the app build (see section 7.4). Any DOI that could not be resolved here is marked *(DOI not machine-verified)*.
- **Positions change.** Every holding or recommendation below has a date. The app should treat anything older than about 12 months as stale.

### 0.1a Fact-check pass (2026-11-10)
An adversarial fact-check was run after drafting. Crossref and doi.org were still blocked, so DOIs were checked against **OpenAlex** (api.openalex.org), which returns Crossref-derived metadata.
- **Three DOIs written from memory were wrong** and pointed to unrelated papers. They are now corrected:
  - Bessembinder 2018 → `10.1016/j.jfineco.2018.06.004`
  - Anarkulova et al. → `10.1016/j.jfineco.2021.06.040`
  - Israelov & Nielsen → `10.2469/faj.v71.n6.1`
- **All other DOIs in §7.4 resolved to the correct title, authors, volume, and pages.**
- **Other sources checked:**
  - Creator claims: YouTube descriptions and publish dates (fetched directly), the Rational Reminder Ep. 434 transcript, Graham's Substack posts, and the Plain Bagel FAQ
  - Rates: FRED series (DGS10, MORTGAGE30US, CPIAUCNS, DFF, DTB3)
  - Contribution limits: IRS pages
- **Corrections are noted inline as "(fact-check: …)".**

### 0.2 Key verified facts at a glance
| Creator | Verified credential/role (as of 2026) | Main conflict-of-interest vector |
|---|---|---|
| Ben Felix | CFA (2016), CFP (2017), MBA (Carleton 2014), BS Mech. Eng. (Northeastern 2011); **Chief Investment Officer, PWL Capital** (owned by OneDigital since about Jan 2025) | Employee and equity holder of the firm whose services his videos promote; PWL uses Dimensional (DFA) funds |
| Richard Coffin (The Plain Bagel) | CFA charterholder; BComm Finance (Carleton); **registered portfolio manager at WDS Investment Management** (Ottawa) | Channel sponsorships (Squarespace, Blinkist, Morning Brew, etc.) and Patreon; NFT collection on site. **No stock-pick videos by policy** |
| Marko Zlatic (Whiteboard Finance) | Finance degree, University of Akron; former finance roles incl. KeyBank (per third-party bio). His own disclaimer says **"I am not a financial adviser."** No CFP found | Heavy sponsorships and affiliate links (Webull, Aven, Nexo, Policygenius, Seeking Alpha); paid membership (WBFU, $10/mo); **a paid small-cap stock promotion ($5,000, GRML, Oct 2026)** |
| Graham Stephan | Former licensed CA real-estate agent (2008 to 2020; sold $125M+); no investment credential | Very heavy sponsorships and affiliates (SoFi Crypto, Public, Webull, Acorns); past courses/mentorship; named in **FTX promoter class action (2023)**; promoted **Yotta** (froze in the 2024 Synapse collapse) |

### 0.3 Evidence-alignment rating scale used throughout
| Rating | Meaning |
|---|---|
| **A: Strong** | Consistent with a large body of peer-reviewed evidence or theory (e.g., low costs, diversification, most active managers underperform) |
| **B: Supported** | Supported, with real debate about magnitude or persistence (e.g., factor premiums) |
| **C: Mixed / context-dependent** | Fine for some people or situations; evidence is mixed or mostly behavioral |
| **D: Weak** | Popular but contrary to the bulk of evidence, or untested |
| **E: Contradicted** | Directly contradicted by theory or evidence (e.g., "dividends are free money") |

---

## 1. Ben Felix (PWL Capital / Rational Reminder / "Common Sense Investing")

### 1.1 Background and credentials
- **Current role:** Chief Investment Officer, PWL Capital (Ottawa). This is his self-introduction in Rational Reminder Ep. 434 "Breaking Down Our Investment Portfolios" (2026-11-05, https://rationalreminder.ca/podcast/434) and in the description of his YouTube video "The Truth About Leveraged ETFs" (2026-09-27, https://www.youtube.com/watch?v=E7pl0tqzIUQ). In 2018 he described himself as "Associate Portfolio Manager at PWL Capital" ("Should You Be Factor Investing?", 2018-08-31, https://www.youtube.com/watch?v=ViTnIebSzj4).
- **Education and designations** (from the Rational Reminder About page, https://rationalreminder.ca/about; verified 2026-11-10; note the About page still calls him "portfolio manager," which is out of date):
  - BSc Mechanical Engineering, Northeastern University (2011), where he played varsity basketball
  - MBA, Carleton University (2014)
  - CFA charter (2016)
  - CFP (2017)
- **Firm ownership:** PWL Capital was acquired by **OneDigital** (US), after nearly 30 years of employee ownership. This was announced in RR Ep. 341 "PWL's Next Chapter" (2025-01-23, https://rationalreminder.ca/podcast/341), which said PWL had about 100 staff and AUM "approaching $4 billion." Cameron Passmore became PWL CEO and stepped back from full-time co-hosting. By Ep. 434 (Nov 2026) Ben's co-host was **Dan Bortolotti**, PWL portfolio manager and "Canadian Couch Potato."
- **Platforms:**
  - YouTube channel "Ben Felix"
  - Rational Reminder podcast (430+ weekly episodes since 2018), community forum (https://community.rationalreminder.ca/)
  - PWL research tools (risk profile, rent-vs-buy, lifecycle portfolio choice "based on Choi et al.")
- **Canadian context:** Most of his content is aimed at Canadians (TFSA/RRSP, Canadian-listed ETFs, Canadian housing). US users have to translate it to 401(k)/IRA/HSA and US-listed funds. The app must flag this.

### 1.2 Core investment philosophy
Rational Reminder sums up its philosophy as "avoiding prediction, keeping costs low, and diversifying broadly" (About page). In detail:
1. **Markets are hard to beat. Price is the best estimate of value.** Most stock pickers lose to the market.
   - Video: "Why Most Stock Pickers Lose to the Market" (2018-12-15, https://www.youtube.com/watch?v=AecvTErBQY8)
   - Supporting evidence: Fama & French (2010), "Luck versus Skill in the Cross-Section of Mutual Fund Returns," *Journal of Finance* 65(5), 1915–1947, DOI 10.1111/j.1540-6261.2010.01598.x *(verified via OpenAlex)*; S&P SPIVA scorecards (https://www.spglobal.com/spdji/en/research-insights/spiva/).
2. **Individual stocks carry large uncompensated risk.**
   - Video: "The Risk of (Individual) Stocks" (2025-06-08, https://www.youtube.com/watch?v=RxCqxhRsHiY)
   - Podcast: RR #363 "The (Underappreciated) Risk of Individual Stocks" (2025-06-26)
   - Main evidence: Bessembinder (2018), "Do Stocks Outperform Treasury Bills?", *Journal of Financial Economics* 129(3), 440–457, DOI **10.1016/j.jfineco.2018.06.004** *(verified via OpenAlex; fact-check: the earlier DOI ending in 2018.03.007 belongs to an unrelated paper)*.
     - Findings for US CRSP common stocks, 1926 to 2016: most stocks (about 58%) had lifetime buy-and-hold returns below one-month T-bills.
     - The best-performing ~4% of listed companies accounted for all net stock-market wealth creation.
3. **Expected returns are explained by exposure to risk factors.** Market beta, size, value (relative price), and profitability are the main ones.
   - Video: "This Paper Could Change How You Invest" (2026-03-22, https://www.youtube.com/watch?v=N5v2b42i_2g), about Fama & French (1993), "Common Risk Factors in the Returns on Stocks and Bonds," *JFE* 33(1), 3–56, DOI 10.1016/0304-405X(93)90023-5 *(verified via OpenAlex)*. The video description says it has "nearly 15,000 citations."
   - Also: Fama & French (2015), "A Five-Factor Asset Pricing Model," *JFE* 116(1), DOI 10.1016/j.jfineco.2014.10.010; Novy-Marx (2013), "The Other Side of Value: The Gross Profitability Premium," *JFE* 108(1), DOI 10.1016/j.jfineco.2013.01.003 (Novy-Marx appeared on RR #149, 2021-05-13).
4. **Global diversification.** He supports modest home bias, not extreme home bias.
   - Videos: "International Diversification" (2023-03-15, https://www.youtube.com/watch?v=1FXuMs6YRCY); "Is Home Country Bias a Mistake?" (2024-02-21, https://www.youtube.com/watch?v=jN8mIHve1Ds). The description of the latter says home bias is "detrimental at the extremes, but modest home country bias is theoretically, practically, and empirically useful."
   - Related evidence: Anarkulova, Cederburg & O'Doherty (2022), "Stocks for the Long Run? Evidence from a Broad Sample of Developed Markets," *JFE* 143(1), 409–433 (published online 2021, January 2022 issue), DOI **10.1016/j.jfineco.2021.06.040** *(verified via OpenAlex; fact-check: the earlier DOI ending in .042 belongs to an unrelated paper on venture-capital contracts)*.
     - Data: 39 developed markets.
     - Finding: long-horizon real equity losses are far more likely than the US-only record suggests.
     - Caveat: the paper argues against "stocks are safe in the long run." It does not argue against holding stocks.
5. **Dividends are irrelevant to expected returns.**
   - Videos: "Dividend Growth Investing" (2019, https://www.youtube.com/watch?v=UpXI_Vd51dA); "The Irrelevance of Dividends" (2019-09-14, https://www.youtube.com/watch?v=f5j9v9dfinQ); "The Relevance of Dividend Irrelevance" (2022-06-09, https://www.youtube.com/watch?v=4iNOtVtNKuU)
   - Podcasts: RR #127 "Fooled by Dividends" (2020-12-03); RR #201 (2022-05-19)
   - Theory and evidence: Miller & Modigliani (1961), "Dividend Policy, Growth, and the Valuation of Shares," *Journal of Business* 34(4), DOI 10.1086/294442; Hartzmark & Solomon (2019), "The Dividend Disconnect," *Journal of Finance* 74(5), 2153–2199, DOI 10.1111/jofi.12785 *(verified via OpenAlex; MM 1961 DOI also verified)*.
6. **Covered-call income funds are a poor deal.** The 2025 description calls the income claim "financial bulls***."
   - Videos: "Covered Calls: The Income Illusion" (about 2023, https://www.youtube.com/watch?v=YMLVdY8y8vM); "Covered Calls: A Devil's Bargain" (2025-09-14, https://www.youtube.com/watch?v=ygVObRx9X68); "Was I Wrong About Covered Calls?" (2025-10-05, https://www.youtube.com/watch?v=xzDFbv_JSks)
   - Podcasts: RR #251 (2023-05-04), #375 (2025-09-18), #379 (2025-10-16)
   - Related evidence: Israelov & Nielsen (2015), "Covered Calls Uncovered," *Financial Analysts Journal* 71(6), 44–57, DOI **10.2469/faj.v71.n6.1** *(verified via OpenAlex; fact-check: the earlier DOI ending in n6.5 belongs to a different FAJ article)*.
     - What the paper shows: a covered call breaks down into equity beta, a short-volatility exposure (which earns the volatility risk premium), and an uncompensated short "equity timing" exposure.
     - What it does not show: it does **not** show that covered calls must underperform on a risk-adjusted basis.
     - The firmer point: distributions from these funds are not extra return.
7. **Cash is a bad long-term investment, even at 5%.** It is fine for liquidity.
   - Video: "Cash is a terrible long-term investment, even at 5% interest" (2023-08-29, https://www.youtube.com/watch?v=KdzOlRRHOU8). The description says cash "feels safe, but it is counterintuitively extremely risky for long-term investors" and cites "Reaching for Yield: Evidence from Households."
   - Podcasts: RR #265 "5% HISA... for the Long-run?" (2023-08-10); RR #277 "The Cash Wedge" (2023-11-02)
8. **Lifecycle thinking: maximize lifetime utility, not just savings.**
   - Video: "The Biggest Myths in Personal Finance" (2026-07-26, https://www.youtube.com/watch?v=3u-mP3C26mg). Myth #1 is "Saving as much as you can early"; the description argues it "ignores what economists call the life-cycle model." The same video lists as myths that "dividends explain 40% of market growth," that "index funds only give average returns," "CAPE is an omen," "bonds and cash are safe," "gold is an inflation hedge," and "renting is throwing away money."
   - Related tool: PWL "lifecycle portfolio choice" app, "based on Choi et al." This likely refers to James J. Choi, Canyao Liu & Pengcheng Liu, "Practical Finance: An Approximate Solution to Lifecycle Portfolio Choice," **NBER Working Paper w34166 (August 2025)** (verified on nber.org; fact-check: the earlier draft said 2022). It is a working paper and has not been peer reviewed. That the PWL app uses this exact paper is an inference **[UNVERIFIED]**.
9. **Leverage can be rational for young investors with high human capital. Leveraged ETFs are a costly, risky way to get it.**
   - Video: "The Truth About Leveraged ETFs" (2026-09-27, https://www.youtube.com/watch?v=E7pl0tqzIUQ). The description notes that $10K in UPRO at its 2009 launch would be worth about $1.3M (verified in the description). *Caveat (fact-check):* that start date is close to the March 2009 market bottom, so the figure is a cherry-picked backtest, not an expectation. The description then gives a framework covering "When Leverage Makes Sense," "The Risk," "Hidden Costs," and "Using Leverage Without Going All-In."
   - Podcasts: RR #430 (2026-10-08), #432 (2026-10-22), #397 with Bessembinder "Constant Leverage" (2026-02-19)
10. **Skeptical of hype.** He is critical of private markets and mega-IPOs.
    - Videos: "The Problem with Private Markets" (2026-03-08); "SpaceX and OpenAI: The Mega IPO Grift" (2026-04-05); "SpaceX IPO Situation is Crazy" (2026-06-11, https://www.youtube.com/watch?v=2tkXhYsGge0), which covers index-inclusion rules and retail IPO-access data
    - Podcasts: RR #402 (2026-03-26), #409 (2026-05-14)
    - On concentration: RR #390 "The 'AI Bubble' and Stock Market Concentration" (2026-01-01); RR #394 "Equal Weight vs. Market Cap Weight Index Funds" (2026-01-29)

### 1.3 Stance on ETFs vs. stock picking
- **Strongly pro broad index/factor funds and against stock picking** for nearly everyone (see the 2018-12-15 and 2025-06-08 videos above).
- He does not treat **plain cap-weighted total-market investing** and **systematic factor tilts** as rivals. Both are valid, and factor tilts are a modest way to increase expected return while reducing reliance on the US large-cap growth market.
  - Video: "Five Factor Investing with ETFs" (2021-03-27, https://www.youtube.com/watch?v=jKWbW7Wgm0w). The description links a caution titled "Why It Would Be Ludicrous To Invest In These Model Portfolios."
  - Video: "Dimensional (DFA) vs. Vanguard" (2025-03-02, https://www.youtube.com/watch?v=JfknibBat2A)
- Specific US-listed ETFs he has discussed for factor exposure (e.g., the Avantis AVUV/AVDV family) are **[UNVERIFIED as to exact video and date]**. A third-party clip titled "Is Small Cap Value Worth It? Ben Felix Explains the Truth About AVUV & Factor Investing" exists (about May 2026, https://www.youtube.com/watch?v=7v8phD7WiDU), but it is not his channel.

### 1.4 Favored frameworks and rules of thumb
| Framework | Source | Notes |
|---|---|---|
| Total market plus factor tilts (size, value, profitability) | RR #40 "Five Factor Thinking" (2019-04-04); RR #129 "Five Factor Investing with ETFs" (2020-12-17); video 2021-03-27 | Tilt, not concentrate. Expect long periods of underperformance (RR #291 "The Quant Winter," 2024-02-08) |
| **5% rule for rent vs. buy**: annual unrecoverable cost of owning ≈ 5% of home value (≈ 1% property tax + 1% maintenance + 3% cost of capital) | "Renting vs. Buying a Home: The 5% Rule" (2019-05-11, https://www.youtube.com/watch?v=Uwl3-jBNEd4) | The components are from memory **[UNVERIFIED breakdown]**. The PWL blog post (pwlcapital.com/rent-or-own-your-home-5-rule/) returned 403. Verified points: the description stresses "You can't compare rent to a mortgage payment," the video was "originally intended for a Canadian audience," and it was published 2019-05-11. **US caveat (fact-check):** US property tax ranges from about 0.3% to over 2% of value by state. The cost-of-capital term should reflect today's rates; the 30-year mortgage rate was 7.18% on 2026-11-05 (FRED MORTGAGE30US). So a fixed 5% may understate 2026 US ownership costs, and the app should compute each component from user inputs. Updated in "The Reckoning" (2026-02-22) and "The Case for Owning" (2026-07-12, https://www.youtube.com/watch?v=16eA5ebWMQQ) |
| Total-return thinking, not income | Dividend videos above | Sell shares ("homemade dividends") instead of chasing yield |
| Modest home bias is fine; extreme home bias is not | 2024-02-21 video | For US investors: hold meaningful ex-US exposure |
| Risk capacity vs. tolerance; human capital as a bond-like asset | Leveraged ETF video (2026); PWL risk-profile tool | Young professionals can usually take equity risk if they can behaviorally hold through drawdowns |
| Don't time the market; investing at all-time highs is normal | "The Truth About Investing at All-Time Highs" (2026-06-28) | |
| Expected returns are uncertain; use a range | RR #213 "Expected Returns and Factor Investing" (2022-08-11) | |

### 1.5 Savings, HYSA, emergency fund
- **Keep cash for liquidity and spending needs, not as a long-term investment.** See the 2023-08-29 video and RR #265 and #277.
- **His own cash:** About **5% of his liquid portfolio, held in a high-interest savings account.** He calls it "more cash than I probably need" (Ep. 434, 2026-11-05; video "Breaking Down My Investment Portfolio," 2026-10-18, https://www.youtube.com/watch?v=h3ajVyC5vJc). Co-host Dan Bortolotti keeps "a couple of months worth of expenses in a savings account" (Ep. 434).
- No specific "X months" emergency-fund rule from Felix himself was verified **[UNVERIFIED]**.

### 1.6 Specific holdings and recurring funds (dated)
| Date | Disclosure | Source |
|---|---|---|
| 2026-10-18 / 2026-11-05 | Most liquid long-term assets in **one fund: Dimensional Global Equity Portfolio (DFA607, a Canadian fund series)**, which tilts toward "smaller companies, lower-priced companies, and more profitable companies." **100% equity, no bonds**, for his whole investing life | Video h3ajVyC5vJc; RR Ep. 434 |
| same | A large stake in **OneDigital private equity**, rolled over from his PWL equity. He calls it "idiosyncratic risk that I want to take" | same |
| same | Home bought late 2020: "Purely from a financial perspective… by far our worst investment" | RR Ep. 434 |
| same | About 5% cash in a high-interest savings account (HISA) | same |
| Dan Bortolotti (co-host), 2026-11 | 70/30 plain cap-weighted ETFs, "benign neglect" | RR Ep. 434 |

**US translation for the app:** DFA607 is not available to US investors. The nearest US-listed analogues are global equity funds with systematic size/value/profitability tilts, such as Dimensional's or Avantis's all-world equity ETFs (e.g., DFAW, AVGE). This is the app's inference, not a Felix recommendation **[UNVERIFIED as endorsement]**.

### 1.7 Criticisms and conflicts of interest
- **Employer promotion:** Most videos link "Meet with PWL Capital." He is CIO and holds equity in PWL's parent, OneDigital. He has a business interest in evidence-based advice. That interest is mostly consistent with the low-cost message, but it is still advisor marketing.
- **DFA closeness:** PWL uses Dimensional funds, and he holds DFA607. His pro-factor framing lines up with his firm's products. Factor skeptics argue that premiums are smaller after publication and after costs:
  - McLean & Pontiff (2016), "Does Academic Research Destroy Stock Return Predictability?", *Journal of Finance* 71(1), 5–32, DOI 10.1111/jofi.12365. Finding: anomaly returns are about 26% lower out-of-sample and about 58% lower after publication **[percentages from memory, UNVERIFIED]**.
  - Harvey, Liu & Zhu (2016), "…and the Cross-Section of Expected Returns," *Review of Financial Studies* 29(1), 5–68, DOI 10.1093/rfs/hhv059. Both DOIs were verified via OpenAlex.
  - RR #176 asked "Is the Value Premium Smaller Than We Thought?" (2021-11-18). To his credit, he hosts skeptics: Brad Cornell (#151), Rob Arnott (#157).
- **Value's long underperformance (about 2007 to 2020)** made factor tilts look bad for over a decade. Tilted investors need strong discipline.
- **Canadian focus:** His tax and account advice does not transfer to US investors.
- **Personal note:** His video "When Compounding Works Against You" (2026-08-16) mentions a cancer diagnosis. This is irrelevant to evidence quality and recorded only for context.

### 1.8 Alignment with academic evidence
**Overall: Very high (A/B).** He cites peer-reviewed literature directly, publishes reference lists (zbib.org links in video descriptions), and interviews the authors (Fama #200, French #100, Asness #93). Where he goes beyond consensus (factor tilt sizes, leverage for the young), he labels the uncertainty.

## 2. The Plain Bagel (Richard Coffin)

### 2.1 Background and credentials
- **Identity:** Richard Coffin, based in Ottawa. He started the channel in 2017 as a hobby to help friends and family start investing. He writes, edits, animates, and hosts the videos; a colleague, Craig, films (https://www.theplainbagel.com/about).
- **Credentials:**
  - **CFA charterholder**
  - Bachelor's in finance, Carleton University
  - Worked in investments since graduating in 2015
  - "Working toward" the CFP (FAQ, https://www.theplainbagel.com/faq). *Fact-check:* the FAQ still lists his job as "investment analyst," so it looks out of date compared with his 2024 to 2026 video disclaimers. Whether he has since earned the CFP is **[UNVERIFIED]**.
- **Day job:** Originally an investment analyst at **WDS Investment Management** (Ottawa wealth manager since 2002), doing "research and making stock recommendations to portfolio managers" (FAQ). Current video disclaimers (2024 to 2026) say he "is a registered portfolio manager at WDS Investment Management" and "does not provide advice through The Plain Bagel, which is not affiliated with his employer." See, e.g., "How BAD Are Stock Valuations Really?" (2026-10-09, https://www.youtube.com/watch?v=0ZCVKL_PDsg).
- **Other projects:** "Dead on the Money" podcast; merch store; an NFT collection linked from the site (theplainbagel.com).
- **Editorial policy:** "We DO NOT post stock pick or forecast videos" (FAQ).

### 2.2 Core philosophy
The Plain Bagel is **educational and explanatory, not prescriptive.** Its stated mission is "basic, unbiased education that will empower viewers to make their own investment decisions" (About page). Recurring themes:
1. **Investing is not a get-rich-quick scheme.** "Investing STILL Won't Make You Rich (Probably)" (2021-10-22, https://www.youtube.com/watch?v=H5jPJQ5cVGU).
2. **Balanced active vs. passive framing.** He presents both sides and generally concludes that passive suits most people.
   - "The Active Vs Passive Investing Debate" (2020-01-10, https://www.youtube.com/watch?v=wTzciLaCksA)
   - "Stock Indices & Index Fund Investing" (about 2018, https://www.youtube.com/watch?v=5A4lp_oZ5zg)
   - The exact conclusion wording is **[UNVERIFIED]**.
3. **Fundamental analysis done properly**, for people who choose to pick stocks.
   - "How I Research Stocks - Step-by-Step Fundamental Analysis" (2023-06-23, https://www.youtube.com/watch?v=kXYvRR7gV2E)
   - Chapters: screening, understanding the business, finances, strategy, valuation, notes
   - Free tools named "(no current affiliation)": Finviz screener, TIKR, Koyfin, Bloomberg watchlist
   - "Stock Multiples: How to Tell When a Stock is Cheap/Expensive" (about 2018, https://www.youtube.com/watch?v=21STUhQ-iP0)
4. **Dividends are mostly irrelevant, with nuance.** "Dividends Are Irrelevant (Sort Of...)" (2023-10-27, https://www.youtube.com/watch?v=wBjBs0VibaY). Chapters cover dividend irrelevance theory, its assumptions, dividend downsides, and "Why People Still Like Dividends." Also "Why Investors Love Dividends (And Why They Can Be Dangerous)" (about 2018).
5. **Debunks market-structure fears.**
   - "The Index Fund/ETF Bubble - How Bad Is It Really?" (2019-11-15, https://www.youtube.com/watch?v=1s7ULX45fjw), a response to Michael Burry's index-bubble claim
   - "Is the S&P 500 Too Concentrated?" (2024-03-01, https://www.youtube.com/watch?v=h6nHqwkZOZg)
   - "How BAD Are Stock Valuations Really?" (2026-10-09)
6. **Consumer protection and finfluencer skepticism** (his most distinctive contribution):
   - "Let's Talk About Influencers Promoting Stocks/Crypto" (2021-07-02, https://www.youtube.com/watch?v=AjyGslr7TC4)
   - "What To Do With Finfluencers?" (2024-03-22, https://www.youtube.com/watch?v=ILsXSJeF9Xc). Cites the CFA Institute's finfluencer report (https://rpc.cfainstitute.org/-/media/documents/article/industry-research/finfluencer-report.pdf) and points viewers to **FINRA BrokerCheck** (https://brokercheck.finra.org/) to verify advisors.
   - **"The Sketchy Companies Paying YouTubers to Promote Their Stock"** (2024-12-02, https://www.youtube.com/watch?v=-Q3g-6jFl2c). Reviewed **123 paid stock-promotion videos** and published a spreadsheet. Promoted microcaps typically fell sharply afterward; e.g., sample rows show AEMC down 67% to 74% after $5K to $40K sponsorships. None of the four creators in this file appear in that 2024 spreadsheet (checked 2026-11-10).
   - "Finfluencer Promotions Are Getting Worse" (2026-07-10, https://www.youtube.com/watch?v=co681TqHDzQ). Covers a Polymarket influencer scandal, "sketchy stock promotions," and AI promotions. Cites a research paper in *Applied Economics Letters*: **de Oliveira Leite, R., Moraes, F., & Moura, M. (2025), "Pay to pump: financial influencers and stock returns," DOI 10.1080/13504851.2025.2546061** (online-first; title and authors verified via OpenAlex).
     - Sample: sponsored YouTube stock-promotion videos, 2022 to 2024.
     - Short-run effect: promoted stocks rose about 7.9% on release day and 11.8% over 5 days.
     - Reversal: prices reverted to pre-promotion levels within about 30 days.
     - Bigger payments were linked to bigger pops.
     - The video also links SEC and Canadian whistleblower pages.
   - "Prediction Markets Are Ruining the Finance Industry" (2026-04-18)
   - "AI Is Giving Financial Advice Now" (2026-05-29, https://www.youtube.com/watch?v=mR4eOVo126Y), on ChatGPT linking to bank accounts. **Directly relevant to this app's design:** chapters cover "Advice Concerns," "Regulatory Ambiguity," and "Considerations for Using AI Advisors."
7. **Macro and current-events explainers**, often Canadian-focused (tariffs, trade, Alberta referendum, exit tax, condo market). Also "The Numbers Behind Anthropic" (2026-10-28) and "AI's Hidden Debt Problem Explained" (2026-09-11).

### 2.3 ETFs vs. stock picking
- **Neutral and educational.** He makes no picks. He teaches how to analyze stocks if you choose to, and explains why most people are well served by diversified index funds **[UNVERIFIED exact phrasing]**.
- **Growth vs. value:** "Growth Vs Value Investing" (2020-09-18, https://www.youtube.com/watch?v=6sUvcWpPvFE).

### 2.4 Frameworks and rules of thumb
| Framework | Source |
|---|---|
| Five-step stock research process: screen, business, finances, strategy, valuation, then write a thesis | 2023-06-23 video |
| Valuation multiples (P/E, EV/EBITDA, P/B) in context, not alone | about 2018 multiples video |
| Before trusting a finfluencer: check registration, disclosures, incentives, and promoted-stock red flags | 2024-03-22 and 2024-12-02 videos |
| Investing is a long, slow process; be wary of "get rich" promises | 2021-10-22 video |
| "5 Steps for Getting Started" (goals, debt, emergency fund, accounts, allocation) | "Investing \| 5 Steps for Getting Started" (about 2018, https://www.youtube.com/watch?v=T1cqSZUviiQ). Steps listed from memory **[UNVERIFIED]** |

### 2.5 Savings and emergency fund
- No dedicated, recent Plain Bagel HYSA video was verified. His older "getting started" content follows standard sequencing: pay down high-interest debt and keep an emergency fund before investing **[UNVERIFIED]**.
- He mostly teaches a Canadian audience (TFSA/RRSP).

### 2.6 Specific recommendations or tickers
- **None, by policy.** He does not post stock-pick or forecast videos (FAQ). Tickers appear only as illustrative examples. The app should never encode a Plain Bagel "buy."

### 2.7 Criticisms and conflicts
- **Sponsors:** Squarespace (2020), Blinkist (2020), Morning Brew (2023), and Wallmine (former sponsor, noted in the 2023 description). These are mostly non-financial-product sponsors, which is a lower conflict than brokerage or crypto sponsors.
- **Revenue:** Patreon and merch (a 2021 mug sale donated proceeds to Doctors Without Borders).
- **NFT collection** on his site: arguably off-brand for a consumer-protection voice **[context UNVERIFIED]**.
- **Day-job risk:** He is a registered PM at WDS. His disclaimers separate the channel from his employer.
- **Gap:** Content is explanatory more than actionable, and Canada-centric.

### 2.8 Alignment with academic evidence
**High (A/B).** He explains theory accurately, including MM dividend irrelevance and its assumptions, and corrects his own errors on screen. The 2023 dividend video description includes "*Correction: The five factor model includes market beta, not 'quality.'*" His finfluencer research is a valuable counterweight that the app should use as a **source-reliability lens**.

---

## 3. Whiteboard Finance (Marko Zlatic)

### 3.1 Background and credentials
- **Identity:** Marko Zlatic, founder of WhiteBoard Finance, based in Northeast Ohio (spoke at CreativeMornings Cleveland, Feb 2020, https://www.youtube.com/watch?v=uClk3ERGBss).
- **Education and experience:**
  - Degree in Finance, University of Akron
  - "Finance roles at companies like KeyBank"
  - Source for both: guest bio in "Coast FIRE w/ $730k | Marko Zlatic," Marriage Kids and Money (2024-10-17, https://www.youtube.com/watch?v=S6-KBFOcxMM)
- **Self-description** (channel About): "real estate investor, stock market investor, finance major, and entrepreneur." His standard disclaimer reads: **"DISCLAIMER: I am not a financial adviser. These videos are for educational purposes only."**
- **No CFP or other advisory credential** was found. I could not run a CFP Board lookup (https://www.cfp.net/verify-a-cfp-professional), so this is **[UNVERIFIED: absence not confirmed]**. Some online summaries may wrongly call him a CFP. **The app should not.**
- **Financial status (self-reported via interview):** reached "Coast FIRE" with about **$730K invested by age 36** (2024-10-17 interview).
- **Products:**
  - **WhiteBoard Finance University (WBFU):** $10/month or $100/year. Courses on personal finance, stocks (index funds, asset allocation, "build a 7-figure portfolio"), and real estate (first rental, deal analysis, leverage). Also weekly live Q&A, spreadsheets, and "portfolio updates" showing his buys and sells "in real-time" (https://lp.whiteboardfinance.com/, fetched 2026-11-10). The page claims "#1 Rated Financial Education Platform" without a named rater.
  - Free "7 Day Money Roadmap" (https://whiteboardfinance.com/roadmap)
  - "Work with me" planning link (https://whiteboardfinance.com/go/planning). The nature of the service and the credentials of whoever provides it are **[UNVERIFIED]**.

### 3.2 Core philosophy
Practical, **sequenced personal finance** plus an **index-fund core** with satellite positions (dividend stocks, income ETFs, Bitcoin, real estate). He is more willing than Felix or Coffin to discuss individual stocks, income strategies, and crypto, often in sponsored contexts.

### 3.3 Signature framework: the order of operations
From **"The Exact Order To Invest Your Money (Most People Get This Wrong)"** (2026-03-31, https://www.youtube.com/watch?v=VdLfZJVfLkY). Sequence from the description and chapters:
1. **Emergency fund** ("the foundation that everything else depends on")
2. **Pay off high-interest debt** ("a guaranteed return"). The description says investing with "22% credit card debt and no emergency savings" is "like trying to fill a bucket that has holes in it."
3. **Employer 401(k) match** ("Free money, but there's one catch"; the catch is likely vesting **[UNVERIFIED]**)
4. **IRA (Roth/Traditional) before more 401(k)** contributions
5. **HSA** ("triple tax advantage," "the one account that beats everything (if you qualify)")
6. **Then more 401(k), then taxable brokerage** ("taxable accounts are the last stop, not the first")
- Sources cited in the description: Empower and Vanguard emergency-savings surveys (2025), Bankrate card rates, IRS IRA/401(k) limits (2025), IRS Pub 969, FRED PSAVERT. The video is sponsored by Storyblocks, a non-financial sponsor (verified).
- **Stale numbers (fact-check):** the video cites **2025** IRS limits. For 2026, use the verified figures in §6.1.
- **Note on ordering:** He puts the emergency fund before the match. Some planners (and Graham's Sept 2026 post) put a *starter* emergency fund first and then the match. On 2026-08-17 he wrote "401k match first, always" in the context of mortgage vs. investing. The app should model this as: **starter EF, then match, then high-interest debt, then full EF, then IRA, then HSA, then more 401(k), then taxable.** It should note that creators differ slightly.

### 3.4 Other frameworks and rules of thumb
| Rule | Source | Notes |
|---|---|---|
| **70/20/10 budget:** 70% needs, 20% saving/investing, 10% debt payoff (then wants) | "How To Manage Your Money (70/20/10 Rule)" (2025-02-10, https://www.youtube.com/watch?v=HkNPZVu-jZM) | "Money management is 90% discipline and 10% knowledge" |
| Mortgage prepay vs. invest decided by **rate**: with a 2.x% to 3.x% rate, investing wins (example: $500/mo on a $400K loan at 2.75% saves ~$64K interest vs. ~$1.1M invested over 30 years); liquidity matters | "Should You Pay Off Your Mortgage Early or Invest? (Here's The Math)" (2026-08-17, https://www.youtube.com/watch?v=IE-Q5BqIB2U) | The $1.1M figure implies about 10%/yr nominal returns ($500/mo for 360 months at 10%/12 ≈ $1.13M; checked arithmetically). That is optimistic and ignores risk. The figure also compares a guaranteed saving with an uncertain market outcome, without after-tax or risk adjustment. The video also contrasts a 6.75% rate. Policygenius-sponsored. The app should show a range |
| Strategy by income level ($35K / $75K / $100K+): avalanche vs. snowball, fee drag ("The 1% fee that costs you $186,000"), cites SPIVA | "How To Invest Your Money By Income Level" (2026-05-14, https://www.youtube.com/watch?v=_8uBw3QfDTU) | Sponsored by Perplexity |
| A simple 5-ETF "hold forever" portfolio: **VTI vs. S&P 500 foundation**, diversification beyond US, an "income layer," a cash component, and an "investment with no ticker symbol" | "The ONLY 5 ETFs You'll Ever Need (Hold FOREVER)" (2026-06-30, https://www.youtube.com/watch?v=kUNzee3IZA0) | **Webull-sponsored.** The exact 5 tickers beyond VTI are **[UNVERIFIED: transcript unavailable]** |
| Retirement income: 4% rule, dividend income vs. dividend growth, "My $1 Million Hybrid Portfolio Strategy," cautions on high-yield products | "Here's What a $1 Million Portfolio Would Pay You Every Year" (2026-07-28, https://www.youtube.com/watch?v=-Z8xm0MHY0A) | |
| Long-run capital market assumptions | "JPMorgan Just Revealed the Next 10 Years of Returns (7%–10%)" (2026-11-02) | Refers to the JPMorgan Long-Term Capital Market Assumptions. Exact numbers [UNVERIFIED] |

### 3.5 Savings, HYSA, emergency fund
- **Emergency fund is step 1** in his order of operations (2026-03-31).
- He keeps an affiliate page "My Favorite High Yield Savings Accounts" (https://whiteboardfinance.com/go/hysa and /go/savings). These are **affiliate links**, so the ranking may reflect commissions.
- The 5-ETF video includes a chapter called "The Cash Component You're Sleeping On" (2026-06-30). It likely refers to money-market or T-bill ETFs **[UNVERIFIED]**.
- His exact months-of-expenses target is **[UNVERIFIED]**. Most of his content follows the standard 3 to 6 months.

### 3.6 Specific recommendations and tickers (dated)
| Date | Item | Source / caveat |
|---|---|---|
| 2021-09-27 | Put **$100,000 into three covered-call/income ETFs: NUSI, DIVO, JEPI** via M1 Finance | "I Invested $100,000 Into These 3 Stocks" (https://www.youtube.com/watch?v=7PSGSU5AoQM). Policygenius-sponsored. **Directly opposite to Felix's covered-call critique.** NUSI (Nationwide Nasdaq-100 Risk-Managed Income ETF) was **reorganized into the NEOS Nasdaq-100 Hedged Equity Income ETF (QQQH) at the close of business on 2024-11-08**, and QQQH assumed NUSI's track record (verified on neosfunds.com). The app should map NUSI to QQQH |
| 2024-08-06 | **36 dividend stocks paying $762+/month** | "36 Dividend Stocks That Pay Me $762+ Per Month" (https://www.youtube.com/watch?v=vNekeaedn1E). The ticker list is **[UNVERIFIED]** |
| 2026-06-30 | VTI as the foundation of a 5-ETF portfolio | 5-ETF video (Webull-sponsored) |
| 2026-07-07 | Bitcoin: "Your Bitcoin Is Dead Money (Here's 5 Ways To Fix It)." Earning yield on crypto and borrowing against it | https://www.youtube.com/watch?v=IeChe-ESuMw. **Sponsored by Nexo** (crypto lending). Crypto lending carries counterparty risk; compare the 2022 Celsius/BlockFi collapses |
| 2026-10-18 | **Paid stock promotion: Greenland Mines (NASDAQ: GRML)** in "The Housing Market is Cooked. (Here's Why)" | https://www.youtube.com/watch?v=-cfUp0f8Vs4. The description says "Marko - WhiteBoard Finance has been compensated five thousand dollars for video content, on behalf of Greenland Mines," funded by Gold Standard Media LLC, and that profiled securities "should be considered high risk." **This is exactly the pattern The Plain Bagel documented (2024-12-02, 2026-07-10).** |
| 2026-09-22 | "The 10-Year Treasury Just Hit 5% (Here's What Happens Next)" | Sponsored by Seeking Alpha (Premium / Alpha Picks) |

### 3.7 Criticisms and conflicts
- **Heavy sponsorship mix.** Many sponsors sell financial products that bear on the advice itself: Webull (brokerage), Aven (HELOC credit card; 3 videos in Aug to Nov 2026), Achieve (HELOC), Nexo (crypto lending), Seeking Alpha (stock-pick subscription), Policygenius (insurance), River (Bitcoin). The rest are unrelated: Perplexity, Storyblocks, Tello, Strawberry.me, Emergent, FSA Store.
- **A paid microcap promotion (GRML, Oct 2026)** is a serious red flag for reliability.
- **WBFU paid community** and real-time trade sharing could encourage copy-trading.
- **No advisory credential.** He disclaims being an adviser.
- **Inconsistency:** He has an index-fund core message but repeatedly promotes income products (JEPI/DIVO/NUSI), dividend-stock income framing, and crypto yield.

### 3.8 Alignment with academic evidence
**Mixed (A to D).**
- **Sequencing, tax-advantaged account priority, and fee awareness are strong (A).** These match the standard household-finance literature and IRS rules, and he cites SPIVA.
- **Dividend-income and covered-call enthusiasm contradicts theory and evidence (D/E).** See MM 1961; Hartzmark & Solomon 2019; Israelov & Nielsen 2015.
- **Crypto yield products are speculative (D).**
- **Return assumptions** (about 10%/yr in examples) are on the optimistic end compared with 2026 forward-looking estimates. Graham's 2026-10-21 video description cites Vanguard's 4% to 5% for US stocks over 5 to 10 years.

## 4. Graham Stephan

### 4.1 Background and credentials
**Personal and career timeline** (from Wikipedia, https://en.wikipedia.org/wiki/Graham_Stephan, fetched 2026-11-10, and his own sources):

| When | Event |
|---|---|
| Born 1989 or 1990 | Grew up in Los Angeles |
| Age 18 | Became a real estate agent; did not attend college |
| 2008 | Got a California real-estate license |
| 2011 | Started investing in property |
| 2015 | Joined the Oppenheim Group |
| 2016 | Started his YouTube channel |
| 2019 | YouTube earnings passed $1M/yr |
| By 2020 | Had sold **over $125M** in real estate |
| 2020 | Left Oppenheim to make content full-time; moved to Las Vegas and lost his California realtor license |
| 2020 | Earned $5.1M, about half from YouTube ads and the rest from sponsorships and marketing |
| April 2026 | Channel had about 5.16M subscribers (Wikipedia, as of 2026-04-23) |

- **No investment credentials** (no CFA, CFP, or RIA registration found).
- **Media:**
  - YouTube: main channel plus "The Graham Stephan Show"
  - Substack newsletter (https://grahamstephan.substack.com). Its About page says: "I will never tell you to 'buy X stock now.' That is gambling, not investing." It also rejects selling panic and get-rich-quick schemes. *Fact-check nuance:* he calls stock *tips* gambling, not owning individual stocks as such.
  - Portfolio-following link via the Blossom app
- **Past businesses and products:**
  - "The Real Estate Agent Academy" / "YouTube Creator Academy" courses and a "weekly mentorship program" (Teachable links in 2020 to 2023 video descriptions)
  - **Bankroll Coffee**, since closed: "closed my coffee company" (Substack "How I messed up," 2025-12-29, https://grahamstephan.substack.com/p/healthy-money)

### 4.2 Core philosophy (as of 2026)
1. **Income and savings rate first.** "Income is the fuel. Investing is the engine" ("Can you get rich by index investing?", 2026-01-09, https://grahamstephan.substack.com/p/can-you-get-rich-by-index-investing).
2. **Index funds as the core; dollar-cost average monthly regardless of news.**
   - He cites Bessembinder (2018): "58% of common stocks have historically failed to beat Treasury bills over their lifetime," and all net wealth from 1926 to 2016 came from "just 4% of the best-performing stocks" (verified quote).
   - He cites Ben Carlson's rolling-period data: "over a 20- and 30-year period, the historical probability of losing money in the US stock market is 0%."
   - His conclusion: "Time in the market beats timing the market" (same post).
   - *Caveats (fact-check):*
     - The "0%" figure is **US-only and survivorship-biased**. Across 39 developed markets, Anarkulova et al. (2022) find meaningful odds of real losses even over 30 years.
     - Investing each paycheck as it arrives is sensible. But when you already hold a lump sum, the historical evidence generally favors investing it at once, which beats DCA about two-thirds of the time in Vanguard's studies **[figure from memory, UNVERIFIED]**. DCA mainly helps behaviorally.
3. **Barbell suggestion for readers:** about **80% "safe"** (index funds and Treasuries) and about **20% "risky"** (thematic trades, individual stocks, and a very small amount of crypto). He also says "I own individual stocks, too" (2026-01-09 post). He does not explicitly present this as his own allocation.
4. **Diversify for a possible lost decade.** "An Urgent Warning For ALL Investors" (2026-10-21, https://www.youtube.com/watch?v=pYySHOdSEQc) cites, in its description:
   - Market data: CAPE about 41, the top 10 stocks about 39% of the S&P 500, record margin debt
   - Forecasts: Vanguard's 4% to 5% annual US equity forecast; GMO negative real forecasts
   - History: 2000 to 2009, when "$10,000 invested in the S&P 500 actually fell to roughly $9,089 including dividends"
   - His advice: own "international stocks, bonds, cash, real estate," avoid timing, and avoid "investing so aggressively that a prolonged decline forces you to sell."
5. **2026 shift: simplify and exit real estate.** "I'm Selling... Where I'm Investing in 2026" (2026-01-05, https://www.youtube.com/watch?v=Ooiv2-tHNaU) says:
   - "broad diversification through index funds. A mix of U.S. broad-market equities, international stocks, and emerging markets"
   - Leaving real estate because risk-adjusted returns have worsened in high-cost states ("renting is often cheaper than buying")
   - A "smaller" Bitcoin allocation
   - Precious metals viewed as a hedge, though "long-term growth expectations still favor equities"
   - "a meaningful allocation to treasuries and municipal bonds… liquidity and peace of mind"
   - Core list: "Buy consistently. Stay diversified. Protect downside risk. Ignore headlines."
6. **Reversed position on mortgage payoff.** "I paid off my 2.875% mortgage — Dave Ramsey was right" (Substack, 2026-09-14, https://grahamstephan.substack.com/p/i-paid-off-my-2875-mortgage-dave):
   - He paid off three mortgages at 2.875% to 3.5%, admitting it was "mathematically incorrect" but "a weight lifted off my shoulders."
   - Priority order he gives: **(1) emergency fund, (2) full 401(k) match, (3) kill debt above 7% to 8%, (4) only then optionally prepay cheap debt.**
   - Caveat: paying down debt brings peace of mind "only if you also have… cash on hand." His verified wording is "Maintain a cash buffer — ALWAYS."
   - He cites an NBER paper: **Amromin, Huang & Sialm (2006), "The Tradeoff Between Mortgage Prepayments and Tax-Deferred Retirement Savings," NBER WP w12502**, later published in *Journal of Public Economics* (2007) (verified on nber.org).
     - What the paper finds: about 38% of households that accelerate mortgage payments instead of saving in tax-deferred accounts "are making the wrong choice."
     - *Fact-check:* Graham describes the cost as "11-17% of their future retirement nest egg." The paper actually reports a mean benefit of **11 to 17 cents per dollar** reallocated, which is not the same as a share of total retirement wealth.
   - He gives no specific emergency-fund months target in this post (checked).

### 4.3 ETFs vs. stock picking
- **Core message: index funds.**
- "The 5 BEST Index Funds That Will Make You RICH" (2020-09-16, https://www.youtube.com/watch?v=vCdTLteTPtM). The description, verified, lists:
  - VFIAX/VOO, VTSAX/VTI, SWPPX, SWTSX
  - "FXIAX," described as Fidelity's S&P 500 fund at "0%"
  - **FZILX** (Fidelity ZERO International)
  - and, as his **#1, FZROX** (Fidelity ZERO Total Market). The earlier draft omitted FZROX.
  - The description also carries a Webull free-stock referral and a paid-mentorship link.
  - **Data-quality note (fact-check):**
    - "FXIAX" is a typo. The Fidelity 500 Index Fund ticker is **FXAIX** (confirmed on Fidelity's site), and its expense ratio is about 0.015%, not 0% **[ER from memory, UNVERIFIED]**.
    - Fidelity's zero-fee US funds are FZROX (total market) and FNILX (large cap). FNILX is **not** an S&P 500 fund.
    - Zero-fee "ZERO" funds can be held only at Fidelity.
    - The app should validate tickers and expense ratios against live data, never against creator descriptions.
- **Contradiction:** He calls "buy X stock now" tips "gambling," yet he holds individual stocks and suggests a 20% "risky" sleeve. He also runs a "follow my investments/stocks" link.

### 4.4 Frameworks and rules of thumb
| Rule | Source |
|---|---|
| Track all expenses for 60 days; "Optimize for Cash" (HYSAs paying "over 4% APY" at the time); avalanche (math) vs. snowball (psychology); then Roth IRA | "How To Invest In 2026 (The BEST Way To Get Rich)" (published **2024-05-22** despite the title, https://www.youtube.com/watch?v=45LuGC66nOA) |
| Pay yourself first; automate; cut housing, transport, food | "How To Save $10,000 FAST" (2023-05-15, https://www.youtube.com/watch?v=udHzPjxq2lg) |
| Milestones: $10K, $100K ("protect yourself against most major financial emergencies"), $500K, $1M, $5M | "Why EVERYTHING Changes After You Save $10K" (2025-04-28, https://www.youtube.com/watch?v=ZAPhN6o3e3o) |
| Order: EF, then match, then debt >7% to 8%, then optional prepay | Substack, 2026-09-14 |
| 80/20 safe/risky barbell | Substack, 2026-01-09 |
| Don't over-optimize frugality: "It's possible to win the money game and lose the life game" | Substack, 2025-12-29 |

### 4.5 Savings, HYSA, emergency fund
- **Strong, long-running HYSA advocacy:**
  - "I found the MOST PROFITABLE Savings Accounts (It's not Robinhood)" (about 2019, https://www.youtube.com/watch?v=K4ImvTvvmJ4)
  - "I FOUND THE 5 BEST BANK ACCOUNTS OF 2023" (2023-04-17, https://www.youtube.com/watch?v=jeq2JaJqbqM)
  - "WHY YOU NEED AN EMERGENCY FUND" (2020-03-31, https://www.youtube.com/watch?v=0HS_O009unM)
  - Recommended HYSAs are usually tied to affiliate or sponsor deals. Exact months-of-expenses targets are **[UNVERIFIED]**.
- **Large personal cash and Treasury holdings:** $3.2M in "CD's, Treasuries, Bonds, and Cash" (Nov 2021). In 2026 he keeps a "meaningful allocation to treasuries and municipal bonds."
- **Caution: Yotta.** He was an angel investor in and promoter of **Yotta**, a prize-linked savings fintech. In 2024 the bankruptcy of its banking-as-a-service middleware, **Synapse**, froze many users' funds (Wikipedia). **Lesson for the app:** fintech "savings" apps are not always directly FDIC-insured at a bank in the user's name. The app should check a bank's FDIC status via the FDIC BankFind API (https://banks.data.fdic.gov/) and warn about intermediated accounts.

### 4.6 Specific holdings (dated)
| Date | Disclosure | Source |
|---|---|---|
| 2019-11 (about) | "Revealing My Entire $6 Million Investment Portfolio \| 29 Years Old" | https://www.youtube.com/watch?v=Uw3EhsmLe08 (details [UNVERIFIED]) |
| **2021-11-24** | **Real estate:** 8 properties (LA single-family homes, a triplex, and duplexes bought 2012 to 2020; Las Vegas primary residence). **Stocks:** "$7,000,000 Within SP500 Index Funds / Individual Stocks." **Cash:** "$3,200,000 in CD's, Treasuries, Bonds, and Cash." **Crypto:** "$1,000,000 Split Between Bitcoin and Ethereum." **Other:** 3 angel investments and a 2005 Ford GT | "Revealing My ENTIRE $20 Million Dollar Portfolio \| 31 Years Old" (https://www.youtube.com/watch?v=qE_mlAsg31E) |
| 2025-12 to 2026-09 | Selling real estate; sold the LA property; paid off three mortgages | Substack 2025-12-29 and 2026-09-14 |
| 2026-01-05 | US total market, international, and emerging-market index funds; smaller Bitcoin allocation; precious metals; Treasuries and munis | 2026-01-05 video |
| 2026-06-29 | "'I Just Sold Everything' - WTF Happened To Bitcoin?!": a Bitcoin drawdown explainer covering Strategy (MSTR) holding about 4% of supply and 50% to 80% historical drawdowns. The title quotes others, not necessarily him **[UNVERIFIED]** | https://www.youtube.com/watch?v=TzuvZLuXzAE |

### 4.7 Criticisms and conflicts
- **FTX:** He was named in a proposed class action (March 2023) against finfluencers who promoted FTX. He apologized, saying he had traded on the platform himself. In May 2025 a federal judge dismissed some claims and let others proceed or be amended (Wikipedia).
- **Yotta/Synapse (2024):** see 4.5.
- **Sponsors bearing on advice:**
  - SoFi Crypto (Oct to Nov 2026)
  - Public.com and Webull (free stock for deposit)
  - Firstrade; Amex referral links
  - Acorns
  - Non-financial: Upside, Surfshark, Incogni, CookUnity
- **Clickbait and macro-fear framing:** titles like "It Started: …", "BREAKING: …", "An Urgent Warning…", "America's Debt Spiral Is Going To Implode Within 3 Years" (2026-10-13), "Americans Are Running Out Of Money" (2026-10-07). These headlines are often more alarming than the descriptions, which tend to be balanced. Frequent macro forecasting content sits awkwardly with his own "ignore headlines" advice.
- **Courses and mentorship** sold during 2019 to 2023.
- **Advice reversals** (mortgage payoff, real estate). These are honest and well explained, but they show that his advice tracks his own life stage and wealth level ($20M+ in 2021). That wealth is not comparable to a user with $20K.

### 4.8 Alignment with academic evidence
**Moderate to high on core advice (A/B); weak on speculative and sponsored content (C/D).**
- **Strong (A):** index-fund DCA, citing Bessembinder, diversifying internationally and across assets, avoiding leverage-driven forced selling, keeping an emergency fund.
- **Mixed (C):** the 80/20 barbell with a "risky" stock/thematic sleeve is behaviorally motivated, not evidence-based. Bitcoin and precious-metals views are mixed.
- **Weak (D):** macro-timing-flavored content and urgent framing. Forecasts like Dalio's have poor out-of-sample accuracy; see Graham's own section "Experts can be very wrong."

## 5. Comparison matrix

| Dimension | Ben Felix | The Plain Bagel | Whiteboard Finance | Graham Stephan |
|---|---|---|---|---|
| Credential | CFA, CFP, MBA; CIO of PWL | CFA; registered PM (WDS) | Finance BA; "not a financial adviser" | Ex-realtor; none |
| Role / incentive | Advisory-firm marketing | Education; non-financial sponsors | Creator business: many financial sponsors, paid community, paid stock promo | Creator business: many financial sponsors; past courses |
| Core equity view | Global total market + size/value/profitability tilt | Explains both; no picks | Index core (VTI) + dividend/income/crypto satellites | Index core (US + intl + EM) + 20% "risky" sleeve |
| Stock picking | Strongly against | Teaches how; neutral; no picks | Discusses and holds individual stocks | Calls stock tips "gambling" but holds some |
| Dividends / income funds | Irrelevant; covered calls bad | Irrelevant "(sort of)" | Enthusiastic (JEPI/DIVO/NUSI 2021; 36 dividend stocks 2024) | Not a focus |
| International | Yes; modest home bias OK | Neutral | Discusses diversification beyond US | Yes (2026) |
| Bonds / cash | 100% equity personally; cash ~5% for liquidity | Educational | "Cash component"; Treasury content | Large Treasuries/munis/cash |
| Crypto | Skeptical/academic (RR crypto series 2022) | Skeptical of promotions | Pro-Bitcoin; Nexo/River sponsors | Holds BTC (smaller in 2026) |
| Real estate | Rent vs. buy is close (5% rule); his home a poor investment | Explainers (REITs, condos) | Rental real estate courses | Was core; exiting in 2026 |
| Mortgage payoff | Treat as asset-allocation question [UNVERIFIED specifics] | n/a | Invest if rate is 2.x% to 3.x% | Paid off (emotional > math, 2026) |
| Emergency fund | Cash for liquidity; not an investment | Standard [UNVERIFIED] | Step 1 | Step 1; HYSA advocate |
| Order of operations | Lifecycle / utility framing | Standard | EF, debt, match, IRA, HSA, 401(k), taxable | EF, match, debt >7% to 8%, optional prepay |
| Market-timing content | Against | Explains valuations, no timing | Some macro-reaction videos | Frequent macro/urgent framing, but says don't time |
| Evidence alignment (overall) | **A** | **A/B** | **B/C** (A on sequencing; D/E on income/crypto) | **B/C** (A core; D on macro/hype) |
| Best use in app | Primary evidence-based voice for portfolio construction and factors | Source-reliability lens; how to analyze stocks; red flags | Cash-flow sequencing and account priority checklist | Savings/HYSA motivation; behavioral "survive the decade" framing |
| Trust weight (suggested) | 0.9 | 0.85 | 0.5 | 0.55 |

*Trust weights and letter grades are editorial judgments by this research pass, not an external rating.*

**Where they agree (high-confidence consensus for the app):**
1. Build an emergency fund before investing.
2. Capture the employer 401(k) match.
3. Eliminate high-interest debt.
4. Use low-cost broad index funds as the core.
5. Don't time the market.
6. Fees matter.

**Where they disagree (show both sides):**
- Dividend and covered-call income: Felix and Coffin vs. Whiteboard.
- Factor tilts: Felix for; others mostly silent.
- Individual stocks and crypto as a satellite.
- Prepaying a low-rate mortgage: Graham 2026 vs. Whiteboard.

---

## 6. Implications for this user (US, moderate risk, ETFs, ~$20K savings)

These are **not personalized advice.** They are decision rules the app should compute using the user's inputs.

1. **Split the $20K.** Emergency fund = monthly essential expenses × N, where N = 3 to 6. Use the higher end for single-income households, variable income, or a moderate-risk temperament. Whiteboard and Graham explicitly put the EF first, and Felix supports holding cash for liquidity. The N = 3 to 6 range is a planner convention, not a creator-verified rule; none of the four was verified to give a specific number.
2. **Where the EF lives:** an FDIC-insured HYSA at a bank verified via FDIC BankFind, or a Treasury money-market fund or T-bills. Check whether the account is direct or intermediated (the Graham/Yotta/Synapse lesson). Don't pick an HYSA from creator affiliate lists without comparing APYs independently.
3. **Excess cash above the EF** should follow the order of operations: match, then high-interest debt, then Roth/Traditional IRA, then HSA if eligible, then more 401(k), then taxable.
4. **Core holdings:** a total US market + total international index (Felix/Graham consensus). Optionally add a modest factor tilt (Felix), framed as B-rated evidence with tracking-error warnings.
5. **Avoid:** covered-call "income" ETFs as a core holding, paid-promotion microcaps (the Plain Bagel lens), leveraged ETFs for a moderate-risk user, and crypto-lending yield products.

### 6.1 Verified numbers a US user needs (checked 2026-11-10; refresh every January)
| Item | 2026 value | Source |
|---|---|---|
| 401(k)/403(b) employee deferral limit | **$24,500** | IRS 401(k) limits page |
| Catch-up, age 50+ / "super catch-up," ages 60 to 63 | $8,000 / $11,250 | same |
| Total annual additions (employee + employer) | $72,000 | same |
| Traditional + Roth IRA combined limit | **$7,500** ($8,600 if 50+) | IRS IRA limits page |
| HSA limit (requires HDHP) | **$4,400 self-only / $8,750 family** (+$1,000 if 55+) | IRS Pub 969 |
| HDHP minimum deductible | $1,700 self-only / $3,400 family | IRS Pub 969 |
| 2027 limits | Not yet published as of 2026-11-10. The IRS usually announces them in November, so the app should re-check | IRS |
| Roth IRA income phase-out ranges (2026) | **[UNVERIFIED]**. Must be pulled from IRS before the app gives Roth vs. Traditional or backdoor-Roth guidance | IRS |

**Market and rate context (FRED, free CSV endpoint):**
- **10-year Treasury yield:** 4.88% on 2026-11-09. It peaked at 5.31% on 2026-10-05, which matches Whiteboard's "Just Hit 5%" video (2026-09-22).
- **30-year mortgage rate:** 7.18% on 2026-11-05 (Freddie Mac PMMS). It peaked at 7.40% in October 2026, so Graham's "about 7.5%" is slightly high.
- **CPI-U inflation:** 3.7% year over year for September 2026 (non-seasonally adjusted, 336.94 vs. 324.80). This confirms Graham's figure.
- **Short-term rates:** effective fed funds 3.88% (2026-11-09); 3-month T-bill 4.06% (2026-11-09).
- **Shiller CAPE:** 39.7 per multpl.com on 2026-11-10. Graham's "about 41" was a recent reading; the source and date differ.

**Savings implications for the ~$20K (missing from the earlier draft):**
- **HYSA:** with fed funds at about 3.9%, competitive APYs should be benchmarked against the 3-month T-bill (about 4.1%). Any HYSA paying well below that is uncompetitive.
- **Taxes:** HYSA interest is taxed federally and by your state. Interest on T-bills and Treasury-only money-market funds is exempt from state and local income tax, which matters in high-tax states.
- **Deposit insurance:** FDIC insurance covers $250,000 per depositor, per insured bank, per ownership category. Fintech "partner bank" accounts are covered only if the records properly identify you (the Synapse lesson).
- **Money-market funds and T-bills** are not FDIC-insured. A brokerage's SIPC coverage protects against broker failure, not market loss.
- **Excess cash above the EF:** after the 401(k) match, funding the 2026 IRA ($7,500) and, if eligible, the HSA ($4,400 self-only) is the next step in the waterfall. Unused annual IRA and HSA room cannot be recovered later. (For a prior tax year, contributions can still be made until the filing deadline.)
- **Inflation:** with CPI at 3.7%, after-tax HYSA yields are roughly zero to slightly negative in real terms (e.g., 4% APY × (1 − 30% tax) ≈ 2.8%, below 3.7% inflation). This is Felix's point that cash belongs to liquidity needs, not long-term growth.

---

## 7. How the app should encode these creators

### 7.1 Design principles
- **Store creators as sources, not authorities.** Every principle carries:
  - an `evidence_alignment` rating
  - an `evidence_refs` list pointing to the papers knowledge base
  - a `conflict_flags` list
  - an `as_of` date
- **Separate principles** (stable, durable) **from positions** (dated, perishable, hidden from "buy" logic after 12 months).
- **No creator ticker is ever a "buy" signal by itself.** Tickers feed a "mentioned by" panel only.
- **Sponsored mentions** (`sponsored: true`) are shown with a warning and excluded from scoring.
- **Paid stock promotions** (`paid_promotion: true`) trigger a red-flag banner.

### 7.2 JSON schema (draft)
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "CreatorProfile",
  "type": "object",
  "required": ["id", "name", "credentials", "trust_weight", "principles", "positions", "conflicts", "last_reviewed"],
  "properties": {
    "id": {"type": "string"},
    "name": {"type": "string"},
    "aliases": {"type": "array", "items": {"type": "string"}},
    "jurisdiction_focus": {"enum": ["US", "CA", "Global"]},
    "credentials": {"type": "array", "items": {"type": "object", "properties": {
      "designation": {"type": "string"}, "year": {"type": ["integer", "null"]},
      "verified": {"type": "boolean"}, "source_url": {"type": "string"}}}},
    "trust_weight": {"type": "number", "minimum": 0, "maximum": 1},
    "principles": {"type": "array", "items": {"$ref": "#/$defs/principle"}},
    "positions": {"type": "array", "items": {"$ref": "#/$defs/position"}},
    "conflicts": {"type": "array", "items": {"$ref": "#/$defs/conflict"}},
    "last_reviewed": {"type": "string", "format": "date"}
  },
  "$defs": {
    "principle": {"type": "object",
      "required": ["id", "statement", "category", "evidence_alignment", "sources", "as_of"],
      "properties": {
        "id": {"type": "string"},
        "statement": {"type": "string"},
        "category": {"enum": ["savings_emergency_fund", "order_of_operations", "budgeting", "asset_allocation",
          "index_vs_active", "factor_investing", "dividends_income", "stock_selection", "international",
          "fixed_income_cash", "crypto", "real_estate", "debt_mortgage", "leverage", "behavior",
          "market_timing", "fees_taxes", "source_reliability"]},
        "evidence_alignment": {"enum": ["A", "B", "C", "D", "E"]},
        "evidence_refs": {"type": "array", "items": {"type": "string"}, "description": "IDs into papers.json"},
        "sources": {"type": "array", "items": {"$ref": "#/$defs/source"}},
        "applies_to_us": {"type": "boolean"},
        "verified": {"type": "boolean"},
        "as_of": {"type": "string", "format": "date"},
        "app_rule": {"type": ["string", "null"], "description": "Machine-usable rule ID, e.g. 'EF_FIRST'"}
      }},
    "position": {"type": "object",
      "required": ["ticker_or_asset", "stance", "date", "source"],
      "properties": {
        "ticker_or_asset": {"type": "string"},
        "stance": {"enum": ["holds", "bought", "sold", "recommends", "discusses", "criticizes"]},
        "amount_note": {"type": "string"},
        "date": {"type": "string", "format": "date"},
        "sponsored": {"type": "boolean"},
        "paid_promotion": {"type": "boolean"},
        "source": {"$ref": "#/$defs/source"},
        "stale_after_days": {"type": "integer", "default": 365}
      }},
    "conflict": {"type": "object",
      "properties": {
        "type": {"enum": ["employer", "sponsor_financial", "sponsor_nonfinancial", "affiliate", "course_membership", "paid_stock_promo", "legal", "equity_stake"]},
        "detail": {"type": "string"}, "date": {"type": "string"}, "severity": {"enum": ["low", "medium", "high"]},
        "source": {"$ref": "#/$defs/source"}}},
    "source": {"type": "object",
      "required": ["title", "url"],
      "properties": {"type": {"enum": ["video", "podcast", "article", "newsletter", "website", "paper", "interview"]},
        "title": {"type": "string"}, "date": {"type": "string"}, "url": {"type": "string"}}}
  }
}
```

### 7.3 Shippable data (seed file `data/creators/*.json`)

Ratings follow section 0.3. `verified: false` means the claim rests on memory or an inference and needs review before release.

```json
[
  {
    "id": "ben_felix",
    "name": "Ben Felix",
    "aliases": ["Benjamin Felix", "Rational Reminder", "Common Sense Investing"],
    "jurisdiction_focus": "CA",
    "credentials": [
      {"designation": "CFA", "year": 2016, "verified": true, "source_url": "https://rationalreminder.ca/about"},
      {"designation": "CFP", "year": 2017, "verified": true, "source_url": "https://rationalreminder.ca/about"},
      {"designation": "MBA, Carleton", "year": 2014, "verified": true, "source_url": "https://rationalreminder.ca/about"},
      {"designation": "Chief Investment Officer, PWL Capital", "year": null, "verified": true, "source_url": "https://rationalreminder.ca/podcast/434"}
    ],
    "trust_weight": 0.9,
    "principles": [
      {"id": "felix_index_over_picking", "statement": "Most stock pickers and active funds underperform low-cost index funds after costs; prefer broad diversified funds.", "category": "index_vs_active", "evidence_alignment": "A", "evidence_refs": ["fama_french_2010_luck_skill", "spiva", "bessembinder_2018"], "sources": [{"type": "video", "title": "Why Most Stock Pickers Lose to the Market", "date": "2018-12-15", "url": "https://www.youtube.com/watch?v=AecvTErBQY8"}], "applies_to_us": true, "verified": true, "as_of": "2018-12-15", "app_rule": "CORE_INDEX"},
      {"id": "felix_single_stock_risk", "statement": "Individual stocks carry large uncompensated idiosyncratic risk; most underperform T-bills over their lifetime.", "category": "stock_selection", "evidence_alignment": "A", "evidence_refs": ["bessembinder_2018"], "sources": [{"type": "video", "title": "The Risk of (Individual) Stocks", "date": "2025-06-08", "url": "https://www.youtube.com/watch?v=RxCqxhRsHiY"}], "applies_to_us": true, "verified": true, "as_of": "2025-06-08", "app_rule": "SINGLE_STOCK_CAP"},
      {"id": "felix_factor_tilt", "statement": "Tilting a total-market portfolio toward small, value (low relative price) and profitable companies raises expected returns, with long stretches of underperformance possible.", "category": "factor_investing", "evidence_alignment": "B", "evidence_refs": ["fama_french_1993", "fama_french_2015", "novy_marx_2013", "mclean_pontiff_2016"], "sources": [{"type": "video", "title": "This Paper Could Change How You Invest", "date": "2026-03-22", "url": "https://www.youtube.com/watch?v=N5v2b42i_2g"}, {"type": "video", "title": "Five Factor Investing with ETFs", "date": "2021-03-27", "url": "https://www.youtube.com/watch?v=jKWbW7Wgm0w"}], "applies_to_us": true, "verified": true, "as_of": "2026-03-22", "app_rule": "OPTIONAL_FACTOR_TILT"},
      {"id": "felix_dividend_irrelevance", "statement": "Dividends do not predict differences in expected returns; dividend-focused portfolios reduce diversification and tax efficiency.", "category": "dividends_income", "evidence_alignment": "A", "evidence_refs": ["miller_modigliani_1961", "hartzmark_solomon_2019"], "sources": [{"type": "video", "title": "The Relevance of Dividend Irrelevance", "date": "2022-06-09", "url": "https://www.youtube.com/watch?v=4iNOtVtNKuU"}], "applies_to_us": true, "verified": true, "as_of": "2022-06-09", "app_rule": "TOTAL_RETURN_NOT_YIELD"},
      {"id": "felix_covered_calls", "statement": "Covered-call ETF distributions are not extra return: the strategy keeps most downside, caps upside, and adds an uncompensated equity-timing exposure, so total return tends to lag the underlying in strong markets.", "category": "dividends_income", "evidence_alignment": "B", "evidence_refs": ["israelov_nielsen_2015"], "sources": [{"type": "video", "title": "Covered Calls: A Devil's Bargain", "date": "2025-09-14", "url": "https://www.youtube.com/watch?v=ygVObRx9X68"}], "applies_to_us": true, "verified": true, "as_of": "2025-09-14", "app_rule": "WARN_COVERED_CALL"},
      {"id": "felix_cash_not_investment", "statement": "Cash feels safe but is risky for long-term goals (inflation, reinvestment risk); hold cash for liquidity, not growth.", "category": "fixed_income_cash", "evidence_alignment": "A", "evidence_refs": [], "sources": [{"type": "video", "title": "Cash is a terrible long-term investment, even at 5% interest", "date": "2023-08-29", "url": "https://www.youtube.com/watch?v=KdzOlRRHOU8"}], "applies_to_us": true, "verified": true, "as_of": "2023-08-29", "app_rule": "EXCESS_CASH_ALERT"},
      {"id": "felix_home_bias_modest", "statement": "Extreme home-country bias is harmful; modest home bias is acceptable.", "category": "international", "evidence_alignment": "B", "evidence_refs": ["anarkulova_2022"], "sources": [{"type": "video", "title": "Is Home Country Bias a Mistake?", "date": "2024-02-21", "url": "https://www.youtube.com/watch?v=jN8mIHve1Ds"}], "applies_to_us": true, "verified": true, "as_of": "2024-02-21", "app_rule": "INTL_ALLOCATION_RANGE"},
      {"id": "felix_rent_vs_buy_5pct", "statement": "Compare rent to the unrecoverable costs of owning (about 5% of home value per year), not to a mortgage payment.", "category": "real_estate", "evidence_alignment": "B", "evidence_refs": [], "sources": [{"type": "video", "title": "Renting vs. Buying a Home: The 5% Rule", "date": "2019-05-11", "url": "https://www.youtube.com/watch?v=Uwl3-jBNEd4"}], "applies_to_us": true, "verified": false, "as_of": "2019-05-11", "app_rule": "RENT_VS_BUY_CALC"},
      {"id": "felix_leveraged_etfs", "statement": "Leverage can be rational for young investors with high human capital, but leveraged ETFs carry volatility decay, hidden costs, and severe drawdown risk.", "category": "leverage", "evidence_alignment": "B", "evidence_refs": [], "sources": [{"type": "video", "title": "The Truth About Leveraged ETFs", "date": "2026-09-27", "url": "https://www.youtube.com/watch?v=E7pl0tqzIUQ"}], "applies_to_us": true, "verified": true, "as_of": "2026-09-27", "app_rule": "BLOCK_LEVERAGED_FOR_MODERATE"},
      {"id": "felix_lifecycle", "statement": "Maximizing saving early is not always optimal; balance consumption across the lifecycle.", "category": "behavior", "evidence_alignment": "B", "evidence_refs": ["choi_lifecycle"], "sources": [{"type": "video", "title": "The Biggest Myths in Personal Finance", "date": "2026-07-26", "url": "https://www.youtube.com/watch?v=3u-mP3C26mg"}], "applies_to_us": true, "verified": true, "as_of": "2026-07-26", "app_rule": null}
    ],
    "positions": [
      {"ticker_or_asset": "DFA607 (Dimensional Global Equity Portfolio, Canada)", "stance": "holds", "amount_note": "Most liquid long-term assets; 100% equity", "date": "2026-10-18", "sponsored": false, "paid_promotion": false, "source": {"type": "video", "title": "Breaking Down My Investment Portfolio", "date": "2026-10-18", "url": "https://www.youtube.com/watch?v=h3ajVyC5vJc"}},
      {"ticker_or_asset": "OneDigital private equity", "stance": "holds", "amount_note": "Large; rolled from PWL equity", "date": "2026-11-05", "sponsored": false, "paid_promotion": false, "source": {"type": "podcast", "title": "Rational Reminder 434: Breaking Down Our Investment Portfolios", "date": "2026-11-05", "url": "https://rationalreminder.ca/podcast/434"}},
      {"ticker_or_asset": "Cash (HISA)", "stance": "holds", "amount_note": "About 5% of liquid portfolio", "date": "2026-11-05", "sponsored": false, "paid_promotion": false, "source": {"type": "podcast", "title": "Rational Reminder 434", "date": "2026-11-05", "url": "https://rationalreminder.ca/podcast/434"}}
    ],
    "conflicts": [
      {"type": "employer", "detail": "CIO of PWL Capital; videos link to PWL advisory services", "date": "2026", "severity": "medium", "source": {"type": "podcast", "title": "Rational Reminder 434", "url": "https://rationalreminder.ca/podcast/434"}},
      {"type": "equity_stake", "detail": "Holds OneDigital equity (PWL parent)", "date": "2026-11-05", "severity": "low", "source": {"type": "podcast", "title": "Rational Reminder 434", "url": "https://rationalreminder.ca/podcast/434"}},
      {"type": "employer", "detail": "PWL uses Dimensional funds, which aligns with his factor message", "date": "2025", "severity": "low", "source": {"type": "video", "title": "Dimensional (DFA) vs. Vanguard", "url": "https://www.youtube.com/watch?v=JfknibBat2A"}}
    ],
    "last_reviewed": "2026-11-10"
  },
  {
    "id": "plain_bagel",
    "name": "Richard Coffin",
    "aliases": ["The Plain Bagel"],
    "jurisdiction_focus": "CA",
    "credentials": [
      {"designation": "CFA", "year": null, "verified": true, "source_url": "https://www.theplainbagel.com/faq"},
      {"designation": "Registered portfolio manager, WDS Investment Management", "year": null, "verified": true, "source_url": "https://www.youtube.com/watch?v=0ZCVKL_PDsg"}
    ],
    "trust_weight": 0.85,
    "principles": [
      {"id": "bagel_no_picks", "statement": "Education over prescriptions; the channel does not post stock picks or forecasts.", "category": "source_reliability", "evidence_alignment": "A", "evidence_refs": [], "sources": [{"type": "website", "title": "The Plain Bagel FAQ", "url": "https://www.theplainbagel.com/faq"}], "applies_to_us": true, "verified": true, "as_of": "2026-11-10", "app_rule": null},
      {"id": "bagel_research_process", "statement": "If picking stocks, follow a structured process: screen, understand business, finances, strategy, valuation, and written notes.", "category": "stock_selection", "evidence_alignment": "C", "evidence_refs": ["bessembinder_2018", "barber_odean_2000"], "sources": [{"type": "video", "title": "How I Research Stocks - Step-by-Step Fundamental Analysis", "date": "2023-06-23", "url": "https://www.youtube.com/watch?v=kXYvRR7gV2E"}], "applies_to_us": true, "verified": true, "as_of": "2023-06-23", "app_rule": "STOCK_RESEARCH_CHECKLIST"},
      {"id": "bagel_dividends", "statement": "Dividends are largely irrelevant to total return under MM assumptions; real-world frictions explain investor preference.", "category": "dividends_income", "evidence_alignment": "A", "evidence_refs": ["miller_modigliani_1961"], "sources": [{"type": "video", "title": "Dividends Are Irrelevant (Sort Of...)", "date": "2023-10-27", "url": "https://www.youtube.com/watch?v=wBjBs0VibaY"}], "applies_to_us": true, "verified": true, "as_of": "2023-10-27", "app_rule": "TOTAL_RETURN_NOT_YIELD"},
      {"id": "bagel_paid_promo_red_flag", "statement": "Paid YouTube stock promotions of small companies are a major red flag; promoted stocks have often collapsed.", "category": "source_reliability", "evidence_alignment": "B", "evidence_refs": ["leite_moraes_moura_2025"], "sources": [{"type": "video", "title": "The Sketchy Companies Paying YouTubers to Promote Their Stock", "date": "2024-12-02", "url": "https://www.youtube.com/watch?v=-Q3g-6jFl2c"}, {"type": "video", "title": "Finfluencer Promotions Are Getting Worse", "date": "2026-07-10", "url": "https://www.youtube.com/watch?v=co681TqHDzQ"}], "applies_to_us": true, "verified": true, "as_of": "2026-07-10", "app_rule": "PAID_PROMO_FLAG"},
      {"id": "bagel_verify_advisor", "statement": "Verify an adviser's or influencer's registration and disclosures (e.g., FINRA BrokerCheck) and weigh their incentives.", "category": "source_reliability", "evidence_alignment": "A", "evidence_refs": ["cfa_finfluencer_report"], "sources": [{"type": "video", "title": "What To Do With Finfluencers?", "date": "2024-03-22", "url": "https://www.youtube.com/watch?v=ILsXSJeF9Xc"}], "applies_to_us": true, "verified": true, "as_of": "2024-03-22", "app_rule": "SOURCE_TRUST_SCORE"},
      {"id": "bagel_ai_advice_caution", "statement": "AI financial advice raises accuracy, conflict and regulatory concerns; use it as a tool with verification.", "category": "source_reliability", "evidence_alignment": "C", "evidence_refs": [], "sources": [{"type": "video", "title": "AI Is Giving Financial Advice Now", "date": "2026-05-29", "url": "https://www.youtube.com/watch?v=mR4eOVo126Y"}], "applies_to_us": true, "verified": true, "as_of": "2026-05-29", "app_rule": "LLM_DISCLAIMER"}
    ],
    "positions": [],
    "conflicts": [
      {"type": "sponsor_nonfinancial", "detail": "Squarespace, Blinkist, Morning Brew sponsorships; Patreon", "date": "2020-2023", "severity": "low", "source": {"type": "video", "title": "How I Research Stocks", "url": "https://www.youtube.com/watch?v=kXYvRR7gV2E"}},
      {"type": "employer", "detail": "Registered PM at WDS; channel stated as unaffiliated", "date": "2026", "severity": "low", "source": {"type": "video", "title": "How BAD Are Stock Valuations Really?", "url": "https://www.youtube.com/watch?v=0ZCVKL_PDsg"}}
    ],
    "last_reviewed": "2026-11-10"
  }
]
```

```json
[
  {
    "id": "whiteboard_finance",
    "name": "Marko Zlatic",
    "aliases": ["WhiteBoard Finance", "Marko - WhiteBoard Finance"],
    "jurisdiction_focus": "US",
    "credentials": [
      {"designation": "BA/BS Finance, University of Akron", "year": null, "verified": false, "source_url": "https://www.youtube.com/watch?v=S6-KBFOcxMM"},
      {"designation": "Self-disclaimer: not a financial adviser", "year": null, "verified": true, "source_url": "https://www.youtube.com/watch?v=xxoqWfPAhhY"}
    ],
    "trust_weight": 0.5,
    "principles": [
      {"id": "wbf_order_of_operations", "statement": "Invest in order: emergency fund, high-interest debt, employer match, IRA, HSA, more 401(k), then taxable brokerage.", "category": "order_of_operations", "evidence_alignment": "A", "evidence_refs": ["irs_limits", "vanguard_emergency_savings_2025"], "sources": [{"type": "video", "title": "The Exact Order To Invest Your Money (Most People Get This Wrong)", "date": "2026-03-31", "url": "https://www.youtube.com/watch?v=VdLfZJVfLkY"}], "applies_to_us": true, "verified": true, "as_of": "2026-03-31", "app_rule": "MONEY_WATERFALL"},
      {"id": "wbf_70_20_10", "statement": "Budget 70% needs, 20% saving/investing, 10% debt payoff (then discretionary).", "category": "budgeting", "evidence_alignment": "C", "evidence_refs": [], "sources": [{"type": "video", "title": "How To Manage Your Money (70/20/10 Rule)", "date": "2025-02-10", "url": "https://www.youtube.com/watch?v=HkNPZVu-jZM"}], "applies_to_us": true, "verified": true, "as_of": "2025-02-10", "app_rule": "BUDGET_TEMPLATE"},
      {"id": "wbf_mortgage_rate_rule", "statement": "If your mortgage rate is around 2% to 3%, investing extra cash likely beats prepaying; liquidity matters; check EF and match first.", "category": "debt_mortgage", "evidence_alignment": "B", "evidence_refs": [], "sources": [{"type": "video", "title": "Should You Pay Off Your Mortgage Early or Invest? (Here's The Math)", "date": "2026-08-17", "url": "https://www.youtube.com/watch?v=IE-Q5BqIB2U"}], "applies_to_us": true, "verified": true, "as_of": "2026-08-17", "app_rule": "PREPAY_VS_INVEST_CALC"},
      {"id": "wbf_fees", "statement": "A 1% advisory fee compounds into very large lifetime costs; prefer low-cost index funds (cites SPIVA).", "category": "fees_taxes", "evidence_alignment": "A", "evidence_refs": ["spiva"], "sources": [{"type": "video", "title": "How To Invest Your Money By Income Level ($35K $75K $100K+)", "date": "2026-05-14", "url": "https://www.youtube.com/watch?v=_8uBw3QfDTU"}], "applies_to_us": true, "verified": true, "as_of": "2026-05-14", "app_rule": "FEE_DRAG_CALC"},
      {"id": "wbf_income_funds", "statement": "Income from covered-call ETFs and dividend stocks as a portfolio strategy.", "category": "dividends_income", "evidence_alignment": "D", "evidence_refs": ["israelov_nielsen_2015", "hartzmark_solomon_2019"], "sources": [{"type": "video", "title": "I Invested $100,000 Into These 3 Stocks", "date": "2021-09-27", "url": "https://www.youtube.com/watch?v=7PSGSU5AoQM"}], "applies_to_us": true, "verified": true, "as_of": "2021-09-27", "app_rule": "WARN_COVERED_CALL"}
    ],
    "positions": [
      {"ticker_or_asset": "NUSI (now QQQH after 2024-11-08 reorganization), DIVO, JEPI", "stance": "bought", "amount_note": "$100,000 total via M1 Finance", "date": "2021-09-27", "sponsored": true, "paid_promotion": false, "source": {"type": "video", "title": "I Invested $100,000 Into These 3 Stocks", "date": "2021-09-27", "url": "https://www.youtube.com/watch?v=7PSGSU5AoQM"}},
      {"ticker_or_asset": "36 dividend stocks", "stance": "holds", "amount_note": "$762+/month income", "date": "2024-08-06", "sponsored": true, "paid_promotion": false, "source": {"type": "video", "title": "36 Dividend Stocks That Pay Me $762+ Per Month", "date": "2024-08-06", "url": "https://www.youtube.com/watch?v=vNekeaedn1E"}},
      {"ticker_or_asset": "VTI", "stance": "recommends", "amount_note": "Foundation of 5-ETF portfolio", "date": "2026-06-30", "sponsored": true, "paid_promotion": false, "source": {"type": "video", "title": "The ONLY 5 ETFs You'll Ever Need (Hold FOREVER)", "date": "2026-06-30", "url": "https://www.youtube.com/watch?v=kUNzee3IZA0"}},
      {"ticker_or_asset": "GRML (Greenland Mines)", "stance": "discusses", "amount_note": "Paid $5,000 promotion via Gold Standard Media", "date": "2026-10-18", "sponsored": true, "paid_promotion": true, "source": {"type": "video", "title": "The Housing Market is Cooked. (Here's Why)", "date": "2026-10-18", "url": "https://www.youtube.com/watch?v=-cfUp0f8Vs4"}}
    ],
    "conflicts": [
      {"type": "paid_stock_promo", "detail": "$5,000 for Greenland Mines (GRML) content", "date": "2026-10-18", "severity": "high", "source": {"type": "video", "title": "The Housing Market is Cooked. (Here's Why)", "url": "https://www.youtube.com/watch?v=-cfUp0f8Vs4"}},
      {"type": "sponsor_financial", "detail": "Webull, Aven (HELOC card), Achieve, Nexo (crypto lending), Seeking Alpha, Policygenius, River", "date": "2024-2026", "severity": "medium", "source": {"type": "video", "title": "The ONLY 5 ETFs You'll Ever Need", "url": "https://www.youtube.com/watch?v=kUNzee3IZA0"}},
      {"type": "affiliate", "detail": "HYSA and brokerage 'favorites' pages are affiliate links", "date": "2026", "severity": "medium", "source": {"type": "website", "title": "Video description resources", "url": "https://www.youtube.com/watch?v=kW1ljrz_jis"}},
      {"type": "course_membership", "detail": "WBF University $10/mo or $100/yr; real-time trade sharing", "date": "2026-11-10", "severity": "low", "source": {"type": "website", "title": "WBFU landing page", "url": "https://lp.whiteboardfinance.com/"}}
    ],
    "last_reviewed": "2026-11-10"
  },
  {
    "id": "graham_stephan",
    "name": "Graham Stephan",
    "aliases": ["The Graham Stephan Show"],
    "jurisdiction_focus": "US",
    "credentials": [
      {"designation": "Former CA real-estate agent (2008-2020; $125M+ sold)", "year": 2008, "verified": true, "source_url": "https://en.wikipedia.org/wiki/Graham_Stephan"}
    ],
    "trust_weight": 0.55,
    "principles": [
      {"id": "graham_index_dca", "statement": "Dollar-cost average into broad index funds regardless of news; time in the market beats timing the market.", "category": "index_vs_active", "evidence_alignment": "A", "evidence_refs": ["bessembinder_2018"], "sources": [{"type": "newsletter", "title": "Can you get rich by index investing?", "date": "2026-01-09", "url": "https://grahamstephan.substack.com/p/can-you-get-rich-by-index-investing"}], "applies_to_us": true, "verified": true, "as_of": "2026-01-09", "app_rule": "CORE_INDEX"},
      {"id": "graham_global_diversify", "statement": "Diversify across US, international and emerging-market index funds plus bonds/cash to survive a possible lost decade; avoid aggressiveness that forces selling.", "category": "asset_allocation", "evidence_alignment": "A", "evidence_refs": ["anarkulova_2022"], "sources": [{"type": "video", "title": "An Urgent Warning For ALL Investors", "date": "2026-10-21", "url": "https://www.youtube.com/watch?v=pYySHOdSEQc"}, {"type": "video", "title": "I'm Selling... Where I'm Investing in 2026", "date": "2026-01-05", "url": "https://www.youtube.com/watch?v=Ooiv2-tHNaU"}], "applies_to_us": true, "verified": true, "as_of": "2026-10-21", "app_rule": "INTL_ALLOCATION_RANGE"},
      {"id": "graham_priority_order", "statement": "Emergency fund, then full 401(k) match, then kill debt above 7% to 8%, then optionally prepay cheap debt.", "category": "order_of_operations", "evidence_alignment": "A", "evidence_refs": ["amromin_huang_sialm_2007"], "sources": [{"type": "newsletter", "title": "I paid off my 2.875% mortgage — Dave Ramsey was right", "date": "2026-09-14", "url": "https://grahamstephan.substack.com/p/i-paid-off-my-2875-mortgage-dave"}], "applies_to_us": true, "verified": true, "as_of": "2026-09-14", "app_rule": "MONEY_WATERFALL"},
      {"id": "graham_hysa", "statement": "Keep cash in a high-yield savings account rather than a big-bank 0.01% account; track spending; pay yourself first.", "category": "savings_emergency_fund", "evidence_alignment": "A", "evidence_refs": [], "sources": [{"type": "video", "title": "How To Invest In 2026 (The BEST Way To Get Rich)", "date": "2024-05-22", "url": "https://www.youtube.com/watch?v=45LuGC66nOA"}], "applies_to_us": true, "verified": true, "as_of": "2024-05-22", "app_rule": "HYSA_COMPARE"},
      {"id": "graham_barbell", "statement": "Barbell: about 80% safe (index funds, Treasuries) and about 20% risky (thematic, individual stocks, small crypto).", "category": "asset_allocation", "evidence_alignment": "C", "evidence_refs": ["barber_odean_2000"], "sources": [{"type": "newsletter", "title": "Can you get rich by index investing?", "date": "2026-01-09", "url": "https://grahamstephan.substack.com/p/can-you-get-rich-by-index-investing"}], "applies_to_us": true, "verified": true, "as_of": "2026-01-09", "app_rule": "SATELLITE_CAP"},
      {"id": "graham_mortgage_emotional", "statement": "Paying off a low-rate mortgage is mathematically suboptimal but can be justified emotionally if liquidity remains.", "category": "debt_mortgage", "evidence_alignment": "C", "evidence_refs": [], "sources": [{"type": "newsletter", "title": "I paid off my 2.875% mortgage — Dave Ramsey was right", "date": "2026-09-14", "url": "https://grahamstephan.substack.com/p/i-paid-off-my-2875-mortgage-dave"}], "applies_to_us": true, "verified": true, "as_of": "2026-09-14", "app_rule": "PREPAY_VS_INVEST_CALC"}
    ],
    "positions": [
      {"ticker_or_asset": "S&P 500 index funds / individual stocks", "stance": "holds", "amount_note": "$7.0M", "date": "2021-11-24", "sponsored": false, "paid_promotion": false, "source": {"type": "video", "title": "Revealing My ENTIRE $20 Million Dollar Portfolio | 31 Years Old", "date": "2021-11-24", "url": "https://www.youtube.com/watch?v=qE_mlAsg31E"}},
      {"ticker_or_asset": "CDs, Treasuries, bonds, cash", "stance": "holds", "amount_note": "$3.2M", "date": "2021-11-24", "sponsored": false, "paid_promotion": false, "source": {"type": "video", "title": "Revealing My ENTIRE $20 Million Dollar Portfolio", "date": "2021-11-24", "url": "https://www.youtube.com/watch?v=qE_mlAsg31E"}},
      {"ticker_or_asset": "BTC, ETH", "stance": "holds", "amount_note": "$1.0M (2021); described as smaller allocation (2026)", "date": "2026-01-05", "sponsored": false, "paid_promotion": false, "source": {"type": "video", "title": "I'm Selling... Where I'm Investing in 2026", "date": "2026-01-05", "url": "https://www.youtube.com/watch?v=Ooiv2-tHNaU"}},
      {"ticker_or_asset": "Rental real estate", "stance": "sold", "amount_note": "Exiting/simplifying; paid off 3 mortgages", "date": "2026-09-14", "sponsored": false, "paid_promotion": false, "source": {"type": "newsletter", "title": "I paid off my 2.875% mortgage", "date": "2026-09-14", "url": "https://grahamstephan.substack.com/p/i-paid-off-my-2875-mortgage-dave"}},
      {"ticker_or_asset": "FZROX (#1), VOO/VFIAX, VTI/VTSAX, SWPPX, SWTSX, FXAIX (typed 'FXIAX' in source), FZILX", "stance": "recommends", "amount_note": "'5 best index funds' list; description wrongly lists the Fidelity S&P 500 fund at 0% ER", "date": "2020-09-16", "sponsored": true, "paid_promotion": false, "source": {"type": "video", "title": "The 5 BEST Index Funds That Will Make You RICH", "date": "2020-09-16", "url": "https://www.youtube.com/watch?v=vCdTLteTPtM"}}
    ],
    "conflicts": [
      {"type": "legal", "detail": "Named in proposed FTX promoter class action (2023); some claims dismissed May 2025", "date": "2023-03", "severity": "high", "source": {"type": "website", "title": "Graham Stephan - Wikipedia", "url": "https://en.wikipedia.org/wiki/Graham_Stephan"}},
      {"type": "equity_stake", "detail": "Angel investor in and promoter of Yotta; users' funds frozen in 2024 Synapse collapse", "date": "2024", "severity": "high", "source": {"type": "website", "title": "Graham Stephan - Wikipedia", "url": "https://en.wikipedia.org/wiki/Graham_Stephan"}},
      {"type": "sponsor_financial", "detail": "SoFi Crypto, Public, Webull, Firstrade, Acorns, Amex referrals", "date": "2020-2026", "severity": "medium", "source": {"type": "video", "title": "It Started: The BIGGEST Wealth Transfer...", "url": "https://www.youtube.com/watch?v=8J_36MGSdVI"}},
      {"type": "course_membership", "detail": "Real Estate Agent Academy, YouTube Creator Academy, weekly mentorship (Teachable)", "date": "2019-2023", "severity": "low", "source": {"type": "video", "title": "WHY YOU NEED AN EMERGENCY FUND", "url": "https://www.youtube.com/watch?v=0HS_O009unM"}}
    ],
    "last_reviewed": "2026-11-10"
  }
]
```

### 7.4 Papers referenced by `evidence_refs` (seed `data/papers.json`)
In the 2026-11-10 fact-check, every DOI below was verified against OpenAlex (title, authors, volume, and pages match) unless marked otherwise. Three DOIs from the first draft were wrong and have been corrected (Bessembinder, Anarkulova, Israelov & Nielsen). **Build step:** resolve each DOI via `https://api.crossref.org/works/{doi}` or `https://api.openalex.org/works/doi:{doi}` and fail CI on a title mismatch.

| id | Citation | DOI / URL |
|---|---|---|
| fama_french_1993 | Fama, E. F., & French, K. R. (1993). Common risk factors in the returns on stocks and bonds. *Journal of Financial Economics*, 33(1), 3–56. | 10.1016/0304-405X(93)90023-5 |
| fama_french_2015 | Fama, E. F., & French, K. R. (2015). A five-factor asset pricing model. *JFE*, 116(1), 1–22. | 10.1016/j.jfineco.2014.10.010 |
| fama_french_2010_luck_skill | Fama, E. F., & French, K. R. (2010). Luck versus skill in the cross-section of mutual fund returns. *Journal of Finance*, 65(5), 1915–1947. | 10.1111/j.1540-6261.2010.01598.x |
| novy_marx_2013 | Novy-Marx, R. (2013). The other side of value: The gross profitability premium. *JFE*, 108(1), 1–28. | 10.1016/j.jfineco.2013.01.003 |
| bessembinder_2018 | Bessembinder, H. (2018). Do stocks outperform Treasury bills? *JFE*, 129(3), 440–457. | 10.1016/j.jfineco.2018.06.004 |
| miller_modigliani_1961 | Miller, M. H., & Modigliani, F. (1961). Dividend policy, growth, and the valuation of shares. *Journal of Business*, 34(4), 411–433. | 10.1086/294442 |
| hartzmark_solomon_2019 | Hartzmark, S. M., & Solomon, D. H. (2019). The dividend disconnect. *Journal of Finance*, 74(5), 2153–2199. | 10.1111/jofi.12785 |
| israelov_nielsen_2015 | Israelov, R., & Nielsen, L. N. (2015). Covered calls uncovered. *Financial Analysts Journal*, 71(6), 44–57. | 10.2469/faj.v71.n6.1 |
| mclean_pontiff_2016 | McLean, R. D., & Pontiff, J. (2016). Does academic research destroy stock return predictability? *Journal of Finance*, 71(1), 5–32. | 10.1111/jofi.12365 |
| harvey_liu_zhu_2016 | Harvey, C. R., Liu, Y., & Zhu, H. (2016). …and the cross-section of expected returns. *Review of Financial Studies*, 29(1), 5–68. | 10.1093/rfs/hhv059 |
| barber_odean_2000 | Barber, B. M., & Odean, T. (2000). Trading is hazardous to your wealth. *Journal of Finance*, 55(2), 773–806. | 10.1111/0022-1082.00226 |
| anarkulova_2022 | Anarkulova, A., Cederburg, S., & O'Doherty, M. S. (2022). Stocks for the long run? Evidence from a broad sample of developed markets. *JFE*, 143(1), 409–433. | 10.1016/j.jfineco.2021.06.040 |
| choi_lifecycle | Choi, J. J., Liu, C., & Liu, P. (2025). Practical Finance: An Approximate Solution to Lifecycle Portfolio Choice. NBER Working Paper w34166 (not peer reviewed). | https://www.nber.org/papers/w34166 |
| amromin_huang_sialm_2007 | Amromin, G., Huang, J., & Sialm, C. (2007). The tradeoff between mortgage prepayments and tax-deferred retirement savings. *Journal of Public Economics* (NBER WP w12502, 2006). [journal volume/pages UNVERIFIED] | https://www.nber.org/papers/w12502 |
| leite_moraes_moura_2025 | de Oliveira Leite, R., Moraes, F., & Moura, M. (2025). Pay to pump: financial influencers and stock returns. *Applied Economics Letters* (online first). | 10.1080/13504851.2025.2546061 |
| spiva | S&P Dow Jones Indices, SPIVA U.S. Scorecard (semiannual). | https://www.spglobal.com/spdji/en/research-insights/spiva/ |
| cfa_finfluencer_report | CFA Institute, "The Finfluencer Appeal" (2024) [title UNVERIFIED]. | https://rpc.cfainstitute.org/-/media/documents/article/industry-research/finfluencer-report.pdf |
| vanguard_emergency_savings_2025 | Vanguard, "Emergency savings may hold key to financial well-being." | https://corporate.vanguard.com/content/corporatesite/us/en/corp/articles/emergency-savings-may-hold-key-financial-well-being.html |
| irs_limits | IRS retirement-plan contribution limits (update annually). | https://www.irs.gov/retirement-plans/plan-participant-employee/retirement-topics-ira-contribution-limits |

### 7.5 App rule IDs these creators feed
| app_rule | Logic sketch | Creator support |
|---|---|---|
| MONEY_WATERFALL | starter EF, then 401(k) match, then debt > ~7%, then full EF (3 to 6 months, from user input), then IRA, then HSA (if HDHP), then more 401(k), then taxable | Whiteboard, Graham (and Felix/Bagel implicitly) |
| HYSA_COMPARE | Rank user-entered or free-data APYs; FDIC BankFind check; flag intermediated or fintech accounts | Graham (and Yotta lesson) |
| CORE_INDEX | Default core = total US + total international, low expense ratio | Felix, Graham, Whiteboard |
| INTL_ALLOCATION_RANGE | Show ex-US range (e.g., 20% to 50% of equity) with rationale | Felix, Graham |
| OPTIONAL_FACTOR_TILT | Opt-in tilt sleeve (e.g., ≤ 30% of equity) with tracking-error warning | Felix |
| SINGLE_STOCK_CAP / SATELLITE_CAP | Cap individual stocks, thematic, and crypto at a user-set % (default ≤ 10% for moderate risk) | Felix, Graham (20% upper bound) |
| WARN_COVERED_CALL / TOTAL_RETURN_NOT_YIELD | Warn when a user adds covered-call or high-yield funds; show total return vs. underlying | Felix, Bagel (contra Whiteboard) |
| BLOCK_LEVERAGED_FOR_MODERATE | Hard warning for leveraged ETFs when risk = moderate | Felix |
| PAID_PROMO_FLAG / SOURCE_TRUST_SCORE | Red banner when a creator mention is sponsored or a paid promotion; weight by trust_weight | Bagel |
| PREPAY_VS_INVEST_CALC | Compare mortgage rate vs. after-tax expected return range; include liquidity and emotional toggle | Whiteboard, Graham |
| RENT_VS_BUY_CALC | 5% rule plus full NPV | Felix |
| FEE_DRAG_CALC | Lifetime fee drag | Whiteboard, Felix |
| EXCESS_CASH_ALERT | If cash > EF target + near-term goals, suggest deploying via the waterfall | Felix |
| LLM_DISCLAIMER | Any LLM output is labeled educational, cites KB entries, and never issues buy orders | Bagel |

---

## 8. Open items for the user / maintainers
1. **Monthly essential expenses** (needed to size the emergency fund out of the $20K), job stability, and whether there's an HDHP/HSA.
2. Verify Marko Zlatic's credentials via CFP Board lookup, and Richard Coffin's via the CFA Institute directory.
3. Pull transcripts (when network allows) for: the Whiteboard 5-ETF video (exact tickers), the Whiteboard dividend-stocks list, Felix's US-listed factor ETF discussions, and Graham's HYSA months-of-expenses rule.
4. Run DOI verification (7.4) in CI.
5. Re-review all `positions` every 6 months; principles yearly.
6. Market context quoted by creators (late 2026). Checked against FRED and multpl.com on 2026-11-10; the app must still refresh it from free data before display:
   - CAPE about 41 and top-10 concentration about 39% (Graham, 2026-10-21). Both are verified as quoted in his description. multpl.com shows CAPE at 39.7 on 2026-11-10. The 39% concentration figure was not independently checked **[UNVERIFIED]**.
   - 10-year Treasury about 5% (Whiteboard, 2026-09-22). **Confirmed:** DGS10 exceeded 5% from late September to October 2026 (peak 5.31% on 2026-10-05) and was 4.88% on 2026-11-09.
   - CPI 3.7% in Sept 2026 (Graham, 2026-10-28). **Confirmed:** CPI-U (non-seasonally adjusted) rose 3.7% year over year.
   - Mortgage rates about 7.5% (Graham, 2026-11-02). **Slightly high:** the Freddie Mac 30-year rate peaked at 7.40% (Oct 2026) and was 7.18% on 2026-11-05.
7. Pull the 2026 Roth IRA income phase-out ranges and, once published, the 2027 IRS limits.

*This document is educational research, not investment advice.*
