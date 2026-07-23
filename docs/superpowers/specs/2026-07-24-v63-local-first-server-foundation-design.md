# AlphaPilot V63.0 Local-first Server Foundation Design

Status: approved_for_implementation
Date: 2026-07-24
Authority: V62.4.2 accepted baseline frozen

## 1. Objective

V63.0 prepares AlphaPilot for a future 4 vCPU / 8 GB server without buying,
logging in to, or deploying a cloud server. The deliverable is a local,
headless, restartable and auditable runtime that preserves the V62.4.2 safety
boundary.

The frozen tag `v13.27.1.62.4.2-runtime-ui-cutover` is an immutable input. V63
does not move or rewrite that tag.

## 2. Architecture decision

V63 uses a modular monolith with six independently startable operating-system
process roles:

| Role | Responsibility | Private exchange credentials | Order access |
| --- | --- | --- | --- |
| control | health aggregation, lifecycle control, reconciliation | no | no |
| market | public market collection and normalized snapshots | no | no |
| demo | no-order shadow evaluation only in V63 | no | no |
| research | bounded campaigns and backtest orchestration | no | no |
| ai | redacted AI orchestration and review | AI provider keys only | no |
| factor | factor bench, Qlib adapters and observer jobs | no | no |

The roles share versioned contracts and a local state root, but each process has
its own identity, allowlisted environment and scoped lease. This keeps local
operations simple while proving the boundaries required for later service
deployment.

## 3. Runtime identity and leases

Every process identity binds:

- runtime ID and role;
- environment and mode;
- repository commit and optional tag;
- deployment manifest hash;
- configuration hash;
- process ID, host and start time;
- lease scope and fencing token;
- order access and private-credential capability, both false in V63.

Lease uniqueness is scoped to `environment + role`. A lease record has a
monotonic fencing token. A stale process cannot renew or release a newer claim.
Expired leases can be replaced only after startup reconciliation confirms the
scope is safe.

## 4. Startup reconciliation

Before a role becomes ready, the control plane checks:

1. deployment manifest and role configuration hashes match;
2. Demo ARM and Live ARM are false;
3. no execution lease, unknown order, partial fill or strategy position exists;
4. Withdraw remains unavailable;
5. the role receives only its secret allowlist;
6. the role can claim its scoped lease;
7. its checkpoint is readable and schema compatible.

Any failed check leaves the role `blocked` and records a redacted reason.

## 5. Headless and shadow operation

The runtime has no browser dependency. `start`, `stop`, `status` and `health`
operate through CLI scripts and JSON receipts.

`shadow_no_order` is the only V63 execution mode. It may:

- read public market data;
- evaluate immutable research definitions;
- write observations, checkpoints and metrics;
- run AI and factor analysis through isolated workers.

It cannot:

- load private exchange credentials;
- approve or ARM a Release;
- instantiate an order client;
- create, cancel or amend orders;
- change risk, model or Release identities;
- enable Live or Withdraw.

## 6. State, backup and restore

SQLite databases are backed up with `sqlite3.Connection.backup`, never by
copying an open file. Each backup includes:

- source and destination hashes;
- source schema and integrity result;
- table counts and maximum sequence values;
- runtime identity and lease state;
- a snapshot receipt.

Restore always targets a new file, runs `PRAGMA integrity_check`, validates the
receipt, and only permits atomic replacement while every role is stopped,
disarmed and lease-free.

## 7. Secret isolation

Each role has an explicit environment allowlist. Unknown secret-like variables
are removed before child-process creation.

- `ai` may receive `DEEPSEEK_API_KEY` and `GEMINI_API_KEY`.
- No V63 role may receive OKX private keys.
- Secrets are never serialized to SQLite, JSON, logs, checkpoints or evidence.
- Health and status expose only secret-presence booleans.

## 8. Checkpoint and resume

Each role writes an atomic checkpoint containing its identity hash, lease
fencing token, stage, cursor and last completed unit. Resume rejects:

- a different deployment manifest;
- a different configuration;
- a stale fencing token;
- a checkpoint from another role;
- a checkpoint that requests order-capable mode.

## 9. Resource budget

The server profile reserves 1.5 GB for Windows/Linux services and filesystem
cache. AlphaPilot receives at most 4 logical CPU and 6.5 GB resident memory.

| Role | CPU weight | Memory ceiling |
| --- | ---: | ---: |
| control | 0.25 | 512 MB |
| market | 0.75 | 1024 MB |
| demo | 0.50 | 768 MB |
| research | 1.25 | 2048 MB |
| ai | 0.75 | 1280 MB |
| factor | 0.50 | 896 MB |

Research, AI and Factor are batch-class roles. At most two batch-class roles
may run concurrently, and the sum of active CPU weights must not exceed 4.0.

## 10. Parallel tracks

### Track A

Build and exercise the local headless foundation in `shadow_no_order` only.

### Track B

Preregister one entirely new bounded AI strategy campaign. It must have new
hypothesis and family identities and must not tune the four V62.4.2 failed
candidates. It may create research artifacts but cannot approve, ARM or enter
Demo.

### Track C

Continue bounded coverage, safety-finding classification, Factor Bench, Qlib,
Observer and deployment-script checks. Unsupported work remains `not_run`;
fixtures and engineering evidence remain clearly labelled.

## 11. Cutover rule

V63 is validated on a separate port and state root. No cutover of the current
V62.4.2 runtime occurs in this version. A future cutover requires a separate
approved plan and a zero-order, zero-position, disarmed checkpoint.
