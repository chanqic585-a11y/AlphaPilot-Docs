# AlphaPilot V13.8.2 - Pre-Live Preparation Pack

## Goal

Based on the current AlphaPilot Control Console V13.8.1, add a local pre-live
preparation pack before any future testnet or live trading integration.

The goal is to finish the first layer of pre-live preparation:

- local order lifecycle rehearsal
- risk limit and kill-switch design
- credential vault design requirements
- disabled pre-live action list
- reference-only lessons from saved open-source research
- visible web console panel

V13.8.2 is not a live trading release.

## Reference Inputs

Use the previously saved references only as design inspiration:

- alpha101: factor research workflow and explainable factor decomposition.
- CryptoAgentPro.beta: API key, order endpoint, emergency close, testnet, and
  full-auto features as risk references for permission isolation.
- TradingAgents: multi-role review workflow before escalation.
- QuantDigger: event lifecycle and state-machine inspiration.

Do not copy executable order code from these references.

## Scope

Add:

- `alphapilot_control_console/pre_live_preparation_pack.py`
- `GET /api/pre-live-preparation-pack`
- `POST /api/pre-live-order-lifecycle/simulate`
- a `PRE-LIVE PREPARATION PACK` section in the web console
- Control Console documentation
- README update

The lifecycle simulator may return a local preview path, but it must not persist
orders and must not contact exchanges.

## Safety Rules

V13.8.2 must keep disabled:

- API Key input
- API Key storage
- Trade API
- Withdraw API
- private exchange endpoints
- real account reads
- real position reads
- testnet order creation
- live order creation
- exchange Dry-run
- automatic trading

Any button must be clearly labeled as local rehearsal only.

## Acceptance Criteria

1. `/api/pre-live-preparation-pack` exists.
2. `/api/pre-live-order-lifecycle/simulate` exists.
3. Web console shows local lifecycle, risk controls, kill switch controls,
   credential boundary, and reference absorption.
4. The simulation endpoint returns `createdExchangeOrder=false`.
5. No API key input is added.
6. No Trade API or Withdraw API is added.
7. No account or position read is added.
8. No real or testnet order can be created.
9. `python -m compileall alphapilot_control_console` passes.
10. `node --check web/app.js` passes.
11. `python -m alphapilot_control_console.http_app --smoke` passes.
12. `git diff --check` passes.
