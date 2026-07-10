# AlphaPilot V13.27.1.1 - Dual Data Warehouse and One-Click Backtest Design

## Status

- Date: 2026-07-11
- Status: Approved product direction; written design awaiting review
- Scope: Historical data preparation, formal backtest evidence, and automatic Local Forward entry
- Safety: Public market data only; no exchange credentials, account access, or orders

## 1. Goal

V13.27.1 proved that a Strategy-page action can create a real backtest workflow
run. The remaining blocker is the historical evidence foundation: current local
files are useful for research, but their provenance is not strong enough to
satisfy a formal promotion gate.

V13.27.1.1 adds one understandable action:

```text
一键双层回测
```

The action must:

```text
Read the immutable strategy version
-> derive its required data contract
-> run a fast research smoke test on existing local data
-> download or update missing official OKX public data
-> validate and freeze an immutable formal data snapshot
-> run purged walk-forward and locked out-of-sample backtests
-> evaluate the versioned formal gate
-> automatically create and start Local Forward only when the gate passes
```

This is not a promise that a strategy will pass. It is a reproducible way to
produce an honest pass, fail, or blocked result.

## 2. Confirmed Product Decisions

1. Historical data stays under `D:\Codex-Workspace\回测数据`.
2. Existing files are not moved, rewritten, or deleted.
3. Existing local files are treated as third-party or unknown-provenance data.
4. Existing local files may produce research smoke evidence only.
5. Formal promotion evidence uses official OKX public data with complete
   provenance, checksums, continuity validation, and immutable manifests.
6. Strategies are classified by primary signal timeframe: `5m`, `15m`, `1h`,
   or `4h`.
7. A formal backtest always evaluates a fixed reward target of at least `2R`.
8. A formal pass may automatically enter Local Forward.
9. A backtest must not automatically enter OKX Demo or Live.
10. Any change to strategy logic or parameters creates a new StrategyVersion and
    restarts at backtesting.
11. A data-only retry does not create a fake new strategy version; it creates a
    new immutable evaluation attempt bound to new data evidence.

## 3. Two-Layer Data Warehouse

### 3.1 Research Warehouse

The research warehouse consists of the existing folders:

```text
D:\Codex-Workspace\回测数据\5m
D:\Codex-Workspace\回测数据\合约数据
D:\Codex-Workspace\回测数据\现货数据
```

These files provide:

- fast parser and strategy implementation checks;
- broad exploratory screening;
- historical comparison and anomaly discovery;
- a way to avoid downloading official data before basic code validity is known.

Every result from this layer is labeled:

```text
evidenceClass = research_smoke
formalPromotionEligible = false
```

A strong smoke result cannot promote a strategy. A weak performance result also
must not automatically reject a strategy, because the source data may be
incomplete or biased. Only implementation errors, malformed data, impossible
contracts, or non-finite calculations may stop the workflow before official
data preparation.

### 3.2 Formal Warehouse

AlphaPilot-owned files are stored without altering the existing raw folders:

```text
D:\Codex-Workspace\回测数据\_alphapilot\
  official\
    okx\
      raw\
  canonical\
    okx\
      swap\
      spot\
  catalog\
  manifests\
  checkpoints\
  reports\
  tmp\
```

The formal warehouse contains only data that can be traced to an official
public source. Each file or partition records:

- exchange and public source URL or endpoint;
- request parameters or archive identity;
- instrument, market type, and timeframe;
- first and last candle timestamps;
- collection time;
- row count and expected interval;
- confirmed or incomplete candle handling;
- SHA-256 checksum;
- downloader and canonicalizer version;
- gap, duplicate, ordering, and numeric validation results.

Formal evidence is labeled:

```text
evidenceClass = formal_backtest
formalPromotionEligible = true only after every data gate passes
```

Third-party rows and official rows must never be silently mixed into one formal
partition. If a formal partition is missing, the workflow downloads it or
remains blocked with a precise reason.

### 3.3 Storage Format and Capacity Rules

