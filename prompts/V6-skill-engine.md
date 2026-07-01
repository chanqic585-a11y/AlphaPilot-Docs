# AlphaPilot V6 - Skill Engine

Status: Draft / To Be Expanded

## Goal

Define a Skill Engine that turns reusable trading research procedures into
versioned, testable skills.

## Scope

- Define skill metadata
- Define skill input and output contracts
- Connect skills to local trade and feature data
- Prepare plugin compatibility
- Keep skills explainable and auditable

## Non Goals

- No autonomous execution
- No hidden strategy changes
- No live trading
- No order placement
- No guaranteed performance claims

## Safety Rules

- Skills must explain their inputs and outputs
- Skills must not bypass risk rules
- Skills must not require exchange Trade API keys
- Skills must not trigger orders
- Skills must not promise profit

## Acceptance Criteria

- Skill contract is documented
- Skill registry direction is defined
- Skill execution remains research-only
- Failure behavior is documented
- Safety boundaries are explicit
