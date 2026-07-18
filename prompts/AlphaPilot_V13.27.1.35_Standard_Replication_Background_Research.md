# AlphaPilot V13.27.1.35 - Standard Replication Background Research

## Objective

Implement the research side of the V33-V40 dual-track program as a bounded,
restartable background service. The execution track remains isolated and may
consume only immutable, approved Release artifacts.

## Scope

- Register six falsifiable research families with at most two frozen variants
  per family.
- Store source metadata as URL, license or usage classification, summary and
  citation only.
- Freeze candidate identity, parameters, data requirements and signal hash.
- Provide atomic state, a single-writer lease, hash-chained receipts, health,
  pause, resume and status commands.
- Append research receipts to the V33 master and research ledgers.
- Stop at `ready_for_prefilter`; executable replay is a later research stage.

## Registered Families

1. Time-series momentum / Turtle.
2. BTC-ETH relative value.
3. Conditional residual mean reversion.
4. Cross-sectional factor ranking, blocked until PIT universe evidence exists.
5. Public event-driven study, blocked until event provenance exists.
6. Chan structure parser, context-only.

Universal fixed 2R is not an admission gate. Each candidate freezes its own
versioned exit policy and risk definition.

## Safety Boundary

- No private account read.
- No Trade API or Withdraw API.
- No API credential storage.
- No Release approval, Demo ARM or order side effect.
- No hidden optimizer and no forced pass after a bounded failure.
- Demo product ledgers remain unchanged unless a future immutable Release is
  explicitly produced by the governed cross-track workflow.

## Runtime Commands

```powershell
powershell -ExecutionPolicy Bypass -File scripts\run_strategy_research_cycle.ps1
powershell -ExecutionPolicy Bypass -File scripts\start_strategy_research_background.ps1 -MaxCycles 288 -IntervalSeconds 300
powershell -ExecutionPolicy Bypass -File scripts\pause_strategy_research.ps1
powershell -ExecutionPolicy Bypass -File scripts\resume_strategy_research.ps1
powershell -ExecutionPolicy Bypass -File scripts\get_strategy_research_status.ps1
```

## Verification

- Full Quant regression: 1141 tests passed and 164 subtests passed.
- Config validation: Live, Trade API and Withdraw API disabled.
- Compile, PowerShell parser, repository safety scan and staged diff checks
  passed.
- Real local cycle: 9 frozen candidates, 2 data-blocked families, 0 Formal
  runs, 0 Locked-OOS reads, 0 Releases, 0 Demo ARM and 0 orders.

## Published Implementation

- Quant branch: `feature/v13.27.1.35-standard-replication-playbook`
- Quant commit: `8d0a70db6b3b3cedfd81cf303964c5e265fbc094`
