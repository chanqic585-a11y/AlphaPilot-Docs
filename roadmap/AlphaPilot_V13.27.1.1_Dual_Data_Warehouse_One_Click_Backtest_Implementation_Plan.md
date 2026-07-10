# AlphaPilot V13.27.1.1 Dual Data Warehouse and One-Click Backtest Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a restart-safe `一键双层回测` workflow that uses existing local files for research smoke checks, prepares provenance-complete official OKX public data for formal validation, and automatically starts Local Forward only after a formal gate pass.

**Architecture:** Keep immutable strategy logic separate from immutable evaluation inputs. Add `StrategyDataContract` and `EvaluationBinding` records, reuse the existing V13.16 data foundation and V13.27 workflow registry, and compose preparation, validation, formal backtest, and Local Forward as checkpointed worker phases. The Control Console remains a thin command and projection client.

**Tech Stack:** Python 3.12, standard-library `unittest`, SQLite, pandas 3.0.1, pyarrow 24.0.0, Parquet/ZSTD, OKX public REST endpoints, PowerShell, vanilla HTML/CSS/JavaScript.

## Global Constraints

- Historical root is exactly `D:\Codex-Workspace\回测数据`.
- Existing `5m`, `合约数据`, and `现货数据` files are read-only inputs and must not be moved, rewritten, or deleted.
- AlphaPilot-owned data lives under `D:\Codex-Workspace\回测数据\_alphapilot`.
- Existing local files produce `research_smoke` evidence only and can never satisfy a formal gate.
- Formal data uses official OKX public endpoints only and records endpoint, parameters, collection time, checksums, candle confirmation, continuity, and point-in-time cutoff.
- Strategy timeframes are `5m`, `15m`, `1h`, or `4h`; `15m/1h/4h` prefer `5m` execution paths and may use a declared `15m` fallback only when the contract permits it.
- Every formal result enforces `targetR >= 2` and conservative stop-first handling when stop and target are ambiguous within the finest available candle.
- Formal validation includes point-in-time universe membership, regime coverage, purged walk-forward, unseen-symbol holdout, final locked OOS, and fee/slippage/funding/latency stress.
- Formal pass automatically starts Local Forward only. Demo and Live remain separate, user-controlled stages.
- Strategy logic or parameter changes create a new StrategyVersion. Data-only retries create a new EvaluationBinding and WorkflowRun attempt for the same content hash.
- Runtime data under repository `data/` remains untracked and must not be deleted or committed.
- No Trade API, Withdraw API, API key, account read, position read, exchange order, or Live execution may be added.
- Minimum free-space guard is 15 GB.
- The current blocked Alpha191 V1 remains immutable and auditable.

---

## File Structure

### AlphaPilot Quant Engine

Create:

- `alphapilot/evolution/workflow/data_contract.py` - derive and validate immutable strategy data requirements.
- `alphapilot/evolution/workflow/evaluation_binding.py` - create immutable run-to-evidence bindings.
- `alphapilot/evolution/workflow/dual_layer.py` - checkpointed top-level one-click workflow.
- `alphapilot/data_foundation/warehouse.py` - fixed warehouse paths, atomic writes, and disk guard.
- `alphapilot/data_foundation/research_smoke.py` - local third-party research-only implementation smoke.
- `alphapilot/data_foundation/official_history.py` - resumable OKX public metadata, OHLCV, and funding collector.
- `alphapilot/data_foundation/formal_snapshot.py` - official partition validation and immutable snapshot registration.
- `alphapilot/evolution/evaluation/validation_pack.py` - walk-forward, holdout, locked OOS, regime, and cost manifests.
- `alphapilot/evolution/evaluation/fixed_r_path.py` - signal/execution timeframe fixed-2R path evaluator.
- `alphapilot/evolution/evaluation/formal_strategy_backtest.py` - snapshot-bound strategy adapter and report.
- `alphapilot/evolution/workflow/local_forward_bridge.py` - formal-pass candidate/release creation and forward start.
- `scripts/run_v13_27_1_1_dual_backtest.ps1` - manual/resumable operator entrypoint.
- `tests/data_foundation/test_warehouse.py`
- `tests/data_foundation/test_research_smoke.py`
- `tests/data_foundation/test_official_history.py`
- `tests/data_foundation/test_formal_snapshot.py`
- `tests/evolution/test_workflow_data_contract.py`
- `tests/evolution/test_evaluation_binding.py`
- `tests/evolution/test_validation_pack.py`
- `tests/evolution/test_fixed_r_path.py`
- `tests/evolution/test_formal_strategy_backtest.py`
- `tests/evolution/test_dual_layer_workflow.py`
- `tests/evolution/test_workflow_local_forward_bridge.py`

Modify:

- `alphapilot/evolution/registry/migrations.py` - add migration v7.
- `alphapilot/evolution/workflow/types.py` - add contract and binding records.
- `alphapilot/evolution/workflow/repository.py` - persist and query v7 records.
- `alphapilot/evolution/workflow/backtest.py` - consume EvaluationBinding instead of strategy-embedded evidence.
- `alphapilot/evolution/workflow/cli.py` - add `one-click-backtest` and stage-aware execution.
- `alphapilot/evolution/workflow/projection.py` - expose phase, evidence class, data coverage, and automatic-forward state.
- `alphapilot/evolution/workflow/bootstrap.py` - declare Alpha191 data requirements without inventing snapshot IDs.
- `alphapilot/data_foundation/okx_public.py` - add bounded historical pagination and public metadata/funding calls.
- `alphapilot/derivatives/feature_panel.py` - build feature panels from snapshot-bound frames.
- `alphapilot/reports/generate_v13_5_23_alpha191_crypto_subset_replay_report.py` - reuse public Alpha191 signal builder without changing legacy output.
- `alphapilot/evolution/forward/release.py` - accept formal workflow evidence through a separate strict factory.
- `alphapilot/evolution/forward/runner.py` - expose a workflow-safe one-cycle entrypoint.
- `tests/evolution/test_registry_migrations.py`
- `tests/evolution/test_workflow_backtest.py`
- `tests/evolution/test_workflow_cli.py`
- `README.md`

