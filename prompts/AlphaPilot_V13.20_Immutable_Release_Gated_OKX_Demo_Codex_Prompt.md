# AlphaPilot V13.20 - Immutable-Release-Gated OKX Demo

## Goal

Generate an OKX Demo release only from a formal StrategyCandidate that passes
fixed OOS, cost, concentration, real-time local-forward, checksum, and risk
gates. Export an immutable checksum-protected Control Console contract.

## Runtime Requirements

- 1000 USDT Demo equity and fixed risk envelope.
- Internal release-bound public-market scanner; reject injected signals.
- OKX Demo balance/position reconciliation before an entry.
- Deterministic arbitration, liquidity/data gates, idempotent lifecycle,
  protective exits, drift pause, recovery, and kill switch.
- Runtime credentials process-only; never store raw secrets.
- No Withdraw API, Live order, or automatic Live promotion.

## Current Acceptance Result

When no formal StrategyCandidate and forward evidence exist, the correct result
is `blocked_no_formal_strategy_candidate`, zero DemoReleases, zero contracts,
and zero orders.
