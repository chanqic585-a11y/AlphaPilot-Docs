# AlphaPilot V13.27.1.34B - Funding, PIT Metadata, and Resumable Forward Collection

V34B extends the accepted V34A OKX public-data pilot with three data-only capabilities:

1. Recent public funding-rate history with explicit causal availability semantics.
2. Daily point-in-time SWAP instrument/product metadata snapshots; no historical state is fabricated.
3. Append-only, resumable forward collection of public derivatives state.

The implementation must reuse `D:\Codex-Workspace\回测数据\okx_official_v1`, preserve the V34A frozen snapshot byte-for-byte, and record provenance and content hashes for every artifact.

V34B must not generate candidates, run Formal validation, read locked results, create Releases, approve or ARM Demo, access accounts, or create orders. Trade API, Withdraw API, private credentials, and live trading remain outside scope.

See the executable implementation plan at:

`D:\Codex-Workspace\AlphaPilot-Quant-V34B-worktree\docs\superpowers\plans\2026-07-19-v13.27.1.34b-funding-pit-forward.md`
