# AlphaPilot V13.27 - Unified Workflow Orchestrator Implementation Plan

## Purpose

Implement the approved four-stage strategy lifecycle without replacing evidence with labels:

```text
Strategy backtest -> Local real-time forward -> OKX Demo -> Live
```

The implementation is split into independently testable releases. A later release may not begin until the previous release is committed, tagged, pushed, and clean.

## Implementation Status - 2026-07-11

- `V13.27.0`: completed, tested, tagged, and pushed.
- `V13.27.1`: completed, tested, tagged, and pushed.
- `V13.27.2`: not started. It may begin only after the V13.27.1 closeout is clean.

The current imported Alpha191 observer remains `backtest / awaiting` because a
formal point-in-time DataSnapshot and evaluation manifests have not been bound.
This is an explicit evidence gate, not an unfinished display migration.

## Shared Rules

1. A strategy definition is immutable after registration.
2. Any strategy parameter or logic change creates a new version and restarts at backtesting.
3. A workflow action creates a real task or an explicit blocker, never only a display-state change.
4. Historical replay and synthetic sandbox outcomes cannot satisfy local-forward gates.
5. Demo Trial is not a formal Demo Release.
6. Live activation requires one explicit release approval.
7. No raw credentials are stored; Withdraw remains unavailable.
8. Default pages stay concise; audit data remains in Advanced Details.

## V13.27.0 - Workflow Foundation

### Quant Engine

Add registry migration v6 with:

- `StrategyVersions`
- `GateProfiles`
- `WorkflowRuns`
- `StageEvents`
- `FailureDiagnoses`

Add `alphapilot/evolution/workflow/`:

- `types.py`: stage, status, retry, command, and projection types
- `states.py`: legal transition map and validation
- `service.py`: idempotent strategy registration, run creation, transition, checkpoint, failure, retry, and challenger operations
- `projection.py`: one-current-stage read model and page lanes
- `__init__.py`: narrow public API

Extend registry types and repository methods. Keep existing FactorRun, StrategyCandidate, ForwardRelease, DemoRelease, LiveRelease, and OutcomeLedger behavior unchanged.

### Tests

- migration is idempotent and foreign keys are valid;
- identical immutable records are idempotent;
- conflicting hashes fail;
- only one active run per strategy version and stage;
- illegal transitions fail closed;
- a user action cannot mark a run passed;
- operational failures may retry the same version;
- strategy-performance failures require a challenger;
- challenger content must differ and starts at backtest;
- checkpoint state survives reconnect;
- projection places a version on one page only.

### Acceptance

- Python compileall passes.
- Evolution tests pass.
- Existing repository tests pass.
- Safety scan shows no new exchange or order integration.
- Quant repo is committed, tagged `v13.27.0`, pushed, and clean.

## V13.27.1 - Strategy Backtest Workflow

### Quant Engine

- Add a backtest worker adapter with manifest/checkpoint support.
- Bind local canonical data, cost model, walk-forward manifest, locked OOS, and GateProfile.
- Persist real run progress, result evidence, and failure diagnosis.
- Add bounded challenger generation; never overwrite a strategy version.
- Import the Alpha191 observation candidate as `backtest / awaiting` or record a precise unsupported-data blocker.

### Control Console

- Add read-only workflow projection API.
- Add commands for run selected, run all awaiting, pause, resume, cancel, retry, archive, and generate challenger.
- Replace the Strategy page with concise lanes:
  - 待回测
  - 回测中
  - 回测通过
  - 回测未通过
  - 已归档
- Put parameters, hashes, raw metrics, and logs under Advanced Details.

### Acceptance

- Clicking Run Backtest creates a real WorkflowRun.
- The UI shows progress and final gate results.
- Failed runs have Chinese explanations and retry disposition.
- Strategy changes create a new version.
- No backtest action can create an exchange order.
- Quant, Console, and Docs commits are tagged and pushed.

### V13.27.1.1 Completion Note

V13.27.1.1 adds the approved dual-data warehouse and one-click backtest bridge
without changing the four-stage lifecycle.

- Existing files under `D:\Codex-Workspace\回测数据\5m`, `合约数据`, and
  `现货数据` remain read-only, third-party-unverified research inputs.
- Official OKX public OHLCV and funding data are stored separately under
  `回测数据\_alphapilot`, checksum-bound, and frozen before formal evaluation.
- The one-click action derives a timeframe-aware contract, runs research smoke,
  resumes official collection, builds purged walk-forward, unseen-symbol,
  locked-OOS, regime, and cost manifests, and then runs a fixed
  `targetR >= 2` formal backtest.
- Only a formal pass may automatically create an awaiting Local Forward run.
  Demo and Live remain manual, gated later stages.

Observed verification on 2026-07-11:

- Local smoke: 14 assets, implementation valid, research-only, no source-file
  metadata changes, and no promotion eligibility.