### AlphaPilot Control Console

Modify:

- `alphapilot_control_console/workflow_client.py` - request `one-click-backtest` and stage-aware recovery.
- `alphapilot_control_console/http_app.py` - return V13.27.1.1 workflow progress.
- `tests/test_workflow_client.py` - verify fixed command construction and background worker behavior.
- `web/index.html` - rename primary actions and add concise progress copy.
- `web/app.js` - render dual-layer phases, blockers, and automatic Local Forward transition.
- `web/styles.css` - keep the new progress presentation mobile-safe.
- `docs/V13.27.1.1-dual-data-one-click-backtest.md`
- `README.md`

### AlphaPilot Docs

Modify:

- `roadmap/AlphaPilot_V13.27_Unified_Workflow_Orchestrator_Implementation_Plan.md`
- `README.md`

---

### Task 1: Add Immutable Data Contract and Evaluation Binding Records

**Files:**
- Modify: `AlphaPilot-Quant-Engine/alphapilot/evolution/registry/migrations.py`
- Modify: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/types.py`
- Modify: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/repository.py`
- Test: `AlphaPilot-Quant-Engine/tests/evolution/test_registry_migrations.py`
- Create: `AlphaPilot-Quant-Engine/tests/evolution/test_evaluation_binding.py`

**Interfaces:**
- Produces: `StrategyDataContractRecord`, `EvaluationBindingRecord`.
- Produces repository methods `create_strategy_data_contract`, `get_strategy_data_contract`, `list_strategy_data_contracts`, `create_evaluation_binding`, `get_evaluation_binding`, and `get_evaluation_binding_for_run`.
- Consumed by Tasks 2, 5, 7, 8, and 9.

- [ ] **Step 1: Write migration tests that require v7 tables and safe v6 upgrade**

Add `StrategyDataContracts` and `EvaluationBindings` to `EXPECTED_TABLES`, then add a test that applies `MIGRATIONS[:6]`, inserts a StrategyVersion and WorkflowRun, applies v7, and verifies old rows and foreign keys remain valid.

Expected schema:

```sql
CREATE TABLE IF NOT EXISTS StrategyDataContracts (
  strategyDataContractId TEXT PRIMARY KEY,
  strategyVersionId TEXT NOT NULL,
  schemaVersion TEXT NOT NULL,
  contractJson TEXT NOT NULL,
  contentHash TEXT NOT NULL,
  createdAt TEXT NOT NULL,
  FOREIGN KEY (strategyVersionId) REFERENCES StrategyVersions(strategyVersionId)
);

CREATE UNIQUE INDEX IF NOT EXISTS idx_strategy_data_contracts_version_hash
ON StrategyDataContracts(strategyVersionId, contentHash);

CREATE TABLE IF NOT EXISTS EvaluationBindings (
  evaluationBindingId TEXT PRIMARY KEY,
  workflowRunId TEXT NOT NULL UNIQUE,
  strategyDataContractId TEXT NOT NULL,
  dataSnapshotId TEXT NOT NULL,
  walkForwardManifestHash TEXT NOT NULL,
  holdoutManifestHash TEXT NOT NULL,
  lockedOosManifestHash TEXT NOT NULL,
  gateProfileId TEXT NOT NULL,
  runnerVersion TEXT NOT NULL,
  costModelJson TEXT NOT NULL,
  evidenceJson TEXT NOT NULL,
  contentHash TEXT NOT NULL,
  createdAt TEXT NOT NULL,
  FOREIGN KEY (workflowRunId) REFERENCES WorkflowRuns(workflowRunId),
  FOREIGN KEY (strategyDataContractId) REFERENCES StrategyDataContracts(strategyDataContractId),
  FOREIGN KEY (dataSnapshotId) REFERENCES DataSnapshots(dataSnapshotId),
  FOREIGN KEY (gateProfileId) REFERENCES GateProfiles(gateProfileId)
);
```

- [ ] **Step 2: Run migration tests and verify they fail before v7 exists**

Run:

```powershell
.\.venv\Scripts\python.exe -m unittest tests.evolution.test_registry_migrations tests.evolution.test_evaluation_binding -v
```

Expected: failure naming missing v7 tables or missing record classes.

- [ ] **Step 3: Add v7 migration, typed records, and idempotent repository methods**

Add records with these exact fields:

```python
@dataclass(frozen=True)
class StrategyDataContractRecord:
    strategyDataContractId: str
    strategyVersionId: str
    schemaVersion: str
    contract: dict[str, Any]
    contentHash: str
    createdAt: str = field(default_factory=utc_now)


@dataclass(frozen=True)
class EvaluationBindingRecord:
    evaluationBindingId: str
    workflowRunId: str
    strategyDataContractId: str
    dataSnapshotId: str
    walkForwardManifestHash: str
    holdoutManifestHash: str
    lockedOosManifestHash: str
    gateProfileId: str
    runnerVersion: str
    costModel: dict[str, Any]
    evidence: dict[str, Any]
    contentHash: str
    createdAt: str = field(default_factory=utc_now)
```

