# FinTec Investment Research Assistant: Documentation Index

**As of:** 2026-11-10 · **Status:** design complete, no code yet · **Budget:** $0 (free data, optional free/local LLM)

> Educational decision-support project, not financial advice. Every rate, limit and creator position in these documents is a dated snapshot. Re-check it at the source before acting.

## Overview

FinTec is a local Python + Streamlit "investment research assistant" for one US-based young professional. The user invests mostly in ETFs, has a 401(k) with a match, an IRA and a taxable account, a 10+ year horizon, moderate risk tolerance, and about $20K in savings. The app helps them (1) size and place an emergency fund and route the rest of their cash through the account order of operations, after tax; (2) choose low-cost core ETFs and optional factor tilts; (3) screen individual stocks as "Buy candidate (satellite)" ideas inside a capped sleeve, using transparent quality/value/safety scores, owner-earnings DCF and checklists; (4) see what peer-reviewed research says *first*, then what eight creators say (Ben Felix, The Plain Bagel, Whiteboard Finance, Graham Stephan, Mark Tilbury, Andrei Jikh, Sven Carlin, Joseph Carlson), with conflicts of interest and dates shown; and (5) slow decisions down with an investment policy statement, a decision journal, a cooling-off timer and freshness checks. The intellectual core is a curated, versioned knowledge base (`knowledge_base/`) distilled from the research briefs below. Live free data (Treasury, FRED, SEC EDGAR, Ken French, yfinance/Tiingo, YouTube RSS, OpenAlex) only feeds numbers into deterministic, cited rules. An optional LLM (OpenRouter free models, local Ollama, or opencode) can explain results but never produces numbers or trade calls, and the app works fully without it.

## Start here

| If you want to… | Read |
|---|---|
| Act on your $20K this month | [`MY_SAVINGS_AND_STARTER_PLAN.md`](MY_SAVINGS_AND_STARTER_PLAN.md) §1 (TL;DR) and §9 (30-day checklist) |
| Know what the app must do | [`SPEC.md`](SPEC.md) §5 (functional requirements) and §10 (formulas) |
| Build it | [`IMPLEMENTATION_PLAN.md`](IMPLEMENTATION_PLAN.md) §0.4-§0.5 (phases, traceability), then the phase sections |
| Use it to make decisions | [`WORKFLOW.md`](WORKFLOW.md) §1-§9 (setup, $20K flow, monthly/quarterly/yearly routines, ETF/stock/crash/hype flows) |
| Check the evidence behind a rule | [`research/`](research/) briefs (below) |

## Documents

### Design documents

| File | What it is | Canonical for |
|---|---|---|
| [`SPEC.md`](SPEC.md) | Spec sheet: purpose, persona, 15 evidence-derived principles, FR-1xx to FR-10xx requirements across 10 modules, NFRs, data model, data sources and fallbacks, page map, scoring and calculation spec (EF months, after-tax yields, stock styles and red flags, dividend safety, DCF, ETF score, creator trust weights, consensus, stress tests, Monte Carlo, pre-trade checklist), KB schema, acceptance criteria, risks, release scope, disclaimer text, open questions | Requirement IDs, formulas, thresholds, weights, caps, flags, disclaimers |
| [`IMPLEMENTATION_PLAN.md`](IMPLEMENTATION_PLAN.md) | Build plan: repo layout, pinned stack, architecture, data-provider fallback chain, DuckDB cache and TTLs, LLM abstraction, KB build pipeline, phases 0-6 with tasks, signatures, definitions of done and effort (about 245-345 h part-time), starter code, testing strategy, risk register, rule-ID index, FR traceability matrix (§0.5) | File, package and page names; phase order; tests |
| [`WORKFLOW.md`](WORKFLOW.md) | User playbook: one-time setup, "What do I do with my $20K?", monthly / quarterly / yearly routines, flows for evaluating an ETF, evaluating a stock, a market crash and influencer hype, the journal, and the developer workflow for refreshing the KB with Claude research | When and how each flow runs |
| [`MY_SAVINGS_AND_STARTER_PLAN.md`](MY_SAVINGS_AND_STARTER_PLAN.md) | Personal, actionable plan for the $20K today: EF sizing table, where to hold it (HYSA vs T-bills vs MMF vs CD vs I bonds, after tax), taxes on interest, order of operations with 2026 limits, sample 80/20 allocations, satellite rules, tax traps (wash sale), lump sum vs DCA, institutional outlooks, what each creator would emphasize, 30-day checklist | Nothing (it applies the SPEC defaults by hand until the app exists) |

