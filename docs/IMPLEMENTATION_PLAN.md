# FinTec Investment Research Assistant: Implementation Plan

**Version:** 1.0 (2026-11-10)
**Owner:** solo Python developer (the user)
**Target:** local Python + Streamlit app, $0 budget, free data only, optional free LLM
**Status:** plan only. No code exists yet.

> **Not financial advice.** The app is a research and education tool. Every number it shows must carry a source and an as-of date (tech brief §0, §6). Scores come from versioned, cited rules, not from an LLM.

---

## 0. How to read this plan

### 0.1 Source briefs and citation keys

Every key decision below cites one of these briefs. The short keys are used throughout.

| Key | File | What it contributes |
|---|---|---|
| **[ACAD]** | `docs/research/academic_evidence.md` | Peer-reviewed evidence, evidence tiers, default ETF/stock score weights, portfolio guardrails |
| **[INF-A]** | `docs/research/influencers_ben_felix_plain_bagel_whiteboard_graham.md` | Felix, Plain Bagel, Whiteboard Finance, Graham Stephan profiles, CreatorProfile JSON schema, app rule IDs |
| **[INF-B]** | `docs/research/influencers_tilbury_jikh_carlin_carlson.md` | Tilbury, Jikh, Carlin, Carlson profiles, principle record schema, seed records |
| **[MKT]** | `docs/research/market_context_2026.md` | Nov 2026 regime snapshot, CMAs, popular products (covered calls, buffers, thematic, crypto), refresh checklist |
| **[SAV]** | `docs/research/savings_and_account_strategy.md` | Cash vehicles, after-tax yield formulas, EF sizing model, order of operations, 2026 IRS limits, the $20K split algorithm |
| **[TECH]** | `docs/research/tech_stack_and_data_sources.md` | Free data sources, verified limits, fallback chain, project layout, cache TTLs, LLM provider abstraction |
| **[METH]** | `docs/research/analysis_methodology.md` | **Missing.** This file did not exist when the plan was written (checked twice on 2026-11-10). Where it would have applied (scoring formulas, DCF details), this plan uses [ACAD] §12 weights, [INF-B] §3.2 (Carlin DCF) and [INF-B] §4.2 (Carlson moat checklist). **Action:** when [METH] lands, reconcile it with §12 (Phase 3) and `knowledge_base/rules/*.yaml`. Rules are config, so this changes YAML, not code. |

### 0.2 Guiding principles (non-negotiable)

1. **Deliver value first.** The user has about $20K in cash today. Phase 1 (savings optimizer and profile) ships before any market-data work. [SAV §4]
2. **Knowledge base is the intellectual core.** Curated, versioned markdown and JSON, researched in advance by Claude and shipped in git. Live data only feeds numbers into deterministic calculators. [TECH §0]
3. **No LLM-generated numbers, no LLM buy/sell calls.** The LLM may only rephrase computed results and cited KB content. The app must work fully with `LLM_PROVIDER=none`. [TECH §4, §6]
4. **Every recommendation is a rule.** Rules live in `knowledge_base/rules/*.yaml`, carry citations, and the UI shows why each item passed or failed each criterion. [TECH §6.3]
5. **Show uncertainty and staleness.** Confidence intervals on factor loadings, `as_of` on every number, amber/red stale badges, "stale-while-error" fallbacks. [TECH §5.4, MKT §9]
6. **Index core, satellite picks.** The default is a low-cost global index core. Individual stocks are a capped satellite with base-rate warnings (Sharpe 1991, Bessembinder 2018). [ACAD §1.1, §2.1]
7. **Creators are sources, not authorities.** Each creator claim carries an evidence-alignment grade, conflict flags and a date. No creator ticker is ever a buy signal on its own. [INF-A §7.1, INF-B §6.1]
8. **Low-frequency by design.** No real-time ticker view. A decision journal and a 48-72 hour cooling-off period come before any "buy" checklist completes. [ACAD §6.1]

### 0.3 Conflicts between briefs and how this plan resolves them

The briefs were researched separately and disagree in places. Resolutions are encoded as config defaults the user can change.

| Topic | Conflict | Resolution in this plan |
|---|---|---|
| EF default months | [ACAD §11] says default 6 × E for a moderate user; [SAV §2.2] gives a scored model (base 3 + adjustments, clamp 3-12, worked example 4.5); Tilbury 5×, Jikh 3-6 [INF-B §6.4] | Use the [SAV §2.2] scored model as the engine. Show three reference markers beside it: 3× (floor), 5× (Tilbury, cautious), 6× ([ACAD] moderate default). Add a "lean conservative" toggle that floors the target at 6 months. **The toggle is off by default** (consistency review 2026-11-10: SPEC §10.1, WORKFLOW §2.3 and `MY_SAVINGS_AND_STARTER_PLAN.md` §2.3 all use the 4.5-month computed target). The profile wizard suggests switching it on when the user answers "sell" to the drawdown question. |
| Order of operations | Whiteboard puts the full EF before the match [INF-A §3.3]; Graham puts EF, then match, then debt over 7-8% [INF-A §4.2]; [SAV §3.1] puts a starter EF, then match, then debt, then full EF | Use [SAV §3.1] as the default ladder (starter EF → match → debt >8% APR → full EF → HSA → IRA → more 401(k) → taxable). Show the creator variants side by side in the "What the experts say" panel. [INF-A §3.3] recommends exactly this. |
| T-bill rate series | [ACAD §11] says pull `DTB3`; [TECH §1.3] says `DTB3` is discount-basis and understates the yield | Use the Treasury daily par-yield CSV (primary) and FRED `DGS3MO` (fallback). Never compare `DTB3` with an APY. [TECH §1.3, §1.4] |
| Satellite cap | [ACAD §12] says individual stocks at most 20% of equities; [INF-A §7.5] and [INF-B §6.4] suggest at most 10% for a moderate profile | Default cap 10% of equities: warn above 10%, strong warning above 20%. Single stock: warn above 5%, strong warning above 10% of investable assets. [ACAD §2.1]. Same values in SPEC §5.1 FR-104 and WORKFLOW §1.7 |
| Yield-trap rule | [ACAD §5.1]: yield > 2× sector median **and** payout > 100% FCF; [MKT §6]: yield > 2× sector median **or** payout > 80% of earnings or > 100% FCF | `YIELD_TRAP_WARN` (soft) at the [MKT] "or" rule; `YIELD_TRAP_FAIL` (blocking) at the [ACAD] "and" rule. Canonical table: SPEC §10.3.4 |
| Anarkulova et al. (2022) DOI | [ACAD §7.2] lists `10.1016/j.jfineco.2021.05.009`; [MKT §4.2] and [INF-A §0.1a] say the corrected DOI is `10.1016/j.jfineco.2021.06.040` | Use `.06.040` (two independent fact-checks). The CI DOI check in Phase 4 must confirm it. |
| Momentum weight | [ACAD §3.2] caps momentum at ≤ 10% and uses it mainly as a "falling knife" flag | Weight 10%, plus a falling-knife flag. |
| Storage engine | Task asks for "SQLite/DuckDB"; [TECH §5.1] recommends DuckDB | DuckDB as the single store. SQLite is only a fallback if file-locking problems appear (see risk R-07). |
| Where user data lives | Earlier drafts put the profile in `data/user/profile.json` and the journal in `journal.jsonl` | **DuckDB user tables are the system of record** (SPEC FR-106, §7.1). `data/user/` holds imports (holdings CSVs) and human-readable exports (profile, IPS, journal as Markdown/CSV/JSONL) written by the app |
| Formulas vs layout | SPEC §10 and this plan's §12/§16 excerpts differed on some metric lists and thresholds | **SPEC §5 and §10 are canonical** for formulas, weights, thresholds and flags; this plan is canonical for file names, page names and phases. YAML excerpts below were aligned to SPEC on 2026-11-10 |

### 0.5 Requirements traceability (SPEC FR → phase → WORKFLOW touchpoint)

Every functional requirement in `SPEC.md` §5 maps to a phase task here and to the place in `WORKFLOW.md` where the user meets it. "v1.0" = Phase 6 unless noted; "C" = backlog.

| SPEC FRs | Phase / task in this plan | WORKFLOW touchpoint |
|---|---|---|
| FR-101 profile fields, FR-106 local storage | Phase 1 §10.1, §10.8 (1) | §1.1-§1.5 setup |
| FR-102 risk questionnaire, FR-103/104 IPS, FR-105 named guardrails | Phase 1 §10.8 (2-3); guardrail rules in Phase 2 §11.1 (8) | §1.1, §1.7 IPS; §9.3 IPS review |
| FR-107 annual IPS / limits reminder | Phase 6 §15 (9) year-rollover | §9.2, §9.3 |
| FR-108 first-run acknowledgement | Phase 0 §9 task 8 + §10.8 (4) | §1 setup (first screen) |
| FR-109 freshness gate | Phase 3 §12.9 (6) | §3.1, §6.8 item 15 |
| FR-201-206 EF sizing, macro overlay, deductible floor, tiers, EF warnings | Phase 1 §10.2, §10.3 | §1.2, §2.1-§2.3, §3.2 |
| FR-210-218 cash vehicles, after-tax yields, live rates, HYSA table, FDIC checks, break-evens, I bonds, rate guardrails | Phase 1 §10.2, §10.4, §10.5 | §2.4, §3.2, §11.4 |
| FR-219 opportunity cost, FR-220 sinking funds | Phase 1 §10.2 | §2.2 step 4, §3.2 |
| FR-221 tax on cash interest, FR-222 tax-year cash summary | Phase 1 §10.8 (5); FR-222 in Phase 6 | §2.4 step 3, §9.4 |
| FR-230-236 waterfall, creator variants, `split_savings`, limits JSON, Roth/backdoor, match check, lump vs staged | Phase 1 §10.2, §10.5 | §2, §3.3, §9.2 |
| FR-237 prepay/rent-vs-buy, FR-238 budget views | Backlog (C) §22 | §1.3, §3.3 (optional) |
| FR-301-310 ETF universe, profile, total return, overlap, look-through, regression, cost, score, badges | Phase 2 §11.1 (1-7) | §5, §3.4, §4.3 |
| FR-311 factor-tilt page, FR-312 asset location, FR-313 TLH partners + wash sale | Phase 2 §11.1 (8) for location; FR-311/313 in Phase 6 (v1.0) | §5.3, §5.4, §7.1 step 5, §9.1 |
| FR-314 cross-source check, FR-315 ticker validation | Phase 2 §11.4 | §5.4 |
| FR-401-410, 413, 415-416, 418 stock core (EDGAR, metrics, screener, styles, flags, DCF, reverse DCF, sensitivity, dividend safety, moat, Bagel process, fit, creator mentions, momentum, label) | Phase 3 §12.2-§12.6, §12.9 | §6.0-§6.10 |
| FR-411 Plain Bagel workflow, FR-412 Carlin quadrant, FR-414 Finnhub view, FR-417 watchlist | Phase 3 §12.4 (411, 414); §12.9 (412, 417) | §6.3, §6.5, §3.6 |
| FR-501-508, 510, 512 creators, topic lens, consensus, "what would X emphasize", conflicts, jurisdiction, RSS, classification, feed, source reliability | Phase 4 §13.1, §13.4 | §3.5, §6.5, §8 |
| FR-509 transcripts + LLM drafts, FR-511 self-reported + active share | Phase 5 task 8 (509); Phase 6 (511) | §3.5, §4.3 |
| FR-601-607 papers, records, search, topics, SPIVA, find-more, DOI CI; FR-608 Rational Reminder mining (C) | Phase 4 §13.1 (4), §7.3 | §6.6, §11.3 |
| FR-701-705, 711 market snapshot, regime banner, CPI gap, P/E vs CAPE, CMAs, "what this means" | Phase 4 §13.1 (6) | §4.1, §4.2 |
| FR-706-709 concentration, international, factor context, themes; FR-710 calendar (C) | Phase 4 §13.1 (6) | §4.1, §5.3 |
| FR-801-808, 810-811 holdings import, household view, model portfolios, rebalancing, contributions, lump vs staged, stress tests, Monte Carlo, turnover, guardrail panel | Phase 2 §11.5 | §1.6, §3.3, §3.4, §4.2, §7 |
| FR-809 behavior gap, FR-812 glide path (C) | Phase 6 (809); backlog (812) | §3.7, §9.3, §9.4 |
| FR-901-904, 907 decision record, pre-trade checklist, cooling-off, sell checklist, export | Phase 3 §12.4 | §6.7-§6.10, §10 |
| FR-905 reviews, FR-906 journal analytics | Phase 6 | §4.4, §4.6, §9.4 |
| FR-1001-1003, 1005-1009 LLM router, OpenRouter discovery, Ollama, RAG, allowed tasks, privacy, labels, no-LLM parity; FR-1004 opencode (C) | Phase 5 §14 | §10 privacy note; §11.1 |
| NFR-01-16 | Phase 0 (07, 15), Phase 6 §15 (01-05, 08-14, 16) | §3.1, §11.5 |

### 0.4 What the user gets at the end of each phase

| Phase | User-visible outcome | Effort (solo, part-time ~10 h/week) |
|---|---|---|
| 0 Setup | Repo, CI, config, empty Streamlit shell, data-source status panel | 1 week (8-12 h) |
| 1 Savings + profile | **"Where should my $20K go?"** EF target, tiered cash plan, after-tax HYSA vs T-bill vs MMF vs CD vs I bond table, order-of-operations waterfall, 2026 IRA/HSA room | 3-4 weeks (36-49 h) |
| 2 ETF research | ETF explorer: costs, fee drag, factor regressions, holdings overlap, look-through concentration, portfolio and accounts page (holdings import, rebalancing, stress tests, Monte Carlo) with guardrails | 5-6 weeks (58-80 h) |
| 3 Stock scoring | Satellite stock screener: quality/value/safety/momentum/dividend-growth score, checklists, Carlin DCF, decision journal | 5-6 weeks (53-72 h) |
| 4 Experts + research | "What the experts say" panel, research library with evidence tiers, creator feed, market regime banner | 3-4 weeks (36-49 h) |
| 5 Optional LLM | Ask-the-KB chat with citations, explanation buttons, draft-summary pipeline | 2 weeks (20-30 h) |
| 6 Polish + tests | Hardening, live smoke tests, docs, refresh runbooks, backups | 3-4 weeks (35-55 h) |
| **Total** | | **~25-33 weeks part-time, about 245-345 h** (7-9 weeks full-time). Re-estimated on 2026-11-10 after the SPEC traceability review added the Module 8 portfolio tasks, IPS/questionnaire and the v1.0 "S" items |

Estimates assume a developer comfortable with Python and new to Streamlit and DuckDB. Content curation for the KB (Phase 1 and Phase 4 seed files) is included. Claude does most first drafts from the briefs.

---

## 1. Repository and folder structure

Based on [TECH §5.3], with three changes: (1) the curated KB folder is named `knowledge_base/` as the task requests; (2) a `fintec.domain` package holds pure calculators with no I/O, so they are trivial to unit-test; (3) `fintec.rules` loads the YAML rule files.

```
FinTec/
├─ pyproject.toml                 # uv-managed; pinned deps; ruff/mypy/pytest config
├─ uv.lock                        # committed lockfile
├─ .python-version                # 3.12 (see §2.1)
├─ .env.example                   # SEC_USER_AGENT=, FRED_API_KEY=, TIINGO_API_KEY=, FINNHUB_API_KEY=,
│                                 # ALPHAVANTAGE_API_KEY=, OPENALEX_API_KEY=, S2_API_KEY=,
│                                 # OPENROUTER_API_KEY=, LLM_PROVIDER=none, OLLAMA_BASE_URL=, OPENCODE_URL=
├─ config.toml                    # non-secret: rate limits, TTLs, universes, creator allowlist, model prefs
├─ .gitignore                     # .env, data/, *.duckdb, __pycache__, .pytest_cache
├─ .pre-commit-config.yaml        # ruff, ruff-format, mypy, detect-secrets
├─ README.md
├─ app/                           # Streamlit UI only (thin; no business logic)
│  ├─ Home.py                     # entry point; st.navigation; regime banner; disclaimers
│  ├─ components/                 # reusable widgets
│  │  ├─ badges.py                # as_of / stale / source / evidence-tier / conflict badges
│  │  ├─ evidence_card.py         # "base rate" cards (Sharpe, Bessembinder ...)
│  │  ├─ sidebar.py               # profile summary, data-source status, LLM quota
│  │  └─ charts.py                # plotly wrappers (drawdown, rolling, loadings with CI)
│  └─ pages/
│     ├─ 0_Profile.py                # profile wizard, risk questionnaire, IPS, first-run acknowledgement
│     ├─ 1_Savings_and_Emergency_Fund.py
│     ├─ 2_Account_Order_of_Operations.py
│     ├─ 3_ETF_Explorer.py
│     ├─ 4_Portfolio_and_Accounts.py  # holdings, allocation/builder, rebalance, stress, Monte Carlo, contributions, TLH
│     ├─ 5_Stock_Research.py
│     ├─ 6_What_Experts_Say.py
│     ├─ 7_Research_Library.py
│     ├─ 8_Market_Context.py
│     ├─ 9_Decision_Journal.py
│     ├─ 10_Ask_the_KB.py         # always listed; LLM chat when a provider is available, KB keyword search otherwise (FR-1009)
│     └─ 11_Settings.py           # keys present, LLM provider, refresh jobs, source status, KB version, backup/restore
├─ src/fintec/
│  ├─ __init__.py
│  ├─ settings.py                 # pydantic-settings (secrets) + config.toml loader
│  ├─ logging_setup.py
│  ├─ http.py                     # shared httpx clients, token buckets, tenacity policies
│  ├─ errors.py                   # SourceError, RateLimited, QuotaExhausted, SchemaDrift
│  ├─ domain/                     # PURE functions/classes, no I/O, 100% unit-tested
│  │  ├─ profile.py               # UserProfile, RiskProfile, TaxProfile (pydantic)
│  │  ├─ savings.py               # EF sizing, tiering, $20K split, waterfall
│  │  ├─ yields.py                # after-tax / tax-equivalent yields, CD break-even, I-bond penalty
│  │  ├─ accounts.py              # IRA/HSA/401k room, Roth vs Traditional, backdoor pro-rata
│  │  ├─ returns.py               # total return, CAGR, max drawdown, rolling, vol
│  │  ├─ fees.py                  # fee-drag calculator
│  │  ├─ factors.py               # factor regression (statsmodels, HAC)
│  │  ├─ overlap.py               # holdings overlap, look-through concentration
│  │  ├─ valuation.py             # owner earnings, Carlin 2-stage DCF, multiples
│  │  ├─ fundamentals.py          # TTM builder, ratios (GP/A, ROE, leverage ...)
│  │  └─ scoring/
│  │     ├─ etf_score.py
│  │     ├─ stock_score.py
│  │     └─ guardrails.py         # portfolio-level warnings
│  ├─ rules/                      # loads knowledge_base/rules/*.yaml into typed models
│  │  ├─ models.py
│  │  └─ loader.py
│  ├─ data/                       # provider adapters (one module per source)
│  │  ├─ base.py                  # Provider protocols, ProviderResult, capability enum
│  │  ├─ chain.py                 # FallbackChain + cross-validation
│  │  ├─ yfinance_src.py
│  │  ├─ tiingo_src.py
│  │  ├─ alphavantage_src.py
│  │  ├─ edgar_src.py             # companyfacts, frames, tag-fallback map
│  │  ├─ fred_src.py
│  │  ├─ treasury_src.py          # par yield curve CSV, bill rates, Fiscal Data API
│  │  ├─ finnhub_src.py
│  │  ├─ french_src.py            # Ken French ZIP/CSV parser
│  │  ├─ aqr_src.py               # AQR XLSX parser
│  │  ├─ shiller_src.py           # CAPE from ie_data.xls
│  │  ├─ etf_holdings/
│  │  │  ├─ base.py
│  │  │  ├─ ishares.py
│  │  │  ├─ ssga.py
│  │  │  ├─ nport.py              # SEC N-PORT fallback
│  │  │  └─ manual_csv.py         # user-uploaded holdings CSV
│  │  └─ manual.py                # curated JSON values (HYSA APYs, I bond, FDIC rates)
│  ├─ cache/
│  │  ├─ db.py                    # DuckDB connection manager (single writer)
│  │  ├─ migrations/              # 001_init.sql, 002_..., applied in order
│  │  ├─ repo.py                  # typed read/upsert helpers per table
│  │  └─ ttl.py                   # TTL policy + staleness classification
│  ├─ research/                   # openalex.py, semantic_scholar.py, arxiv.py
│  ├─ creators/                   # rss.py, tagger.py, ytdlp_meta.py, transcripts.py (opt-in)
│  ├─ kb/
│  │  ├─ models.py                # pydantic models for every KB file type
│  │  ├─ loader.py                # loads + validates knowledge_base/
│  │  ├─ index.py                 # DuckDB FTS index over KB chunks
│  │  └─ verify.py                # DOI / URL / staleness verifier (CI + CLI)
│  ├─ llm/
│  │  ├─ base.py                  # LLMMessage, LLMResult, LLMProvider protocol
│  │  ├─ openai_compat.py         # OpenRouter + Ollama (+ any OpenAI-compatible URL)
│  │  ├─ opencode.py              # experimental: opencode serve HTTP / `opencode run`
│  │  ├─ null.py                  # NullProvider (templated, no network)
│  │  ├─ router.py                # ordered fallback + prompt-hash cache + quota tracking
│  │  ├─ rag.py                   # retrieve -> prompt -> citation post-check
│  │  ├─ redact.py                # strips PII / balances before any remote call
│  │  └─ prompts/                 # versioned templates (*.md with front-matter version)
│  ├─ services/                   # orchestration used by pages (I/O + domain)
│  │  ├─ savings_service.py
│  │  ├─ etf_service.py
│  │  ├─ stock_service.py
│  │  ├─ experts_service.py
│  │  └─ market_service.py
│  └─ jobs/
│     ├─ __main__.py              # `python -m fintec.jobs <job>`
│     └─ refresh.py               # prices | macro | treasury | fundamentals | factors |
│                                 # holdings | feeds | research | kb-verify | backup
├─ knowledge_base/                # SHIPPED curated KB (versioned in git) - see §6
│  ├─ MANIFEST.json               # kb_version, last_reviewed, file checksums
│  ├─ topics/*.md
│  ├─ research/papers.json
│  ├─ research/claims.json
│  ├─ creators/{slug}.json
│  ├─ creators/channels.json      # verified channel-ID allowlist
│  ├─ etfs/etf_universe.json
│  ├─ etfs/spiva_latest.json
│  ├─ savings/cash_vehicles.json
│  ├─ savings/hysa_rates.json
│  ├─ savings/ibond_rates.json
│  ├─ savings/fdic_national_rates.json
│  ├─ accounts/limits_2026.json
│  ├─ accounts/tax_brackets_2026.json
│  ├─ accounts/state_tax_rates.json
│  ├─ market/cma.json
│  ├─ market/snapshot_manual.json # FactSet / GTM figures (link-and-review items)
│  └─ rules/
│     ├─ savings.yaml
│     ├─ etf_scoring.yaml
│     ├─ stock_scoring.yaml
│     ├─ guardrails.yaml
│     └─ product_warnings.yaml    # covered call, buffer, thematic, crypto, leveraged, private credit
├─ data/                          # git-ignored runtime data
│  ├─ fintec.duckdb
│  ├─ raw/                        # downloaded ZIP/XLSX/CSV (French, AQR, SEC bulk)
│  ├─ parquet/                    # parsed bulk data (EDGAR companyfacts)
│  ├─ cache/transcripts/          # never committed, never redistributed
│  ├─ user/                       # imports (holdings CSVs) + exports (profile, IPS, journal); system of record is DuckDB
│  └─ backups/
├─ scripts/
│  ├─ bootstrap.py                # first-run: migrations, initial downloads
│  ├─ verify_citations.py         # thin wrapper on fintec.kb.verify
│  └─ refresh_market_data.py      # [MKT §9.3] alias for jobs refresh --all-auto
├─ tests/
│  ├─ conftest.py
│  ├─ fixtures/                   # French CSV sample, AQR XLSX sample, EDGAR facts JSON, ...
│  ├─ cassettes/                  # vcrpy recordings
│  ├─ unit/                       # domain/, rules/, kb/
│  ├─ golden/                     # scoring + regression golden files (JSON)
│  ├─ contract/                   # adapters against cassettes
│  ├─ property/                   # hypothesis tests on calculators
│  └─ live/                       # @pytest.mark.live, skipped by default
└─ docs/
   ├─ README.md                   # index of all docs + consistency rules
   ├─ SPEC.md                     # requirements, formulas, thresholds (canonical for numbers)
   ├─ IMPLEMENTATION_PLAN.md      # this file (canonical for names and layout)
   ├─ WORKFLOW.md                 # user playbook (canonical for cadence)
   ├─ MY_SAVINGS_AND_STARTER_PLAN.md
   ├─ research/                   # the briefs
   └─ runbooks/                   # kb_refresh.md, rates_refresh.md, troubleshooting.md
```