Repository inserts must return an existing record only when its content hash is identical. Conflicting hashes raise the existing immutable-record conflict.

- [ ] **Step 4: Run migration, repository, and workflow tests**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.evolution.test_registry_migrations tests.evolution.test_evaluation_binding tests.evolution.test_workflow_orchestrator -v
```

Expected: all pass; `PRAGMA foreign_key_check` returns no rows.

- [ ] **Step 5: Commit Quant schema work**

```powershell
git add alphapilot/evolution/registry/migrations.py alphapilot/evolution/workflow/types.py alphapilot/evolution/workflow/repository.py tests/evolution/test_registry_migrations.py tests/evolution/test_evaluation_binding.py
git commit -m "Add immutable workflow evaluation bindings"
```

### Task 2: Derive Timeframe-Aware Strategy Data Contracts

**Files:**
- Create: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/data_contract.py`
- Create: `AlphaPilot-Quant-Engine/tests/evolution/test_workflow_data_contract.py`
- Modify: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/bootstrap.py`

**Interfaces:**
- Consumes: `StrategyVersionRecord` and `WorkflowRepository.create_strategy_data_contract`.
- Produces: `derive_strategy_data_contract(version, repository) -> StrategyDataContractRecord`.
- Produces: `timeframe_plan(primary_timeframe) -> dict[str, Any]`.

- [ ] **Step 1: Write contract tests for all four supported timeframes**

The parameterized expectations are:

```python
EXPECTED = {
    "5m": {"signal": "5m", "execution": "5m", "fallback": None},
    "15m": {"signal": "15m", "execution": "5m", "fallback": None},
    "1h": {"signal": "1h", "execution": "5m", "fallback": "15m"},
    "4h": {"signal": "4h", "execution": "5m", "fallback": "15m"},
}
```

Tests must also reject unsupported timeframes, `targetR < 2`, missing market, and strategy content hash mismatch. Repeated derivation must return the same contract ID.

- [ ] **Step 2: Run contract tests and verify red**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.evolution.test_workflow_data_contract -v
```

Expected: import failure for `data_contract`.

- [ ] **Step 3: Implement canonical contract derivation**

The contract payload must contain these exact top-level keys:

```python
contract = {
    "schemaVersion": "strategy_data_contract_v1",
    "strategyVersionId": version.strategyVersionId,
    "strategyContentHash": version.contentHash,
    "marketType": "swap",
    "direction": definition["direction"],
    "signalTimeframe": plan["signal"],
    "executionTimeframe": plan["execution"],
    "executionFallbackTimeframe": plan["fallback"],
    "requestedStart": "2020-01-01T00:00:00+00:00",
    "requestedEndPolicy": "latest_completed_at_run",
    "targetR": float(definition["targetR"]),
    "requiredDataKinds": ["ohlcv", "funding", "instrument_metadata"],
    "universePolicy": {
        "type": "point_in_time_dynamic_liquid_usdt_swap",
        "minimumMembers": 20,
        "targetMembers": 50,
        "candidateDiscovery": ["local_catalog", "okx_public_instruments"],
    },
    "validationPolicy": {
        "purgedWalkForward": True,
        "unseenSymbolHoldout": True,
        "lockedOos": True,
        "regimeCoverage": ["bull", "bear", "range", "crash", "volatility_expansion"],
        "sameBarAmbiguity": "stop_first",
    },
    "costPolicy": {
        "feeRate": 0.0005,
        "slippageRate": 0.0002,
        "fundingRequiredForSwap": True,
        "latencyBars": [0, 1, 2],
        "stressMultipliers": [1.0, 1.5, 2.0],
    },
}
```

Use `stable_hash(contract, prefix="strategy_data_contract")` for both identity and conflict checks.

- [ ] **Step 4: Update Alpha191 bootstrap definition without data IDs**

Keep the immutable strategy behavior unchanged. Add only declarative data policy fields under `definition["dataRequirements"]`; do not insert a snapshot, walk-forward hash, or locked-test hash.

- [ ] **Step 5: Run tests and commit**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.evolution.test_workflow_data_contract tests.evolution.test_workflow_cli -v
git add alphapilot/evolution/workflow/data_contract.py alphapilot/evolution/workflow/bootstrap.py tests/evolution/test_workflow_data_contract.py
git commit -m "Derive timeframe-aware strategy data contracts"
```

### Task 3: Build Warehouse Layout and Research-Only Smoke Validation

**Files:**
- Create: `AlphaPilot-Quant-Engine/alphapilot/data_foundation/warehouse.py`
- Create: `AlphaPilot-Quant-Engine/alphapilot/data_foundation/research_smoke.py`
- Create: `AlphaPilot-Quant-Engine/tests/data_foundation/test_warehouse.py`
- Create: `AlphaPilot-Quant-Engine/tests/data_foundation/test_research_smoke.py`

**Interfaces:**
- Consumes: a StrategyDataContract payload and `catalog.discover_raw_assets`.
- Produces: `WarehouseLayout.from_root(root: Path | str) -> WarehouseLayout`, `ensure_capacity(layout: WarehouseLayout, estimated_bytes: int) -> None`, and `run_research_smoke(contract: StrategyDataContractRecord, layout: WarehouseLayout, output_path: Path | str) -> dict[str, Any]`.
- Produces evidence with `evidenceClass="research_smoke"` and `formalPromotionEligible=False`.

- [ ] **Step 1: Write path, disk-guard, and evidence-class tests**

Use a temporary root with `5m`, `合约数据`, and `现货数据`. Assert AlphaPilot paths are under `_alphapilot`, existing source files remain byte-identical, and mocked free space below 15 GB raises `WarehouseCapacityError`.

Research smoke tests must assert:

```python
self.assertEqual(result["evidenceClass"], "research_smoke")
self.assertFalse(result["formalPromotionEligible"])
self.assertNotIn("passedFormalGate", result)
```

- [ ] **Step 2: Run tests and verify red**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.data_foundation.test_warehouse tests.data_foundation.test_research_smoke -v
```

