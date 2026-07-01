# AlphaPilot V12 - Semi-Auto Trading

Status: Draft / To Be Expanded

## Goal

Design a semi-automatic trading workflow where a human approves every real
trading action after research, risk checks, and review gates pass.

## Scope

- Define human approval gates
- Define Risk Engine enforcement
- Define audit logging
- Define permission requirements
- Prepare exchange adapter boundaries

## Non Goals

- No fully automatic trading
- No hidden order placement
- No Withdraw API permissions
- No bypass of human approval
- No unsupported exchange integration

## Safety Rules

- Human approval is mandatory
- Risk Engine checks are mandatory
- Withdraw permissions are forbidden
- Every action must be logged
- UI must show that funds are at risk

## Acceptance Criteria

- Approval workflow is documented
- Permission model is explicit
- Risk Engine dependency is non-optional
- Audit logs are required
- The system cannot place orders without approval