- Canonical data uses typed Parquet with ZSTD compression.
- UTC milliseconds are the canonical timestamp representation.
- Partitions are keyed by exchange, market type, instrument, timeframe, and
  bounded date range.
- Duplicate `annual` and `ALL` copies are not imported into the canonical layer.
- Temporary downloads are checksum-verified before atomic rename.
- A download must stop before free disk space falls below 15 GB.
- Large jobs use resumable checkpoints and bounded request concurrency.
- Failed or partial files stay outside immutable snapshots.

## 4. Strategy Data Contract

Before any data access, the workflow derives an immutable `StrategyDataContract`
from the StrategyVersion.

Required fields:

- `strategyVersionId` and `strategyContentHash`;
- market type and allowed direction;
- primary signal timeframe;
- execution-path timeframe;
- required OHLCV and derived feature inputs;
- historical point-in-time universe policy;
- requested start and end time;
- required market-regime coverage;
- fee, funding, slippage, latency, and gap assumptions;
- stop-loss model and `targetR >= 2`;
- walk-forward, symbol holdout, and locked-test profile versions;
- minimum sample and coverage requirements;
- data source and quality policy version.

The contract is stored before execution so the system cannot quietly change the
test after seeing results.

## 5. Timeframe Requirements

### 5.1 5m Strategy

```text
signal timeframe: 5m
execution path: 5m
```

When stop and target are both inside the same candle and no finer official data
is available, the result is resolved conservatively as stop-first. The report
must count and expose these ambiguous candles.

### 5.2 15m Strategy

```text
signal timeframe: 15m
execution path: 5m
```

Entries are decided only from completed 15m candles. The later 5m path is used
to evaluate stop, target, gap, and execution order.

### 5.3 1h Strategy

```text
signal timeframe: 1h
preferred execution path: 5m
fallback execution path: 15m
```

The fallback must be declared in the DataContract and reported as lower path
resolution. It cannot happen silently.

### 5.4 4h Strategy

```text
signal timeframe: 4h
preferred execution path: 5m
fallback execution path: 15m
```

Higher-timeframe features may be derived only from completed lower-timeframe
candles aligned to the exchange session clock. No future candle may leak into a
signal.

## 6. Seven-Part Formal Validation Pack

Every formal strategy evaluation contains all seven parts below.

### 6.1 Signal-Timeframe History

Official OHLCV for the strategy's primary timeframe, with sufficient warm-up
for every indicator and model feature.

### 6.2 Finer Execution Path

Official lower-timeframe candles used to determine the actual order of entry,
stop, target, gap, and exit events. This prevents a 4h candle from pretending
that the intrabar path is known.

### 6.3 Point-in-Time Dynamic Universe

The universe is reconstructed for each historical date from instruments that
were available and met the versioned liquidity policy at that time. It must not
be built only from coins that survive today.

The first formal pack targets a liquid OKX USDT swap universe of roughly 20 to
50 instruments when historical coverage permits. Later packs may expand it,
but every expansion remains snapshot-bound and reproducible.

### 6.4 Market-Regime Coverage

The requested 2020-2026 interval is divided into evidence-backed regimes such
as bull, bear, range, market crash, volatility expansion, and liquidity stress.
Unavailable pre-listing periods are recorded as unavailable, never fabricated.

### 6.5 Leakage-Resistant Validation

The formal pack includes:

- purged walk-forward training and validation windows;
- embargo around split boundaries;
- unseen-symbol holdout where the universe allows it;
- one final locked time period that is evaluated once per immutable strategy
  version and gate profile.

Repeated tuning against the locked period invalidates the evaluation and
requires a new declared protocol, not a relabeled pass.

### 6.6 Execution and Cost Stress

The backtest applies versioned assumptions for:

- maker and taker fees;
- historical public funding where applicable;
- base and stressed slippage;
- decision and order latency;
- missing candles and stale data;
- gap-through-stop handling;
- liquidity and participation limits.