- [ ] **Step 3: Implement fixed layout and atomic report paths**

`WarehouseLayout` must expose:

```python
rawRoot
localFiveMinuteRoot
localSwapRoot
localSpotRoot
alphaPilotRoot
officialRawRoot
canonicalRoot
catalogRoot
manifestRoot
checkpointRoot
reportRoot
temporaryRoot
```

`ensure_capacity` uses `shutil.disk_usage(layout.alphaPilotRoot.parent)` and fails before a write if projected free bytes would fall below `15 * 1024**3`.

- [ ] **Step 4: Implement research smoke as an implementation check, not a performance gate**

Select at most BTC, ETH, SOL plus up to two contract-required symbols that exist locally. Read bounded rows through existing readers, validate ordering/OHLCV/finiteness/warm-up, and save a hash-bound JSON report. Performance fields may be included for diagnosis but cannot determine formal promotion.

- [ ] **Step 5: Run tests and commit**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.data_foundation.test_warehouse tests.data_foundation.test_research_smoke tests.data_foundation.test_catalog -v
git add alphapilot/data_foundation/warehouse.py alphapilot/data_foundation/research_smoke.py tests/data_foundation/test_warehouse.py tests/data_foundation/test_research_smoke.py
git commit -m "Add dual warehouse research smoke layer"
```

### Task 4: Add Resumable Official OKX Public History Collection

**Files:**
- Modify: `AlphaPilot-Quant-Engine/alphapilot/data_foundation/okx_public.py`
- Create: `AlphaPilot-Quant-Engine/alphapilot/data_foundation/official_history.py`
- Create: `AlphaPilot-Quant-Engine/tests/data_foundation/test_official_history.py`
- Modify: `AlphaPilot-Quant-Engine/tests/data_foundation/test_okx_public.py`

**Interfaces:**
- Consumes: StrategyDataContract, WarehouseLayout, checkpoint path, and pause marker.
- Produces: `OkxOfficialHistoryCollector.collect(contract, progress) -> OfficialCollectionResult`.
- Calls only `GET /api/v5/public/instruments`, `GET /api/v5/market/history-candles`, and `GET /api/v5/public/funding-rate-history`.

- [ ] **Step 1: Write fake-client pagination, rate-limit, resume, and quarantine tests**

Tests must cover:

- descending OKX pages are normalized to ascending timestamps;
- only `confirm == "1"` candles are persisted;
- request parameters and endpoint are stored;
- a checkpoint resumes from the oldest completed cursor without duplicates;
- an identical retry returns existing checksum-matching partitions;
- malformed or misaligned rows are quarantined;
- a pause marker stops after the current atomic partition;
- no credential or private endpoint argument exists.

- [ ] **Step 2: Run tests and verify red**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.data_foundation.test_okx_public tests.data_foundation.test_official_history -v
```

- [ ] **Step 3: Extend the public client with bounded methods**

Add exact signatures:

```python
def public_instruments(self, *, instrument_type: str = "SWAP") -> list[dict[str, Any]]:
    return [
        dict(item)
        for item in self._get(
            "/api/v5/public/instruments",
            {"instType": instrument_type},
        )
        if isinstance(item, dict)
    ]

def funding_rate_history(
    self,
    *,
    instrument_id: str,
    before_ms: int | None = None,
    after_ms: int | None = None,
    limit: int = 100,
) -> list[dict[str, Any]]:
    parameters: dict[str, Any] = {
        "instId": instrument_id,
        "limit": max(1, min(int(limit), 100)),
    }
    if before_ms is not None:
        parameters["before"] = int(before_ms)
    if after_ms is not None:
        parameters["after"] = int(after_ms)
    return [
        dict(item)
        for item in self._get("/api/v5/public/funding-rate-history", parameters)
        if isinstance(item, dict)
    ]

def history_candle_page(
    self,
    *,
    instrument_id: str,
    timeframe: str,
    after_ms: int | None,
    limit: int = 100,
) -> list[list[Any]]:
    parameters: dict[str, Any] = {
        "instId": instrument_id,
        "bar": BAR_VALUES[timeframe],
        "limit": max(1, min(int(limit), 100)),
    }
    if after_ms is not None:
        parameters["after"] = int(after_ms)
    return [
        list(item)
        for item in self._get("/api/v5/market/history-candles", parameters)
        if isinstance(item, list)
    ]
```

Keep the default throttle at or above 0.12 seconds and retry OKX rate-limit responses with bounded exponential backoff. Never retry non-rate-limit schema errors indefinitely.

- [ ] **Step 4: Implement strategy-first partition collection**

Candidate instruments are the union of names discovered in the local catalog and current official USDT swap instruments. Inclusion requires official candle confirmation. Build point-in-time membership from official first/last candle and rolling quote-volume eligibility. Collect the current strategy pack before shared/background packs.

