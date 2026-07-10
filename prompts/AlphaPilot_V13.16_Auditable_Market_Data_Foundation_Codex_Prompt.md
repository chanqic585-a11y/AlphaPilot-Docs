# AlphaPilot V13.16 - Auditable Market Data Foundation

## Goal

Build the point-in-time market-data foundation required by the AlphaPilot
automatic research and controlled-execution roadmap.

V13.16 must turn the existing read-only historical files and unauthenticated
public market increments into classified, quality-checked, checksum-addressed,
resume-safe, immutable DataSnapshots. It must not create a strategy or cross
any Demo/Live release gate.

## Fixed Boundaries

```text
Raw source: D:\Codex-Workspace\回测数据
Raw source mode: read-only
Generated market artifacts: Quant repo data/market/
Minimum future strategy reward/risk: >= 2R
API keys: forbidden
Private account reads: forbidden
Trade API: forbidden
Withdraw API: forbidden
Orders: forbidden
Live execution: forbidden
```

## Required Work

1. Catalog every raw CSV/XLSX file without rewriting it.
2. Prefer `_ALL` files over duplicate annual partitions.
3. Exclude checkpoint files and timestamped duplicate exports.
4. Prefer `timestamp_ms`; convert Excel serial dates only as fallback.
5. Remove unconfirmed candle tails and invalid OHLC/volume rows.
6. Measure duplicates, backward timestamps, gaps, and missing bars.
7. Write canonical Parquet atomically with ZSTD compression.
8. Checkpoint full SHA-256 catalog work and support safe resume.
9. Collect only public OKX history-candle increments without credentials.
10. Continue from both the local-base and saved-increment cutoff.
11. Reject unsupported timeframe input before any request.
12. Verify continuity across all base/increment fragments.
13. Use the earliest group end as a multi-asset point-in-time cutoff.
14. Register immutable DataSnapshots in the existing evolution registry.
15. Preserve unknown local-source provenance as a hard promotion blocker.
16. Bind canonical quality metadata to source and Parquet checksums so
    unchanged XLSX sources do not require a full reread on every resume.

## Evidence Commands

```powershell
powershell -ExecutionPolicy Bypass -File scripts\build_v13_16_data_foundation.ps1
powershell -ExecutionPolicy Bypass -File scripts\build_v13_16_data_foundation.ps1 -HashMode all
powershell -ExecutionPolicy Bypass -File scripts\start_v13_16_full_hash_job.ps1
powershell -ExecutionPolicy Bypass -File scripts\get_v13_16_full_hash_job_status.ps1
powershell -ExecutionPolicy Bypass -File scripts\seed_v13_16_canonical_metadata.ps1
powershell -ExecutionPolicy Bypass -File scripts\collect_v13_16_public_increment.ps1
powershell -ExecutionPolicy Bypass -File scripts\build_v13_16_composite_snapshot.ps1
```

## Required Reports

```text
reports/v13_16_data_foundation_report.json
reports/v13_16_data_foundation_summary.md
reports/v13_16_public_increment_report.json
reports/v13_16_public_increment_idempotency_report.json
reports/v13_16_composite_data_snapshot_report.json
reports/v13_16_composite_data_snapshot_summary.md
reports/v13_16_canonical_metadata_seed_report.json
```

## Acceptance Criteria

1. Raw files remain unchanged.
2. Catalog counts and exclusions are explicit.
3. BTC/ETH/SOL x 15m/1h/4h/1d canonical smoke groups exist.
4. All 12 composite groups have zero duplicate, gap, and misaligned intervals.
5. Public collection is idempotent across restarts.
6. Composite manifest hash and all file checksums verify.
7. Snapshot is registered locally.
8. Unknown historical provenance keeps formal promotion false.
9. Unit tests, compileall, config validation, safety scan, and diff checks pass.
10. No FactorRun, model, StrategyCandidate, DemoRelease, LiveCandidate, or order
    is fabricated in this phase.

## Next Phase

V13.17 may materialize point-in-time FactorRuns and `>=2R` labels only from a
verified snapshot. Engineering smoke research may use a provenance-blocked
snapshot, but formal promotion must remain disabled until the source policy is
resolved.
