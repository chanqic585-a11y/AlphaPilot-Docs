# AlphaPilot V13.7.44 - Simulation Review Queue and Strategy Promotion Panel

## Goal

Based on V13.7.43, turn the local simulation learning bridge into a reviewable
strategy queue.

V13.7.44 should not add strategies, exchange credentials, exchange Dry-run,
Trade API, Withdraw API, account reads, position reads, order creation, live
trading, or automatic trading.

The goal is to make existing local sandbox samples:

- reviewable
- sortable
- promotable as a suggestion
- demotable as a suggestion
- auditable
- safer to use for later learning

## Required Work

1. Clean up uncommitted AlphaPilot-Quant-Engine artifacts before feature work.
2. Keep valuable report artifacts and ignore local runtime logs.
3. Add a local simulation review API.
4. Add a desktop console review queue panel.
5. Show sample gates:
   - 30 closed samples for human review
   - 100 closed samples for later Dry-run sample readiness
   - 300 closed samples for stability review
6. Show metrics:
   - closed samples
   - virtual PnL / equity
   - win rate
   - profit factor
   - average win / loss in R
   - max consecutive losses
   - max drawdown in R
   - pair / direction / market-regime breakdown when available
7. Do not fabricate missing direction, regime, fee, or slippage fields.
8. Keep all promoted / demoted states as suggestions only.

## API

Add:

- `GET /api/simulation-review`
- `GET /api/simulation-review/strategies`
- `GET /api/simulation-review/queue`
- `GET /api/simulation-review/strategies/{strategyId}`

## Safety Boundary

V13.7.44 must keep:

- no API key storage
- no Trade API
- no Withdraw API
- no real account reads
- no real position reads
- no order creation
- no exchange Dry-run
- no live trading
- no automatic trading

## Acceptance Criteria

1. Control console reports version `V13.7.44`.
2. `/api/simulation-review` returns strategy review queue data.
3. Review queue uses deduped local daily-report `closedPaperSampleCount` for sample gates.
4. UI shows collecting / review-ready / promoted-candidate / demoted counts.
5. UI shows per-strategy metrics and warnings.
6. `dryRunApproved=false`.
7. `liveTradingApproved=false`.
8. Quant Engine working tree is clean after report cleanup.
9. Python compile check passes.
10. JS syntax check passes.
11. Smoke check passes.
12. Safety scan shows no new executable trading integration.