Canonical partitions use:

```text
_alphapilot/canonical/okx/swap/{data_kind}/{instrument_id}/{timeframe}/{start}-{end}-{sha16}.parquet
```

- [ ] **Step 5: Run tests and commit**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.data_foundation.test_okx_public tests.data_foundation.test_official_history -v
git add alphapilot/data_foundation/okx_public.py alphapilot/data_foundation/official_history.py tests/data_foundation/test_okx_public.py tests/data_foundation/test_official_history.py
git commit -m "Collect resumable official OKX history"
```

### Task 5: Freeze Formal Snapshot and Seven-Part Validation Pack

**Files:**
- Create: `AlphaPilot-Quant-Engine/alphapilot/data_foundation/formal_snapshot.py`
- Create: `AlphaPilot-Quant-Engine/alphapilot/evolution/evaluation/validation_pack.py`
- Create: `AlphaPilot-Quant-Engine/tests/data_foundation/test_formal_snapshot.py`
- Create: `AlphaPilot-Quant-Engine/tests/evolution/test_validation_pack.py`

**Interfaces:**
- Consumes: official collection result and StrategyDataContract.
- Produces: registered `DataSnapshotRecord` plus walk-forward, holdout, locked-OOS, regime, and cost manifest files.
- Produces: `FormalValidationPack` with hashes consumed by EvaluationBinding.

- [ ] **Step 1: Write provenance and leakage-gate tests**

Tests must reject snapshots that contain a `third_party_unverified` file, checksum mismatch, missing confirmed flag, unresolved gap, end time after point-in-time cutoff, fewer than the contract minimum universe members, absent BTC regime series, or no unseen-symbol holdout.

- [ ] **Step 2: Write deterministic split tests**

For a fixed snapshot and contract, assert repeated builds produce identical hashes. The locked OOS begins after all training/validation folds. Purge equals label horizon; embargo is at least maximum holding period. Holdout symbols never appear in training folds.

- [ ] **Step 3: Run tests and verify red**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.data_foundation.test_formal_snapshot tests.evolution.test_validation_pack -v
```

- [ ] **Step 4: Implement official snapshot metadata and validation pack**

Snapshot metadata must include:

```python
{
    "provenanceComplete": True,
    "pointInTimeValidated": True,
    "formalResearchEligible": True,
    "formalPromotionEligible": True,
    "evidenceClass": "formal_backtest",
    "strategyDataContractId": contract.strategyDataContractId,
    "universeManifestHash": universe_hash,
    "qualityManifestHash": quality_hash,
}
```

The validation pack saves all manifests under `_alphapilot/manifests/{contract_id}/{snapshot_id}/` using atomic writes and hashes canonical JSON content.

- [ ] **Step 5: Run tests and commit**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.data_foundation.test_formal_snapshot tests.evolution.test_validation_pack tests.evolution.test_purged_walk_forward -v
git add alphapilot/data_foundation/formal_snapshot.py alphapilot/evolution/evaluation/validation_pack.py tests/data_foundation/test_formal_snapshot.py tests/evolution/test_validation_pack.py
git commit -m "Build formal strategy validation packs"
```

### Task 6: Implement Snapshot-Bound Fixed-2R Formal Backtest

**Files:**
- Create: `AlphaPilot-Quant-Engine/alphapilot/evolution/evaluation/fixed_r_path.py`
- Create: `AlphaPilot-Quant-Engine/alphapilot/evolution/evaluation/formal_strategy_backtest.py`
- Modify: `AlphaPilot-Quant-Engine/alphapilot/derivatives/feature_panel.py`
- Modify: `AlphaPilot-Quant-Engine/alphapilot/reports/generate_v13_5_23_alpha191_crypto_subset_replay_report.py`
- Create: `AlphaPilot-Quant-Engine/tests/evolution/test_fixed_r_path.py`
- Create: `AlphaPilot-Quant-Engine/tests/evolution/test_formal_strategy_backtest.py`

**Interfaces:**
- Consumes: StrategyVersion, EvaluationBinding, DataSnapshot, and validation manifests.
- Produces: `run_formal_strategy_backtest(strategy_version: StrategyVersionRecord, evaluation_binding: EvaluationBindingRecord, snapshot: DataSnapshotRecord, manifest_root: Path | str) -> BacktestAdapterResult`.
- Produces actual-candle outcomes with gross/net R, fees, slippage, funding, latency, MFE, MAE, ambiguity, regime, symbol, and fold attribution.

- [ ] **Step 1: Write path-order and no-lookahead tests**

Cover long and short target, stop, time exit, gap-through stop, both-hit stop-first, next-bar entry, funding cost, stressed slippage, and one/two-bar latency. Assert signals at candle `t` cannot use candle `t+1` features.

- [ ] **Step 2: Write formal adapter tests with tiny snapshot fixtures**

Use three symbols, separate signal and execution frames, three walk-forward folds, one holdout symbol, and one locked segment. Assert output sections are disjoint and result evidence contains every manifest hash.

- [ ] **Step 3: Run tests and verify red**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.evolution.test_fixed_r_path tests.evolution.test_formal_strategy_backtest -v
```

- [ ] **Step 4: Extract a public Alpha191 signal builder and implement the formal adapter**

Move the deterministic overlay predicate behind this public interface while keeping the V13.5.23 report output unchanged:

```python
def build_alpha191_observer_signals(
    feature_panel: pd.DataFrame,
    *,
    overlay_id: str,
) -> pd.DataFrame:
    """Return immutable signal rows without labels or future path columns."""
```

