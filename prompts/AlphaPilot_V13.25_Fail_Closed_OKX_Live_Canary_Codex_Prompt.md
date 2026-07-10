# AlphaPilot V13.25 - Fail-Closed OKX Live Canary

## Goal

Add a real but default-off OKX Live Canary adapter behind immutable releases,
versioned RiskProfiles, reconciliation, protection, idempotency, and explicit
process/operator gates.

## Requirements

- Separate process-only Live credentials from Demo credentials.
- Allowlist account config, balance, positions, order status, pending orders,
  fills, protected order, cancel order, and cancel-all-after only.
- Do not implement Withdraw.
- Create an immutable LiveRelease only after checksum-bound user approval.
- Require isolated margin, attached TP and SL, and reward/risk >= 2R.
- Require master/read/Canary/order process gates plus manual ARM.
- Persist no account balances, position detail, or raw credentials.
- Pause on unknown state, mismatch, stale data, risk breach, or restart gap.
- Keep the initial runtime kill switch active.
- Use fake transports in tests; do not place a real order during implementation.