**Personal data rule:** everything under `data/user/` is local only. It is never sent to a remote LLM (see §5.4) and is excluded from git. [TECH §4.1, §5.5]

---

## 2. Tech stack with pinned rationale

### 2.1 Runtime

- **Python 3.12** pinned in `.python-version`, even though the dev machine has 3.14.10. Reason: scientific wheels (statsmodels, duckdb, polars) reliably exist for 3.12. Move to 3.13 or 3.14 once `uv sync` installs all wheels without building from source. `uv` installs the pinned interpreter on its own (`uv python install 3.12`).
- **uv** for environments and locking. `uv run streamlit run app/Home.py`. [TECH §5.1]

### 2.2 Dependencies

Pin with `~=` (compatible release) to the versions verified in [TECH] on 2026-11-10, and lock exact versions in `uv.lock`. Upgrade deliberately, once a month, after `pytest -m "not live"` and `pytest -m live` both pass.

| Package | Pin | Why this choice | Brief |
|---|---|---|---|
| streamlit | `~=1.67` | Multipage via `st.navigation`; `st.cache_data`/`st.cache_resource`; fastest path to a local dashboard for a Python developer | [TECH §5.1] (1.67.0 verified) |
| duckdb | `~=2.0` | One file, reads Parquet/CSV/JSON directly, fast analytics over the 1.4 GB EDGAR companyfacts dump, built-in FTS for KB retrieval | [TECH §1.2, §5.1] (2.0.0 verified) |
| pandas | `~=2.2` | Ecosystem glue: yfinance and statsmodels return or expect pandas | [TECH §5.1] |
| polars | `~=2.1` | Heavy screening over EDGAR frames; convert at boundaries only | [TECH §5.1] (2.1.0 verified) |
| pydantic | `~=2.9` | Models for every external payload and KB record | [TECH §5.1] |
| pydantic-settings | `~=2.6` | `.env` + `SecretStr` so keys never print | [TECH §5.5] |
| httpx | `~=0.28` | Sync pooled client per source; also used for the LLM client to avoid SDK churn | [TECH §4.4, §5.1] |
| tenacity | `~=9.0` | Exponential backoff with jitter on 429/5xx | [TECH §1.1, §5.1] |
| yfinance | `==1.8.*` | Primary free prices; pinned tightly because it breaks when Yahoo changes | [TECH §1.1] (1.8.0 verified) |
| statsmodels | `~=0.14` | OLS factor regressions with Newey-West HAC errors | [TECH §5.2] |
| plotly | `~=5.24` | Interactive charts in `st.plotly_chart` | [TECH §5.1] |
| feedparser | `~=6.0` | YouTube RSS, Rational Reminder, arXiv | [TECH §3.3] |
| openpyxl, xlrd | latest | AQR XLSX, Shiller XLS | [TECH §1.11], [MKT §9.1] |
| pyyaml | `~=6.0` | Rule files | this plan |
| python-dotenv | `~=1.0` | `.env` loading | [TECH §5.1] |
| **Optional extras** | | | |
| `[creators]` yt-dlp | `==2026.10.26` | Metadata only, never media; opt-in | [TECH §3.3] |
| `[creators]` youtube-transcript-api | `~=1.2.5` | Local-only, rate-limited, opt-in | [TECH §3.3] |
| `[edgar]` edgartools | `~=6.0` | Optional helper; the core path stays raw httpx | [TECH §1.2] |
| **Dev** | | | |
| pytest, pytest-cov | latest | | [TECH §5.7] |
| vcrpy / pytest-recording | latest | Record HTTP cassettes for adapter contract tests | [TECH §5.7] |
| hypothesis | latest | Property tests on calculators | [TECH §5.7] |
| freezegun | latest | Freeze "today" for staleness/TTL/tax-year tests | [TECH §5.7] |
| ruff, mypy | latest | Lint, format, types (strict on `fintec.domain`) | [TECH §5.1] |
| pre-commit, detect-secrets | latest | Keep keys out of git | [TECH §5.5] |

**Deliberately not used:**
- The `openai` SDK (3.49.0 verified) is optional. Raw httpx covers OpenRouter, Ollama and any OpenAI-compatible server with about 60 lines and no SDK churn. [TECH §4.4]
- APScheduler is not used in-process: Streamlit reruns scripts, so in-process schedulers can double-start. Use Windows Task Scheduler calling `uv run python -m fintec.jobs refresh ...`. [TECH §5.1]
- Stooq is dropped from the automated chain (JS proof-of-work wall). [TECH §1.9]
- SSRN is never scraped. Store links only. [TECH §2.4]
- No brokerage logins. Holdings are entered by hand or by CSV import. [TECH §5.5]

### 2.3 API keys (all free) and when each is needed

| Key | Needed from | Without it |
|---|---|---|
| `SEC_USER_AGENT` (not a key: "Name email") | Phase 3 | EDGAR disabled; fundamentals fall back to yfinance (marked "provider estimate") |
| `FRED_API_KEY` | Phase 1 | Keyless `fredgraph.csv` fallback [TECH §1.3] |
| `TIINGO_API_KEY` | Phase 2 | No price fallback; yfinance only |
| `FINNHUB_API_KEY` | Phase 3 (analyst trends, news) | Panel hidden |
| `ALPHAVANTAGE_API_KEY` | Phase 2 (spot checks) | Skipped |
| `OPENALEX_API_KEY` | Phase 4 | Keyless shared $0.10/day budget [TECH §2.1] |
| `S2_API_KEY` | Phase 4 (optional) | Skipped (keyless is unreliable) |
| `OPENROUTER_API_KEY` | Phase 5 (optional) | Ollama or NullProvider |

---

## 3. Architecture overview

```
 ┌──────────────── app/ (Streamlit pages, thin) ────────────────┐
 │  reads services, renders badges/charts, never calls HTTP      │
 └──────────────┬───────────────────────────────────────────────┘
                │
 ┌──────────────▼──────────── fintec.services ──────────────────┐
 │ savings_service | etf_service | stock_service | experts | mkt │
 └───┬───────────────┬────────────────┬─────────────────┬───────┘
     │               │                │                 │
 ┌───▼────┐   ┌──────▼──────┐   ┌─────▼─────┐   ┌───────▼──────┐
 │ domain │   │ rules (YAML)│   │ kb loader │   │ llm.router   │ (optional)
 │ (pure) │   │ + guardrails│   │ + FTS     │   │ → rag        │
 └────────┘   └─────────────┘   └───────────┘   └──────────────┘
                     ▲                ▲
 ┌───────────────────┴────────────────┴──────────────────────────┐
 │ cache.repo (DuckDB)  ◄── data.chain (FallbackChain) ◄── adapters│
 │   prices, macro, facts, holdings, papers, videos, llm_cache     │
 └─────────────────────────────────────────────────────────────────┘
                     ▲
          jobs.refresh (Windows Task Scheduler / manual button)
```

**Layering rules:**
- `domain` imports nothing from `data`, `cache`, `llm` or `streamlit`. Everything there is a pure function of typed inputs, so every formula can be unit-tested against known values.
- Pages call services only. Services read from the cache. A refresh writes to the cache through the fallback chain. A page never blocks on the network unless the user presses "Refresh now".
- Every value that crosses a layer boundary is a `Sourced[T]`: the value plus `source`, `as_of` and `confidence`. This follows [SAV §5]: "store each rate as `{value, unit, as_of, source_url, confidence}`."

```python
# src/fintec/domain/sourced.py
from datetime import date
from typing import Generic, Literal, TypeVar
from pydantic import BaseModel

T = TypeVar("T")
Confidence = Literal["high", "medium", "low"]

class Sourced(BaseModel, Generic[T]):
    value: T
    unit: str | None = None          # "pct_apy", "pct_coupon_equiv", "usd", ...
    source: str                      # "treasury_par_curve", "fred:DGS3MO", "kb:hysa_rates"
    source_url: str | None = None
    as_of: date
    confidence: Confidence = "high"
    note: str | None = None
```

---

## 4. Data-provider abstraction and fallback chain

### 4.1 Capabilities and chains

Each adapter declares the **capabilities** it serves. Each capability has a `FallbackChain` that tries adapters in order. It skips any adapter that is unavailable: missing key, quota spent, or circuit open. The chains come from [TECH §0] and [TECH §1.13].

| Capability | Chain (in order) | Cross-validation | Brief |
|---|---|---|---|
| `PRICES_DAILY_ADJ` | yfinance → Tiingo → Alpha Vantage (raw close only; flags `adjusted=False`) → manual CSV import | When two sources exist, compare adjusted closes and flag any day where they diverge by more than 1% | [TECH §1.13] |
| `FUNDAMENTALS` | SEC EDGAR companyfacts (bulk Parquet, then per-company API) → yfinance `.financials` → Finnhub `stock/metric` | EDGAR wins; other values are labeled "provider estimate" | [TECH §1.2, §1.13] |
| `KEY_STATS` (P/E, yield, market cap) | yfinance `.info` (with unit sanity checks) → Finnhub `stock/metric` → computed from EDGAR plus price | Check yield against `trailingAnnualDividendRate / price` | [TECH §1.1] |
| `TBILL_YIELDS` | Treasury par yield-curve CSV → Treasury daily bill rates → FRED `DGS3MO`/`DGS1` | Never use `DTB3` for APY comparisons | [TECH §1.3, §1.4], [SAV §5] |
| `MACRO_SERIES` | FRED API → keyless `fredgraph.csv` → Treasury Fiscal Data (where applicable) | | [TECH §1.3] |
| `FACTOR_RETURNS` | Ken French library ZIPs → AQR XLSX (QMJ, BAB, HML Devil) | | [TECH §1.10, §1.11] |
| `ETF_HOLDINGS` | Issuer adapter (iShares/SSGA/...) → SEC N-PORT → yfinance `funds_data` (top holdings only) → user-uploaded CSV | Weights must sum to 100% ± 2% | [TECH §1.12] |
| `ETF_METADATA` (ER, inception, index) | `knowledge_base/etfs/etf_universe.json` (hand-verified) → yfinance | A mismatch over 1 bp shows a "verify at issuer" badge | [TECH §1.12] |
| `ANALYST_NEWS` | Finnhub → yfinance `.recommendations`/`.news` | | [TECH §1.5] |
| `CASH_RATES` (HYSA, CD, MMF, I bond, FDIC national) | Curated KB JSON plus user-entered quotes (no free API exists) | Stale after 7 days (HYSA) or 6 months (I bond) | [SAV §4.6, §5], [TECH §1.4] |
| `CAPE` | Shiller `ie_data.xls` → manual KB value (checked against multpl) | | [MKT §9.1] |
| `PAPERS_META` | OpenAlex → Semantic Scholar | Do not auto-import author names: OpenAlex lists "Caroline Zhu" where the author is Heqing Zhu | [TECH §2.1, §1.10] |
| `CREATOR_FEED` | YouTube RSS (allowlisted channel IDs) → yt-dlp flat metadata (opt-in) | RSS is flaky: never treat a single 404 as "channel deleted" | [TECH §3.1] |

### 4.2 Adapter contract

The full skeleton is in §16.1.

```python
# src/fintec/data/base.py
class Capability(StrEnum): PRICES_DAILY_ADJ = ...; FUNDAMENTALS = ...; ...

class Provider(Protocol):
    name: str
    capabilities: frozenset[Capability]
    def available(self) -> Availability: ...                 # key present? quota left? circuit closed?
    def fetch(self, req: DataRequest) -> ProviderResult: ...  # raises SourceError subclasses
```

- **Rate limits.** These come from `config.toml` `[rate_limits]`, using the [TECH §5.6] values, which sit below the published caps:

  | Source | Limit |
  |---|---|
  | SEC | 5/s |
  | FRED | 1/s |
  | Tiingo | 45/h |
  | Finnhub | 50/min |
  | Alpha Vantage | 20/day |
  | OpenAlex | 5/s |
  | arXiv | 1 per 3 s |
  | yfinance | 1/s |
  | OpenRouter | 15/min |

- **Quota ledger.** A DuckDB table `quota_usage(source, day, calls)` lets `available()` refuse a call once a daily cap is near: Alpha Vantage 25/day, OpenRouter about 50/day, and the OpenAlex dollar budget.
- **Circuit breaker.** After 3 consecutive failures, skip the source for 15 minutes. This handles yfinance 429 storms.
- **Validation.**
  - Each adapter returns pydantic- or pandera-validated frames.
  - Prices must be positive, dates monotonic, and (ticker, date) pairs unique.
  - Missing keys become `None`, never exceptions. [TECH §1.1]
- **SEC.** [TECH §1.2]
  - Declare a `User-Agent` from `SEC_USER_AGENT`, plus `Accept-Encoding: gzip, deflate`.
  - Keep a tag-fallback map per metric, e.g. `Revenues` → `RevenueFromContractWithCustomerExcludingAssessedTax` → `SalesRevenueNet`.
  - Deduplicate facts on `(end, fp, form)`, keeping the latest `filed`.

### 4.3 Stale-while-error

If every source in a chain fails, the service returns the cached value with `confidence="low"`. The UI shows a yellow "stale since X" badge and never a blank. [TECH §5.4]

---

## 5. Caching design (DuckDB)

### 5.1 Why DuckDB, and the concurrency rule

DuckDB is the single store (`data/fintec.duckdb`). It reads Parquet and CSV directly and handles the EDGAR bulk data well. [TECH §5.1]

**Concurrency rule.** DuckDB allows only one read-write process at a time, so:
- The Streamlit app opens **one** read-write connection through `st.cache_resource` and uses a short cursor per request.
- **v1 approach:** refresh jobs run inside the app from a "Refresh" button. Task Scheduler runs jobs only at night, when the app is closed. The job exits cleanly if it cannot get the lock.
- **v2 option:** a job that cannot get the lock writes Parquet to `data/staging/`, and the app ingests staged files on its next refresh. See risk R-07.

### 5.2 Tables and TTLs

TTLs come from [TECH §5.4], [MKT §9] and [SAV §5]:
- **Stale:** older than the TTL. Shown with an amber badge.
- **Expired:** older than 2 × TTL. Shown with a red badge and excluded from scoring. [MKT §9: "mark stale after 2× cadence"]

| Table | Key | Content | TTL / refresh |
|---|---|---|---|
| `prices` | (ticker, date) | adj_close, close, volume, dividend, split, source, fetched_at | Daily after 18:00 ET (incremental). Full re-pull monthly to catch split and dividend re-adjustments |
| `ticker_info` | (source, ticker) | JSON blob from yfinance/Finnhub | 7 days |
| `edgar_facts` | (cik, taxonomy, tag, unit, end, fp, form) | Values from bulk Parquet | Weekly bulk. Per company on demand when `filed` is newer than the last load |
| `fundamentals_ttm` | (cik, period_end) | Derived TTM metrics | Rebuilt after each EDGAR load |
| `macro` | (series_id, date) | FRED series | Daily for rates and spreads; monthly for CPI and UNRATE |
| `treasury_curve` | (date, tenor) | Par yields | Daily |
| `factors` | (dataset, freq, date, factor) | French/AQR returns (decimal) | Monthly, using HTTP `If-Modified-Since` |
| `etf_holdings` | (etf, as_of, holding_id) | weight, name, sector, country | Manual or monthly |
| `cash_rates` | (vehicle_id, as_of) | KB values plus user quotes | HYSA/CD: 7 days. MMF/SGOV: 7 days. I bond: until the next May 1 / Nov 1. FDIC national: monthly |
| `papers` | doi | OpenAlex metadata, cited_by_count | Weekly |
| `videos` | video_id | RSS metadata, topic tags | Daily or weekly |
| `llm_cache` | sha256(provider, model, template_ver, prompt) | Completion | Forever; a template version bump invalidates it |
| `quota_usage` | (source, day) | Call counts / credits | Rolling |
| `fetch_log` | id | source, capability, status, latency, error | 90 days |
| `journal` | id | Decision-journal entries (personal data) | Never expires |

