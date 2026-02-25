# Portfolio Design Process for Loom

A Loom process package for allocator-level portfolio construction under explicit
investor constraints, including optional external household exposures (for
example private real estate) so recommendations optimize total balance-sheet
risk, not just the liquid sleeve.

## What it does

This process runs a strict 9-phase workflow:

1. Intake Normalization
2. Total Balance-Sheet Exposure Model
3. Macro and Sentiment Regime
4. Universe and Candidate Construction
5. Expected Return Risk Model
6. Opportunity Ranking
7. Portfolio Construction
8. Stress Testing and Hedge Validation
9. Recommendation and Monitoring

It is designed to produce:

- target weights and dollar allocations that reconcile exactly to input capital
- objective-aware construction logic (return target, preservation, or hedge)
- explicit stress-test coverage with mitigation actions
- thesis-break triggers and monitoring alert rules for ongoing governance

## Objective modes

The process supports both numeric and natural-language objectives:

- numeric: `target_return_annual`, `max_drawdown`
- mandate-style: "protect capital in turmoil", "hedge my real-estate exposure"

Objective type is normalized into one of:

- `return_target`
- `capital_preservation`
- `income`
- `balanced`
- `total_portfolio_hedge`

## Input modes

### Goal-text mode

Run with natural language only:

```text
/run Design a portfolio for $600k targeting 9% annualized return with max 15% drawdown.
```

### Workspace-brief mode (preferred)

Provide files in the workspace:

- `portfolio-brief.yaml` or `portfolio-brief.json`
- optional `external-holdings.csv`
- optional `constraints.json`

The process normalizes inputs into `portfolio-intake.normalized.json`.

## Built-in tools used

This package uses Loom finance and planning tools, including:

- market and fundamentals: `market_data_api`, `symbol_universe_api`, `sec_fundamentals_api`
- regime and factor tools: `macro_regime_engine`, `sentiment_feeds_api`, `factor_exposure_engine`
- construction and validation: `opportunity_ranker`, `portfolio_optimizer`, `portfolio_evaluator`, `portfolio_recommender`, `valuation_engine`
- macro helpers: `economic_data_api`, `historical_currency_normalizer`, `inflation_calculator`
- artifact generation: `spreadsheet`, `calculator`, `document_write`, `read_file`, `write_file`, `search_files`

## Installation

Install from GitHub:

```bash
loom install https://github.com/sfw/portfolio-design
# or
loom install sfw/portfolio-design
```

Install from local path:

```bash
loom install /path/to/portfolio-design
```

## Usage

Interactive:

```bash
loom -w /path/to/workspace --process portfolio-design
```

Then run:

```text
/run Protect $500000 in market turmoil; I also own $1.2M Bay Area private real estate and want to hedge total exposure.
```

Non-interactive:

```bash
loom run "Design a portfolio for $600k targeting 9% annualized return with max 15% drawdown." \
  --workspace /path/to/workspace \
  --process portfolio-design
```

## Example inputs

See [`examples/portfolio-brief.template.yaml`](examples/portfolio-brief.template.yaml) and related files:

- [`examples/portfolio-brief.return-target.yaml`](examples/portfolio-brief.return-target.yaml)
- [`examples/portfolio-brief.external-hedge.yaml`](examples/portfolio-brief.external-hedge.yaml)
- [`examples/external-holdings.template.csv`](examples/external-holdings.template.csv)
- [`examples/constraints.template.json`](examples/constraints.template.json)

## Core deliverables

| Phase | Key files |
|-------|-----------|
| Intake Normalization | `portfolio-intake.normalized.json`, `intake-notes.md` |
| Exposure Model | `external-exposure-model.csv`, `total-portfolio-baseline-risk.md` |
| Regime | `macro-regime.md`, `headwind-tailwind-scorecard.csv`, `sentiment-scorecard.csv` |
| Candidate Construction | `candidate-universe.csv`, `candidate-rationales.md` |
| Return/Risk Model | `expected-returns.csv`, `covariance-matrix.csv`, `factor-exposure-matrix.csv`, `assumptions-register.md` |
| Ranking | `opportunity-rankings.csv`, `thesis-breakers.csv` |
| Construction | `target-portfolio-weights.csv`, `dollar-allocation-plan.csv`, `rebalancing-trade-list.csv` |
| Stress and Hedge Validation | `stress-test-results.csv`, `hedge-effectiveness.md`, `total-portfolio-outcomes.csv` |
| Final Recommendation | `portfolio-recommendation.md`, `monitoring-alert-rules.csv`, `rebalance-policy.md`, `source-index.csv` |

## Verification highlights

The package enforces:

- allocation math integrity and capital reconciliation
- objective-specific alignment checks
- required hedge effectiveness analysis when external exposures exist
- required scenario coverage (equity selloff, recession, inflation spike, rate shock, plus real-estate downturn when relevant)
- assumption transparency and thesis-break coverage
- monitoring rule completeness (metric, threshold, action, cadence)
- placeholder blocking (`[TBD]`, `[TODO]`, `[INSERT]`, `[PLACEHOLDER]`)

## Testing

Run deterministic package tests:

```bash
loom process test .
```

Run a single deterministic case:

```bash
loom process test . --case smoke-total-portfolio-hedge
```

Run live canary:

```bash
loom process test . --live
```
