# Portfolio Design Process Package Plan (2026-02-25)

## Objective
Design a new Loom process package for portfolio construction that accepts:
1. Deployable capital amount (for example `$500000`).
2. Goal style:
   - Numeric target (`8% annual return`, `max drawdown <= 12%`).
   - Natural-language mandate (`protect capital in turmoil`, `hedge real-estate exposure`).
3. Optional external exposures (for example private real estate holdings) so recommendations hedge total household balance-sheet risk rather than isolated liquid assets.

## Proposed Package
1. Name: `portfolio-design`
2. Version: `1.0`
3. Schema: `schema_version: 2`
4. Mode: `phase_mode: strict`
5. Primary Output: actionable portfolio recommendation with weights, dollar allocations, risk diagnostics, hedging rationale, and monitoring triggers.

## Why Separate From `investment-analysis`
1. `investment-analysis` is security-level due diligence and memo synthesis.
2. `portfolio-design` is allocator-level optimization under investor constraints and external exposure context.
3. It should own intake normalization, objective translation, constraint solving, and ongoing monitoring plans.

## Input Contract (Proposed)
Because process packages do not define a strict runtime JSON schema today, use a two-lane contract:

1. Goal text lane:
   - Example: `Design a portfolio for $600k targeting 9% annualized return with max 15% drawdown.`
   - Example: `Protect my capital in market turmoil; I also own $1.2M Bay Area real estate; hedge total exposure.`
2. Workspace brief lane (preferred for reliability):
   - `portfolio-brief.yaml` or `portfolio-brief.json`
   - Optional `external-holdings.csv`
   - Optional `constraints.json`

Normalize all inputs in phase 1 into `portfolio-intake.normalized.json`.

## Recommended Normalized Intake Fields
1. `capital_usd`
2. `objective_type` (`return_target`, `capital_preservation`, `income`, `balanced`, `total_portfolio_hedge`)
3. `target_return_annual` (optional)
4. `max_drawdown` (optional)
5. `horizon_years`
6. `liquidity_needs`
7. `risk_profile` (`conservative`, `balanced`, `aggressive`)
8. `allowed_assets` / `excluded_assets`
9. `tax_notes` (optional)
10. `external_exposures[]` (asset_type, market_value, location, proxy_factors)
11. `constraints` (max weight, turnover budget, cash floor, concentration caps)

## Tooling Design
### Required Built-in Tools
1. `market_data_api`
2. `symbol_universe_api`
3. `sec_fundamentals_api`
4. `sentiment_feeds_api`
5. `macro_regime_engine`
6. `factor_exposure_engine`
7. `valuation_engine`
8. `opportunity_ranker`
9. `portfolio_optimizer`
10. `portfolio_evaluator`
11. `portfolio_recommender`
12. `economic_data_api`
13. `historical_currency_normalizer`
14. `inflation_calculator`
15. `spreadsheet`
16. `calculator`
17. `document_write`
18. `read_file`
19. `write_file`
20. `search_files`

### Excluded Tools
1. `delete_file`
2. `shell_execute`
3. `git_command`

## Proposed Phase Blueprint
1. `intake-normalization`
   - Parse goal + optional brief/holdings files.
   - Produce machine-readable constraints.
   - Deliverables:
     - `portfolio-intake.normalized.json`
     - `intake-notes.md`

2. `total-balance-sheet-exposure-model`
   - Translate external exposures (for example real estate) into proxy factor map.
   - Quantify current household concentration risks.
   - Deliverables:
     - `external-exposure-model.csv`
     - `total-portfolio-baseline-risk.md`

3. `macro-and-sentiment-regime`
   - Build macro regime + sentiment context.
   - Generate headwind/tailwind map for candidate exposures.
   - Deliverables:
     - `macro-regime.md`
     - `headwind-tailwind-scorecard.csv`
     - `sentiment-scorecard.csv`

4. `universe-and-candidate-construction`
   - Build candidate set consistent with constraints/objective.
   - Deliverables:
     - `candidate-universe.csv`
     - `candidate-rationales.md`

