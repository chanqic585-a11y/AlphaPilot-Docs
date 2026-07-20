# AlphaPilot V13.27.1.34B - Funding, PIT Metadata, and Resumable Forward Collection

V34B extends the accepted V34A OKX public-data pilot with three data-only capabilities:

1. Recent public funding-rate history with explicit causal availability semantics.
2. Daily point-in-time SWAP instrument/product metadata snapshots; no historical state is fabricated.
3. Append-only, resumable forward collection of public derivatives state.

The implementation must reuse `D:\Codex-Workspace\回测数据\okx_official_v1`, preserve the V34A frozen snapshot byte-for-byte, and record provenance and content hashes for every artifact.

V34B must not generate candidates, run Formal validation, read locked results, create Releases, approve or ARM Demo, access accounts, or create orders. Trade API, Withdraw API, private credentials, and live trading remain outside scope.

See the executable implementation plan at:

`D:\Codex-Workspace\AlphaPilot-Quant-V34B-worktree\docs\superpowers\plans\2026-07-19-v13.27.1.34b-funding-pit-forward.md`

## Execution Closeout

V34B completed as a public-data-only extension:

- Frozen V34A snapshot preserved: `okx_official_v1_snapshot_12e78e3946f5a9eb19cf693936b8e9a9e510c05ab12d464f4e47662efd04b240`.
- New V34B snapshot: `okx_official_v1_v34b_snapshot_e80523c6c48c15abbbf69f082240c3efe034ef05ee6e596a33c3af83b228cc51`.
- V34B manifest SHA-256: `d4b6050ffe6b617d9ad019117e0be363ce7731d144dcea51671358009a5c5e75`.
- 427 point-in-time SWAP instrument records were captured without reconstructing historical states.
- BTC, ETH, and SOL each received 287 public funding-history rows, covering `2026-04-14T08:00:00Z` through `2026-07-18T16:00:00Z`.
- Seven append-only forward streams were captured for the three pilot instruments.
- Replaying the same collection resumed from its final checkpoint in 545 ms without duplicate ledger writes.
- Candidate, Formal, result-read, Release, approval, Demo ARM, and order counts remained zero.
- Full regression: 1096 tests and 157 subtests passed.

The authoritative closeout files are:

- `reports/dual_track/alphapilot_dual_track_v33_6af4e494293f4167/v34b_data_extension_closeout.json`
- `reports/dual_track/alphapilot_dual_track_v33_6af4e494293f4167/v34b_data_extension_closeout.md`

Known limits: public funding history is recent-history only; PIT product history begins with V34B; the collector is resumable but is not yet a permanent scheduler.