- Bounded OKX public pack: BTC/ETH/SOL, 5m/15m/4h, 9/9 OHLCV partitions,
  3 funding files, 12-file immutable snapshot, 4 walk-forward folds, and SOL
  unseen-symbol holdout.
- Quant: 207 tests passed. Console: 76 tests passed.
- The bounded pack validates the data pipeline only. Alpha191 did not pass the
  full 2020-2026 dynamic-universe formal backtest and did not enter Local
  Forward.
- No private endpoint, credential persistence, account read, position read,
  order creation, Demo transition, or Live activation was added.

## V13.27.2 - Local Real-Time Forward Workflow

### Quant Engine

- Connect the existing `alphapilot.evolution.forward` engine to WorkflowRuns.
- Consume only post-release public market bars.
- Persist virtual positions, equity, outcomes, data gaps, and checkpoints.
- Evaluate elapsed-time, closed-sample, freshness, drift, regime, concentration, and performance gates.
- Classify legacy deterministic sandbox evidence as `legacy_synthetic` only.

### Control Console

- Replace the Local Simulation page with:
  - 待前向模拟
  - 前向模拟中
  - 前向模拟通过
  - 前向模拟未通过
- Show elapsed time, new closed samples, virtual equity/PnL, freshness, and next gate.
- Keep old replay details collapsed and excluded from formal counts.

### Acceptance

- A start button launches a restart-safe forward task.
- Repeated refreshes do not create samples.
- Historical bars cannot be counted after release time.
- Reboot recovery resumes from the latest checkpoint.
- Passed/failed decisions include evidence hashes.

## V13.27.3 - OKX Demo Workflow and Concise Execution UI

### Quant Engine

- Generate immutable Demo Releases only from passed forward evidence.
- Bind release, GateProfile, RiskProfile, strategy hash, and evidence hashes.
- Import closed Demo outcomes for offline diagnosis.

### Control Console

- Keep Demo Trial separate from formal Demo.
- Add commands to start, pause, resume, and emergency-stop eligible formal Demo releases.
- After one stage-start action, allow automatic public scanning, arbitration, Demo order submission, protection, reconciliation, and closing within the immutable release and RiskProfile.
- No per-order ticket is required for formal automated Demo operation.

### Demo First Viewport

Show only:

- connection and automation status;
- running strategies;
- current symbols;
- open positions;
- entry/current/TP/SL prices;
- realized and unrealized PnL;
- today and total PnL;
- fees and slippage;
- protection and reconciliation status.

Use lanes:

- 待启动 Demo
- Demo 运行中
- Demo 已通过
- Demo 未通过

Move internal IDs, hashes, raw payloads, and low-level codes into Advanced Details.

### Acceptance

- Every order and position is release-bound and strategy-attributed.
- Demo positions and PnL reconcile with OKX Demo.
- Operational retry cannot mutate strategy content.
- Strategy changes create a challenger and return to backtesting.
- Raw credentials are never persisted.

## V13.27.4 - Live Workflow and Champion/Challenger Feedback

### Quant Engine

- Generate Live Candidates only from validated Demo evidence.
- Keep closed Demo/Live outcomes in offline evidence classes.
- Produce bounded challenger proposals with lineage to the Champion.
- Require every challenger to restart at backtesting.

### Control Console

- Keep Live default-off.
- Require one explicit approval per immutable Live Release.
- After approval, allow mechanical execution only inside the active RiskProfile.
- Show the same understandable strategy, symbol, position, protection, and PnL structure as Demo.
- Add Champion, Challenger, paused, and retired states.

### Acceptance

- Live cannot start without an approved release and active risk profile.
- Kill switch, reconciliation, idempotency, and restart recovery pass.
- A running Champion never changes parameters online.
- A Challenger cannot replace a Champion without completing the full pipeline and a new approval.

## Commit and Tag Sequence

```text
V13.27.0  Workflow foundation
V13.27.1  Backtest workflow and Strategy page
V13.27.2  Real-time local forward workflow
V13.27.3  OKX Demo workflow and concise execution UI
V13.27.4  Live workflow and Champion/Challenger feedback
```

For each release:

1. Run targeted tests while implementing.
2. Run the full affected-repository test suite.
3. Run compile/type/syntax checks.
4. Run `git diff --check` and a safety scan.
5. Commit and tag App/Console/Quant/Docs repositories that changed.
6. Push commits and tags.
7. Confirm all changed repositories are clean before the next release.

## Stop Conditions

Stop within the current phase and do not cross into the next phase when:

- a migration or existing test fails;
- a workflow action can bypass an evidence gate;
- a strategy change can preserve old passing evidence;
- a Demo/Live operation loses release or risk-profile lineage;
- runtime credentials would be persisted;
- a long task lacks checkpoint and restart recovery;
- the repository cannot be returned to a clean, auditable state.
