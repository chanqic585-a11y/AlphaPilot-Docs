# AlphaPilot V13.26 - Formal Execution Outcome Feedback

## Goal

Connect fully closed and reconciled OKX Demo and future Live Canary outcomes to
the immutable Quant Engine Outcome Ledger for offline-only research feedback.

## Requirements

1. Persist only complete closed outcomes with entry, exit, quantity, gross PnL,
   fee, slippage, net PnL, risk amount, R multiples, and exit reason.
2. Treat an opening order marked `filled` as incomplete until explicit close
   evidence exists.
3. Bind every outcome to a DataSnapshot, StrategyCandidate, and exact immutable
   DemoRelease or LiveRelease checksum.
4. Bind Live outcomes to the exact RiskProfile id and checksum.
5. Export a checksummed JSON manifest containing no credentials or account
   values.
6. Quarantine incomplete, malformed, ambiguous, missing-parent, or
   checksum-mismatched records. Never invent lineage or default good results.
7. Import accepted outcomes into the offline Outcome Ledger only.
8. Preserve ForwardRelease, DemoRelease, LiveCandidatePackage, and LiveRelease
   checksums before and after offline feedback.
9. Do not mutate an online model, auto-promote a strategy, create a release, or
   create an order.
10. Do not expose Withdraw, store raw credentials, or persist private account
    balances or positions.

## Current-state reporting

If no genuine closed Demo/Live outcome exists, the V13.26 report must return
`blocked_no_formal_execution_outcomes`. Do not create mock production evidence
to make the report pass.

## Verification

- Unit-test valid Demo and Live lineage.
- Unit-test missing parent and checksum tampering quarantine.
- Unit-test that entry-only fills remain quarantined.
- Run full Python tests and compile checks in Quant Engine and Control Console.
- Run JavaScript syntax, HTTP/API, browser, safety, and git diff checks.
