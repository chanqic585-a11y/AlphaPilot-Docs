# AlphaPilot V9 - Signal Engine

Status: Draft / To Be Expanded

## Goal

Design a research-only Signal Engine that converts tested rules into
explainable observations, not trade commands.

## Scope

- Define signal input data
- Define signal output schema
- Require explanation for every signal
- Connect signals to Risk Engine checks
- Prepare review and audit logs

## Non Goals

- No automatic trading
- No order placement
- No hidden strategy execution
- No private exchange API
- No guarantee that a signal is profitable

## Safety Rules

- Signals must be explainable
- Signals must not bypass risk limits
- Signals must not become orders
- Signals must not use private account data
- Signals must be labeled as research observations

## Acceptance Criteria

- Signal schema is documented
- Signal lifecycle is defined
- Risk Engine dependency is explicit
- Signals have audit trails
- UI language avoids buy or sell commands