### Research briefs (`research/`, source material for the KB)

| File | Cited as | Covers |
|---|---|---|
| [`research/academic_evidence.md`](research/academic_evidence.md) | [ACAD] / [AE] | Peer-reviewed evidence: index vs active (Sharpe, SPIVA, Fama-French, Carhart), skewness (Bessembinder), factors and replication (FF5, Novy-Marx, QMJ, McLean-Pontiff, Hou-Xue-Zhang, Harvey-Liu-Zhu), dividends (MM 1961, Hartzmark-Solomon), behavior (Barber-Odean), diversification and international (Anarkulova et al.), valuation (Shiller, Goyal-Welch), lump sum vs DCA, emergency savings; default weights and guardrails |
| [`research/influencers_ben_felix_plain_bagel_whiteboard_graham.md`](research/influencers_ben_felix_plain_bagel_whiteboard_graham.md) | [CR-A] / [INF-A] / [CR1] | Ben Felix, The Plain Bagel, Whiteboard Finance, Graham Stephan: credentials, principles, dated positions, conflicts, evidence grades A-E, trust weights, CreatorProfile JSON schema, app rule IDs |
| [`research/influencers_tilbury_jikh_carlin_carlson.md`](research/influencers_tilbury_jikh_carlin_carlson.md) | [CR-B] / [INF-B] / [CR2] | Mark Tilbury, Andrei Jikh, Sven Carlin, Joseph Carlson: same structure, principle-record schema and seed records |
| [`research/market_context_2026.md`](research/market_context_2026.md) | [MKT] / [MC] | November 2026 regime: valuations (forward P/E 17.7, CAPE 39.7), rates, inflation, concentration (top 10 = 40.6% of the S&P 500), international, factors, popular products (covered calls, buffers, thematic, crypto, private credit), institutional CMAs, refresh checklist |
| [`research/savings_and_account_strategy.md`](research/savings_and_account_strategy.md) | [SAV] / [SA] | Cash vehicles and rates, after-tax yield formulas, EF sizing model, account order of operations, verified 2026 IRS limits, Roth vs Traditional, the $20K split algorithm and worked examples |
| [`research/tech_stack_and_data_sources.md`](research/tech_stack_and_data_sources.md) | [TECH] / [TS] | Free data APIs and verified limits, fallbacks, LLM layer (OpenRouter free, Ollama, opencode), Python stack, project layout, caching, testing |
| [`research/analysis_methodology.md`](research/analysis_methodology.md) | [METH] | ETF evaluation thresholds and the factor-regression recipe, ETF reference data (ER/AUM/yield), stock quality/distress/valuation/dividend/moat methods, worked DCF/composite/stress examples, portfolio rules, expert-consensus design. This brief was recovered after its original write failed. SPEC §10 is canonical, and §5 reconciles the differences. It was not fact-checked by a second agent. |

## Consistency rules (from the 2026-11-10 review)

- If documents disagree, **SPEC** wins on numbers and rules, **IMPLEMENTATION_PLAN** on names and layout, **WORKFLOW** on cadence. Fix the other document.
- The KB folder is `knowledge_base/`. Pages are `app/pages/0_Profile.py` … `11_Settings.py`.
- Key defaults used everywhere: EF = scored model (4.5 months for the persona; "lean conservative" 6-month floor is opt-in); 80/20 equity/bond; ex-US 36% of equities (floor 20%); satellite cap 10% of equities (strong warning > 20%); single stock warn > 5% / strong > 10% of investable assets; factor tilt 0-30%; thematic ≤ 5%; crypto 0% (opt-in ≤ 5%); cooling-off 72 h (minimum 48 h); stale after 1× refresh cadence (amber), expired after 2× (red, blocks "Ready").
- 2026 limits (IRS IR-2025-111, Notice 2025-67, Rev. Proc. 2025-19): 401(k) $24,500; catch-up $8,000 / $11,250 (60-63); §415(c) $72,000; IRA $7,500; HSA $4,400 / $8,750; Roth phase-out $153-168K single, $242-252K MFJ. 2027 limits were not published as of 2026-11-10.

## Refresh calendar for these documents

| What | When |
|---|---|
| HYSA / CD / MMF rates in the savings plan and KB | Weekly while shopping; the app marks them stale after 7 days |
| I bond rate | May 1 and Nov 1 |
| IRS limits and brackets | Each November (add `limits_<year>.json`) |
| Market context and CMAs | Quarterly |
| Creator positions / principles | Every 6 months / yearly |
| Full research re-run (WORKFLOW §11.1) | Every 6-12 months, with a separate fact-check pass |
