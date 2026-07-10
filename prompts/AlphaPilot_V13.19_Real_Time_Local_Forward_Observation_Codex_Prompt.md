# AlphaPilot V13.19 - Real-Time Local Forward Observation

## Goal

Advance an immutable, replay-qualified StrategyCandidate through public-market
local forward observation without exchange orders. The runner must use a 1000
USDT virtual account, completed public candles, next-bar virtual fills, a 1R
stop, a target of at least 2R, and append-only restart state.

## Required Boundary

- Historical replay and local forward use different evidence classes.
- Downtime is a collection gap and is never backfilled as forward evidence.
- Open positions, pending signals, rejections, costs, outcomes, and checkpoints
  survive restart.
- Engine probes and legacy synthetic samples cannot create a ForwardRelease.
- No API key, private endpoint, account/position read, Trade API, Withdraw API,
  Demo order, Live order, or automatic Live approval.

## Current Acceptance Result

The implementation and fixtures may prove the full state machine, but the real
registry must remain `blocked_no_eligible_forward_release` while V13.17 has no
formal StrategyCandidate and V13.18 has no candidate-bound formal replay.
