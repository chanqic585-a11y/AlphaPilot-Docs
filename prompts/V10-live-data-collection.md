# AlphaPilot V10 - Live Data Collection

Status: Draft / To Be Expanded

## Goal

Add a controlled live public data collection system that stores market data for
research and later model evaluation.

## Scope

- Collect public ticker and OHLCV data
- Store raw and normalized market data
- Track collection gaps
- Add service health checks
- Prepare replayable datasets

## Non Goals

- No order execution
- No private exchange API
- No strategy automation
- No real-time trading recommendation
- No user fund access

## Safety Rules

- Collect public data only
- Do not store exchange credentials
- Do not collect private account balances
- Do not create trading actions
- Preserve data provenance

## Acceptance Criteria

- Data collection architecture is documented
- Raw and normalized storage direction is defined
- Failure and gap handling are documented
- Public-only boundary is explicit
- Collected data can support future replay