Add `build_derivatives_feature_panel_from_frames` so formal runs consume only files named in the DataSnapshot. Do not read default `user_data/data` paths from the formal adapter.

- [ ] **Step 5: Compute gate-ready metrics by split, symbol, and regime**

The result must include at least `tradeCount`, `profitFactor`, `averageNetR`, `maximumDrawdownR`, `winRate`, `ambiguousPathCount`, `holdoutTradeCount`, `lockedTradeCount`, and cost-stress checks. Missing required evidence is a block, never zero-filled success.

- [ ] **Step 6: Run tests and commit**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.evolution.test_fixed_r_path tests.evolution.test_formal_strategy_backtest -v
git add alphapilot/evolution/evaluation/fixed_r_path.py alphapilot/evolution/evaluation/formal_strategy_backtest.py alphapilot/derivatives/feature_panel.py alphapilot/reports/generate_v13_5_23_alpha191_crypto_subset_replay_report.py tests/evolution/test_fixed_r_path.py tests/evolution/test_formal_strategy_backtest.py
git commit -m "Run snapshot-bound fixed 2R backtests"
```

### Task 7: Compose the Restart-Safe Dual-Layer Workflow

**Files:**
- Create: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/evaluation_binding.py`
- Create: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/dual_layer.py`
- Modify: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/backtest.py`
- Modify: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/service.py`
- Modify: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/projection.py`
- Create: `AlphaPilot-Quant-Engine/tests/evolution/test_dual_layer_workflow.py`
- Modify: `AlphaPilot-Quant-Engine/tests/evolution/test_workflow_backtest.py`

**Interfaces:**
- Produces: `run_dual_layer_backtest_workflow(workflow: WorkflowRepository, registry: RegistryRepository, workflow_run_id: str, warehouse_root: Path | str, output_root: Path | str, dependencies: DualLayerDependencies | None = None) -> WorkflowRunRecord`.
- Produces: `retry_backtest_for_data_preparation(repository, blocked_run_id, actor)`.
- Changes `run_backtest_workflow` to require an EvaluationBinding and never read evidence IDs from `StrategyVersion.definition` for new runs.

- [ ] **Step 1: Write phase, checkpoint, retry, and evidence-separation tests**

The test worker uses fake smoke, official collector, snapshot, pack, and adapter dependencies. Assert phase order exactly matches:

```python
[
    "checking_local_data",
    "research_smoke_running",
    "preparing_official_data",
    "validating_official_data",
    "freezing_data_snapshot",
    "building_validation_manifests",
    "formal_backtest_running",
    "evaluating_gate",
]
```

Assert a smoke performance failure does not stop official preparation, while malformed data or adapter exceptions block safely. Assert resume skips checksum-valid completed phases.

- [ ] **Step 2: Run tests and verify red**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.evolution.test_dual_layer_workflow tests.evolution.test_workflow_backtest -v
```

- [ ] **Step 3: Implement binding construction and data-only retry**

The binding content hash includes contract, snapshot, every validation manifest, cost model, gate profile, runner version, and evidence checksums. Retrying the existing Alpha191 blocked run closes only that attempt as `cancelled` with a StageEvent, creates attempt 2 for the same StrategyVersion, and leaves attempt 1 intact.

- [ ] **Step 4: Implement checkpointed phase composition**

Each phase writes progress through `checkpoint_workflow_run`. Pause and cancel are checked before network pages, between canonical partitions, before formal adapter execution, and before Local Forward promotion.

- [ ] **Step 5: Run tests and commit**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.evolution.test_dual_layer_workflow tests.evolution.test_workflow_backtest tests.evolution.test_workflow_orchestrator -v
git add alphapilot/evolution/workflow/evaluation_binding.py alphapilot/evolution/workflow/dual_layer.py alphapilot/evolution/workflow/backtest.py alphapilot/evolution/workflow/service.py alphapilot/evolution/workflow/projection.py tests/evolution/test_dual_layer_workflow.py tests/evolution/test_workflow_backtest.py
git commit -m "Orchestrate dual-layer one-click backtests"
```

### Task 8: Bridge Formal Pass to Local Forward Only

**Files:**
- Create: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/local_forward_bridge.py`
- Modify: `AlphaPilot-Quant-Engine/alphapilot/evolution/forward/release.py`
- Modify: `AlphaPilot-Quant-Engine/alphapilot/evolution/forward/runner.py`
- Modify: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/dual_layer.py`
- Create: `AlphaPilot-Quant-Engine/tests/evolution/test_workflow_local_forward_bridge.py`

**Interfaces:**
- Consumes: passed backtest run and EvaluationBinding.
- Produces: immutable StrategyCandidate lineage record when absent, ForwardRelease, ForwardSession, and `local_forward` WorkflowRun.
- Produces: `start_local_forward_after_pass(workflow: WorkflowRepository, registry: RegistryRepository, strategy_version: StrategyVersionRecord, backtest_run: WorkflowRunRecord, evaluation_binding: EvaluationBindingRecord, code_commit: str, market_data: ForwardPublicMarket) -> WorkflowRunRecord`.

- [ ] **Step 1: Write strict promotion tests**

Assert promotion is rejected for smoke-only evidence, failed gates, target R below 2, hash mismatch, missing signal policy, missing risk profile, or non-public execution. Assert the generated next run is `local_forward/running`, has a release/session ID, and creates no Demo or Live record.