Funding may be `null` only when unavailable and must then trigger an explicit
limitation or a conservative configured fallback. It must not be invented.

### 6.7 Fixed 2R Outcome Evaluation

Every trade records:

- entry basis and executable entry;
- stop price and initial risk in price and currency units;
- target price at `targetR >= 2`;
- gross and net R;
- fee, funding, slippage, and latency costs;
- MFE and MAE;
- exit reason;
- ambiguity and data-quality flags.

Changing the stop rule, target R, entry rule, or position-sizing rule changes
the strategy content hash and creates a new StrategyVersion.

## 7. One-Click Workflow

### 7.1 User Action

The Strategy page exposes one primary action for eligible strategies:

```text
一键双层回测
```

Batch selection may run multiple strategies, but the scheduler deduplicates
shared data contracts and enforces disk, CPU, memory, and public rate limits.

### 7.2 Workflow States

```text
queued
-> checking_local_data
-> research_smoke_running
-> preparing_official_data
-> validating_official_data
-> freezing_data_snapshot
-> building_validation_manifests
-> formal_backtest_running
-> evaluating_gate
-> passed | failed | blocked | paused | cancelled
-> local_forward_starting
-> local_forward_running
```

The visible Chinese statuses are:

- 检查本地数据
- 本地研究烟测
- 准备官方数据
- 校验正式数据
- 构建正式验证集
- 正式回测中
- 回测通过
- 回测未通过
- 数据或任务阻塞
- 正在进入本地前向
- 本地前向运行中

### 7.3 Local Smoke Behavior

The local smoke test answers only:

- Can the strategy load its declared fields?
- Are indicators finite after warm-up?
- Can the entry and 2R exit path execute without exceptions?
- Are timestamps ordered and duplicates handled?
- Can a small multi-symbol report be produced?

It does not answer whether the strategy is formally profitable or promotable.

### 7.4 Official Data Preparation

After implementation validity is known, the workflow:

1. compares the DataContract with the official catalog;
2. reuses checksum-matching valid partitions;
3. downloads only missing or stale official public partitions;
4. resumes interrupted work from checkpoints;
5. canonicalizes and validates each partition;
6. freezes an immutable DataSnapshot manifest;
7. creates walk-forward, holdout, and locked-OOS manifests.

Data download priority:

1. the currently requested strategy pack;
2. shared packs needed by active candidate strategies;
3. the liquid OKX USDT swap universe;
4. broader market history as resumable background preparation.

The button must not attempt an unbounded all-market download before giving the
current strategy a usable result.

### 7.5 Formal Backtest and Gate

Only the formal run may update the strategy workflow gate. It binds:

- immutable StrategyVersion;
- immutable StrategyDataContract;
- immutable DataSnapshot;
- purged WalkForward manifest;
- symbol Holdout manifest;
- LockedOOS manifest;
- CostModel version;
- GateProfile version;
- runner code version and environment hash.

The result includes pass/fail metrics, failed thresholds, concentration,
regime breakdown, ambiguity counts, robustness stress, and a plain Chinese
diagnosis. A failed strategy remains on the Strategy page and does not enter
Local Forward.

### 7.6 Automatic Local Forward Entry

When and only when the formal gate passes, the orchestrator automatically:

1. creates an immutable Local Forward Release;
2. records all formal evidence hashes;
3. sets the forward start cutoff to the current UTC time;
4. starts public-market collection and virtual execution;
5. moves the strategy from the Strategy page to the Local Simulation page;
6. preserves the complete backtest history in Advanced Details.

If Local Forward cannot start, the backtest remains passed but the forward run
is `blocked` with a recoverable operational reason. It must not be silently
returned to backtest or promoted to Demo.

## 8. Evaluation Binding and Immutability

Strategy logic and evaluation data have different identities.

### 8.1 StrategyVersion

Contains only behavior-changing material:

- factors, features, and model artifact;
- entry, exit, stop, target, and sizing rules;
- direction, timeframe, and universe-selection logic;
- strategy parameters.

