# AlphaPilot V13.27.1.61 Global Remediation Plan

## Status

- version: V13.27.1.61
- status: completed_with_open_readiness_items
- executionMode: isolated_worktrees
- liveApproval: not_granted
- liveArm: forbidden
- withdraw: forbidden

## Frozen Baseline

- Quant: `469ff9122df972f1e9cedb56d41d2d49337f78d4`
- Console: `1e45e6175a943c659a90536f1022fab64bf03136`
- Docs: `e477596a87f29041ebdb775450f8431ace0daaa5`
- Master prompt SHA-256: `1e8097d587ef34018758050fe1f0e047e59687784ba749c6c2314382891ea9eb`

The production console is not edited in place. Its HTTP service is reachable, but the previous process-bound Demo ARM is stale and the current process is disarmed. This is recorded as a degraded-safe runtime state, not as active Demo continuity.

## Execution Tracks

### Track A - Runtime Continuity

1. Observe production runtime, release identity, order state, positions, and safety flags read-only.
2. Do not restart the process or request credentials unless a later approved cutover requires it.
3. Keep Live disabled and Withdraw absent.

### Track B - Global Remediation

1. Re-audit the 17 master-prompt findings against the frozen baseline.
2. Classify each finding as `confirmed_present`, `already_fixed`, `partially_fixed`, `obsolete`, or `not_evaluable`.
3. Add failing tests for every confirmed or partial executable defect.
4. Implement P0 controls first: authority, readiness semantics, model mode, kill switch, PIT timestamps, latency, risk units, exit policy, HTTP writes, and SQLite governance.
5. Split oversized modules only where required to establish clear ownership and test boundaries.

### Track C - Autonomous Research

1. Keep the research worker process isolated from all order authority.
2. Persist recoverable jobs, bounded budgets, lineage, failure attribution, and immutable artifacts.
3. Allow automatic research-to-Demo-candidate production only; never automatic Live approval.
4. Preserve all not-run evidence as truthful states.

### Track D - Minimal Trading UI

1. Implement `/ui-preview/demo-v2` and `/ui-preview/live-v2` first with mock or read-only data.
2. Project real runtime, release, order, position, and PnL state through versioned read APIs.
3. Keep normal pages concise and move gates, hashes, lineage, and audit details behind disclosure controls.
4. Verify desktop and 390 px layouts before any route replacement.

## Safe Change Procedure

Any execution-core or database change follows this sequence:

1. Unit and contract tests.
2. Deterministic fixture Shadow Parity: 100% agreement.
3. Replay Shadow Parity: 100% agreement for unchanged semantics.
4. Read-only real-market shadow scans with every difference explained.
5. SQLite migration rehearsal on a copy with integrity checks and rollback evidence.
6. Closed-candle cutover checkpoint with zero unknown orders and zero unreconciled partial fills.
7. Explicit operator action if process-only credentials or Demo ARM are required.

No cutover occurs while the current runtime is disarmed or while parity evidence is incomplete.

## Validation

- Quant and Console full test suites
- Python `compileall`
- configuration validation
- safety scans
- `git diff --check`
- HTTP write-boundary tests
- database integrity and backup/restore tests
- deterministic and replay parity reports
- desktop and 390 px UI screenshots
- remote branch and tag SHA verification

## Deliverable

Generate:

`D:\Codex-Workspace\deliveries\AlphaPilot_Global_Remediation_Continuous_Demo_Autonomous_Research_UI_V2_Evidence.zip`

The ZIP must include the required closeout, self-check, audit classifications, runtime continuity evidence, adaptive-learning evidence, research-worker evidence, risk evidence, UI evidence, security evidence, database evidence, tests, Git receipts, and artifact manifest. Every unavailable or unexecuted item must include a truthful status and reason.

## Completion Checkpoint

- Console full suite: 655 tests passed.
- Quant full suite: 426 tests passed.
- Compile, configuration, safety, JavaScript syntax, and diff checks passed.
- Nineteen non-empty production SQLite ledgers passed online backup, restore, schema-parity, and integrity checks on copies.
- Deterministic fixture and replay Shadow Parity both reached 100%; no production cutover occurred.
- Desktop and 390 px Demo/Live previews passed layout and browser-error checks.
- Production Demo remains safely disarmed because its process-bound ARM is stale; it was not restarted or hot-patched.
- Adaptive Learning remains `blocked_not_ready` at 9 of 19 capabilities. Live is disabled, Withdraw is disabled, and no strategy Live order was created.