- [ ] **Step 2: Run tests and verify red**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.evolution.test_workflow_local_forward_bridge -v
```

- [ ] **Step 3: Implement a separate formal workflow release factory**

Do not weaken legacy `create_forward_release`. Add:

```python
def create_workflow_forward_release(
    *,
    strategy_version: StrategyVersionRecord,
    strategy_candidate: StrategyCandidateRecord,
    evaluation_binding: EvaluationBindingRecord,
    backtest_result: dict[str, Any],
    repository: RegistryRepository,
    code_commit: str,
    risk_profile: RiskProfileRecord | None = None,
) -> ForwardReleaseRecord:
    if backtest_result.get("status") != "passed":
        raise ValueError("Formal passed backtest evidence is required")
    if evaluation_binding.evidence.get("evidenceClass") != "formal_backtest":
        raise ValueError("Formal workflow evidence is required")
    if float(strategy_version.definition.get("targetR", 0.0)) < 2.0:
        raise ValueError("Forward release requires targetR >= 2")
    return _persist_workflow_forward_release(
        strategy_version=strategy_version,
        strategy_candidate=strategy_candidate,
        evaluation_binding=evaluation_binding,
        backtest_result=backtest_result,
        repository=repository,
        code_commit=code_commit,
        risk_profile=risk_profile,
    )
```

`_persist_workflow_forward_release` is private to `release.py`; it validates the registered candidate, active `local_forward` RiskProfile, frozen signal policy, public-only market definition, and every formal evidence hash before inserting the immutable record.

The release is virtual/public-only and binds every formal hash.

- [ ] **Step 4: Start one real public cycle and leave a restart-safe running workflow**

Use the existing `run_forward_cycle`. If no newly completed public candle exists, persist a healthy checkpoint without fabricating a sample. Operational failure blocks the Local Forward run but does not revoke the passed backtest.

- [ ] **Step 5: Run tests and commit**

```powershell
.\.venv\Scripts\python.exe -m unittest tests.evolution.test_workflow_local_forward_bridge tests.evolution.test_local_forward_runner tests.evolution.test_local_forward_engine -v
git add alphapilot/evolution/workflow/local_forward_bridge.py alphapilot/evolution/forward/release.py alphapilot/evolution/forward/runner.py alphapilot/evolution/workflow/dual_layer.py tests/evolution/test_workflow_local_forward_bridge.py
git commit -m "Start local forward after formal backtest pass"
```

### Task 9: Add CLI, Control Console Action, and Concise UI

**Files:**
- Modify: `AlphaPilot-Quant-Engine/alphapilot/evolution/workflow/cli.py`
- Modify: `AlphaPilot-Quant-Engine/tests/evolution/test_workflow_cli.py`
- Create: `AlphaPilot-Quant-Engine/scripts/run_v13_27_1_1_dual_backtest.ps1`
- Modify: `AlphaPilot-Control-Console/alphapilot_control_console/workflow_client.py`
- Modify: `AlphaPilot-Control-Console/alphapilot_control_console/http_app.py`
- Modify: `AlphaPilot-Control-Console/tests/test_workflow_client.py`
- Modify: `AlphaPilot-Control-Console/web/index.html`
- Modify: `AlphaPilot-Control-Console/web/app.js`
- Modify: `AlphaPilot-Control-Console/web/styles.css`

**Interfaces:**
- Quant CLI example: `one-click-backtest --run-id workflow_run_example --warehouse-root D:\Codex-Workspace\回测数据`.
- Console action: `run-dual-layer` with `workflowRunId` only.
- Projection fields: `workflowMode`, `evidenceClass`, `phase`, `phaseLabel`, `dataCoverage`, `downloadProgress`, `automaticNextStage`.

- [ ] **Step 1: Write CLI and console command tests**

Assert commands are fixed argument lists with no shell, warehouse root is exactly the approved D-drive path, and user payload cannot inject endpoints, Python modules, or credentials.

- [ ] **Step 2: Run tests and verify red**

```powershell
Set-Location D:\Codex-Workspace\AlphaPilot-Quant-Engine
.\.venv\Scripts\python.exe -m unittest tests.evolution.test_workflow_cli -v
Set-Location D:\Codex-Workspace\AlphaPilot-Control-Console
..\AlphaPilot-Quant-Engine\.venv\Scripts\python.exe -m unittest tests.test_workflow_client -v
```

- [ ] **Step 3: Add stage-aware CLI and background worker command**

`one-click-backtest` queues a data-preparation retry when needed, then invokes the dual-layer worker. `recover` dispatches from the current stage and checkpoint. `run-all-awaiting` invokes the same one-click path for eligible backtest runs.

- [ ] **Step 4: Update the Strategy page**

Change the primary label to `一键双层回测`. Show only current phase, downloaded/required partitions, formal or smoke evidence class, blocker, and next step. Keep IDs, hashes, paths, per-symbol quality, regimes, split manifests, and raw metrics in Advanced Details.

Required status labels:

```javascript
const workflowPhaseLabels = {
  checking_local_data: "检查本地数据",
  research_smoke_running: "本地研究烟测",
  preparing_official_data: "准备官方数据",
  validating_official_data: "校验正式数据",
  freezing_data_snapshot: "冻结正式快照",
  building_validation_manifests: "构建正式验证集",
  formal_backtest_running: "正式回测中",
  evaluating_gate: "评估回测门槛",
  local_forward_starting: "正在进入本地前向",
  local_forward_running: "本地前向运行中",
};
```

- [ ] **Step 5: Run syntax, unit, and responsive checks**

```powershell
Set-Location D:\Codex-Workspace\AlphaPilot-Control-Console
& 'C:\Users\阿俊\.cache\codex-runtimes\codex-primary-runtime\dependencies\node\bin\node.exe' --check web\app.js
..\AlphaPilot-Quant-Engine\.venv\Scripts\python.exe -m unittest discover -s tests -p 'test_*.py' -q
```

Start a credential-free test service on a free port, verify `/api/health`, `/api/workflow`, and the Strategy page in desktop and mobile widths, then stop that exact test process.

- [ ] **Step 6: Commit Quant and Console separately**

```powershell
git commit -m "Expose V13.27.1.1 one-click data backtest"
```

Use one commit in each changed repository and never stage runtime `data/`.

### Task 10: Run Real Data Smoke, Full Verification, Documentation, and Release

**Files:**
- Modify: `AlphaPilot-Quant-Engine/README.md`
- Create: `AlphaPilot-Control-Console/docs/V13.27.1.1-dual-data-one-click-backtest.md`
- Modify: `AlphaPilot-Control-Console/README.md`
- Modify: `AlphaPilot-Docs/roadmap/AlphaPilot_V13.27_Unified_Workflow_Orchestrator_Implementation_Plan.md`
- Modify: `AlphaPilot-Docs/README.md`

**Interfaces:**
- Runs the real Alpha191 V1 data-preparation retry without modifying V1.
- Produces runtime reports only under ignored data/report paths.

- [ ] **Step 1: Run a bounded real research smoke on existing local data**

```powershell
Set-Location D:\Codex-Workspace\AlphaPilot-Quant-Engine
powershell -ExecutionPolicy Bypass -File scripts\run_v13_27_1_1_dual_backtest.ps1 -StrategyName "Alpha191 加密因子观察策略" -SmokeOnly
```

Expected: `research_smoke`, `formalPromotionEligible=false`, no changed source file under `D:\Codex-Workspace\回测数据\5m`, `合约数据`, or `现货数据`.

- [ ] **Step 2: Run one bounded official-public integration pack**

Use BTC/ETH/SOL with Alpha191's `4h` signal and declared execution fallback. Limit the integration command to a bounded date interval and fake no provenance. Expected outcomes are either a checksum-valid official snapshot or an explicit public-source/coverage blocker.

- [ ] **Step 3: Run all automated checks**

Quant:

```powershell
.\.venv\Scripts\python.exe -m unittest discover -s tests -p 'test_*.py' -q
.\.venv\Scripts\python.exe -m compileall alphapilot
.\.venv\Scripts\python.exe -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

