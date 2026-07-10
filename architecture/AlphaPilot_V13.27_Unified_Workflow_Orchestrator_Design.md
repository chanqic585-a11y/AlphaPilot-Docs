# AlphaPilot V13.27 - Unified Workflow Orchestrator Design

## Status

- Date: 2026-07-11
- Status: Proposed design, approved product direction
- Scope: AlphaPilot Quant Engine, Control Console, OKX Demo, Live Canary
- Safety: No Withdraw API; Live remains default-off and release-gated

## 1. Product Goal

AlphaPilot needs one auditable workflow that moves an immutable strategy version through four user-facing stages:

```text
Strategy research and backtest
-> Local real-time forward simulation
-> OKX Demo automated simulation
-> Live automated execution
```

Each strategy version appears on exactly one current-stage page. Previous stages remain available only in its history and evidence drawer.

The workflow must never promote a strategy by changing a label alone. Every transition requires a real job result and a versioned gate decision.

## 2. Confirmed Hard Rule

Any change to strategy parameters or trading logic creates a new immutable strategy version.

Changes that require a new version include:

- factors or features;
- entry or exit conditions;
- symbol-selection logic;
- timeframe or market-regime logic;
- stop-loss, take-profit, or position-sizing logic that belongs to the strategy;
- model artifact, model features, or model hyperparameters.

The new version must restart from backtesting:

```text
new version -> backtest -> local forward -> Demo -> Live approval
```

The same version may retry its current stage only when the failure is operational and does not change strategy content, for example a network outage, exchange maintenance, credential expiry, or a recoverable worker interruption.

## 3. Current-State Correction

The current console does not yet implement this complete workflow:

- The Strategy page cannot launch a real Quant Engine backtest job.
- The legacy local sandbox uses historical replay and deterministic virtual outcomes. It is not official forward evidence.
- A real public-market local forward engine exists in the Quant Engine, but it is not fully connected to the console workflow.
- The current ten promoted strategies are Demo Trial observations, not immutable formal Demo Releases.
- Stage assignments currently control visibility, but they do not yet represent complete job execution and evidence gates.

V13.27 replaces label-only stage changes with job-backed workflow transitions.

## 4. Recommended Architecture

### 4.1 Ownership

#### AlphaPilot Quant Engine

The Quant Engine is the authoritative owner of:

- immutable strategy definitions and versions;
- FactorRun and model lineage;
- backtest and walk-forward jobs;
- local real-time forward releases and evidence;
- stage gate evaluation;
- strategy-version history;
- immutable Demo Release and Live Candidate generation.

#### AlphaPilot Control Console

The Control Console owns:

- user actions that request, pause, resume, or cancel workflow jobs;
- concise stage-specific views;
- process-local OKX Demo and Live credentials;
- Demo and Live runtime monitoring;
- read-only projections of Quant Engine workflow records.

The console must not create a passing decision by directly mutating a stage label.

#### Demo and Live Execution Ledgers

Execution ledgers own:

- intents, exchange orders, fills, positions, protection state, reconciliation, and outcomes;
- realized and unrealized PnL;
- fees, slippage, MFE, MAE, and R-multiple;
- exchange and strategy reason codes;
- restart recovery and idempotency records.

Closed outcomes flow back to the Quant Engine offline evidence importer. Running strategies never modify themselves online.

### 4.2 Core Components

```text
Workflow Orchestrator
  -> Backtest Worker Adapter
  -> Local Forward Worker Adapter
  -> OKX Demo Worker Adapter
  -> Live Worker Adapter
  -> Gate Evaluator
  -> Failure Diagnoser
  -> Stage Projection API
```

The Workflow Orchestrator core should live under `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/`. The Control Console calls a narrow local command service and reads projections; it does not own strategy truth.

## 5. Workflow Data Model

### 5.1 StrategyVersion

Required fields:

- `strategyVersionId`
- `strategyFamilyId`
- `parentStrategyVersionId`
- `displayName`
- `contentHash`
- `sourceType`: mined, ML, manual import, or generated challenger
- `definitionJson`
- `parameterJson`
- `modelArtifactId`
- `createdAt`
- `createdBy`

An existing StrategyVersion is immutable.

### 5.2 WorkflowRun

Required fields:

- `workflowRunId`
- `strategyVersionId`
- `stage`: backtest, local_forward, demo, or live
- `status`: awaiting, queued, running, passed, failed, blocked, paused, cancelled, or retired
- `attemptNumber`
- `gateProfileVersion`
- `riskProfileVersion`
- `progressJson`
- `startedAt`
- `checkpointAt`
- `completedAt`
- `failureDiagnosisId`
- `contentHash`

Only one active run per strategy version and stage is allowed.

### 5.3 StageEvent

Every state change is append-only and includes:

- event ID;
- workflow run ID;
- strategy version ID;
- previous and next state;
- reason code;
- evidence hashes;
- actor: system, user, worker, or recovery;
- timestamp.

### 5.4 FailureDiagnosis

Failure categories:

