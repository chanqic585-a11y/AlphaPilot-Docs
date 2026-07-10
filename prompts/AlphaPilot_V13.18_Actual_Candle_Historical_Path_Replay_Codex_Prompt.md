# AlphaPilot V13.18 - Actual-Candle Historical Path Replay

## Goal

Implement a no-lookahead historical replay engine using canonical candle paths
and persist immutable Outcome Ledger evidence. Do not treat synthetic or engine
probe outcomes as formal strategy performance.

## Required Work

1. Evaluate signals only after a completed decision candle.
2. Fill at the next eligible candle open.
3. Keep stop at `1R` and target at least `2R`.
4. Resolve same-candle stop/target ambiguity as stop first.
5. Support timeout, fees, slippage, optional funding, MFE, and MAE.
6. Enforce instrument position and portfolio concurrency state.
7. Add immutable, checksum-bound Outcome Ledger records.
8. Bind replay artifacts to DataSnapshot, source entity, and code commit.
9. Mark old precomputed/synthetic sandbox outcomes `legacy_synthetic`.
10. Exclude legacy and engine probes from formal training and promotion.
11. Expose a stable local-replay console contract.
12. Create no candidate, Demo release, Live release, or order without evidence.

## Evidence Command

```powershell
powershell -ExecutionPolicy Bypass -File scripts\run_v13_18_historical_path_replay.ps1
```

## Safety Boundary

Local historical research only. No API key, account read, position read, Trade
API, Withdraw API, order creation, Demo execution, Live execution, or automatic
trading may be added.