### 8.2 EvaluationBinding

Contains one evaluation attempt's evidence inputs:

- DataContract hash;
- DataSnapshot hash;
- WalkForward, Holdout, and LockedOOS manifest hashes;
- CostModel and GateProfile versions;
- runner version;
- attempt number and timestamps.

A network retry or newly completed official partition creates a new
EvaluationBinding and WorkflowRun attempt while preserving the same strategy
content hash. Previous attempts remain immutable.

The currently blocked Alpha191 V1 remains unchanged as an audit record. When
the official pack is ready, a new evaluation attempt is attached to that
immutable strategy version. No missing evidence is filled with placeholders.

## 9. Concise Strategy Page

The default card shows only:

- understandable strategy name and version;
- direction and primary timeframe;
- current workflow status;
- progress and current data task;
- most recent formal result or blocker;
- primary action;
- pause, resume, or cancel when relevant.

The Advanced Details drawer contains:

- DataContract;
- source and partition catalog;
- snapshot and manifest hashes;
- smoke and formal reports;
- regime and symbol breakdown;
- costs, ambiguity, and data-quality diagnostics;
- checkpoints and raw logs.

The page must not display research smoke results as formal pass metrics.

## 10. Failure and Recovery

- Every workflow command uses an idempotency key.
- Downloads, canonicalization, manifests, and backtests expose checkpoints.
- Reboot recovery resumes the last incomplete stage without duplicating rows or
  evidence.
- Pause preserves checkpoints; cancel preserves audit events and valid files.
- A corrupted checksum quarantines the partition and blocks formal use.
- A public source outage blocks only the affected partitions.
- Insufficient historical listing coverage is reported per instrument.
- Disk guard, rate limit, or memory pressure pauses safely with a Chinese reason.
- A failed formal gate cannot be manually changed to passed.
- Generating a challenger never overwrites the failed version.

## 11. Formal Acceptance Criteria

The implementation is accepted only when:

1. Existing raw folders remain unchanged.
2. Research and formal warehouse evidence cannot be confused.
3. Every strategy produces a stored StrategyDataContract before data access.
4. `5m`, `15m`, `1h`, and `4h` contracts select the declared signal and
   execution-path timeframes.
5. Local smoke can identify implementation defects but cannot promote.
6. Official downloads are public-only, resumable, checksum-verified, and
   provenance-complete.
7. Formal snapshots never silently contain third-party rows.
8. Dynamic universe construction is point-in-time and does not use only current
   survivors.
9. Purged walk-forward, embargo, holdout, and locked OOS are immutable inputs.
10. Fixed `targetR >= 2` is enforced and same-bar ambiguity is conservative.
11. Fees, slippage, funding, latency, gaps, and liquidity stress are reported.
12. A formal failed or blocked result stays on the Strategy page with a reason.
13. A formal pass automatically starts Local Forward and no later stage.
14. Data-only retries preserve strategy identity and append a new evaluation.
15. Logic or parameter changes create a new StrategyVersion.
16. Restart recovery does not duplicate downloads, trades, or evidence.
17. The default UI remains concise and technical detail stays collapsible.
18. No account, position, private exchange, order, or credential access exists in
    this data and backtest workflow.

## 12. Safety Boundary

- Public market data only.
- No Trade API.
- No Withdraw API.
- No API key storage or input.
- No real account balance or position access.
- No exchange order creation or cancellation.
- Backtest and Local Forward use research or virtual execution only.
- No automatic transition from backtest to Demo or Live.
- No historical result is described as guaranteed future profitability.

## 13. Research Integrity References

The locked-test and leakage controls follow the general backtest-overfitting
risks described in:

- Bailey et al., *The Probability of Backtest Overfitting*:
  https://escholarship.org/uc/item/4w1110bb
- Bailey and Lopez de Prado, *The Deflated Sharpe Ratio*:
  https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2460551

These references guide validation discipline; they do not determine whether an
AlphaPilot strategy passes its versioned GateProfile.