- `data_integrity`
- `strategy_performance`
- `overfitting_risk`
- `market_regime_mismatch`
- `execution_quality`
- `risk_limit`
- `exchange_operational`
- `worker_operational`

Each diagnosis provides:

- a plain Chinese explanation;
- failed metrics and thresholds;
- whether same-version retry is allowed;
- whether a new challenger version is required;
- bounded parameter or factor research suggestions;
- links to evidence and logs.

Suggestions are research proposals, not automatic mutation of the current strategy.

## 6. Stage Definitions

### 6.1 Strategy and Backtest

All mined factors, ML candidates, generated challengers, and manually imported strategies enter the Strategy page as `awaiting`.

User actions:

- Run selected backtests
- Run all awaiting backtests
- Pause or cancel queued/running jobs
- Review result
- Generate challenger version
- Archive

Backtest execution belongs to the Strategy page and runs in the Quant Engine against local, provenance-checked historical data.

Backtest gates are versioned, not hard-coded in the UI. They include:

- point-in-time data integrity;
- purged walk-forward and locked out-of-sample evidence;
- fees, slippage, latency, and 2R target treatment;
- minimum sample and market-regime coverage;
- drawdown, profit factor, expectancy, concentration, and overfitting checks;
- benchmark comparison.

The final locked test cannot be repeatedly tuned against. Parameter optimization uses training and validation data under a bounded search budget.

Failed strategies remain visible in the failed lane with plain-language reasons. Clicking Generate Challenger creates a new version and preserves the failed version unchanged.

### 6.2 Local Real-Time Forward Simulation

The Local Simulation page represents only real-time forward observation after a release timestamp.

It must not count historical replay, synthetic `+2R/-1R` outcomes, or repeated heartbeats as official forward evidence.

User actions:

- Start selected forward runs
- Start all awaiting forward runs
- Pause or resume
- Stop and diagnose
- Archive or create challenger

Forward runs consume newly arriving public market data, use virtual capital, and persist restart-safe checkpoints. A button starts continuous observation; it cannot instantly produce a valid forward result.

Forward gates include versioned minimum elapsed time, closed outcomes, data freshness, data-gap tolerance, regime coverage, performance, drift, and concentration checks.

### 6.3 OKX Demo Automated Simulation

The Demo page has two clearly separated internal states:

- Demo Trial: connectivity, public scanning, and workflow observation; not formal promotion evidence.
- Formal Demo: immutable Demo Release, OKX Demo account execution, reconciliation, and pass/fail evaluation.

User actions:

- Start selected eligible Demo releases
- Start all eligible releases within the active risk profile
- Pause strategy
- Emergency stop Demo automation
- Retry an operational failure
- Review failure and create challenger

After a user starts an eligible formal release, order-by-order tickets are not required. The system may automatically scan eligible OKX instruments, arbitrate conflicting strategies, submit Demo orders, manage exchange-hosted protection, reconcile state, and close positions within the immutable release and versioned RiskProfile.

Parameter or logic changes cannot retry Demo directly. They create a challenger and restart at backtesting.

### 6.4 Live Automated Execution

A strategy enters the Live page only after formal Demo validation and Live Candidate generation.

Every new Live Release requires one explicit user approval. After approval, the fixed release may execute mechanically inside its immutable risk envelope without per-order approval.

Live outcomes are recorded for offline analysis. If a better strategy is proposed:

- the current Champion may remain active;
- a Challenger starts from backtesting;
- the Challenger completes forward and Demo validation in parallel;
- replacement requires a new Live Release approval.

Live strategies never modify themselves online.

## 7. Page Design

### 7.1 Global Navigation

Keep only:

- 策略
- 本地模拟
- Demo 模拟
- 实盘交易
- 手机控制台

Each page shows only its own current-stage strategies.

### 7.2 Default Information Density

Default pages show only information needed to make the next decision.

Hide these in a collapsed Advanced Details drawer:

- internal IDs and hashes;
- raw JSON;
- full parameter sets;
- low-level blocker codes;
- source file paths;
- complete event logs;
- legacy experimental metrics.

Do not delete these fields; keep them available for audit and debugging.

### 7.3 Strategy Page

Top tabs:

- 待回测
- 回测中
- 回测通过
- 回测未通过
- 已归档

Default strategy card:

- understandable strategy name and version;
- source: factor mining, ML, manual import, or challenger;
- direction and timeframe;
- latest status;
- last run time;
- primary action;
- one-line failure or next-step explanation.

### 7.4 Local Simulation Page

Top tabs:

- 待前向模拟
- 前向模拟中
- 前向模拟通过
- 前向模拟未通过

Default running card:

- strategy name and version;
- elapsed real time;
- new closed sample count;
- virtual equity and current PnL;
- data freshness;
- current regime coverage;
- next gate or failure reason.

### 7.5 Demo Simulation Page

The first viewport must answer:

```text
Which strategies are running?
Which symbols do they currently hold?
How much have they made or lost?
Are protection and reconciliation healthy?
```

Top summary:

