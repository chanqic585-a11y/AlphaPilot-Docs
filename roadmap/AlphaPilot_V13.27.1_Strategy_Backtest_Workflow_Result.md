# AlphaPilot V13.27.1 - Strategy Backtest Workflow Result

Status: completed and pushed on 2026-07-11

## Delivered

### Quant Engine

- manifest-bound local backtest worker;
- immutable strategy and GateProfile binding;
- point-in-time DataSnapshot, walk-forward, locked-OOS, cost-model, and 2R
  prerequisite checks;
- centrally recalculated gate decisions;
- atomic manifest, checkpoint, result, and failure evidence;
- explicit pause, cancel, retry, archive, recover, advance, and challenger
  command boundaries;
- duplicate-worker rejection and cross-process serialization for adapters that
  write fixed report paths;
- real process termination on pause/cancel;
- operational retry on the same immutable version and mandatory new version
  after a strategy-performance failure.

### Control Console

- `GET /api/workflow` read projection;
- `POST /api/workflow/action` fixed workflow actions;
- Strategy-page lanes for awaiting, running, passed, failed/blocked, and
  archived versions;
- concise Chinese progress and failure explanations;
- legacy research assets kept in collapsed advanced details;
- responsive phone layout with full-width actions and no horizontal overflow.

## Current real state

One immutable strategy version is registered:

```text
Alpha191 加密因子观察策略
stage: backtest
status: awaiting
```

It has not been declared passed. Its formal DataSnapshot and evaluation
manifests are still absent, so running it now would produce a precise
data-integrity blocker rather than a favorable default.

## Verification

- Quant Engine full suite: 174 tests passed.
- Control Console full suite: 75 tests passed.
- Python compileall passed for both affected Python packages.
- Node syntax check passed for `web/app.js`.
- Quant configuration validation passed with Live, Trade API, and Withdraw
  disabled.
- Cached diff checks passed.
- Added-line safety scan found only negative safety statements.
- Local HTTP smoke test returned V13.27.1 workflow data.
- Desktop and 390px mobile DOM checks showed no horizontal overflow.

## Commits and tags

- Quant Engine commit: `c58af9c` (`v13.27.1`)
- Control Console commit: `be88cd2` (`v13.27.1`)

Both commits and tags were pushed to their `origin` remotes.

## Safety boundary

V13.27.1 adds no credential persistence, Withdraw API, exchange order
creation, automatic stage promotion, or Live activation. Existing Demo/Live
modules are unchanged. V13.27.2 local real-time forward work has not started.
