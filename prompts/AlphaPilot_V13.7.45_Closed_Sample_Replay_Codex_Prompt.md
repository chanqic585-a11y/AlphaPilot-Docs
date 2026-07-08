# AlphaPilot V13.7.45 - Closed Sample Detail and Trade Replay View

## Goal

Add a read-only closed-sample detail and trade replay panel to the local
AlphaPilot Control Console.

The feature should help review local sandbox closed samples without changing
strategy execution, exchange access, or trading safety boundaries.

## Scope

- Add a backend replay API for local sandbox closed samples.
- Add a Control Console panel showing representative closed samples.
- Use de-duplicated `closedPaperSampleCount` from daily reports.
- Show raw outcome-log count separately.
- Show missing replay fields explicitly.
- Keep all state transitions manual and read-only.

## Required API

- `GET /api/closed-sample-replay`
- `GET /api/closed-sample-replay/strategies`
- `GET /api/closed-sample-replay/samples`
- `GET /api/closed-sample-replay/strategies/{strategyId}`

## UI Requirements

The panel must show:

- total strategies
- de-duplicated closed samples
- representative sample count
- missing full-path sample count
- Dry-run status
- live trading status
- strategy list
- selected strategy detail
- representative sample cards
- missing fields such as entry/exit, MFE/MAE, fee/slippage, direction, and
  market regime

## Safety Rules

Do not add API key storage, Trade API, Withdraw API, exchange testnet orders,
real account reads, real position reads, order creation, exchange Dry-run, live
trading, or automatic trading.

Closed sample replay is local research only.
