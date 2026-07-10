# AlphaPilot V13.17 - Point-in-Time FactorRun and Backtest

## Goal

Materialize approved point-in-time factors and `>=2R` directional labels from
an immutable V13.16 DataSnapshot, run purged walk-forward research, train
deterministic offline challenger models, and create a formal StrategyCandidate
only when every evidence gate passes.

## Required Work

1. Parse and validate all materialized factors through the restricted DSL.
2. Use only values known at each completed bar close.
3. Decide at bar `t` and enter at the next eligible bar open.
4. Keep stop distance at `1R` and target distance at least `2R`.
5. Treat same-bar target/stop ambiguity as stop first.
6. Record fees, slippage, timeout, and one-bar-delay outcomes.
7. Register FactorDefinitions and immutable FactorRuns with result hashes.
8. Reserve a locked final test before model selection.
9. Use expanding purged walk-forward with an embargo covering label horizon.
10. Compare deterministic logistic and boosted-stump challengers on OOS data.
11. Record baseline, 2x, 3x, latency, and gap stress.
12. Record pair/month stability and multiple-testing decisions.
13. Never create a formal candidate if provenance or performance is blocked.

## Evidence Command

```powershell
powershell -ExecutionPolicy Bypass -File scripts\run_v13_17_factor_run_backtest.ps1
```

## Required Reports

```text
reports/v13_17_factor_run_backtest_report.json
reports/v13_17_factor_run_backtest_summary.md
```

## Safety Boundary

V13.17 is local/public-data research only. No API key, account read, position
read, Trade API, Withdraw API, order, Demo release, Live release, or automatic
trading capability may be added.
