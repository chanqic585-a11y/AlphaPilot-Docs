# AlphaPilot V13.8 - Research Execution Pipeline and Testnet Readiness Pack

## Goal

Build the next local-only AlphaPilot Control Console step after V13.7.49.

V13.8 completes four requested research workflow layers:

1. V13.7.50 Research Action Executor
2. V13.7.51 Candidate Promotion Gate
3. V13.7.52 Simulation Command Center
4. V13.8 Testnet Readiness Pack

This release is still research-only. It must not connect exchange trading
permissions, save raw API keys, create orders, run exchange Dry-run, or enable
automatic trading.

## Scope

### Research Action Executor

Convert strategy weakness action items into local research checks:

- sample expansion
- parameter stress
- regime breakdown
- cost and slippage stress
- strategy simplification

The executor may update only local research task status. It must not rewrite
strategy code automatically.

### Candidate Promotion Gate

Combine sandbox review results, weakness action status, and research execution
checks into promotion tiers:

- collecting evidence
- sandbox review candidate
- testnet readiness candidate

The gate is a research classification layer. It is not a trading approval
system.

### Simulation Command Center

Aggregate local sandbox state, review queue, daily report, promotion gate, and
research action status into a single console payload.

The command center should help the user see:

- current local simulation stage
- next research action
- active candidate strategies
- unresolved blockers

### Testnet Readiness Pack

Add a readiness checklist for future testnet design:

- credential vault design
- disabled Trade API adapter boundary
- order lifecycle simulator
- kill switch
- max order and loss limits
- manual confirmation gate
- audit trail
- public data probe

V13.8 must keep testnet disabled.

## Required APIs

- `GET /api/research-action-executor`
- `POST /api/research-action-executor/run`
- `GET /api/candidate-promotion-gate`
- `GET /api/simulation-command-center`
- `GET /api/testnet-readiness-pack`
- `GET /api/research-execution-pipeline`
- `POST /api/research-execution-pipeline/run`

## UI

Add a Research Execution Pipeline panel to the desktop console.

It should display:

- research action execution count
- updated local task count
- sandbox review candidates
- testnet readiness candidates
- testnet blockers
- simulation stage
- next local research action

## Safety Rules

V13.8 must not add:

- API key storage
- Trade API
- Withdraw API
- real account reads
- real position reads
- real order creation
- testnet order creation
- exchange Dry-run
- live trading
- automatic trading

Allowed wording:

- local research
- sandbox review
- promotion gate
- testnet readiness checklist
- disabled trading capability

## Acceptance Criteria

1. Research Action Executor exists.
2. Candidate Promotion Gate exists.
3. Simulation Command Center exists.
4. Testnet Readiness Pack exists.
5. Research Execution Pipeline endpoint exists.
6. Desktop console shows pipeline status.
7. Local research task status can be updated by the executor.
8. Testnet remains disabled.
9. No API key storage is added.
10. No Trade API or Withdraw API is added.
11. No real or testnet orders are created.
12. No exchange Dry-run is enabled.
13. No automatic trading is enabled.
14. Compile and smoke checks pass.
