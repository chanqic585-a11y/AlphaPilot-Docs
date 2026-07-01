# AlphaPilot V5 - Python Feature Engine

Status: Draft / To Be Expanded

## Goal

Move market feature calculation from the mobile app into a Python feature
engine that can compute structured research fields from public market data.

## Scope

- Build Python feature calculation modules
- Read public ticker and OHLCV data from the V4 backend service
- Generate normalized feature records
- Keep app-side SQLite compatibility
- Prepare for future indicator libraries

## Non Goals

- No real AI
- No Signal Engine
- No Paper Trading
- No automatic trading
- No exchange order placement

## Safety Rules

- Use public market data only
- Do not save API keys
- Do not call exchange private endpoints
- Do not generate buy or sell advice
- Do not treat features as trade commands

## Acceptance Criteria

- Feature engine module structure is defined
- Feature output shape is documented
- Missing public data can be represented as `null`
- App behavior remains stable when feature generation fails
- README or docs describe the safety boundary
