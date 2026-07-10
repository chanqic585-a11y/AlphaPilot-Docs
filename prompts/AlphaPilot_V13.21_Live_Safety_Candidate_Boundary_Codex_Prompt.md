# AlphaPilot V13.21 - Live Safety Candidate Boundary

## Goal

Convert only a fully validated immutable Demo release into a checksum-bound
Live review candidate, then prove that approval alone cannot enable execution.

## Requirements

1. Require complete Demo sample, calendar, cost, drawdown, drift, ledger,
   checksum, symbol, regime, and time-stability evidence.
2. Bind candidate, Demo release, evidence, code/data/model lineage, rollback
   target, and fixed risk budget by checksum.
3. Keep manual approval append-only and checksum-bound.
4. Add local request expiry, idempotency, instrument/price validation,
   reconciliation, restart recovery, circuit breaker, and kill switch audits.
5. Persist preflight decisions locally for operator and mobile summaries.
6. A passing preflight must still end as `validated_execution_disabled`.
7. No Live adapter, runtime credential storage, real account/position read,
   order creation, automatic Live promotion, or Withdraw capability.

## Acceptance

- Empty or incomplete Demo evidence produces no Live package.
- Approval does not create execution permission.
- Expired, mismatched, unreconciled, stale-price, suspended-instrument, breaker,
  and kill-switch requests fail closed.
- Restart and idempotency behavior are test-covered.
- Real reports record the actual blocked state without fabricated samples.
