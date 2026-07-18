# AlphaPilot V13.27.1.34C - Public Data Scheduler

## Goal

Build a resumable foreground scheduler around the accepted V34B OKX public-data collectors. V34C must run only due public-data tasks, persist durable scheduler state, prevent concurrent writers, append hash-chained cycle receipts, and produce mechanical quality reports.

The canonical warehouse is:

```text
D:\Codex-Workspace\回测数据\okx_official_v1
```

V34C must preserve these immutable identities:

- V34A base snapshot: `okx_official_v1_snapshot_12e78e3946f5a9eb19cf693936b8e9a9e510c05ab12d464f4e47662efd04b240`
- V34B extension snapshot: `okx_official_v1_v34b_snapshot_e80523c6c48c15abbbf69f082240c3efe034ef05ee6e596a33c3af83b228cc51`

## Scope

Schedule public instrument metadata, funding increments, instrument state, current funding, open interest, mark price, index price, ticker spread, order-book summaries, and quality checks. Use immutable policy hashes, per-task `nextDueAt`, bounded retry state, a single-owner lease, incremental high-water marks, append-only artifacts, and an operator pause file.

Provide both a one-cycle CLI and an explicit foreground PowerShell loop. Do not install a Windows service or scheduled task.

## Safety Boundary

V34C must not:

- generate or promote strategy candidates;
- run Formal validation or read Locked OOS evidence;
- create or approve a Release;
- ARM Demo or Live execution;
- read private account data;
- save API credentials;
- call Trade API or Withdraw API;
- place, cancel, or modify an order.

All candidate, Formal, result-read, Locked-OOS, Release, approval, Demo ARM, and order counters must remain zero.

## Acceptance

1. A first bounded cycle runs every due task successfully.
2. A later cycle runs only tasks whose cadence is due.
3. Cycle receipts form a valid hash chain.
4. Funding collection resumes from existing high-water marks without duplicate timestamps.
5. V34A/V34B artifacts remain byte-identical.
6. Quality reports classify freshness and failure state mechanically.
7. Unicode paths resolve to `D:\Codex-Workspace\回测数据`; no alternate mojibake warehouse is created.
8. Targeted tests, full pytest, compileall, config validation, safety scan, and Git whitespace checks pass, except any separately documented pre-existing publication-readiness gate.

## Execution Closeout

The bounded real pilot completed two cycles:

- Service ID: `okx_official_v1_v34c_service_32ab0fdc0c87df06b912e4fdf821ad72b5364f87abb23dc1068d86355c19f02c`
- Policy hash: `v34c_collection_policy_04dab84d8475bad0efb535cc31547071c1ff03fd9da591aea0fc224d73aac1bb`
- Latest cycle hash: `v34c_cycle_a6cd1c64bf19ddbffc5338fd2f81baa6744ee02f766ee631f57a706ac210d99a`
- Latest completed quality status: `healthy`
- Cycle-chain mismatch count: `0`
- Prior V34A/V34B artifact mismatch count: `0`
- V34C artifact mismatch count: `0`
- Candidate, Formal, result-read, Release, approval, Demo ARM, and order counts: `0`

The first cycle ran all ten tasks. The second ran only mark price, index price, ticker spread, and order-book summary; six slower tasks were correctly skipped. Incremental funding returned `no_new_rows` at the existing high-water marks and introduced no duplicates.

Authoritative evidence:

- `docs/V13.27.1.34C-public-data-scheduler.md`
- `reports/dual_track/alphapilot_dual_track_v33_6af4e494293f4167/v34c_public_data_service_closeout.json`
- `reports/dual_track/alphapilot_dual_track_v33_6af4e494293f4167/v34c_public_data_service_closeout.md`
