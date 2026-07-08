# AlphaPilot V13.8.3 - Pre-Live Operational Closure Pack

## Goal

Based on AlphaPilot Control Console V13.8.2, complete the local pre-live
operational closure layer.

V13.8.3 must turn local lifecycle previews into saved local rehearsal records
and show a closure report in the desktop console.

V13.8.3 is not testnet trading, not live trading, not automatic execution, and
not exchange API integration.

## Scope

Implement:

1. Persistent local pre-live rehearsal records.
2. A closure report for local passed and rejected rehearsal paths.
3. Recent rehearsal display in the desktop control console.
4. A local operational runbook.
5. `POST /api/pre-live-order-lifecycle/rehearse`.
6. README and docs updates.

## Non Goals

Do not implement:

1. API key input.
2. API key storage.
3. Trade API.
4. Withdraw API.
5. Private exchange endpoints.
6. Account balance reads.
7. Position reads.
8. Real or testnet order creation.
9. Exchange Dry-run.
10. Live trading.
11. Automatic trading.

## Safety Boundary

Saved rehearsals are local audit records only. They must state:

- no exchange order was created
- no exchange connection was opened
- no API key was stored
- no account or position data was read
- no automatic execution happened

## Acceptance Criteria

1. `GET /api/pre-live-preparation-pack` returns V13.8.3.
2. `POST /api/pre-live-order-lifecycle/rehearse` saves a local rehearsal record.
3. Rehearsal records appear in the preparation pack.
4. The web console shows rehearsal totals, passed paths, rejected paths, latest
   state, closure checks, runbook rows, and recent rehearsals.
5. No testnet or live execution capability is added.
6. `python -m compileall alphapilot_control_console` passes.
7. `node --check web/app.js` passes.
8. `python -m alphapilot_control_console.http_app --smoke` passes.
9. `git diff --check` passes.
