# AlphaPilot Controlled Execution Design

## Purpose

This document records the future controlled-execution boundary for AlphaPilot. It is an architecture note, not an implementation in V13.1.3.

The design goal is to make every future execution path explicit, reviewable, rejectable, and auditable.

## Core Objects

### Trade Proposal

A Trade Proposal is a future research artifact that can describe:

- symbol
- market type
- technical context
- risk context
- proposed simulation or execution parameters
- evidence links
- rejection reasons

A proposal is not an order and must not automatically create an order.

### Workflow State Machine

Future workflow states should be explicit:

- draft
- research_review
- risk_check
- human_review
- approved_for_dry_run
- dry_run_active
- rejected
- archived

Live execution states must be designed separately and must never be introduced silently.

### Risk Gate

The Risk Gate must check:

- leverage limit
- maximum risk per action
- data quality
- account exposure
- stop and invalidation context
- cooldown and duplicate protection
- unsupported market or symbol conditions

Risk Gate failure should preserve the record for review instead of deleting it.

### Human Gate

The Human Gate must require explicit user confirmation before any future controlled execution path.

The user must see:

- what data was used
- what risk checks passed or failed
- what the action means
- what will not happen automatically
- how to cancel or reject

## Future Execution Boundary

V13.1.3 does not implement execution. Future execution design must keep these boundaries:

- no raw API key storage
- no Withdraw API
- no hidden Trade API use
- no background automatic order creation
- no AI-owned execution
- no direct path from AI analysis to real order
- full audit log for proposals, approvals, rejections, and execution attempts

## Audit Requirements

Every future controlled action should record:

- source
- createdAt
- user confirmation state
- risk gate result
- human gate result
- evidence JSON
- final status
- rejection or cancellation reason

## V13.1.3 Status

In V13.1.3, this document is only a design boundary. The app only fixes Android runtime issues and documents the future path.
