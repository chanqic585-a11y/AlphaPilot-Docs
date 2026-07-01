# AlphaPilot V13 - Auto Trading Design

Status: Draft / To Be Expanded

## Goal

Document the design requirements for possible future automatic trading after
research, backtesting, paper trading, and semi-automatic workflows prove stable.

## Scope

- Define strict prerequisites for automation
- Define kill switches
- Define Risk Engine enforcement
- Define audit and replay requirements
- Define deployment and monitoring requirements

## Non Goals

- No immediate implementation
- No order placement in this phase
- No Withdraw API permissions
- No bypass of prior research stages
- No profit guarantees

## Safety Rules

- Automation must come after research stages
- Risk Engine cannot be bypassed
- Kill switch is mandatory
- Withdraw permissions remain forbidden
- Every decision must be explainable and logged

## Acceptance Criteria

- Auto trading prerequisites are documented
- Risk and kill-switch requirements are explicit
- Audit and monitoring requirements are defined
- The design forbids skipping earlier stages
- Documentation states that V13 is design only