UI-level caching: `st.cache_data(ttl=3600)` on pure service functions that read DuckDB. [TECH §5.4]

```python
# src/fintec/cache/ttl.py
TTL = {
    "prices": timedelta(days=1), "ticker_info": timedelta(days=7),
    "edgar_facts": timedelta(days=7), "macro_daily": timedelta(days=1),
    "macro_monthly": timedelta(days=35), "treasury_curve": timedelta(days=1),
    "factors": timedelta(days=35), "etf_holdings": timedelta(days=35),
    "hysa": timedelta(days=7), "ibond": None,   # valid until next reset date (May 1 / Nov 1)
    "cma": timedelta(days=365), "papers": timedelta(days=7), "videos": timedelta(days=7),
}

class Freshness(StrEnum): FRESH = "fresh"; STALE = "stale"; EXPIRED = "expired"

def classify(as_of: date, kind: str, today: date) -> Freshness: ...
```

- **Migrations** are plain SQL files, applied in order and recorded in `schema_version`.
- **Backups:** Phase 6 adds a weekly backup of `data/fintec.duckdb` and `data/user/` to `data/backups/`.

---

## 6. LLM provider abstraction (optional, Phase 5)

### 6.1 Providers

| Provider | Transport | Model selection | Privacy |
|---|---|---|---|
| `NullProvider` | None | n/a. Returns templated text built from KB content and computed results | Total |
| `OllamaProvider` | `OpenAICompatProvider(base_url="http://localhost:11434/v1", api_key="ollama")` | From `ollama list`, plus the user's preference in config | Local. **Preferred for anything touching personal data** [TECH §4.3] |
| `OpenRouterProvider` | `OpenAICompatProvider(base_url="https://openrouter.ai/api/v1")` | At startup, `GET /models` and keep IDs ending in `:free` or with zero pricing. Rank by context length, then by config preference. Cache the list for 24 h. **Never hard-code model IDs** | Free models may log or train on prompts, so send **redacted** prompts only [TECH §4.1] |
| `OpencodeProvider` (experimental) | `opencode serve` HTTP (default port 4096, optional basic auth), or an `opencode run "<prompt>"` subprocess | Whatever opencode is configured with | Depends on its backend. Main use is **dev-time KB curation**, not runtime [TECH §4.2] |

- **Router order** is set in config, default `llm.order = ["openrouter", "ollama", "none"]` (SPEC FR-1001); users who want personal context in prompts should put `"ollama"` first.
- On HTTP 429 or quota exhaustion, the router falls through to the next provider.
- Every completion is cached by prompt hash.
- The sidebar shows remaining OpenRouter quota from `GET /api/v1/key`. [TECH §4.1]

### 6.2 What the LLM may and may not do

**Allowed** [TECH §4]:
- Q&A over KB chunks, with mandatory `[S#]` citations.
- Plain-English explanations of a metric the app has already computed.
- Drafting KB summaries from cached transcripts and papers, for human review.

**Forbidden:**
- Producing numbers that are not in the provided context.
- Buy or sell recommendations.
- Receiving any `data/user/*` content when the provider is remote.