- Demo connection and automation status;
- running strategy count;
- open position count;
- today realized PnL;
- total realized PnL;
- current unrealized PnL;
- risk and reconciliation status.

Top tabs:

- 待启动 Demo
- Demo 运行中
- Demo 已通过
- Demo 未通过

Running strategy row:

- strategy name and version;
- status;
- symbols currently traded;
- open positions;
- closed trade count;
- realized and unrealized PnL;
- win rate, profit factor, and R only after enough valid samples;
- latest action time.

Open position table:

- strategy;
- symbol;
- direction;
- entry and current price;
- notional and margin mode;
- unrealized PnL;
- stop-loss and take-profit price;
- holding time;
- protection status.

Closed result list:

- strategy and symbol;
- entry and exit reason;
- gross PnL, fee, slippage, and net PnL;
- R-multiple;
- MFE and MAE;
- failure attribution or improvement note.

### 7.6 Live Page

Use the same understandable structure as Demo, with an unmistakable LIVE banner, active RiskProfile, kill switch, reconciliation status, account-level limits, and current release approval state.

## 8. Promotion and Retry Rules

Initial operating mode:

- User manually starts each stage.
- The system automatically classifies pass, fail, blocked, or paused from versioned gates.
- The user cannot manually mark a failed run as passed.
- Automatic cross-stage promotion remains off until the orchestrator has stable audit evidence.
- Live Release activation always requires explicit user approval.

Later optional mode:

- Backtest-passed strategies may auto-queue local forward.
- Forward-passed strategies may auto-generate a Demo Release and wait in Demo eligibility.
- Demo-passed strategies may auto-generate a Live Candidate, but may not activate Live automatically.

## 9. Migration of Current Records

- `Alpha191 因子观察策略` becomes `backtest / awaiting` with a Run Backtest action.
- The current ten Demo Trial strategies stay in `demo / trial_observation`; they are not relabeled as formal Demo releases.
- Legacy synthetic sandbox aggregates remain in history with evidence class `legacy_synthetic` and cannot satisfy formal gates.
- Existing immutable Demo and Live records retain their lineage.
- No historical sample or audit record is deleted.

## 10. Failure and Recovery

- Every job has an idempotency key and restart-safe checkpoint.
- Rebooted workers recover queued or running jobs without duplicating evidence.
- Long backtests expose progress, current shard, and last checkpoint.
- Data gaps fail closed and show a plain-language reason.
- Demo and Live reconciliation errors pause the affected release before new orders.
- Emergency stop is separate from strategy pass/fail.
- Retry never overwrites the previous attempt.

## 11. Testing and Acceptance

The design is accepted only when:

1. A strategy version appears on one current-stage page only.
2. Clicking a primary action creates a real WorkflowRun, not a label mutation.
3. The Alpha191 candidate can start a real backtest or show a precise unsupported-data blocker.
4. Backtest results show passed or failed gates and understandable reasons.
5. Parameter optimization creates a new StrategyVersion.
6. Official local forward evidence uses only post-release public market data.
7. Historical replay and synthetic outcomes cannot pass a forward gate.
8. Demo shows running strategies, symbols, positions, realized PnL, unrealized PnL, protection, and reconciliation.
9. Formal Demo orders require an immutable Demo Release and active Demo RiskProfile.
10. Live orders require an approved immutable Live Release and active Live RiskProfile.
11. No raw API credential is written to SQLite, JSON, logs, or browser storage.
12. Withdraw remains unavailable.
13. Every interruption, retry, failure, and promotion is auditable.
14. Default pages remain concise; technical data is accessible only through Advanced Details.

## 12. Implementation Sequence

### Phase 1 - Orchestrator Foundation

- StrategyVersion, WorkflowRun, StageEvent, FailureDiagnosis, and gate profile schema
- idempotent commands and worker checkpoints
- read-only stage projection API

### Phase 2 - Strategy Backtest Workflow

- Quant Engine backtest worker adapter
- Strategy page lanes and actions
- failure diagnosis and challenger creation

### Phase 3 - Real-Time Local Forward Workflow

- connect the existing public-market forward engine
- remove legacy replay from official forward evidence
- Local Simulation page lanes, progress, pause, resume, and gates

### Phase 4 - Demo Workflow and UI

- formal Demo Release creation after forward pass
- release-bound OKX Demo automation
- strategy, symbol, position, PnL, protection, reconciliation, and result panels
- Demo pass/fail and failure diagnosis

### Phase 5 - Live Workflow and Offline Evolution

- Live Candidate generation and one-time release approval
- Champion/Challenger operation
- closed-outcome import and offline factor/model research
- no online self-modification

## 13. Safety Boundary

- No Withdraw API.
- No raw credential persistence.
- Backtest and local forward cannot create exchange orders.
- Demo and Live credentials remain process-only.
- Demo and Live are separate environments and release types.
- Live remains default-off.
- A strategy cannot bypass backtest, forward, or Demo by changing a stage label.
- No claim of profitability is made from historical, forward, Demo, or Live samples alone.