5. `expected-return-risk-model`
   - Estimate expected returns, covariance, factor exposures, and quality flags.
   - Deliverables:
     - `expected-returns.csv`
     - `covariance-matrix.csv`
     - `factor-exposure-matrix.csv`
     - `assumptions-register.md`

6. `opportunity-ranking`
   - Rank candidates and define thesis-break conditions.
   - Deliverables:
     - `opportunity-rankings.csv`
     - `thesis-breakers.csv`

7. `portfolio-construction`
   - Build optimized target portfolio according to objective profile:
     - Return-target mode: MVO/constraint objective.
     - Preservation mode: CVaR/drawdown minimization.
     - Hedge mode: penalize overlap with external exposures.
   - Deliverables:
     - `target-portfolio-weights.csv`
     - `dollar-allocation-plan.csv`
     - `rebalancing-trade-list.csv`

8. `stress-testing-and-hedge-validation`
   - Stress both liquid portfolio and total portfolio (including external assets).
   - Required scenarios include:
     - equity selloff
     - recession
     - inflation spike
     - rate shock
     - real-estate downturn (if external real-estate exposure present)
   - Deliverables:
     - `stress-test-results.csv`
     - `hedge-effectiveness.md`
     - `total-portfolio-outcomes.csv`

9. `recommendation-and-monitoring`
   - Final recommendation + implementation guidance + alerting policy.
   - Deliverables:
     - `portfolio-recommendation.md`
     - `monitoring-alert-rules.csv`
     - `rebalance-policy.md`
     - `source-index.csv`

## Acceptance Criteria (Core)
1. Capital allocation sums to 100% +/- 0.5%.
2. Dollar allocations reconcile exactly to supplied capital.
3. Objective-specific checks pass:
   - Return-target: modeled expected return >= target or explicit infeasibility explanation.
   - Preservation: drawdown/CVaR improved vs baseline.
   - Hedge: total-portfolio concentration/correlation to specified external risk reduced vs baseline.
4. At least 5 stress scenarios with downside analysis and mitigation actions.
5. Every recommendation has explicit thesis-break triggers.
6. Monitoring rules include metric, threshold, action, and cadence.

## Verification Rules (Recommended)
1. `allocation-math-integrity` (error, llm or deterministic check).
2. `objective-alignment` (error, llm).
3. `hedge-effectiveness-required-when-external-exposure` (error, llm).
4. `scenario-coverage` (error, llm).
5. `assumption-transparency` (warning, llm).
6. `no-placeholders` (error, regex).

## Memory Types (Recommended)
1. `investor_constraint`
2. `external_exposure`
3. `regime_signal`
4. `portfolio_assumption`
5. `risk_trigger`
6. `thesis_breaker`
7. `allocation_decision`

## Workspace Analysis Guidance
Scan for:
1. `portfolio-brief.*`
2. `*holdings*.csv`
3. `*constraints*.json`
4. `*risk*.md`
5. existing portfolio snapshots (`*.csv`)

## Process Tests (Proposed)
1. `smoke-return-target` (deterministic)
   - Goal: design portfolio for fixed capital and return target.
2. `smoke-capital-preservation` (deterministic)
   - Goal: protect capital in turmoil.
3. `smoke-total-portfolio-hedge` (deterministic)
   - Goal: external real-estate exposure hedging.
4. `live-canary` (live, requires network)
   - Goal: run full process using keyless market + macro data.

## Package Layout
```
portfolio-design/
├── process.yaml
├── README.md
├── examples/
│   ├── portfolio-brief.template.yaml
│   └── external-holdings.template.csv
└── tests/
    └── process-fixtures/ (optional if we add package-level fixture files)
```

## Suggested Next Step
1. Scaffold `packages/portfolio-design/process.yaml` with the phase blueprint above.
2. Add two example briefs under `packages/portfolio-design/examples/`:
   - return-target
   - external-exposure hedge mandate.
3. Run `loom process test portfolio-design` once package is installed/linked.