**Post-checks** (in `llm/rag.py`):
1. **Citation check.** Reject an answer that cites no `[S#]`, or cites an ID that is not in the context. Show "LLM answer rejected; showing sources" instead.
2. **Number guard.** Every number in the answer must appear in the context, allowing for formatting differences. If one does not, strip the answer and fall back to NullProvider output.
3. **Disclaimer.** Append the LLM disclaimer (`LLM_DISCLAIMER` rule, based on The Plain Bagel's AI-advice caution [INF-A §2.2]).

### 6.3 Retrieval

- **Index:** DuckDB FTS (`fts` extension) over KB chunks: topic markdown split by heading, each paper's `finding_summary`, and creator principles.
- **Optional reranking:** if Ollama has an embedding model (e.g. `nomic-embed-text`; the tag is unverified [TECH §4.3]), add vector reranking.
- **Top-k:** 6.

---

## 7. Knowledge-base build process

### 7.1 Content types and schemas

All KB files are validated by pydantic models in `fintec/kb/models.py`, at app start and in CI. One broken file fails CI. At runtime the app logs and skips the bad record; it never crashes.

| File | Record model | Required fields (beyond id) | Source schema |
|---|---|---|---|
| `research/papers.json` | `Paper` | doi or url, title, authors, year, venue, `peer_reviewed`, `evidence_tier` (very_strong / strong / moderate / weak / convention / contested / speculative), `finding_summary`, `practical_implication`, `caveats`, `sample_period`, `markets`, `kb_topics`, `verified` | [TECH §2.6], [ACAD §12 tier legend] |
| `research/claims.json` | `Claim` | statement, `evidence_strength`, `paper_ids`, `app_rule_ids`, `unverified_numbers` (list) | Each "Program rule/implication" in [ACAD] |
| `creators/{slug}.json` | `CreatorProfile` | name, channel_ids, credentials (with verified flag), `trust_weight`, principles, positions, conflicts, `last_reviewed` | Merge of [INF-A §7.2] and [INF-B §6.2] (see §7.2) |
| `creators/channels.json` | `Channel` | creator_slug, channel_id, handle, `official: bool` | [TECH §3.1]: 8 IDs plus Joseph Carlson After Hours; the Sven Carlin impersonator is blocked |
| `etfs/etf_universe.json` | `EtfMeta` | ticker, name, issuer, ER, index, inception, category, `factor_tags`, `is_active`, `is_thematic`, `is_covered_call`, `is_leveraged`, issuer URL, `verified_on` | [TECH §1.12] |
| `etfs/spiva_latest.json` | `SpivaRow` | category, horizon_years, pct_underperforming, report_date, `unverified` | [ACAD §1.2] |
| `savings/*.json` | `CashRate` | vehicle_id, value, unit (`apy` / `coupon_equiv` / `sec_30d` / `7day`), `promo`, `min_balance`, `residency_restriction`, `fdic_direct`, as_of, source_url, confidence | [SAV §1.2, §5] |
| `accounts/limits_2026.json` | `TaxYearLimits` | See the values below this table, plus source URLs | [SAV §3.2, §3.3] |
| `accounts/tax_brackets_2026.json` | `Brackets` | 2026 federal brackets; standard deduction $16,100 single / $32,200 MFJ; 0% LTCG bracket up to $49,450 single | [SAV §3.4], [INF-B §6.5] |
| `market/cma.json` | `Cma` | institution, published, as_of, horizon, asset_class, low / high / point, nominal_or_real, url | [MKT §8] |
| `topics/*.md` | Front-matter | `id`, `title`, `kb_topics`, `paper_ids`, `creator_principle_ids`, `last_reviewed`, `stale_after_days` | This plan |
| `rules/*.yaml` | `RuleSet` | `version`, plus rules with `id`, `params`, `citations` (paper/claim IDs), `severity` | §10-§12 below |

**2026 values for `limits_2026.json`** [SAV §3.2, §3.3]:

| Item | 2026 value |
|---|---|
| 401(k) employee deferral | $24,500 |
| Catch-up, age 50+ / ages 60-63 | $8,000 / $11,250 |
| Total annual additions (§415(c)) | $72,000 |
| IRA limit (Traditional + Roth) | $7,500 (+$1,100 catch-up at 50+) |
| Roth IRA phase-out | $153K-$168K single; $242K-$252K MFJ |
| Traditional IRA deduction phase-out (covered by workplace plan, single) | $81K-$91K |
| HSA limit | $4,400 self-only / $8,750 family |
| HDHP minimum deductible | $1,700 self-only / $3,400 family |

### 7.2 Unifying the two creator schemas

The two influencer briefs use different schemas:
- [INF-A] uses a nested `CreatorProfile` (principles, positions, conflicts) with A-E grades.
- [INF-B] uses flat records with a `type` field (principle / framework / pick / commentary / product_mention) and A-D/NA grades.

The plan uses one nested model that keeps both:

| Model part | Contents |
|---|---|
| `principles[]` | Gains `type: principle \| framework \| commentary` and `parameters: dict`, from [INF-B] |
| `positions[]` | Holds the [INF-B] `pick` records. `stale_after_days` is 120 for picks and 60 for commentary [INF-B §6.1]. For tickers, 120 overrides the [INF-A] default of 365 for positions |
| `product_mentions[]` | Sponsored or creator-owned products, with `sponsored` and `creator_owned_product` flags |
| Grades | The [INF-A §0.3] **A-E** scale plus `NA`. [INF-B] grades map directly: A→A, B→B, C→C, D→D (contradicts the evidence for this profile), NA→NA. E ("directly contradicted") is used only where a brief says so explicitly |
| `fit_for_profile` | Computed at runtime. Any D or E grade, or `risk_level: high` for a moderate user, gives at least `caution` [INF-B §6.1] |
| `trust_weight` | Seeds from [INF-A §5]: Felix 0.9, Plain Bagel 0.85, Graham 0.55, Whiteboard 0.5. [INF-B] gives no numeric weights, so this plan uses the SPEC §10.5 proposals: Tilbury 0.50, Jikh 0.55, Carlin 0.60 and Carlson 0.60, marked `editorial: true` and editable by the user. Weights only set the display order of quotes; they never enter a score |

### 7.3 Build pipeline (initial shipped content)

1. **Extract (Claude, offline).** Convert each brief into KB files:
   - **Papers:** [ACAD] §1-§11, plus the reference lists in [INF-A §7.4] and [INF-B §8].
   - **Claims:** each "Program rule/implication" section.
   - **Creators:** the JSON seed blocks in [INF-A §7.3] and [INF-B §6.3].
   - **Cash and limits data:** [SAV].
   - **CMAs:** [MKT §8].
   - **ETF universe:** seeded with VTI, VOO, VXUS, VT, BND, AGG, AVUV, AVDV, DFAC, AVGE, DFAW, QUAL, VLUE, SCHD, VIG, DGRO, RSP, QQQM, SCHG, SGOV, BIL, JEPI, JEPQ and IBIT. Enter expense ratios and other figures **only** after checking the issuer's page.
2. **Carry flags through.** Every `[UNVERIFIED]`, `[PARSE-UNCERTAIN]` or `[S]` tag in a brief becomes `verified: false` or `confidence: low` on the record. The UI never shows an unverified number as fact. [ACAD §0]
3. **Validate.** Run `python -m fintec.kb.verify --schema` (pydantic) and `--links` (HEAD requests; a 403 produces a warning, not a failure).
4. **Check DOIs.** `--dois` resolves each DOI through OpenAlex `works/doi:` and compares normalized titles. **CI fails on a title mismatch** [INF-A §7.4]. This would catch a regression of the three wrong DOIs the fact-checks found (Bessembinder, Anarkulova, Israelov & Nielsen).
5. **Write the manifest.** `MANIFEST.json` holds `kb_version` (CalVer, e.g. `2026.11.0`), a SHA-256 per file and `last_reviewed`. The app footer shows it. [MKT §9.3 step 5]
6. **Index.** Rebuild the FTS index at app start whenever the manifest checksum changes.

### 7.4 Refresh cadences and process

| Content | Cadence | How | Who |
|---|---|---|---|
| Rates (T-bill, FRED) | Daily | Automatic job | App |
| HYSA / CD / MMF quotes | Weekly | An in-app form writes `savings/hysa_rates.json`; the user confirms each rate on the bank's site | User |
| I bond rate | May 1 / Nov 1 | In-app reminder, then edit the JSON | User |
| FDIC national rates | Monthly | Manual (the FDIC offers no CSV) | User |
| IRS limits | Every Oct-Nov | Add a new `limits_2027.json` (2027 limits were not posted as of 2026-11-10). The app uses the file for the current tax year and warns in December if next year's file is missing | User / Claude |
| CMAs, GTM, FactSet | Quarterly / annual | Link-and-review list from [MKT §9.2], shown in-app as a "due for review" list | User / Claude |
| Creator principles and positions | Principles yearly; positions every 6 months | New RSS items arrive tagged. The user (or the LLM draft pipeline) proposes record updates as a git diff | User, optionally with LLM |
| Papers metadata | Weekly | OpenAlex job (about 100 calls) | App |
| Full KB re-research | Every 6-12 months | Re-run the Claude research workflow that produced `docs/research/*`, then repeat §7.3 | Claude |

**LLM-assisted refresh (optional).** `python -m fintec.jobs kb-draft --creator ben_felix` does the following:
1. Fetches new RSS items.
2. Pulls transcripts locally (rate-limited, opt-in).
3. Asks the LLM for a draft principle record.
4. Writes the draft to `knowledge_base/_drafts/` with `verified: false`.

The app never loads anything from `_drafts/`. A human reviews each draft and moves it into place. opencode, run inside the repo, can do the same thing interactively. [TECH §4.2]

**Copyright rule.** Ship only paraphrased summaries, short quotes and timestamped links. Transcripts stay in the git-ignored `data/cache/transcripts/`. [TECH §3.4]

---

## 8. Phased milestones (overview)

```
Phase 0 ─► Phase 1 ─► Phase 2 ─► Phase 3 ─► Phase 4 ─► Phase 5 (optional) ─► Phase 6
setup      savings     ETFs       stocks     experts     LLM                  polish
1 wk       3-4 wk      5-6 wk     5-6 wk     3-4 wk      2 wk                 3-4 wk
           ▲ USER ACTS ON $20K HERE
```

Phase 4 content work (creator JSON, papers JSON) can start in parallel with Phase 2, because it is mostly KB curation from the briefs. Phase 5 can be skipped entirely.

Each phase below lists: **goal, tasks, files, key signatures, definition of done (DoD), effort**.

---

## 9. Phase 0: Setup (1 week, 8-12 h)

**Goal:** a reproducible repo with CI, config, secrets handling, a DuckDB cache, and an empty Streamlit shell showing a data-source status panel.

### Tasks
1. `uv init`, pin Python 3.12, add the dependencies from §2.2, commit `uv.lock`.
2. Configure ruff, mypy (strict for `fintec.domain`, `fintec.rules`, `fintec.kb.models`) and pytest markers (`live`, `slow`). Add pre-commit with detect-secrets.
3. `settings.py`: pydantic-settings with `SecretStr` keys and `LLM_PROVIDER` in `none|ollama|openrouter|opencode`. Also load `config.toml` (rate limits, TTLs, creator allowlist, guardrail defaults). [TECH §5.5]
4. `cache/db.py` + `migrations/001_init.sql` (all tables in §5.2) + `schema_version`.
5. `http.py`: a `RateLimiter` token bucket and `make_client(source)` that returns a pooled httpx client with headers and timeouts. Shared tenacity policy for 429/5xx.
6. `data/base.py` + `data/chain.py` (skeleton in §16.1) with one trivial adapter (`ManualJsonSource`) so the chain is testable from day one.
7. `kb/models.py` + `kb/loader.py` with empty seed files and `MANIFEST.json`.
8. `app/Home.py` with `st.navigation`, a global disclaimer banner (SPEC §16.1), the **first-run acknowledgement gate** (SPEC §16.2, FR-108; accepted text version and timestamp stored in DuckDB), the sidebar data-source panel (key present / reachable / last success), and the KB version in the footer. `11_Settings.py` stub with key presence (never values) and source status.
9. GitHub Actions (or a local `make ci` if the repo stays private and offline): ruff, mypy, `pytest -m "not live"`, `kb verify --schema`.
10. `scripts/bootstrap.py`: runs migrations, creates `data/` folders, validates `.env`.

### Key signatures
```python
# settings.py
class Settings(BaseSettings):
    sec_user_agent: str | None = None
    fred_api_key: SecretStr | None = None
    tiingo_api_key: SecretStr | None = None
    finnhub_api_key: SecretStr | None = None
    alphavantage_api_key: SecretStr | None = None
    openalex_api_key: SecretStr | None = None
    openrouter_api_key: SecretStr | None = None
    llm_provider: Literal["none", "ollama", "openrouter", "opencode"] = "none"
    ollama_base_url: str = "http://localhost:11434/v1"
    opencode_url: str = "http://localhost:4096"

def get_settings() -> Settings: ...
def get_config() -> AppConfig: ...          # parsed config.toml (pydantic)

# cache/db.py
def connect(path: Path = DB_PATH, read_only: bool = False) -> duckdb.DuckDBPyConnection: ...
def migrate(conn: duckdb.DuckDBPyConnection) -> int: ...      # returns new schema version

# http.py
class RateLimiter:
    def __init__(self, rate: float, per: float = 1.0, burst: int = 1): ...
    def acquire(self) -> None: ...
def make_client(source: str) -> httpx.Client: ...
```

### Definition of done
- `uv run streamlit run app/Home.py` opens with a status panel and no errors when `.env` is empty.
- `uv run pytest` passes (at least the chain and TTL unit tests). CI is green.
- `python -m fintec.kb.verify --schema` passes on the empty KB.
- No secret is printable via `repr(settings)`.

---

## 10. Phase 1: Savings optimizer + profile (3-4 weeks, 36-49 h). **Deliver value first.**

**Goal:** answer "where should my $20K go right now?" with an emergency-fund target, a tiered cash plan, an after-tax comparison of cash vehicles at today's rates, and the account order of operations with 2026 room. All of it is grounded in [SAV] §1-§4 and [ACAD §11].

### 10.1 User inputs (Profile page)
From [SAV §4.1] and the open questions in [SAV §6]:

| Field | Type | Default | Note |
|---|---|---|---|
| `liquid_savings` | USD | 20,000 | |
| `monthly_essential_expenses` (M) | USD | **required** | Helper: itemize rent, utilities, groceries, insurance, minimum debt payments, transport, healthcare [SAV §2.2] |
| EF adjustment flags | checkboxes | none | The [SAV §2.2] table |
| `lean_conservative` | bool | **False** | When on, floors the target at 6 months ([ACAD §11] moderate default). Suggested (not forced) when the drawdown answer is "sell" |
| high-interest debts | list (balance, APR) | [] | APR > 8% counts as "high" [SAV §3.1] |
| known expenses ≤ 36 months | list (amount, date) | [] | Sinking funds |
| 401(k) match: % matched, cap % of salary, vesting, currently captured? | | | |
| salary, filing status, state | | single | Drives brackets, state rate, Roth eligibility |
| `t_fed`, `t_state` | derived, overridable | from brackets | |
| HSA eligible, HSA contributed YTD | | False | Includes bronze/catastrophic plans from 2026 [SAV §3.2] |
| IRA contributed for 2026 / 2027 | | 0 / 0 | 2026 contributions allowed until 2027-04-15 |
| pre-tax IRA balances | USD | 0 | Backdoor pro-rata check |
| risk profile | enum | moderate | |
| horizon | years | 10 | |
| current HYSA APY / bank | | | Underpaying warning |

The profile is stored in the DuckDB user tables (SPEC FR-106; local only) and can be exported to `data/user/profile.json`.

### 10.2 Calculations (all in `fintec.domain`)

| Function | Formula / logic | Source |
|---|---|---|
| `ef_target_months(flags, macro_lt_unemp_share, lean_conservative)` | `clamp(3.0 + Σadj, 3, 12)`. Add +0.5 if the BLS 27+ week share is above 25% (Nov 2026: 27.6%). Cap the combined backstop/stable-role reductions at −1.0. If `lean_conservative`, `max(result, 6)` | [SAV §2.2], [ACAD §11] |
| `ef_reference_markers(M)` | 3×M, 5×M (Tilbury), 6×M ([ACAD]), plus the computed target | [INF-B §6.4] |
| `tier_emergency_fund(ef_alloc, M, rates)` | tier1 checking 0.5×M (optional); tier2 HYSA = min(ef, 3×M); tier3 = rest in T-bills/SGOV/Treasury MMF **if** `after_tax_tbill > after_tax_hysa + 0.10pp`, else HYSA. I bonds never count as EF before 12 months | [SAV §4.3] |
| `after_tax_yield(vehicle, y, t_fed, t_state, exempt_share)` | HYSA/CD: `y(1−t_f−t_s)`. T-bill: `y(1−t_f)`. MMF: `y(1−t_f−t_s(1−exempt))`. I bond: `y(1−t_f)` (deferral noted) | [SAV §1.4] |
| `hysa_equivalent_of_tbill(y, t_fed, t_state)` | `y(1−t_f)/(1−t_f−t_s)` | [SAV §1.4]; golden: 4.22, 0.22, 0.093 → **4.79%** |
| `cd_break_even_state_rate(cd_apy, tbill, t_fed)` | solve `cd(1−t_f−s) = tbill(1−t_f)` | [SAV §1.5]; golden: 4.65, 4.54, 0.22 → **≈1.85%** |
| `cd_net_yield_if_broken(apy, m_months, penalty_months)` | `(apy·m/12 − penalty·apy/12)/(m/12)` | [SAV §1.5] |
| `ibond_composite(fixed, semiannual_infl)` | `fixed + 2·infl + fixed·infl` | [SAV §1.6]; golden: 1.40, 2.04 → **5.51%** (5.5086) |
| `ibond_effective_yield(r, months_held)` | 0 (not redeemable) if < 12; `r·(n−3)/n` if 12 ≤ n < 60; `r` after | [SAV §1.6] |
| `real_yield(nominal_after_tax, cpi)` | `(1+n)/(1+π) − 1` | [SAV §0] real-yield warning |
| `fdic_exposure_warnings(accounts)` | > $250K per bank per ownership category; fintech/BaaS intermediated → warning | [SAV §1.2] |
| `split_savings(inputs, today)` | The [SAV §4.2] algorithm: EF target → sinking funds → high-interest debt (never below starter EF = max(M, 2000)) → EF fill → SF fill → excess → match bridge → HSA → IRA (2026 room first, 2027 from Jan 1) → taxable / 401(k) increase | [SAV §4.2] |
| `money_waterfall(profile)` | Ordered steps with status (done / partial / todo) and evidence citations: starter EF → match → debt > 8% → full EF → HSA → IRA → 401(k) toward $24,500 → mega-backdoor (if plan allows) → taxable | [SAV §3.1], [INF-A §7.5 MONEY_WATERFALL] |
| `roth_vs_traditional(bracket, modifiers)` | [SAV §3.4] heuristic table + modifiers | [SAV §3.4] |
| `roth_ira_eligibility(magi, status, limits)` | Phase-out interpolation; above the range → backdoor explainer | [SAV §3.3] |
| `backdoor_taxable_fraction(basis, dec31_trad_balance, conversion)` | `1 − basis/(dec31 + conversion)` (simplified Form 8606) | [SAV §3.3] |
| `deploy_plan(excess, existing_portfolio, risk_profile)` | Lump sum by default; if moderate risk **and** excess > 25% of the existing portfolio, offer 3-6 monthly installments, labelled with the cost (lump sum won about 2/3 of the time historically) | [SAV §4.4], [ACAD §10] |
| `opportunity_cost(cash, years, r_equity_real, r_cash_real)` | `cash·((1+r_e)^y − (1+r_c)^y)`, user-adjustable, labelled "expected, not guaranteed" | [SAV §2.4] |

### 10.3 Guardrails (rules/savings.yaml)
From [SAV §4.6], [ACAD §11], [INF-B §6.4]:
- `NEVER_BELOW_STARTER_EF`: never allocate below the starter EF except to high-interest debt.
- `WARN_MATCH_NOT_CAPTURED`: excess > 0 but the match is not captured.
- `WARN_SHORT_GOAL_IN_EQUITY`: a known expense within 24 months is allocated to equities.
- `WARN_HYSA_UNDERPAYING`: HYSA APY < 3.0% while top-tier APYs are ≥ 4%.
- `WARN_FDIC_LIMIT`: more than $250K at one bank in one ownership category.
- `BLOCK_IBOND_AS_EF_BEFORE_12M`.
- `BLOCK_NON_INSURED_YIELD_AS_EF`: crypto or fintech "yield" products cannot count as EF (Jikh BlockFi/Voyager, Graham Yotta/Synapse lessons). [INF-B §2.4], [INF-A §4.5]
- `WARN_EF_IN_STOCKS_OR_BONDS` (2022-style correlated drawdown). [SAV §4.3]
- `STALE_RATE_7D`: rates older than 7 days are marked stale.
- `WARN_401K_IS_NOT_EF` (Kaplan-Violante "wealthy hand-to-mouth"). [ACAD §11]

### 10.4 Data in Phase 1
- **Live:** Treasury par curve CSV (3 Mo, 6 Mo, 1 Yr), FRED `DGS3MO`, `CPIAUCSL` (with the October 2025 gap: compute y/y only when both months exist [MKT §9.1]), `DFEDTARU/L`, and the BLS long-term unemployment share (FRED `UEMPMED` or hand-entered; series IDs unverified [SAV §5]).
- **Curated KB:** `hysa_rates.json` (Bankrate snapshot 2026-11-10, `promo` flags: e.g. CIT 4.35% promo vs 3.75% base), `cash_vehicles.json` (SGOV 3.85% SEC yield, BIL 3.86%, VMFXX/SPAXX as low-confidence, SWVXX 3.75%), `ibond_rates.json` (5.51% / 1.40%), `fdic_national_rates.json` (0.38% savings, 2026-10-19), `limits_2026.json`, `tax_brackets_2026.json`, `state_tax_rates.json` (top marginal rate per state, user-overridable).
- **User-entered:** their own bank's APY and MMF yields (sponsor pages load dynamically [SAV §1.3]).

### 10.5 Pages
- `0_Profile.py`: the form, with "why we ask" help text linking topic pages.
- `1_Savings_and_Emergency_Fund.py`:
  - EF target card showing the target, the markers and the adjustment breakdown.
  - A tier diagram.
  - An after-tax yield table: rows HYSA, Treasury MMF, government MMF, SGOV/BIL, 4/13/26/52-week bills, 6-month/1-year CDs, no-penalty CD, I bond. Columns: nominal, after-tax, real, liquidity, insurance, state-tax treatment, as_of badge.
  - Opportunity-cost chart.
  - "What the experts say about emergency funds" (static from KB in Phase 1, full panel in Phase 4).
- `2_Account_Order_of_Operations.py`:
  - The waterfall with step status.
  - The $20K split result (a Sankey chart).
  - IRA/HSA room for 2026 and 2027, with deadlines.
  - Roth vs Traditional recommendation.
  - Backdoor explainer.
  - SECURE 2.0 notes (PLESA, student-loan match, 2027 Saver's Match). [SAV §3.3a]
- **Export:** "Action checklist" as markdown/PDF (e.g. "Open FDIC-insured HYSA at a bank verified on BankFind; move $X; buy $Y of 13-week bills at your brokerage; contribute $Z to the 2026 Roth IRA by 2027-04-15").

### 10.6 Files
`domain/profile.py`, `domain/savings.py`, `domain/yields.py`, `domain/accounts.py`, `rules/models.py`, `rules/loader.py`, `knowledge_base/rules/savings.yaml`, `data/treasury_src.py`, `data/fred_src.py`, `data/manual.py`, `services/savings_service.py`, `app/pages/0_Profile.py`, `app/pages/1_...py`, `app/pages/2_...py`, `app/components/badges.py`, the KB files listed in 10.4, and `knowledge_base/topics/{emergency-fund,hysa-vs-tbills,order-of-operations,roth-vs-traditional,ibonds,fdic-insurance}.md`, plus tests.

### 10.7 Definition of done
- With M = 3,000, single, renter, stable W-2, no debt, match captured, no HSA, CA resident (22% / 9.3%): the app reproduces the [SAV §4.5] logic. With `lean_conservative=False`, the target is 4.5 months = $13,500 and about $6,500 is investable, routed to the 2026 Roth IRA. With the toggle on, the target is 6 months = $18,000 and $2,000 is investable. **Both are golden tests.**
- The [SAV §4.5] table (M = 2,500 / 3,500 / 4,500) is reproduced as a golden test.
- The golden formula values above pass: 4.79%, 1.85%, 5.51%.
- Every rate on screen has an as_of badge. Rates older than 7 days turn amber.
- Everything works with no API keys (keyless Treasury CSV + KB values).
- The user can print an action checklist and act on it.

**Effort:** domain + tests 12-15 h; data adapters 5-7 h; KB content 5-8 h (mostly Claude-drafted from [SAV]); UI 8-10 h.

### 10.8 Additional Phase 1 tasks (added by the SPEC traceability review, §0.5)
1. **Profile persistence (FR-101, FR-106):** DuckDB tables `user_profile`, `employer_plan`, `contribution_ytd`, `debt`, `known_expense`, `ef_adjustment`, `cash_account` (SPEC §7.1). JSON export to `data/user/profile.json`.
2. **Risk questionnaire (FR-102):** 8-10 items that separate tolerance from capacity; output score 1-10, label, equity range. Include the three WORKFLOW §1.1 questions (30%/2-year drawdown, 20%-drop lump-sum question, tracking-error tolerance). Golden fixtures per SPEC AC-1.2.
3. **IPS generator (FR-103-105):** `domain/ips.py` builds the IPS from the profile and questionnaire with the SPEC FR-104 defaults; named guardrails from `rules/guardrails.yaml`; out-of-range edits require a logged reason (AC-1.4); versioned; Markdown export. Rendered on `0_Profile.py`.
4. **First-run acknowledgement (FR-108)** wired to every personalized page.
5. **Tax on cash interest (FR-221):** `yields.annual_interest_tax(balance, y, vehicle, t_fed, t_state, exempt_share)` and a column "est. annual interest / est. tax" in the cash table, plus the 1099-INT/1099-DIV explainer topic `knowledge_base/topics/taxes-on-cash-interest.md`. Known-value test: $20,000 x 4.0% at 22% federal, 0% state gives $800 interest and $176 tax.

**Effort:** +6-9 h, included in the 36-49 h Phase 1 total.

---

## 11. Phase 2: ETF research (5-6 weeks, 58-80 h including §11.5)

**Goal:** an evidence-based ETF explorer and portfolio builder. Costs first, factor exposures with confidence intervals, look-through concentration, guardrails for a moderate investor.

### 11.1 Features
1. **ETF universe** from `etf_universe.json`, filterable by category (core US, core intl, global, bonds, factor/SCV, quality, dividend, T-bill, covered call, thematic, crypto, leveraged).
2. **Cost panel:** ER, fee-drag calculator (10/20/30-year dollar drag of an ER gap at a user-set return) [ACAD §1.5], tracking difference vs index (from total-return series), "cheapest equivalent" lookup.
3. **Performance panel:** total return only (never price-only, never yield-sorted [ACAD §5.1], [MKT §7.1]): CAGR, volatility, max drawdown, worst 12 months, rolling 3/5/10-year returns, drawdown chart. Sorting by 1-year return shows a Carhart (1997) warning. [ACAD §1.4]
4. **Factor regression:** CAPM / FF3 / FF5+MOM on monthly excess returns with Newey-West HAC (6 lags). Show loadings with 95% CIs, t-stats, R², `n_months`. Require at least 36 months; warn when R² < 0.8 for a "broad index" fund. Use Developed ex-US factors for international funds. [TECH §5.2], [ACAD §3.1]
5. **Holdings & look-through:** overlap matrix between selected ETFs; effective weight of the top 10 US names and of AI-linked industries; warn when a single name is over 10% of total exposure or AI-linked exposure is over 1.5× market weight. [MKT §2.2], [ACAD §7.1]
6. **Product warnings** (`rules/product_warnings.yaml`) [MKT §7], [INF-A §7.5]:
   - Covered call: "Yield ≠ return" badge above 6% yield; total return vs underlying at 1/3/5 years; upside-cap illustration; "not aligned with accumulation goal"; red warning for single-stock option-income funds. (Israelov & Nielsen 2015)
   - Buffer ETF: compare with a "simple equivalent" (e.g. 70% equity + 30% T-bills); warn on mid-period entry.
   - Thematic: Ben-David et al. (2023) warning; suggested cap of 5%.
   - Crypto: speculative satellite, default 0%, opt-in cap 5%, always show max drawdown. (Liu & Tsyvinski 2021)
   - Leveraged: `BLOCK_LEVERAGED_FOR_MODERATE` (hard warning). [INF-A §1.2]
   - Active: SPIVA card; ER gap and 10-year cost on $10K. [ACAD §1.2]
   - Private credit / interval funds: "not recommended by default". [MKT §7.6]
7. **ETF score** (`domain/scoring/etf_score.py`) using the [ACAD §12] weights: expense ratio 30, factor/goal fit 20, tracking difference 15, diversification 15, liquidity/spread 10, tax efficiency 10. The score ranks ETFs **within a category only**, and the UI shows each component.
8. **Portfolio builder:**
   - Inputs: target equity/bond split (default 80/20 for moderate [ACAD §9]), US/ex-US (default global cap weight ~64/36 [MKT §4.2]), optional factor tilt 0-30% [ACAD §3.1].
   - Account location suggestions [SAV §3.5]: bonds in 401(k)/IRA; SCV/factor in Roth; broad US and international in taxable (foreign tax credit). Only suggest location tweaks when the estimated saving is over 0.1%/yr.
   - Guardrails: international ≥ 20% of equities; factor tilt ≤ 30%; leverage 0; behavior-gap badge on thematic/sector funds. [ACAD §6.2, §12]
   - The 401(k) menu can be entered by hand (fund name, ER, category), so the builder works with the user's real plan.
9. **Expected-return ranges:** CMA consensus band from `cma.json` (Vanguard 4.8-6.8% US equity, JPM 7.5%, etc.). Default is the conservative end for a moderate user. Historical factor premia get a ×0.5 haircut (McLean & Pontiff). [MKT §8], [ACAD §4.1]

### 11.2 Key signatures
```python
# domain/returns.py
def total_return_index(adj_close: pd.Series) -> pd.Series: ...
def to_monthly_returns(adj_close: pd.Series) -> pd.Series: ...
def cagr(index: pd.Series) -> float: ...
def max_drawdown(index: pd.Series) -> tuple[float, date, date]: ...   # depth, peak, trough
def rolling_cagr(index: pd.Series, years: int) -> pd.Series: ...
def tracking_difference(fund_tr: pd.Series, index_tr: pd.Series) -> float: ...

# domain/fees.py
def fee_drag(principal: float, years: int, gross_return: float,
             er_a: float, er_b: float, annual_contrib: float = 0.0) -> FeeDragResult: ...

# domain/factors.py
FactorModel = Literal["CAPM", "FF3", "FF5", "FF5+MOM"]
def factor_regression(fund_ret: pd.Series, factors: pd.DataFrame,
                      model: FactorModel = "FF5+MOM", hac_lags: int = 6,
                      min_months: int = 36) -> FactorRegressionResult: ...

# domain/overlap.py
def holdings_overlap(a: pd.DataFrame, b: pd.DataFrame) -> float: ...   # sum of min weights
def look_through(portfolio: dict[str, float], holdings: dict[str, pd.DataFrame]) -> pd.DataFrame: ...

# domain/scoring/etf_score.py
def score_etf(m: EtfMetrics, peers: list[EtfMetrics], rules: EtfScoringRules,
              goal: InvestorGoal) -> ScoreCard: ...

# domain/scoring/guardrails.py
def check_portfolio(p: PortfolioSpec, profile: UserProfile, rules: GuardrailRules) -> list[Finding]: ...

# data/french_src.py
def parse_french_csv(raw: bytes) -> dict[str, pd.DataFrame]: ...   # blocks: monthly/annual/daily, /100, -99.99 → NaN
```

### 11.3 Files
`data/yfinance_src.py`, `data/tiingo_src.py`, `data/alphavantage_src.py`, `data/french_src.py`, `data/aqr_src.py`, `data/etf_holdings/*`, `domain/returns.py`, `domain/fees.py`, `domain/factors.py`, `domain/overlap.py`, `domain/scoring/etf_score.py`, `domain/scoring/guardrails.py`, `knowledge_base/rules/{etf_scoring,guardrails,product_warnings}.yaml`, `knowledge_base/etfs/*.json`, `knowledge_base/market/cma.json`, `knowledge_base/topics/{index-vs-active,factor-investing,covered-calls,dividends,international,concentration,lump-sum-vs-dca}.md`, `services/etf_service.py`, `app/pages/3_ETF_Explorer.py`, `app/pages/4_Portfolio_and_Accounts.py`, `jobs/refresh.py` (prices, factors, holdings), tests.

### 11.4 Definition of done
- **Golden regression:** VTI on FF5+MOM over 2010-2025 gives Mkt-RF ≈ 1.0 (0.95-1.05), |SMB| < 0.1, R² > 0.95. AVUV gives SMB > 0.5 and HML > 0.3. [TECH §5.7]
- The French CSV parser passes on fixture files (monthly, annual and daily blocks; missing-value codes).
- The yfinance → Tiingo fallback is tested with cassettes; the divergence flag fires on a synthetic 2% gap.
- JEPI is shown with the "Yield ≠ return" badge and a 5-year total return vs SPY (expected roughly 7.6% vs 11.4% per [MKT §7.1], live-computed).
- The portfolio builder produces a 3-fund (or 401(k)-menu) allocation with zero guardrail violations at default settings, and warnings appear when the user enters 100% US or 40% thematic.
- Every chart and metric shows its source and as_of.

**Effort:** adapters + parsers 12-15 h; returns/factor/overlap domain + tests 12-15 h; scoring + rules 6-8 h; KB ETF/CMA content 4-6 h; UI 8-11 h.

### 11.5 Portfolio & allocation module (SPEC Module 8; page `4_Portfolio_and_Accounts.py`)
These SPEC "M" requirements were not tasked in earlier drafts. They ship in Phase 2 because they reuse the returns, factor and look-through code.
1. **Holdings input (FR-801):** manual tables per account plus CSV import (Fidelity/Schwab/Vanguard templates and a generic column mapper); lots with cost basis and date for taxable. `data/user/` keeps the raw CSVs. Test: SPEC AC-8.1.
2. **Household view and guardrail panel (FR-802, FR-811):** allocation by account, asset class, region, factor, sector and look-through names; live status of every IPS rule.
3. **Model portfolios (FR-803):** three-fund 51/29/20, VT + BND, three-fund + 10-20% tilt, target-date reference, core + 10% satellite; each with ER, CMA band and stress drawdowns. Labelled "examples, not advice".
4. **Rebalancing engine (FR-804):** +/-5 pp absolute or +/-25% relative bands, checked at each monthly review; cash flow first, then inside tax-advantaged accounts, then taxable with a capital-gains estimate. Trade list for manual execution. Test: SPEC AC-8.2.
5. **Contribution planner (FR-805):** per-paycheck routing from the waterfall and IPS weights; YTD vs limits.
6. **Lump-sum vs staged planner (FR-806):** UI over `deploy_plan()` from Phase 1.
7. **Stress tests (FR-807):** `domain/stress.py` replays the SPEC §10.7 historical scenarios and hypothetical shocks on asset-class proxies (French Mkt / Dev ex-US / EM / RF, AGG). "Would you hold?" prompt. Test: SPEC AC-8.3.
8. **Monte Carlo (FR-808):** `domain/montecarlo.py`, 10,000 paths, block-bootstrap and lognormal modes, conservative CMA default, factor premium x 0.5, optional skewed single-stock mode. Seeded and reproducible (AC-8.4); under 5 s (NFR-11).
9. **Turnover tracker (FR-810):** annualized from logged trades; warn above 50% (AC-8.5).

**Files:** `domain/portfolio.py`, `domain/rebalance.py`, `domain/stress.py`, `domain/montecarlo.py`, `data/broker_csv.py`, `services/portfolio_service.py`, `app/pages/4_Portfolio_and_Accounts.py`, tests.
**Effort:** +18-25 h. Phase 2 becomes about 5-6 weeks (58-80 h); see §18.

---

## 12. Phase 3: Stock scoring, the satellite screener (5-6 weeks, 53-72 h including §12.9)

**Goal:** a disciplined, evidence-weighted way to research individual stocks as a **satellite** (at most 10-20% of equities). It uses free EDGAR fundamentals, transparent scores, Carlin-style valuation, a Carlson-style moat checklist, a Plain Bagel-style research process, and a decision journal. **It never says "buy".** It says "passes N of M evidence-based screens; here is why; here is the base rate."

### 12.1 Framing that appears on every stock page
- **Base-rate card:** "Most individual US stocks underperformed T-bills over their lifetimes. About 4% of firms created all net wealth (Bessembinder 2018). The index owns them by construction." [ACAD §2.1]
- **Sharpe (1991) banner:** treat picks as a satellite. [ACAD §1.1]
- **Overlap note:** "You already own X% of this company through your ETFs." This comes from the Phase 2 look-through. [ACAD §7.1]

### 12.2 Score model (`knowledge_base/rules/stock_scoring.yaml`)

Weights follow [ACAD §12]. Components follow QMJ (Asness, Frazzini & Pedersen 2019), Novy-Marx (2013) and Fama-French (2015). Scores are **cross-sectional percentiles within a peer universe**: by default, the S&P 500 plus S&P 400 constituents by sector, taken from EDGAR frames. They are not absolute thresholds.

**Canonical definitions are in SPEC §10.3** (metric formulas §10.3.1, normalization §10.3.2, style weights §10.3.3, red flags §10.3.4, dividend safety §10.3.5, DCF §10.3.6). The YAML excerpt in §16.3 mirrors them. Summary for the default `quality_value` style:

| Block | Weight | Metrics (each is a sector-relative percentile 0-100; ≥ 20 peers per sector, else universe-wide with a "thin sector" flag) | Evidence |
|---|---|---|---|
| **Quality** | 35 | GP/Assets (at least 25% of the block), operating profitability, ROIC, 5-year change in GP/A, margin stability, accruals, net payout | [ACAD §3.3, §3.4] |
| **Value** | 30 | EBIT/EV, FCF yield, Carlin owner-earnings yield (OCF − capex − SBC)/market cap, B/M (≤ 20% of block), sales/EV | [ACAD §3.1, §8.2], [INF-B §3.2] |
| **Safety** | 15 | Net debt/EBITDA (inverted), interest coverage, 60-month beta (inverted), 10-year ROE volatility (inverted), Piotroski F-score | [ACAD §3.4, §3.5] |
| **Momentum** | 10 | 12-1 month total return | [ACAD §3.2] (≤ 10%) |
| **Dividend growth** | 10 | Consecutive years of DPS increases, 5-year DPS CAGR, FCF payout in the 30-70% band. **Optional:** if the user turns it off, the weight moves to Quality | [ACAD §12], Carlson interest |

**Styles** (SPEC FR-404, §10.3.3): `quality_value` (default), `dividend_growth`, `quality_growth` (Carlson-style; adds a 10% contested growth block) and `deep_value` (Carlin-style). Each is a re-weighting of the same sub-scores plus style gates; the UI can show a stock under all four side by side.

**Flags** (shown separately; any **blocking** flag sets the label to "Blocked" whatever the score; each soft flag is −5, max −20): `VALUE_TRAP`, `FALLING_KNIFE`, `LOTTERY`, `YIELD_TRAP_WARN` (soft), `YIELD_TRAP_FAIL` (blocking), `UNPROFITABLE` (soft; blocking is a moderate-profile option), `HIGH_LEVERAGE`, `NEG_OWNER_EARNINGS`, `DILUTION`, `DATA_DISCREPANCY` (soft, score greyed), `PAID_PROMOTION` (blocking, overridable with a logged acknowledgement; e.g. GRML), `MICROCAP_ILLIQUID` (blocking), `STALE_DATA` (blocking until refresh). Thresholds: SPEC §10.3.4.

**Composite:** `score = Σ w_b · sub_b`, where each block sub-score is the weighted mean of its available metric percentiles. If less than 60% of a block's weight has data, the block is null and the composite is labelled "insufficient data" (SPEC §10.3.2). Labels: ≥ 70 plus the FR-418 conditions → "Buy candidate (satellite)"; 55-69 → "Watch"; < 55 → "Pass".

**Evidence tier gate:** only `canonical` signals can carry more than 10% weight (Hou-Xue-Zhang 2020). [ACAD §4.2] The YAML loader enforces this, and a rule file that violates it fails validation.

**Multiple-testing guard:** if the user edits weights or builds custom screens, the app counts the variants tried and shows "You tested N variants; the best is expected to look good by chance" (Harvey-Liu-Zhu 2016). [ACAD §4.3]

### 12.3 Valuation tools (`domain/valuation.py`)
- **Carlin two-stage owner-earnings DCF** [INF-B §3.2]:
  - Owner earnings/share = OCF − capex − SBC.
  - Years 1-5 grow at g₁ = min(5-year revenue CAGR, 15%) by default; years 6-10 at g₂ = 5% (sector-average default) [SPEC §10.3.6].
  - Terminal value = year-10 owner earnings × terminal multiple (default 15; worst 10, best 20). If OE₀ ≤ 0 the DCF is not computed.
  - Fixed 10% discount rate (user-editable).
  - Three scenarios (worst / normal / best) with example weights 0.3 / 0.4 / 0.3.
  - The output includes `worst_case_positive: bool`.
  - Labelled **framework, B-rated**: the result is driven by the hurdle-rate choice.
- **Multiples in context:** P/E, EV/EBIT and P/B vs the company's own 10-year history and the sector median (Plain Bagel multiples video). [INF-A §2.4]
- **Reverse DCF:** the growth rate implied by today's price at a 10% discount rate. This is often more useful than a forward DCF.

### 12.4 Checklists and journal
- **Plain Bagel research process** (`STOCK_RESEARCH_CHECKLIST`) [INF-A §2.4]: screen → understand the business → finances → strategy → valuation → written thesis.
- **Carlson moat checklist** [INF-B §4.2]: high barriers to entry? majority market share? entrenched brand or distribution? top of the food chain? reliance on high-risk R&D? growth only through acquisitions? high leverage? would it survive a bad recession? Free-text answers are stored in the journal.
- **Decision journal** (`9_Decision_Journal.py`) [ACAD §6.1, §6.3]:
  - Thesis, what would make me wrong, position size, the base-rate acknowledgement checkbox.
  - **48-72 hour cooling-off timestamp** before the checklist can be marked "complete".
  - Sell checklist: "Would you buy this today at this price?"
  - Turnover tracker: warn when annual turnover is above 50%.
  - Wash-sale checker across **all** accounts, including IRA purchases and DRIP (61-day window; Rev. Rul. 2008-5). [ACAD §6.3], [INF-B §2.5]
- **Position sizing guardrails:** single stock warn at > 5% / strong warning at > 10% of investable assets; all individual stocks warn at > 10% / strong warning at > 20% of equities; "diversified" label only with ≥ 30 stocks across ≥ 8 GICS sectors. [ACAD §2.1, §7.1, §12]
- **Analyst panel** (Finnhub recommendation trends, earnings surprises), clearly labelled "what Wall Street says", with no weight in the score.

### 12.5 EDGAR pipeline
1. Download `company_tickers.json` (ticker → CIK) weekly.
2. Weekly: conditional GET of `companyfacts.zip` (about 1.42 GB). Stream-extract the JSON → normalize to long format → Parquet under `data/parquet/edgar/` → DuckDB `edgar_facts`. Run it from Task Scheduler at night: expect 10-30 minutes and several GB of disk (risk R-05). [TECH §1.2]
   - **Light mode (default for v1):** fetch per-company `companyfacts/CIK##########.json` only for the user's watchlist plus a peer universe of about 900 tickers (S&P 500 + 400), at 5 req/s ≈ 3 minutes. Use the `frames` API for sector-wide single metrics.
3. Apply the tag-fallback map and dedupe → TTM builder (sum of the last 4 quarterly flows; balance-sheet items from the latest instant) → `fundamentals_ttm`.
4. Price-dependent ratios join `prices` at the latest close.
5. Foreign filers (`ifrs-full`, 20-F/40-F) are marked "limited coverage" in v1.

### 12.6 Key signatures
```python
# domain/fundamentals.py
TAG_FALLBACKS: dict[str, list[str]]           # metric -> ordered us-gaap tags
def build_ttm(facts: pl.DataFrame, metric: str, as_of: date) -> float | None: ...
def compute_ratios(ttm: Fundamentals, price: float, shares: float) -> Ratios: ...

# domain/valuation.py
def owner_earnings_per_share(ocf: float, capex: float, sbc: float, shares: float) -> float: ...
def two_stage_value(oe0: float, g1: float, g2: float, terminal_pe: float,
                    discount: float = 0.10, years1: int = 5, years2: int = 5) -> float: ...
def scenario_value(scenarios: list[Scenario]) -> ScenarioResult: ...
def implied_growth(price: float, oe0: float, terminal_pe: float, discount: float = 0.10) -> float: ...

# domain/scoring/stock_score.py   (skeleton in §16.3)
def percentile_rank(values: pd.Series, higher_is_better: bool) -> pd.Series: ...
def score_universe(metrics: pd.DataFrame, rules: StockScoringRules) -> pd.DataFrame: ...
def explain(ticker: str, scored: pd.DataFrame, rules: StockScoringRules) -> ScoreCard: ...
```

### 12.7 Files
`data/edgar_src.py`, `data/finnhub_src.py`, `domain/fundamentals.py`, `domain/valuation.py`, `domain/scoring/stock_score.py`, `knowledge_base/rules/stock_scoring.yaml`, `knowledge_base/topics/{stock-picking-base-rates,quality-investing,value-investing,dividend-growth,valuation-dcf}.md`, `services/stock_service.py`, `app/pages/5_Stock_Research.py`, `app/pages/9_Decision_Journal.py`, golden fixtures, tests.

### 12.8 Definition of done
- **Golden scoring test:** a frozen 20-company fixture universe (metrics JSON) produces exactly the stored ranks, block scores and flags. Any change to weights or formulas requires updating the golden file in the same commit.
- The DCF passes known-value tests (see §17.2).
- The TTM builder passes on EDGAR fixtures with duplicate facts, restated filings and a tag change (Revenues → RevenueFromContract...).
- A blocking flag forces the "Blocked" label whatever the composite score; each of the four style weight vectors sums to 1.0 (SPEC AC-4.3).
- The journal enforces the cooling-off period; the wash-sale checker catches an IRA repurchase within 30 days.
- Each score row shows a "why" panel (every metric, percentile, weight and citation).

**Effort:** EDGAR adapter + TTM 15-20 h; scoring + flags + rules 10-12 h; valuation 5-7 h; journal + checklists 6-8 h; UI 8-10 h; tests are included in each item.

### 12.9 Additional Phase 3 tasks (SPEC traceability)
1. **Per-style scorecards (FR-404)** from the `styles:` block in `stock_scoring.yaml`, with style gates and a side-by-side view.
2. **DCF sensitivity grid (FR-408)** and **dividend safety score (FR-409, SPEC §10.3.5)** in `domain/valuation.py` and `domain/dividends.py`, with total-return display and the taxable-account tax drag.
3. **Pre-trade checklist (FR-902)** implements all 15 items in SPEC §10.8; WORKFLOW §6.8 is the user-facing view of the same list.
4. **Watchlist (FR-417)** with research-again dates and data-event triggers only (dividend cut, earnings miss, price at or below worst-case intrinsic value). No price alerts.
5. **Risk & reward quadrant (FR-412, S):** scatter of implied 10-year return vs permanent-loss risk.
6. **Freshness gate (FR-109):** `services/decision_service.ready_blockers()` lists every input with a red badge; amber inputs need an "I verified at source" tick.
7. **Wash-sale checker (FR-313/FR-904 core):** 61-day window across every account, including IRA/Roth, 401(k) auto-buys and DRIP (Rev. Rul. 2008-5). Needed here for the sell checklist; the TLH partner list comes in Phase 6.

**Effort:** +8-12 h. Phase 3 becomes about 5-6 weeks (53-72 h).

---

## 13. Phase 4: Expert lens and research library (3-4 weeks, 36-49 h including §13.4)

**Goal:** "What are the experts saying, and what research backs it?", with the academic verdict ranked first, creators shown with grades, conflicts and dates, and a browsable library of papers with evidence tiers.

### 13.1 Features
1. **Creator KB load:** 8 profiles in `knowledge_base/creators/` from the [INF-A §7.3] and [INF-B §6.3] seeds, plus records written by Claude from the narrative sections for Tilbury, Jikh, Carlin and Carlson (the INF-B seeds are partial), in the unified schema of §7.2. The channel allowlist is in `channels.json`; the Sven Carlin impersonator channel `UC67NIxAks8wXAmGPoyY7r5A` is explicitly blocked. [TECH §3.1]
2. **"What the experts say about X"** (`6_What_Experts_Say.py`):
   - Topic picker, mapped to principle `category`: emergency fund, order of operations, index vs active, factors, dividends/covered calls, international, crypto, leverage, mortgage prepay, market timing.
   - Layout: (a) **Research verdict** card from `claims.json` with evidence tier and papers; (b) **Consensus** list, e.g. the [INF-A §5] agreements (EF first, capture the match, kill high-interest debt, low-cost index core, don't time, fees matter); (c) **Disagreements** side by side (dividend/covered-call income: Felix and Bagel vs Whiteboard; factor tilts; satellites; mortgage prepay); (d) per-creator cards with the quote paraphrase, grade A-E, `fit_for_profile`, conflict badges (sponsored, affiliate, creator-owned product, paid promo, legal), date, and stale badge.
   - **Ticker lookup:** "Who mentioned SCHD / META / GRML?" Shows dated positions only, never as recommendations, with expiry and conflict flags. [INF-B §6.4]
3. **Source-reliability lens** (Plain Bagel): a "Check this finfluencer claim" form (sponsored? paid promo? credential verifiable on FINRA BrokerCheck? self-reported performance? US-relevant?). [INF-A §2.2]
4. **Research library** (`7_Research_Library.py`):
   - Filter by topic, tier, peer-reviewed vs practitioner vs preprint.
   - Each card: citation, DOI link, plain-English finding, practical implication, caveats, sample period, the app rules it drives, `cited_by_count` (refreshed weekly via OpenAlex).
   - The guardrail papers (Harvey-Liu-Zhu, McLean-Pontiff, Hou-Xue-Zhang vs Jensen-Kelly-Pedersen) are pinned to every factor topic. [TECH §2.6]
   - **"Find more research":** OpenAlex search plus Semantic Scholar `tldr`, with results marked **"not reviewed by KB"** until the user promotes them (promotion writes to `_drafts/`). [TECH §2.6]
5. **Creator feed:** weekly RSS poll of the allowlist plus Rational Reminder, with a rule-based topic tagger (keywords: ETF, index, factor, dividend, valuation, HYSA, Roth, 401k, market timing, crypto, AI bubble ...). Macro-alarm titles are tagged `commentary` (Jikh/Graham 2026 drift). Show "new since last review" and conflict notes parsed from descriptions (sponsor, affiliate, "compensated"). [TECH §3.5], [INF-B §2.2]
6. **Market context page** (`8_Market_Context.py`) [MKT §0, §9]:
   - **Regime banner:** forward P/E (manual FactSet value), CAPE, 10-year real yield (DFII10), CPI y/y (with the Oct 2025 gap note), fed funds, HY OAS. Each has its percentile vs history. **Descriptive, never prescriptive.**
   - CMA consensus band.
   - The "Popular ideas" explainer cards (AI concentration, international rotation, SCV, covered calls, buffers, active ETFs, thematic, crypto, private credit), each with proponents vs skeptics, evidence and the app's rule.
   - Every narrative paragraph is templated from snapshot JSON, so numbers refresh without rewriting prose.

### 13.2 Key signatures
```python
# kb/models.py (excerpt)
class Principle(BaseModel):
    id: str; creator: str; type: Literal["principle", "framework", "commentary"]
    statement: str; category: Category; parameters: dict[str, Any] = {}
    evidence_alignment: Literal["A", "B", "C", "D", "E", "NA"]
    evidence_refs: list[str] = []; risk_level: Literal["low", "medium", "high"] = "medium"
    sources: list[SourceRef]; verified: bool; as_of: date; app_rule: str | None = None
    applies_to_us: bool = True; stale_after_days: int | None = None

# services/experts_service.py
def expert_view(topic: Category, profile: UserProfile, today: date) -> ExpertPanel: ...
def ticker_mentions(ticker: str, today: date) -> list[PositionView]: ...
def fit_for_profile(p: Principle, profile: UserProfile) -> Literal["good", "caution", "poor"]: ...

# creators/tagger.py
def tag_video(title: str, description: str) -> set[str]: ...
def detect_conflicts(description: str) -> list[ConflictHint]: ...   # "sponsored", "affiliate", "compensated"

# research/openalex.py
def get_work_by_doi(doi: str) -> PaperMeta | None: ...
def search_works(q: str, per_page: int = 25) -> list[PaperMeta]: ...
def rebuild_abstract(inverted_index: dict[str, list[int]]) -> str: ...
```

### 13.3 Definition of done
- All 8 creators load and validate. Each has at least 4 principles with sources and dates. Every unverified claim shows an "unverified" badge.
- `kb verify --dois` passes in CI (all DOIs resolve to matching titles).
- For the "dividends" topic the page shows: the MM 1961 and Hartzmark-Solomon verdict first; Felix (A), Bagel (A), Jikh (A, "Don't Do Dividends"), Whiteboard income funds (D, sponsored), Carlson's past dividend phase (C, superseded).
- GRML lookup shows the paid-promotion red banner.
- The RSS job handles a 404/500 with retry and never deletes a channel.
- The regime banner shows 6 indicators with as_of and percentiles, and no "sell" language (a unit test greps rendered text for forbidden phrases such as "sell now" or "get out").

**Effort:** KB curation (Claude-drafted, user-reviewed) 10-14 h; experts service + page 8-10 h; library + OpenAlex 6-8 h; feed + tagger 4-5 h; market page 5-7 h.

### 13.4 Additional Phase 4 tasks (SPEC traceability)
1. **Consensus/dissent computation (FR-503, SPEC §10.6):** `experts_service.consensus(topic)` with item weights `trust x grade_factor x freshness`; consensus at |S| >= 0.6 with at least 5 non-silent creators. The academic verdict is shown first and never averaged in.
2. **"What would X emphasize?" (FR-504):** category matching that returns each creator's 2-3 most relevant principles for an ETF, stock or savings context, with no LLM.
3. **Conflict-of-interest cards (FR-505)** for all 8 creators (PWL/DFA; GRML paid promo; FTX and Yotta/Synapse; BlockFi/Voyager; $499 platform; Qualtrim; Trading 212/eToro/Webull affiliates).
4. **Jurisdiction translation (FR-506):** TFSA/RRSP to Roth/401(k); ISA to Roth IRA; UCITS tickers to US-domiciled equivalents with a PFIC warning.
5. **Video classification and expiry (FR-508):** commentary 60 days, picks 120 days, positions 365 days.
6. **"I just saw a pitch" form** on `6_What_Experts_Say.py` (WORKFLOW §8), reusing the tagger, the conflict detector and the FINRA BrokerCheck checklist (FR-512).

**Effort:** +6-9 h.

---

## 14. Phase 5: Optional LLM assistant (2 weeks, 20-30 h)

**Goal:** natural-language Q&A over the KB and computed results, with citations, and a fully working app when it is off.

### Tasks
1. `llm/base.py`, `null.py`, `openai_compat.py`, `router.py` (skeleton in §16.4).
2. OpenRouter free-model discovery (`GET /models`, filter `:free` or zero pricing, rank, cache 24 h). Quota display via `GET /api/v1/key`. Re-verify the limits at build time: about 20 RPM and about 50/day under $10 of lifetime credit are **unverified**. [TECH §4.1, §7]
3. Ollama provider (OpenAI-compatible `/v1`), with model chosen from `ollama list`.
4. `opencode.py` (experimental): HTTP client for `opencode serve` (port 4096, optional `OPENCODE_SERVER_PASSWORD` basic auth), with a fallback to `subprocess.run(["opencode", "run", prompt], timeout=...)`. Behind a feature flag. The repo moved to `github.com/anomalyco/opencode`, so check the CLI flags at install time. [TECH §4.2]
5. `llm/redact.py`: before any **remote** call, strip dollar amounts tied to the user's accounts, the profile, the journal, names and emails. The profile is never included in remote prompts; for remote providers, the "explain my plan" feature sends only the generic computed structure ("EF target 6 months; tier split 3/3"), with no balances. Local Ollama may receive full context.
6. `llm/rag.py`: FTS retrieval → prompt template (`prompts/qa_v1.md`) → citation check → number guard → disclaimer.
7. Page `10_Ask_the_KB.py` plus "Explain this" buttons on score cards and the savings plan.
8. KB draft pipeline (`jobs kb-draft`): transcript summarization into `_drafts/`. Batch about 40/day on OpenRouter. [TECH §4.1]

### System prompt (v1)
> You are an educational research assistant. Answer ONLY from the numbered sources provided. Cite sources as [S1], [S2]. If the sources do not cover the question, say so. Do not invent numbers; every number you write must appear in the sources. Do not recommend buying or selling specific securities. Present creator opinions as opinions, with their evidence grade. End with: "Educational information, not individualized advice."

### Definition of done
- `LLM_PROVIDER=none`: every page works, the Ask page shows "LLM disabled; showing the most relevant KB sources" with the top-k chunks.
- Mocked-provider tests: a 429 falls through to the next provider; an uncited answer is rejected; an answer containing a number not in the context is rejected; cache hits avoid network calls; redaction removes balances from remote prompts (property test with random profiles).
- A golden Q&A set (15 questions, e.g. "Should my emergency fund be in SGOV?", "Do dividends beat the index?") is checked for citation presence and the forbidden-phrase list (manual run with a live model; CI uses mocks).

**Effort:** providers + router 6-8 h; RAG + guards 6-8 h; redaction 3-4 h; UI 3-5 h; draft pipeline 3-5 h.

---

## 15. Phase 6: Polish, hardening, tests (3-4 weeks, 35-55 h including the v1.0 items)

### Tasks
1. **Live smoke suite** (`pytest -m live`), run weekly by Task Scheduler. It covers yfinance schema drift (`.info` keys, dividend-yield units), Treasury CSV header, FRED, French file names, issuer holdings URLs, RSS feeds, OpenAlex. Failures write to `fetch_log` and show a sidebar alert. [TECH §1.1, §5.7]
2. **Refresh runbooks** in `docs/runbooks/`: weekly rates, semiannual I bond, annual IRS limits, quarterly CMA/GTM, creator review, full KB re-research.
3. **"Due for review" dashboard:** lists every KB record past `stale_after_days`, and every `[UNVERIFIED]` item still shown.
4. **Backups:** weekly copy of the DuckDB file plus `data/user/` to `data/backups/` (keep 8), and a restore command.
5. **Performance:** precompute scores after refresh; `st.cache_data` on views; check cold start is under 10 s (NFR-11; aim for 5 s) and a page switch under 1 s on cached data.
6. **Accessibility and clarity:** consistent badge colors, plain-English tooltips, glossary page, printable action plans.
7. **Error UX:** every adapter error becomes a friendly message plus a "stale since" badge. No stack traces in the UI.
8. **Coverage targets:** `fintec.domain` ≥ 95% lines, `fintec.rules` and `fintec.kb` ≥ 90%, adapters ≥ 70% (cassettes).
9. **Year-rollover test:** with freezegun at 2027-01-02, the app selects `limits_2027.json` or shows a "2027 limits missing" warning, and IRA room for both 2026 (until 2027-04-15) and 2027 appears.
10. **Security pass:** detect-secrets baseline, no keys in logs, `.env` permissions note, no personal data in any remote LLM prompt (test).
11. README: setup in 10 minutes, key sign-up links, first-run walkthrough.
12. **v1.0 "S" requirements** (SPEC §15): factor-tilt page (FR-311), TLH partner suggestions (FR-313), Finnhub "Wall Street view" (FR-414), active-share check of creator and satellite holdings (FR-511), "new since last visit" feed (FR-510), personal behavior gap IRR vs TWR (FR-809), scheduled reviews and journal analytics (FR-905, FR-906), tax-year cash-interest export (FR-222), optional encryption of user tables (FR-106, C). About +15-20 h; Phase 6 becomes 3-4 weeks.

### Definition of done
- All suites are green; coverage targets are met.
- The weekly live suite has run for 2 consecutive weeks without unexplained failures.
- A fresh clone on a new machine works: `uv sync && uv run python scripts/bootstrap.py && uv run streamlit run app/Home.py`, in under 15 minutes with no keys.
- Runbooks exist for every manual refresh.

---

## 16. Starter code skeletons

These are working-quality starting points, not pseudo-code. They follow the layering rules in §3: `domain` code is pure, and `data` and `llm` code does the I/O.

### 16.1 Data-provider interface and fallback chain

```python
# src/fintec/data/base.py
from __future__ import annotations

from dataclasses import dataclass, field
from datetime import date, datetime, timezone
from enum import StrEnum
from typing import Any, Protocol, runtime_checkable

import pandas as pd


class Capability(StrEnum):
    PRICES_DAILY_ADJ = "prices_daily_adj"
    FUNDAMENTALS = "fundamentals"
    KEY_STATS = "key_stats"
    TBILL_YIELDS = "tbill_yields"
    MACRO_SERIES = "macro_series"
    FACTOR_RETURNS = "factor_returns"
    ETF_HOLDINGS = "etf_holdings"
    ETF_METADATA = "etf_metadata"
    ANALYST_NEWS = "analyst_news"
    CASH_RATES = "cash_rates"
    CAPE = "cape"
    PAPERS_META = "papers_meta"
    CREATOR_FEED = "creator_feed"


class SourceError(Exception):
    """Base class: any adapter failure that should trigger fallback."""

class MissingKey(SourceError): ...
class RateLimited(SourceError): ...
class QuotaExhausted(SourceError): ...
class SchemaDrift(SourceError): ...      # payload no longer matches the pydantic/pandera model
class NotCovered(SourceError): ...       # e.g. ticker unknown to this source


@dataclass(frozen=True)
class DataRequest:
    capability: Capability
    symbols: tuple[str, ...] = ()
    start: date | None = None
    end: date | None = None
    params: dict[str, Any] = field(default_factory=dict)


@dataclass(frozen=True)
class Availability:
    ok: bool
    reason: str = ""          # "missing FRED_API_KEY", "daily quota used 25/25", "circuit open"


@dataclass
class ProviderResult:
    data: pd.DataFrame
    source: str
    fetched_at: datetime = field(default_factory=lambda: datetime.now(timezone.utc))
    as_of: date | None = None
    confidence: str = "high"            # high | medium | low
    warnings: list[str] = field(default_factory=list)
    covered: frozenset[str] = frozenset()   # symbols actually returned


@runtime_checkable
class Provider(Protocol):
    name: str
    capabilities: frozenset[Capability]

    def available(self) -> Availability: ...
    def fetch(self, req: DataRequest) -> ProviderResult: ...
```

```python
# src/fintec/data/chain.py
from __future__ import annotations

import logging
import time
from dataclasses import dataclass, field, replace
from typing import Callable, Sequence

import pandas as pd

from fintec.data.base import Capability, DataRequest, Provider, ProviderResult, SourceError

log = logging.getLogger(__name__)


@dataclass
class _Circuit:
    failures: int = 0
    open_until: float = 0.0


class FallbackChain:
    """Try providers in order. Each provider fills only the symbols still missing.

    - Skips providers whose available() is False (missing key, quota, open circuit).
    - Opens a per-provider circuit after `max_failures` consecutive failures.
    - Writes every successful result to the cache via `on_result`, tagged with its source.
    - Never raises for a partial failure; the caller reads from the cache afterwards
      (stale-while-error).
    """

    def __init__(
        self,
        capability: Capability,
        providers: Sequence[Provider],
        on_result: Callable[[ProviderResult], None],
        validate: Callable[[pd.DataFrame], None] | None = None,
        max_failures: int = 3,
        cooldown_s: float = 900.0,
    ) -> None:
        bad = [p.name for p in providers if capability not in p.capabilities]
        if bad:
            raise ValueError(f"providers {bad} do not serve {capability}")
        self.capability = capability
        self.providers = list(providers)
        self.on_result = on_result
        self.validate = validate
        self.max_failures = max_failures
        self.cooldown_s = cooldown_s
        self._circuits: dict[str, _Circuit] = {p.name: _Circuit() for p in providers}

    def run(self, req: DataRequest) -> ChainReport:
        missing = set(req.symbols)
        report = ChainReport(capability=self.capability)
        for p in self.providers:
            if req.symbols and not missing:
                break
            circuit = self._circuits[p.name]
            if circuit.open_until > time.monotonic():
                report.skipped[p.name] = "circuit open"
                continue
            avail = p.available()
            if not avail.ok:
                report.skipped[p.name] = avail.reason
                continue
            sub = replace(req, symbols=tuple(sorted(missing))) if req.symbols else req
            try:
                res = p.fetch(sub)
                if self.validate is not None:
                    self.validate(res.data)
            except SourceError as e:
                circuit.failures += 1
                if circuit.failures >= self.max_failures:
                    circuit.open_until = time.monotonic() + self.cooldown_s
                report.errors[p.name] = f"{type(e).__name__}: {e}"
                log.warning("source %s failed for %s: %s", p.name, self.capability, e)
                continue
            circuit.failures = 0
            self.on_result(res)
            report.served[p.name] = sorted(res.covered) if req.symbols else ["*"]
            missing -= set(res.covered)
            if not req.symbols:          # non-symbol requests: first success wins
                break
        report.unresolved = sorted(missing)
        return report


@dataclass
class ChainReport:
    """Shown in the sidebar 'Data sources' panel and written to fetch_log."""
    capability: Capability
    served: dict[str, list[str]] = field(default_factory=dict)
    skipped: dict[str, str] = field(default_factory=dict)
    errors: dict[str, str] = field(default_factory=dict)
    unresolved: list[str] = field(default_factory=list)


def cross_validate_prices(a: pd.DataFrame, b: pd.DataFrame, tol: float = 0.01) -> list[str]:
    """Return tickers whose adjusted closes diverge by > tol on any common date [TECH §1.13]."""
    m = a.merge(b, on=["ticker", "date"], suffixes=("_a", "_b"))
    rel = (m["adj_close_a"] / m["adj_close_b"] - 1).abs()
    return sorted(m.loc[rel > tol, "ticker"].unique())
```

```python
# src/fintec/data/treasury_src.py  (example adapter: keyless, primary for T-bill yields)
from __future__ import annotations

import io
from datetime import date

import pandas as pd

from fintec.data.base import (Availability, Capability, DataRequest, ProviderResult,
                              SchemaDrift, SourceError)
from fintec.http import make_client

URL = ("https://home.treasury.gov/resource-center/data-chart-center/interest-rates/"
       "daily-treasury-rates.csv/{year}/all?type=daily_treasury_yield_curve"
       "&field_tdr_date_value={year}&page&_format=csv")
EXPECTED = {"Date", "1 Mo", "3 Mo", "6 Mo", "1 Yr"}


class TreasuryParCurveSource:
    name = "treasury_par_curve"
    capabilities = frozenset({Capability.TBILL_YIELDS})

    def __init__(self) -> None:
        self._http = make_client(self.name)

    def available(self) -> Availability:
        return Availability(ok=True)

    def fetch(self, req: DataRequest) -> ProviderResult:
        year = (req.end or date.today()).year
        try:
            r = self._http.get(URL.format(year=year))
            r.raise_for_status()
        except Exception as e:  # httpx errors -> SourceError for the chain
            raise SourceError(str(e)) from e
        df = pd.read_csv(io.StringIO(r.text))
        if not EXPECTED.issubset(df.columns):
            raise SchemaDrift(f"unexpected columns: {list(df.columns)[:8]}")
        df["Date"] = pd.to_datetime(df["Date"]).dt.date
        long = (df.melt(id_vars="Date", var_name="tenor", value_name="yield_pct")
                  .dropna().rename(columns={"Date": "date"}))
        long["yield"] = long["yield_pct"] / 100.0       # store decimals; unit = coupon-equivalent
        return ProviderResult(data=long[["date", "tenor", "yield"]], source=self.name,
                              as_of=long["date"].max(), confidence="high")
```

### 16.2 Emergency-fund calculator and savings split (pure domain)

```python
# src/fintec/domain/savings.py
"""Emergency fund sizing, tiering and the $20K split.

Sources: [SAV] §2.2 (scored months model), §4.2 (split algorithm), §4.3 (tiers),
         §4.6 (guardrails); [ACAD] §11 (6x default for a moderate profile).
"""
from __future__ import annotations

from dataclasses import dataclass, field
from datetime import date
from enum import StrEnum

BASE_MONTHS = 3.0
MIN_MONTHS, MAX_MONTHS = 3.0, 12.0
CONSERVATIVE_FLOOR_MONTHS = 6.0
STARTER_EF_MIN_USD = 2_000.0
LT_UNEMPLOYMENT_TRIGGER = 0.25    # BLS share unemployed 27+ weeks [SAV §2.2]


class EfFlag(StrEnum):
    SINGLE_INCOME = "single_income"                  # +1.0
    VARIABLE_INCOME = "variable_income"              # +1.5
    SELF_EMPLOYED = "self_employed"                  # +3.0 (replaces VARIABLE_INCOME)
    LOW_JOB_SECURITY = "low_job_security"            # +1.0
    SPECIALIZED_ROLE = "specialized_role"            # +0.5
    DEPENDENTS = "dependents"                        # +1.0
    HOMEOWNER_OR_OLD_CAR = "homeowner_or_old_car"    # +0.5
    HDHP_NO_HSA_CASH = "hdhp_no_hsa_cash"            # +0.5
    HIGH_MEDICAL = "high_medical"                    # +0.5
    DUAL_STABLE_INCOME = "dual_stable_income"        # -1.0
    VERY_SECURE_ROLE = "very_secure_role"            # -0.5
    ROTH_BASIS_BACKSTOP = "roth_basis_backstop"      # -0.5


ADJUSTMENTS: dict[EfFlag, float] = {
    EfFlag.SINGLE_INCOME: 1.0, EfFlag.VARIABLE_INCOME: 1.5, EfFlag.SELF_EMPLOYED: 3.0,
    EfFlag.LOW_JOB_SECURITY: 1.0, EfFlag.SPECIALIZED_ROLE: 0.5, EfFlag.DEPENDENTS: 1.0,
    EfFlag.HOMEOWNER_OR_OLD_CAR: 0.5, EfFlag.HDHP_NO_HSA_CASH: 0.5, EfFlag.HIGH_MEDICAL: 0.5,
    EfFlag.DUAL_STABLE_INCOME: -1.0, EfFlag.VERY_SECURE_ROLE: -0.5,
    EfFlag.ROTH_BASIS_BACKSTOP: -0.5,
}
_REDUCERS = {EfFlag.VERY_SECURE_ROLE, EfFlag.ROTH_BASIS_BACKSTOP}   # combined cap -1.0


@dataclass(frozen=True)
class EfTarget:
    months: float
    amount: float
    breakdown: dict[str, float]            # label -> months contributed (for the UI)
    markers: dict[str, float]              # "3x", "5x (Tilbury)", "6x (ACAD default)" -> USD
    notes: list[str] = field(default_factory=list)


def ef_target(
    monthly_essentials: float,
    flags: set[EfFlag],
    lt_unemployment_share: float | None = None,
    lean_conservative: bool = False,   # default off: SPEC §10.1 / WORKFLOW §2.3 use the computed 4.5 months
) -> EfTarget:
    if monthly_essentials <= 0:
        raise ValueError("monthly essential expenses must be > 0")
    flags = set(flags)
    if EfFlag.SELF_EMPLOYED in flags:
        flags.discard(EfFlag.VARIABLE_INCOME)

    breakdown: dict[str, float] = {"base": BASE_MONTHS}
    reducers = 0.0
    for f in sorted(flags):
        adj = ADJUSTMENTS[f]
        if f in _REDUCERS:
            reducers += adj
        else:
            breakdown[f.value] = adj
    if reducers:
        breakdown["reducers_capped"] = max(reducers, -1.0)
    if lt_unemployment_share is not None and lt_unemployment_share > LT_UNEMPLOYMENT_TRIGGER:
        breakdown["macro_long_term_unemployment"] = 0.5

    raw = sum(breakdown.values())
    months = min(max(raw, MIN_MONTHS), MAX_MONTHS)
    notes: list[str] = []
    if lean_conservative and months < CONSERVATIVE_FLOOR_MONTHS:
        notes.append(f"Raised from {months:.1f} to 6.0 months (lean-conservative default, "
                     "moderate risk profile [ACAD §11]).")
        months = CONSERVATIVE_FLOOR_MONTHS

    m = monthly_essentials
    return EfTarget(
        months=months, amount=round(months * m, 2), breakdown=breakdown,
        markers={"3x": 3 * m, "5x (Tilbury)": 5 * m, "6x (ACAD default)": 6 * m},
        notes=notes,
    )


def starter_ef(monthly_essentials: float) -> float:
    return max(monthly_essentials, STARTER_EF_MIN_USD)


@dataclass(frozen=True)
class KnownExpense:
    amount: float
    due: date


@dataclass(frozen=True)
class SplitInputs:
    savings: float
    monthly_essentials: float
    ef: EfTarget
    high_interest_debt: float = 0.0
    known_expenses: tuple[KnownExpense, ...] = ()
    match_gap_annual: float = 0.0          # extra yearly deferral needed to capture full match
    hsa_eligible: bool = False
    hsa_room: float = 0.0
    ira_room: float = 7_500.0              # current tax-year room; from limits_<year>.json


@dataclass
class SplitResult:
    ef_target: float
    ef_alloc: float
    sinking_target: float
    sinking_alloc: float
    debt_paydown: float
    match_bridge: float
    hsa: float
    ira: float
    taxable_or_401k: float
    ef_shortfall: float
    warnings: list[str] = field(default_factory=list)


def _months_until(d: date, today: date) -> int:
    return (d.year - today.year) * 12 + (d.month - today.month)


def split_savings(x: SplitInputs, today: date) -> SplitResult:
    """[SAV §4.2] waterfall. Every dollar is assigned exactly once."""
    if x.savings < 0:
        raise ValueError("savings must be >= 0")
    sf_target = sum(k.amount for k in x.known_expenses if 0 <= _months_until(k.due, today) <= 36)

    floor = starter_ef(x.monthly_essentials)
    debt = min(x.high_interest_debt, max(0.0, x.savings - floor))   # never below starter EF
    remaining = x.savings - debt

    ef_alloc = min(x.ef.amount, remaining); remaining -= ef_alloc
    sf_alloc = min(sf_target, remaining); remaining -= sf_alloc

    months_left = 12 - today.month + 1
    bridge = min(remaining, x.match_gap_annual * months_left / 12); remaining -= bridge
    hsa = min(remaining, x.hsa_room) if x.hsa_eligible else 0.0; remaining -= hsa
    ira = min(remaining, x.ira_room); remaining -= ira

    res = SplitResult(
        ef_target=x.ef.amount, ef_alloc=ef_alloc, sinking_target=sf_target,
        sinking_alloc=sf_alloc, debt_paydown=debt, match_bridge=bridge, hsa=hsa, ira=ira,
        taxable_or_401k=remaining, ef_shortfall=max(0.0, x.ef.amount - ef_alloc),
    )
    if x.high_interest_debt > debt:
        res.warnings.append("High-interest debt remains; route new monthly savings to it first.")
    if res.ef_shortfall > 0:
        res.warnings.append("Emergency fund not yet full; finish it before investing more.")
    return res


@dataclass(frozen=True)
class EfTiers:
    checking_buffer: float     # outside EF math, informational
    hysa: float                # instant access tier
    treasury_tier: float       # SGOV / T-bill ladder / Treasury MMF
    treasury_tier_reason: str


def tier_emergency_fund(ef_alloc: float, monthly_essentials: float,
                        after_tax_hysa: float, after_tax_tbill: float,
                        min_edge: float = 0.0010) -> EfTiers:
    """[SAV §4.3]: up to 3 months in HYSA; the rest in Treasuries only if they win after tax."""
    hysa = min(ef_alloc, 3 * monthly_essentials)
    rest = max(0.0, ef_alloc - hysa)
    if rest and after_tax_tbill > after_tax_hysa + min_edge:
        return EfTiers(0.5 * monthly_essentials, hysa, rest,
                       f"T-bills win after tax by {(after_tax_tbill - after_tax_hysa) * 100:.2f} pp")
    return EfTiers(0.5 * monthly_essentials, hysa + rest, 0.0,
                   "HYSA is within 0.10 pp after tax; keep it simple")
```

```python
# src/fintec/domain/yields.py  [SAV §1.4-§1.6]
def after_tax_hysa(y: float, t_fed: float, t_state: float) -> float:
    return y * (1 - t_fed - t_state)

def after_tax_tbill(y: float, t_fed: float) -> float:
    return y * (1 - t_fed)                                  # state/local exempt

def after_tax_mmf(y: float, t_fed: float, t_state: float, exempt_share: float) -> float:
    return y * (1 - t_fed - t_state * (1 - exempt_share))

def hysa_equivalent_of_tbill(y: float, t_fed: float, t_state: float) -> float:
    return y * (1 - t_fed) / (1 - t_fed - t_state)

def cd_break_even_state_rate(cd_apy: float, tbill: float, t_fed: float) -> float:
    # cd*(1-t_fed-s) = tbill*(1-t_fed)  ->  s = (1-t_fed)*(1 - tbill/cd)
    return (1 - t_fed) * (1 - tbill / cd_apy)

def ibond_composite(fixed: float, semiannual_infl: float) -> float:
    return fixed + 2 * semiannual_infl + fixed * semiannual_infl

def ibond_effective_yield(r: float, months_held: int) -> float | None:
    if months_held < 12:
        return None                                         # cannot redeem
    if months_held < 60:
        return r * (months_held - 3) / months_held          # 3-month interest penalty
    return r
```

### 16.3 Scoring model (rules-driven, explainable)

```yaml
# knowledge_base/rules/stock_scoring.yaml (excerpt)
version: "2026.11.0"
universe: {default: "sp500+sp400", min_peers_per_sector: 20}   # SPEC §10.3.2
coverage: {min_block_coverage: 0.6}   # SPEC §10.3.2: < 60% of block weight with data -> block null
blocks:
  quality:
    weight: 0.35
    citations: [asness_qmj_2019, novy_marx_2013, fama_french_2015]
    metrics:
      # metric list = SPEC §10.3.1 "Quality" block
      - {id: gp_assets,        higher_is_better: true,  weight: 0.25, tier: canonical}   # >= 25% of block
      - {id: op_profitability, higher_is_better: true,  weight: 0.15, tier: canonical}
      - {id: roic,             higher_is_better: true,  weight: 0.15, tier: canonical}
      - {id: profit_growth_5y, higher_is_better: true,  weight: 0.15, tier: canonical}
      - {id: margin_stability, higher_is_better: true,  weight: 0.10, tier: canonical}
      - {id: accruals,         higher_is_better: true,  weight: 0.10, tier: canonical}   # -(NI-CFO)/TA
      - {id: net_payout,       higher_is_better: true,  weight: 0.10, tier: canonical}
  value:
    weight: 0.30
    citations: [fama_french_1993, lakonishok_1994]
    metrics:
      # metric list = SPEC §10.3.1 "Value" block (EBIT/EV replaces separate E/P and EV/EBIT)
      - {id: ebit_ev,             higher_is_better: true,  weight: 0.30, tier: canonical}
      - {id: fcf_yield,           higher_is_better: true,  weight: 0.25, tier: canonical}
      - {id: owner_earnings_yld,  higher_is_better: true,  weight: 0.15, tier: contested}   # 0.30 x 0.15 = 4.5% overall
      - {id: book_to_market,      higher_is_better: true,  weight: 0.15, tier: canonical}   # <= 20% of block
      - {id: sales_ev,            higher_is_better: true,  weight: 0.15, tier: canonical}
  safety:
    weight: 0.15
    citations: [asness_qmj_2019, frazzini_pedersen_2014]
    metrics:
      # metric list = SPEC §10.3.1 "Safety" block
      - {id: net_debt_ebitda,   higher_is_better: false, weight: 0.25, tier: canonical}
      - {id: interest_coverage, higher_is_better: true,  weight: 0.20, tier: canonical}
      - {id: beta_60m,          higher_is_better: false, weight: 0.20, tier: canonical}
      - {id: earnings_vol_10y,  higher_is_better: false, weight: 0.20, tier: canonical}   # stdev(ROE, 10 y)
      - {id: piotroski_f,       higher_is_better: true,  weight: 0.15, tier: canonical}
  momentum:
    weight: 0.10
    citations: [jegadeesh_titman_1993, daniel_moskowitz_2016]
    metrics: [{id: mom_12_1, higher_is_better: true, weight: 1.0, tier: canonical}]
  dividend_growth:
    weight: 0.10
    optional: true
    reassign_to: quality
    citations: [hartzmark_solomon_2019, miller_modigliani_1961]
    metrics:
      - {id: div_growth_years, higher_is_better: true,  weight: 0.5, tier: convention}
      - {id: dps_cagr_5y,      higher_is_better: true,  weight: 0.3, tier: convention}
      - {id: fcf_payout,       higher_is_better: false, weight: 0.2, tier: convention}
  growth:                      # used only by the quality_growth style
    weight: 0.0
    citations: []
    metrics:
      - {id: revenue_cagr_5y,  higher_is_better: true, weight: 0.5, tier: contested}
      - {id: oe_cagr_5y,       higher_is_better: true, weight: 0.5, tier: contested}
styles:                        # SPEC §10.3.3; each row sums to 1.0; default style = quality_value
  quality_value:   {quality: 0.35, value: 0.30, safety: 0.15, momentum: 0.10, dividend_growth: 0.10, growth: 0.0}
  dividend_growth: {quality: 0.40, value: 0.25, safety: 0.20, momentum: 0.05, dividend_growth: 0.10, growth: 0.0,
                    gates: ["div_yield > 0", "div_growth_years >= 5", "dividend_safety >= 60"]}
  quality_growth:  {quality: 0.45, value: 0.20, safety: 0.15, momentum: 0.10, dividend_growth: 0.0, growth: 0.10,
                    gates: ["revenue_cagr_5y > 0", "gp_assets >= sector_median", "moat_checklist_complete"]}
  deep_value:      {quality: 0.25, value: 0.45, safety: 0.20, momentum: 0.05, dividend_growth: 0.05, growth: 0.0,
                    gates: ["dcf_worst >= price or override_reason"]}
penalties: {soft_flag: -5, max_total: -20}
labels: {buy_candidate_satellite: 70, watch: 55}   # >= 70 + FR-418 conditions; 55-69 Watch; < 55 Pass
flags:                         # canonical table: SPEC §10.3.4
  - {id: VALUE_TRAP,         severity: soft,     when: "value_pct >= 70 and (gp_assets_pct <= 30 or fcf_ttm < 0 or ni_ttm <= 0)"}
  - {id: FALLING_KNIFE,      severity: soft,     when: "value_pct >= 70 and mom_12_1_pct <= 10"}
  - {id: LOTTERY,            severity: soft,     when: "vol_1y_pct >= 90"}
  - {id: YIELD_TRAP_WARN,    severity: soft,     when: "div_yield > 2 * sector_median_yield or eps_payout > 0.8 or fcf_payout > 1.0"}
  - {id: YIELD_TRAP_FAIL,    severity: blocking, when: "div_yield > 2 * sector_median_yield and fcf_payout > 1.0"}
  - {id: UNPROFITABLE,       severity: soft,     when: "op_income_ttm < 0"}          # blocking if profile.unprofitable_blocks
  - {id: HIGH_LEVERAGE,      severity: soft,     when: "net_debt_ebitda > 4 or interest_coverage < 3"}
  - {id: NEG_OWNER_EARNINGS, severity: soft,     when: "oe_negative_years_5 >= 3"}
  - {id: DILUTION,           severity: soft,     when: "diluted_shares_cagr_3y > 0.05"}
  - {id: DATA_DISCREPANCY,   severity: soft,     when: "max_provider_gap > 0.10 or missing_share > 0.30"}
  - {id: PAID_PROMOTION,     severity: blocking, when: "paid_promotion_mention_365d", overridable_with_ack: true}
  - {id: MICROCAP_ILLIQUID,  severity: blocking, when: "market_cap < 3e8 or adv_3m < 1e6"}
  - {id: STALE_DATA,         severity: blocking, when: "days_since_last_filing > 210"}
```

```python
# src/fintec/domain/scoring/stock_score.py
from __future__ import annotations

from dataclasses import dataclass, field

import numpy as np
import pandas as pd

from fintec.rules.models import StockScoringRules   # pydantic model of the YAML above

CANONICAL_MAX_WEIGHT_FOR_OTHERS = 0.10   # [ACAD §4.2]: non-canonical signals cap at 10% overall


def validate_rules(rules: StockScoringRules) -> None:
    total = sum(b.weight for b in rules.blocks.values())
    if not np.isclose(total, 1.0):
        raise ValueError(f"block weights sum to {total}, expected 1.0")
    for name, b in rules.blocks.items():
        if not np.isclose(sum(m.weight for m in b.metrics), 1.0):
            raise ValueError(f"metric weights in block {name} must sum to 1.0")
        for m in b.metrics:
            effective = b.weight * m.weight
            if m.tier != "canonical" and effective > CANONICAL_MAX_WEIGHT_FOR_OTHERS + 1e-9:
                raise ValueError(f"{m.id} ({m.tier}) has effective weight {effective:.3f} > 0.10")


def percentile_rank(values: pd.Series, higher_is_better: bool) -> pd.Series:
    """0-100 percentile within the given series; NaN stays NaN."""
    pct = values.rank(pct=True, method="average") * 100
    return pct if higher_is_better else 100 - pct + (100 / values.notna().sum())


def score_universe(metrics: pd.DataFrame, rules: StockScoringRules,
                   use_dividend_block: bool = True) -> pd.DataFrame:
    """metrics: index=ticker, columns include 'sector' and every metric id.
    Returns block scores, composite, and per-metric percentiles (suffix _pct)."""
    validate_rules(rules)
    out = pd.DataFrame(index=metrics.index)
    blocks = dict(rules.blocks)
    weights = {k: b.weight for k, b in blocks.items()}
    if not use_dividend_block and "dividend_growth" in blocks:
        target = blocks["dividend_growth"].reassign_to or "quality"
        weights[target] += weights.pop("dividend_growth")
        blocks.pop("dividend_growth")

    for name, block in blocks.items():
        cols, ws = [], []
        for m in block.metrics:
            pct = metrics.groupby("sector")[m.id].transform(
                lambda s, hib=m.higher_is_better: percentile_rank(s, hib))
            out[f"{m.id}_pct"] = pct
            cols.append(f"{m.id}_pct"); ws.append(m.weight)
        mat = out[cols].to_numpy(dtype=float)
        w = np.array(ws)
        present = ~np.isnan(mat)
        coverage = (present * w).sum(axis=1)
        weighted = np.nansum(mat * w, axis=1)
        block_score = np.where(coverage >= rules.coverage.min_block_coverage,
                               weighted / np.where(coverage == 0, 1, coverage), np.nan)
        out[f"{name}_score"] = block_score
        out[f"{name}_coverage"] = coverage

    bcols = [f"{n}_score" for n in blocks]
    bw = np.array([weights[n] for n in blocks])
    mat = out[bcols].to_numpy(dtype=float)
    present = ~np.isnan(mat)
    denom = (present * bw).sum(axis=1)
    out["composite"] = np.where(denom > 0, np.nansum(mat * bw, axis=1) / np.where(denom == 0, 1, denom), np.nan)
    out["composite_coverage"] = denom
    return out


@dataclass
class ScoreCard:
    ticker: str
    composite: float | None
    blocks: dict[str, float | None]
    metrics: dict[str, tuple[float | None, float | None]]   # id -> (raw value, percentile)
    flags: list[tuple[str, str]] = field(default_factory=list)  # (flag id, severity)
    passes_screen: bool = True
    citations: dict[str, list[str]] = field(default_factory=dict)
    base_rate_note: str = ("Most individual US stocks underperformed T-bills over their lifetime; "
                           "~4% of firms created all net wealth (Bessembinder 2018).")
```

Flag evaluation uses a tiny safe-expression evaluator (a whitelist of names and comparison and boolean operators, parsed with `ast`). It never uses `eval`.

### 16.4 LLM provider abstraction

```python
# src/fintec/llm/base.py
from __future__ import annotations
from typing import Literal, Protocol
from pydantic import BaseModel

class LLMMessage(BaseModel):
    role: Literal["system", "user", "assistant"]
    content: str

class LLMResult(BaseModel):
    text: str
    model: str
    provider: str
    cached: bool = False
    remote: bool = False                      # True -> prompt left the machine

class LLMProvider(Protocol):
    name: str
    remote: bool
    def available(self) -> bool: ...
    def complete(self, messages: list[LLMMessage], *, max_tokens: int = 800,
                 temperature: float = 0.2) -> LLMResult: ...

class LLMError(Exception): ...
class LLMRateLimited(LLMError): ...
class LLMUnavailable(LLMError): ...
```

```python
# src/fintec/llm/openai_compat.py
from __future__ import annotations
import time
import httpx
from tenacity import retry, retry_if_exception_type, stop_after_attempt, wait_exponential
from fintec.llm.base import LLMMessage, LLMRateLimited, LLMResult, LLMUnavailable


class OpenAICompatProvider:
    """One client for OpenRouter (remote) and Ollama (local) - both OpenAI-compatible [TECH §4.4]."""

    def __init__(self, name: str, base_url: str, api_key: str | None, model: str | None,
                 remote: bool, extra_headers: dict[str, str] | None = None) -> None:
        self.name, self.remote, self.model = name, remote, model
        headers = {"Authorization": f"Bearer {api_key or 'none'}", **(extra_headers or {})}
        self._http = httpx.Client(base_url=base_url, headers=headers, timeout=60)

    def available(self) -> bool:
        try:
            return self._http.get("/models", timeout=5).status_code == 200
        except httpx.HTTPError:
            return False

    def list_models(self) -> list[dict]:
        r = self._http.get("/models"); r.raise_for_status()
        return r.json().get("data", [])

    @retry(stop=stop_after_attempt(3), wait=wait_exponential(min=2, max=30),
           retry=retry_if_exception_type(httpx.TransportError))
    def complete(self, messages: list[LLMMessage], *, max_tokens: int = 800,
                 temperature: float = 0.2) -> LLMResult:
        if not self.model:
            raise LLMUnavailable(f"{self.name}: no model selected")
        r = self._http.post("/chat/completions", json={
            "model": self.model, "messages": [m.model_dump() for m in messages],
            "max_tokens": max_tokens, "temperature": temperature})
        if r.status_code == 429:
            raise LLMRateLimited(r.text[:200])
        if r.status_code >= 500:
            raise LLMUnavailable(f"{self.name} {r.status_code}")
        r.raise_for_status()
        body = r.json()
        return LLMResult(text=body["choices"][0]["message"]["content"], model=self.model,
                         provider=self.name, remote=self.remote)


def pick_openrouter_free_model(models: list[dict], preferences: list[str]) -> str | None:
    """Never hard-code model IDs; the free roster rotates [TECH §4.1]."""
    def is_free(m: dict) -> bool:
        p = m.get("pricing", {}) or {}
        return m.get("id", "").endswith(":free") or (str(p.get("prompt")) == "0"
                                                     and str(p.get("completion")) == "0")
    free = [m for m in models if is_free(m)]
    for pref in preferences:                       # user-ordered substrings from config.toml
        for m in free:
            if pref in m["id"]:
                return m["id"]
    free.sort(key=lambda m: m.get("context_length", 0), reverse=True)
    return free[0]["id"] if free else None


def make_openrouter(api_key: str, preferences: list[str]) -> OpenAICompatProvider:
    p = OpenAICompatProvider("openrouter", "https://openrouter.ai/api/v1", api_key, None, remote=True,
                             extra_headers={"X-Title": "FinTec Research Assistant"})
    p.model = pick_openrouter_free_model(p.list_models(), preferences)
    return p


def make_ollama(base_url: str, model: str) -> OpenAICompatProvider:
    return OpenAICompatProvider("ollama", base_url, "ollama", model, remote=False)
```

```python
# src/fintec/llm/null.py
from fintec.llm.base import LLMMessage, LLMResult

class NullProvider:
    name, remote = "none", False
    def available(self) -> bool: return True
    def complete(self, messages: list[LLMMessage], **_: object) -> LLMResult:
        return LLMResult(text="(LLM disabled - showing curated KB sources and computed results only.)",
                         model="none", provider="none")
```

```python
# src/fintec/llm/router.py
from __future__ import annotations
import hashlib, json, logging
from fintec.llm.base import LLMError, LLMMessage, LLMProvider, LLMResult
from fintec.llm.null import NullProvider
from fintec.llm.redact import redact_messages

log = logging.getLogger(__name__)

class LLMRouter:
    """Ordered fallback (e.g. ollama -> openrouter -> none) with a forever cache keyed on
    sha256(provider, model, template_version, messages) [TECH §5.4]."""

    def __init__(self, providers: list[LLMProvider], cache, template_version: str) -> None:
        self.providers = [*providers, NullProvider()]
        self.cache, self.template_version = cache, template_version

    def _key(self, p: LLMProvider, msgs: list[LLMMessage]) -> str:
        blob = json.dumps([p.name, getattr(p, "model", None), self.template_version,
                           [m.model_dump() for m in msgs]], sort_keys=True)
        return hashlib.sha256(blob.encode()).hexdigest()

    def complete(self, messages: list[LLMMessage], *, contains_personal: bool, **kw) -> LLMResult:
        for p in self.providers:
            if p.remote and contains_personal:
                msgs = redact_messages(messages)             # never send balances/PII remotely
            else:
                msgs = messages
            key = self._key(p, msgs)
            if (hit := self.cache.get(key)) is not None:
                return hit.model_copy(update={"cached": True})
            if not p.available():
                continue
            try:
                res = p.complete(msgs, **kw)
            except LLMError as e:
                log.warning("LLM provider %s failed: %s", p.name, e)
                continue
            if p.name != "none":
                self.cache.put(key, res)
            return res
        return NullProvider().complete(messages)
```

```python
# src/fintec/llm/rag.py (post-checks only)
import re
CITE = re.compile(r"\[S(\d+)\]")
NUM = re.compile(r"(?<![\w.])-?\d+(?:[.,]\d+)*%?")

def citations_ok(answer: str, n_sources: int) -> bool:
    ids = {int(i) for i in CITE.findall(answer)}
    return bool(ids) and all(1 <= i <= n_sources for i in ids)

def numbers_grounded(answer: str, context: str) -> bool:
    norm = lambda s: s.replace(",", "").rstrip("%")
    ctx = {norm(x) for x in NUM.findall(context)}
    body = CITE.sub("", answer)
    return all(norm(x) in ctx for x in NUM.findall(body))
```

---

## 17. Testing strategy

### 17.1 Test pyramid

| Layer | Location | Runs | Tools | Covers |
|---|---|---|---|---|
| Unit (pure domain) | `tests/unit/domain/` | Every commit | pytest | Every formula, with known values (17.2) |
| Property | `tests/property/` | Every commit | hypothesis | Invariants (17.3) |
| Golden | `tests/golden/` | Every commit | pytest + JSON snapshots | Scoring, savings split, factor regression (17.4) |
| Rules / KB | `tests/unit/kb/` | Every commit | pydantic, `kb verify --schema` | Schema validity, weight sums, tier gate, IDs referenced exist |
| Contract (adapters) | `tests/contract/` | Every commit (offline) | vcrpy cassettes, fixture files | Parsers and pydantic models per source |
| Provider mocks | `tests/unit/data/`, `tests/unit/llm/` | Every commit | Fake providers | Fallback chain, circuit breaker, router, redaction (17.5) |
| UI smoke | `tests/ui/` | Every commit | `streamlit.testing.v1.AppTest` | Each page renders with an empty `.env` and seeded cache |
| Live | `tests/live/` (`@pytest.mark.live`) | Weekly + before upgrades | real network | Schema drift, URL changes, quota changes |
| DOI / link check | `kb verify --dois --links` | CI weekly + KB edits | OpenAlex | Citation integrity |

### 17.2 Known-value unit tests (financial formulas)

| Test | Inputs | Expected | Source |
|---|---|---|---|
| HYSA-equivalent of T-bill | y = 4.22%, t_fed 22%, t_state 9.3% | 4.791% (±0.005) | [SAV §1.4] |
| No-income-tax state | y = 4.22%, 22%, 0% | 4.22% | [SAV §1.4] |
| CD vs T-bill break-even state rate | CD 4.65%, bill 4.54%, 22% | 1.845% (±0.01) | [SAV §1.5] |
| I bond composite | fixed 1.40%, semiannual 2.04% | 5.5086% → displays 5.51% | [SAV §1.6] |
| I bond 0.90% fixed this period | 0.90%, 2.04% | 5.00% (5.0018) | [SAV §1.6 note] |
| I bond effective yield | r = 5.51%, 24 months | 5.51 × 21/24 = 4.82% | [SAV §1.6] |
| I bond < 12 months | 11 months | `None` (not redeemable) | [SAV §1.6] |
| CD broken early | APY 4.65%, m = 6, penalty 3 months | 2.325% | [SAV §1.5] formula |
| EF months | single income + macro (27.6% LT share), lean off | 4.5 | [SAV §4.5] |
| EF months | same, lean on | 6.0 | [ACAD §11] |
| EF clamp | self-employed + dependents + low security + single + all | ≤ 12.0 | [SAV §2.2] |
| EF reducers cap | very secure + Roth basis + dual stable | base 3 − 1.0 − 1.0 → clamped at 3.0 | [SAV §2.2] |
| Split | S = 20,000, M = 3,000, target 4.5, no debt/SF, IRA room 7,500 | EF 13,500, IRA 6,500, taxable 0 | [SAV §4.5] |
| Split table | M = 2,500 / 3,500 / 4,500, SF 1,500 / 1,500 / 0 | Excess 7,250 / 2,750 / 0; EF short 250 at 4,500 | [SAV §4.5] |
| Starter floor | S = 5,000, M = 2,500, debt 10,000 | Debt paydown 2,500 (keeps the 2,500 starter) | [SAV §4.2] |
| Backdoor pro-rata | basis 7,500, Dec-31 trad 0, conversion 7,500 | taxable fraction 0 | [SAV §3.3] |
| Backdoor pro-rata | basis 7,500, Dec-31 trad 22,500, conversion 7,500 | 0.75 | [SAV §3.3] |
| Roth phase-out | MAGI 160,500 single | allowed = 7,500 × (168,000 − 160,500)/15,000 = 3,750 | [SAV §3.3] (IRS rounding rules noted) |
| Fee drag | $10,000, 30 y, 7% gross, ER 0.03% vs 0.50% | ≈ $9,700 difference (exact value computed and pinned in the test) | [ACAD §1.5] |
| CAGR | index 100 → 200 over 10 y | 7.177% | math |
| Max drawdown | [100, 120, 60, 90, 130] | −50%, peak index 1, trough index 2 | math |
| Owner-earnings DCF | oe0 = 5, g1 = 10%, g2 = 5%, terminal P/E 15, r = 10%, 5 + 5 y | value pinned in the test (closed form, computed in the test docstring) | [INF-B §3.2] |
| Real yield | 4.2% nominal, 3.7% CPI | 0.482% | [SAV §0] |
| French parser | fixture with monthly, annual and `-99.99` | Correct block split, values / 100, NaN for missing | [TECH §1.10] |
| CPI y/y with gap | October 2025 missing | `None` + note "2025 shutdown gap" | [MKT §9.1] |

### 17.3 Property tests (hypothesis)

- `split_savings`: every dollar is allocated exactly once (`sum(parts) == savings` within 1 cent); no part is negative; the EF allocation never drops below `min(savings, starter)` unless debt paydown is the cause; higher M never increases the investable excess.
- `ef_target`: months always in [3, 12]; monotonic in added positive flags.
- After-tax yields: after-tax ≤ pre-tax; T-bill after-tax ≥ HYSA after-tax at equal yield when t_state > 0.
- `percentile_rank`: outputs in (0, 100]; inverting twice preserves order.
- Redaction: for random profiles, no account balance string from the profile appears in any remote-bound prompt.

### 17.4 Golden tests

- `tests/golden/stock_scores_v1.json`: 20 synthetic companies across 4 sectors with known metrics. Expected block scores, composite and flags are pinned. **Changing a weight in `stock_scoring.yaml` without updating the golden file fails CI.** That makes every methodology change an explicit, reviewable diff.
- `tests/golden/etf_scores_v1.json`: 8 ETFs within the "US total market" and "SCV" categories.
- `tests/golden/factor_vti.json`: VTI regression on a frozen 2010-2025 price fixture plus frozen French factors: Mkt-RF in [0.95, 1.05], |SMB| < 0.1, R² > 0.95. A second case, AVUV: SMB > 0.5, HML > 0.3. [TECH §5.7]
- `tests/golden/savings_split.json`: the [SAV §4.5] worked examples.

### 17.5 Provider mocks

```python
# tests/unit/data/test_chain.py (sketch)
class FakeProvider:
    def __init__(self, name, covers, fail=None, ok=True):
        self.name, self.capabilities = name, frozenset({Capability.PRICES_DAILY_ADJ})
        self._covers, self._fail, self._ok = set(covers), fail, ok
    def available(self): return Availability(self._ok, "" if self._ok else "missing key")
    def fetch(self, req):
        if self._fail: raise self._fail
        got = self._covers & set(req.symbols)
        return ProviderResult(data=make_prices(got), source=self.name, covered=frozenset(got))

def test_fallback_fills_only_missing():
    written = []
    chain = FallbackChain(Capability.PRICES_DAILY_ADJ,
                          [FakeProvider("yf", {"VTI"}), FakeProvider("tiingo", {"VTI", "AVUV"})],
                          on_result=written.append)
    rep = chain.run(DataRequest(Capability.PRICES_DAILY_ADJ, ("VTI", "AVUV")))
    assert rep.served == {"yf": ["VTI"], "tiingo": ["AVUV"]} and rep.unresolved == []

def test_circuit_opens_after_three_failures(): ...
def test_unavailable_provider_skipped_with_reason(): ...
def test_schema_drift_triggers_fallback(): ...
```

LLM mocks: `FakeLLM(responses=[LLMRateLimited(), "answer [S1]"])` checks fall-through, cache hits (the second call makes no request), citation rejection, number-guard rejection, and that a remote provider receives redacted messages.

### 17.6 CI gates

`ruff check`, `ruff format --check`, `mypy --strict src/fintec/domain src/fintec/rules src/fintec/kb`, `pytest -m "not live" --cov` with thresholds, `python -m fintec.kb.verify --schema`, plus a weekly scheduled job running `--dois --links` and `pytest -m live`.

---

## 18. Effort summary (solo Python developer)

| Phase | Hours (low-high) | Calendar at ~10 h/week | Main risk to the estimate |
|---|---|---|---|
| 0 Setup | 8-12 | 1 week | Learning curve with uv and DuckDB |
| 1 Savings + profile | 36-49 | 3-4 weeks | Getting tax inputs right (state rates, MMF exemptions) |
| 2 ETF research | 58-80 | 5-6 weeks | ETF holdings sources (issuer endpoints are fragile) |
| 3 Stock scoring | 53-72 | 5-6 weeks | EDGAR tag mapping and TTM edge cases |
| 4 Experts + library | 36-49 | 3-4 weeks | Curation time; transcript access is limited |
| 5 LLM (optional) | 20-30 | 2 weeks | Free-model churn and quotas |
| 6 Polish | 35-55 | 3-4 weeks | Live-test flakiness |
| **Total** | **245-345** | **~25-33 weeks** | |
| **Minimum useful product (Phases 0-1)** | **44-61** | **~4-5 weeks** | |

Claude can draft most KB content and first-pass code for each module from this plan and the briefs. That plausibly cuts 30-40% of the hours, but the user should keep review time for every number and rule.

---

## 19. Risk register

Scoring: Likelihood (L) and Impact (I) on a 1-3 scale; exposure = L × I.

| ID | Risk | L | I | Exp. | Mitigation | Owner / trigger |
|---|---|---|---|---|---|---|
| R-01 | **yfinance breaks** (Yahoo endpoint, cookie or crumb change; 401/429) | 3 | 2 | 6 | Pin `1.8.*`; Tiingo fallback; circuit breaker; weekly live test; stale-while-error; manual CSV import [TECH §1.1] | Live test fails |
| R-02 | **Stale or wrong rates lead to a bad cash decision** (HYSA APYs have no API; promo rates) | 3 | 3 | **9** | as_of on every rate; 7-day stale badge; `promo` flag and base APY ranking; "verify at the bank" link; user confirms before saving [SAV §1.2, §4.6] | Rate older than 7 days |
| R-03 | **Tax rules or limits change** (2027 limits, OBBBA provisions, state MMF rules) | 2 | 3 | 6 | Per-year JSON with source URLs; December "next year missing" warning; state MMF exemption entered by the user [SAV §3.2], [TECH §6 item 6] | Oct-Nov each year |
| R-04 | **Misplaced precision / overconfidence**: user treats a score as "buy" | 2 | 3 | 6 | No "buy" wording; base-rate cards; hard-flag fails; cooling-off journal; satellite caps; multiple-testing warning [ACAD §1.1, §2.1, §4.3, §6.1] | Always |
| R-05 | EDGAR bulk ZIP size (1.42 GB compressed, several GB uncompressed) and parsing time | 2 | 2 | 4 | Light mode by default (watchlist + ~900 peers via per-company API); bulk only by opt-in; Parquet; night job [TECH §1.2] | Disk or time issues |
| R-06 | **ETF holdings endpoints fail** (iShares HTML gate, SSGA redirect, Vanguard changes) | 3 | 2 | 6 | Adapter per issuer with schema tests; N-PORT fallback (2-5 months old, labelled); user CSV upload [TECH §1.12] | Weight-sum check fails |
| R-07 | DuckDB single-writer lock conflicts between the app and scheduled jobs | 2 | 2 | 4 | In-app refresh button for v1; night jobs only when the app is closed; staging Parquet in v2; SQLite fallback | "database is locked" errors |
| R-08 | **KB content errors** (wrong DOI, misattributed creator claim, unverified numbers shown as fact) | 2 | 3 | 6 | `verified` flags carried from the briefs; DOI title-match CI; "unverified" badges; "due for review" dashboard; 3 DOI errors already caught and corrected [INF-A §0.1a], [MKT §12] | KB edits; weekly CI |
| R-09 | **LLM hallucinates numbers or advice** | 2 | 3 | 6 | Citation check; number guard; forbidden-phrase test; NullProvider fallback; LLM only rephrases [TECH §4] | Any LLM answer |
| R-10 | **Privacy leak** to remote free models (which may log or train) | 2 | 3 | 6 | Redaction layer; personal context only to local Ollama; property test [TECH §4.1, §4.3] | Remote provider enabled |
| R-11 | OpenRouter free roster or quota changes (~50/day unverified) | 3 | 1 | 3 | Dynamic model discovery; cache; Ollama fallback; app works without an LLM [TECH §4.1, §7] | 429s / 404 models |
| R-12 | Creator ToS and copyright (transcripts, yt-dlp gray area) | 2 | 2 | 4 | RSS and Data API first; transcripts local-only, rate-limited, opt-in; ship paraphrases and links only [TECH §3.4] | Any transcript fetch |
| R-13 | Impersonator or scam channels ingested | 1 | 3 | 3 | Hard-coded channel-ID allowlist; Sven Carlin impersonator blocked [TECH §3.1] | New channel added |
| R-14 | Factor-tilt regret: the user abandons a tilt after years of lagging | 2 | 2 | 4 | Default tilt 0%, max 30%; "tracking-error tolerance" question; longest-underperformance stat; ×0.5 premium haircut [ACAD §3.1, §4.1], [MKT §5.2] | User enables a tilt |
| R-15 | Regime banner read as a timing signal (CAPE ~40) | 2 | 2 | 4 | Descriptive text only; forbidden-phrase test; CAPE used only for expected-return ranges [ACAD §8.2], [MKT §0] | Always |
| R-16 | Scope creep for a solo developer | 3 | 2 | 6 | Phase gates with DoD; Phase 5 optional; ship Phase 1 first | Phase slips > 50% |
| R-17 | Missing methodology brief ([METH]) diverges from this plan | 2 | 1 | 2 | All weights and thresholds in YAML; reconcile when it lands | File appears |
| R-18 | Python 3.14 wheel gaps | 2 | 1 | 2 | Pin 3.12 via uv | `uv sync` builds from source |
| R-19 | Survivorship bias in backtests (delisted tickers absent from free data) | 3 | 2 | 6 | No stock backtesting module in v1; label any historical screen "survivors only"; rely on the published research for premia | Backtest feature requested |
| R-20 | Fintech or BaaS deposit risk (Synapse-type) in a recommended HYSA | 1 | 3 | 3 | Only direct FDIC/NCUA institutions count as EF; BankFind link; intermediated-account warning [SAV §1.2], [INF-A §4.5] | HYSA added |

**Top three to watch:** R-02 (rates), R-04 (overconfidence), R-01 / R-06 (fragile free data).

---

## 20. User workflow: how the app is used to make decisions

### 20.1 First run (week 1 after Phase 1)
1. Fill in the profile (M, debts, match details, state, MAGI, HSA, IRA contributions so far).
2. Read the EF target and its breakdown; adjust flags if needed.
3. Review the after-tax cash table at today's rates. Pick the HYSA (FDIC-direct, verified on BankFind) and decide on the Treasury tier (SGOV or a 13/26-week ladder at the brokerage).
4. Follow the order-of-operations waterfall. Confirm the match is captured; fund the 2026 Roth IRA (deadline 2027-04-15) from the excess.
5. Choose lump sum or a 3-6 month staged plan for the investable excess.
6. Export the action checklist; execute it at the bank and brokerage (the app never connects to accounts).

### 20.2 Monthly (about 30 minutes)
1. Home page: data freshness, regime banner (context only), any guardrail warnings.
2. Update HYSA and MMF quotes if they are stale (7-day badge).
3. `4_Portfolio_and_Accounts`: check drift from the target allocation; rebalance with new contributions first.
4. "What's new from creators": skim tagged items; anything that changes a principle goes to `_drafts/` for review.

### 20.3 Considering an ETF
1. ETF Explorer → costs → total return → factor loadings with CIs → overlap with current holdings → product warnings.
2. Compare with the cheapest equivalent in the same category.
3. "What the experts say" for the category; the research verdict comes first.
4. Decide on account location; record the decision in the journal.

### 20.4 Considering a stock (satellite only)
1. Check satellite headroom (caps) and look-through overlap.
2. Stock Research: score card, flags, valuation (Carlin DCF, reverse DCF, multiples), moat checklist, analyst trends.
3. Creator mentions (dated, with conflict flags).
4. Write the journal thesis, "what would make me wrong", and position size, then wait 48-72 hours.
5. After the cooling-off period, re-open and either complete or abandon the checklist.

### 20.5 Twice a year / yearly
- May 1 / Nov 1: update I bond rates. Oct-Nov: new IRS limits file. Quarterly: CMA/GTM review. Yearly: creator principles review and a full KB refresh (§7.4).

---

## 21. Open questions for the user

These fill gaps the briefs could not close. Answers change defaults, not architecture.

1. **Monthly essential expenses (M)?** This is the single most important input for the $20K split. Is the $20K in one account, or already split? [SAV §6]
2. **High-interest debt?** Any credit cards, personal loans or car loans above about 8% APR? Student loans and their rates?
3. **401(k) match:** what formula (e.g. 100% up to 4%), what vesting schedule, and are you capturing it today? Does the plan offer Roth 401(k), after-tax contributions (mega-backdoor), a PLESA, or student-loan matching?
4. **Income and filing status:** approximate MAGI and state of residence (Roth eligibility, bracket, the T-bill state-tax advantage).
5. **Health plan:** HSA-eligible (HDHP, or a bronze/catastrophic plan under the 2026 rules)?
6. **IRA status:** contributed anything for 2026 yet? Any pre-tax IRA balances (backdoor pro-rata)?
7. **Known expenses within 3 years** (car, move, wedding, home down payment)?
8. **Job stability:** industry, single or dual income, dependents?
9. **Current holdings:** which ETFs, in which accounts, roughly what amounts? Can you export a CSV from your brokerage and from the 401(k) fund menu (names and expense ratios)?
10. **Risk check:** "If your portfolio fell 20% the week after you invested, would you keep going?" (sets the lump-sum vs staged default). [ACAD §10]
11. **Factor tilt appetite:** would you hold a small-cap value or quality tilt through 10 years of lagging the S&P 500? If not, the default tilt stays 0%.
12. **Dividend preference:** keep the dividend-growth block in the stock score, or reassign its weight to quality?
13. **Satellite budget:** what percentage of equities do you want available for individual stocks (default warning at 10%, strong warning at 20%)?
14. **LLM:** do you want the optional assistant? Can your machine run Ollama locally (RAM)? Is a free OpenRouter key acceptable given that free models may log prompts?
15. **Refresh habits:** will you update HYSA and MMF quotes weekly, or should the app treat them as user-maintained with a monthly reminder?
16. **Corporate network:** the research machine's proxy blocked OpenRouter, opencode.ai and some issuer sites. Will the app run on the same network? If so, plan for those sources being unreachable.

---

## 22. Appendix: rule ID index

| Rule ID | Phase | Where | Source |
|---|---|---|---|
| MONEY_WATERFALL | 1 | `rules/savings.yaml` | [SAV §3.1], [INF-A §7.5] |
| HYSA_COMPARE, EXCESS_CASH_ALERT | 1 | `rules/savings.yaml` | [INF-A §7.5], [INF-B §2.4] |
| NEVER_BELOW_STARTER_EF, WARN_MATCH_NOT_CAPTURED, WARN_SHORT_GOAL_IN_EQUITY, WARN_HYSA_UNDERPAYING, WARN_FDIC_LIMIT, BLOCK_IBOND_AS_EF_BEFORE_12M, BLOCK_NON_INSURED_YIELD_AS_EF, WARN_401K_IS_NOT_EF | 1 | `rules/savings.yaml` | [SAV §4.6], [ACAD §11] |
| CORE_INDEX, INTL_ALLOCATION_RANGE, OPTIONAL_FACTOR_TILT | 2 | `rules/guardrails.yaml` | [ACAD §1.1, §3.1, §7.2], [INF-A §7.5] |
| FEE_DRAG_CALC, TOTAL_RETURN_NOT_YIELD, WARN_COVERED_CALL, BLOCK_LEVERAGED_FOR_MODERATE, THEMATIC_CAP, CRYPTO_CAP, BUFFER_COMPARE, ACTIVE_SPIVA_CARD | 2 | `rules/product_warnings.yaml` | [MKT §7], [ACAD §1.2, §5] |
| SINGLE_STOCK_CAP, SATELLITE_CAP, DIVERSIFIED_LABEL | 3 | `rules/guardrails.yaml` | [ACAD §2.1, §7.1] |
| STOCK_RESEARCH_CHECKLIST, MOAT_CHECKLIST, COOLING_OFF, TURNOVER_WARN, WASH_SALE_CHECK | 3 | `rules/stock_scoring.yaml` | [INF-A §2.4], [INF-B §4.2], [ACAD §6] |
| PAID_PROMO_FLAG, SOURCE_TRUST_SCORE | 4 | `rules/guardrails.yaml` | [INF-A §2.2, §7.5] |
| LLM_DISCLAIMER | 5 | `llm/prompts` | [INF-A §2.2] |
| PREPAY_VS_INVEST_CALC, RENT_VS_BUY_CALC | backlog | | [INF-A §7.5] |

**Backlog (post-v1, SPEC priority C):** mortgage prepay vs invest calculator and rent vs buy with US inputs (FR-237); budget templates (FR-238); opencode provider (FR-1004); event calendar (FR-710); Rational Reminder paper mining (FR-608); glide-path notes (FR-812). Monte Carlo (FR-808) moved to Phase 2; behavior gap (FR-809), TLH (FR-313) and active share (FR-511) moved to Phase 6, to match SPEC priorities.

*Educational research tool, not individualized investment advice. All figures carry their as-of dates and must be re-verified before acting.*