Console:

```powershell
..\AlphaPilot-Quant-Engine\.venv\Scripts\python.exe -m unittest discover -s tests -p 'test_*.py' -q
& 'C:\Users\阿俊\.cache\codex-runtimes\codex-primary-runtime\dependencies\node\bin\node.exe' --check web\app.js
git diff --check
```

Safety scan must show no new Trade API, Withdraw API, raw credential persistence, account read, position read, order creation, or Live activation.

- [ ] **Step 4: Update docs with exact observed results**

Document test counts, source endpoints, smoke result, official integration result, remaining data coverage, disk usage, and whether Alpha191 stayed blocked, failed formally, passed, or entered Local Forward. Never write a pass before the evidence exists.

- [ ] **Step 5: Commit, tag, push, and verify clean status**

Quant:

```powershell
git add alphapilot/evolution alphapilot/data_foundation alphapilot/derivatives/feature_panel.py alphapilot/reports/generate_v13_5_23_alpha191_crypto_subset_replay_report.py scripts/run_v13_27_1_1_dual_backtest.ps1 tests/data_foundation tests/evolution README.md
git diff --cached --check
git commit -m "Add V13.27.1.1 dual data one-click backtest"
git tag v13.27.1.1
git push origin main
git push origin v13.27.1.1
```

Console:

```powershell
git add alphapilot_control_console/workflow_client.py alphapilot_control_console/http_app.py tests/test_workflow_client.py web/index.html web/app.js web/styles.css docs/V13.27.1.1-dual-data-one-click-backtest.md README.md
git diff --cached --check
git commit -m "Add V13.27.1.1 dual data workflow UI"
git tag v13.27.1.1
git push origin main
git push origin v13.27.1.1
```

Docs:

```powershell
git add README.md roadmap/AlphaPilot_V13.27_Unified_Workflow_Orchestrator_Implementation_Plan.md
git diff --cached --check
git commit -m "Document V13.27.1.1 implementation result"
git tag v13.27.1.1-docs
git push origin main
git push origin v13.27.1.1-docs
```

Final status may contain only pre-existing ignored/untracked runtime data that was present before implementation. Report it explicitly; do not delete it to claim a clean source tree.

---

## Execution Order and Stop Conditions

Execute Tasks 1-10 in order. Each task requires its targeted tests and commit before the next task.

Stop within the current task when:

- a migration or existing baseline test regresses;
- a formal snapshot includes third-party rows;
- a split leaks future labels or holdout symbols;
- a retry mutates StrategyVersion content;
- a smoke result can promote;
- fixed `targetR >= 2` is weakened;
- the disk guard triggers;
- official public data cannot satisfy provenance or coverage;
- a workflow phase cannot resume from a checkpoint;
- Local Forward would create an exchange order;
- any Demo or Live transition occurs automatically;
- runtime credentials or user data would enter Git.

When stopped, preserve checkpoints, report the precise blocker, and do not mark the strategy passed.
