# AlphaPilot V63.0 Local-first Server Foundation Implementation Plan

Status: preregistered_before_implementation
Date: 2026-07-24

## Frozen inputs

- Console base tag: `v13.27.1.62.4.2-runtime-ui-cutover`
- Quant base tag: `v13.27.1.62.4.1-formal-quant`
- Docs base tag: `v13.27.1.62.4.1-acceptance-docs`
- Current V62.4.2 Runtime remains separate and is not cut over.

## Safety invariants

- Demo ARM: false
- Live ARM: false
- strategy orders: forbidden
- strategy positions: forbidden
- Withdraw: forbidden
- private exchange credentials: forbidden
- V62.4.2 frozen tag: unchanged

## Phase 1 - Contracts and failing tests

1. Add role, identity, deployment, lease, checkpoint and health contracts.
2. Add failing tests for role isolation, secret allowlists and no-order mode.
3. Add failing tests for fencing-token leases and startup reconciliation.
4. Add failing tests for SQLite online backup and guarded restore.
5. Add failing tests for checkpoint/resume and resource admission.

Exit gate: new tests fail for missing production behavior and no existing test
regresses.

## Phase 2 - Local headless foundation

1. Implement the six role registry.
2. Implement runtime identity and scoped lease store.
3. Implement startup reconciliation and fail-closed readiness.
4. Implement headless worker loop and health/status receipts.
5. Implement secret filtering and order-capability denial.

Exit gate: all role and safety tests pass.

## Phase 3 - Operations and persistence

1. Implement online backup receipts and restore validation.
2. Implement atomic checkpoint/resume.
3. Implement server directory mapping and deployment manifest.
4. Implement the 4 vCPU / 8 GB resource scheduler.
5. Add unified PowerShell `start`, `stop`, `status` and `health` scripts.

Exit gate: a fresh local state root can start six roles, report healthy, stop,
resume and restore without browser use or private credentials.

## Phase 4 - Parallel tracks

1. Preregister a new bounded Track B campaign with fresh family identities.
2. Run only its bounded dry research preparation; no Release approval or ARM.
3. Run Track C coverage and security classification.
4. Run available Factor Bench, Qlib and Observer preflight checks.
5. Record unavailable tasks as `not_run`, not as passed.

Exit gate: Track B has immutable preregistration and Track C has an honest
status matrix.

## Phase 5 - Evidence and release

1. Run targeted and full tests.
2. Run compile, config, secret, trade-boundary and diff checks.
3. Exercise online backup/restore in a temporary state root.
4. Exercise no-order Shadow start/status/health/stop/resume.
5. Generate V63 Foundation Evidence and artifact manifest.
6. Commit each repository independently.
7. Tag and push exact V63 commits.
8. Verify remote branch and tag SHAs.

Expected tags:

- `v13.27.1.63-server-foundation-console`
- `v13.27.1.63-server-foundation-quant`
- `v13.27.1.63-server-foundation-docs`

## Stop conditions

Stop without bypass if any of the following occurs:

- an order, position, ARM or Withdraw path becomes reachable;
- a private exchange credential reaches a V63 worker;
- restore cannot prove integrity;
- a lease can be held by two owners in one scope;
- Track B reuses one of the four failed V62.4.2 candidate identities;
- evidence cannot distinguish `passed`, `failed`, `blocked` and `not_run`.
