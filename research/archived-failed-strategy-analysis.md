# AlphaPilot Archived Failed Strategy Analysis

This research package turns existing archived strategy evidence into a durable
negative-research asset.

## Scope

- Inventory failed, rejected, and negative-reference strategies from local
  status archives.
- Preserve source reports and raw-artifact provenance.
- Normalize metrics without converting missing values to zero.
- Separate signal-edge failure from account/risk-model failure.
- Record cost, frequency, pair, time, regime, exit, data, and runtime
  weaknesses where evidence supports them.
- Publish negative rules, reusable research components, and bounded revival
  conditions.

## Outputs

Implementation and generated artifacts are in the
`AlphaPilot-Quant-Engine` repository:

- `alphapilot/reports/archived_strategy_inventory.py`
- `alphapilot/reports/signal_level_failure_attribution.py`
- `alphapilot/reports/generate_archived_strategy_failure_analysis.py`
- `reports/archived_failed_strategy_*`
- `docs/archived-failed-strategy-analysis.md`
- `docs/failure-attribution-methodology.md`
- `docs/signal-edge-vs-risk-model-failure.md`
- `docs/negative-research-rules.md`
- `docs/strategy-revival-policy.md`

## Boundary

This is report-only research. It does not edit a strategy, tune parameters,
run a backtest, call exchange APIs, store credentials, read accounts or
positions, create orders, or change Demo/Live eligibility.
