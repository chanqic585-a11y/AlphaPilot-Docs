# AlphaPilot V11 - Paper Trading Lab

Status: Draft / To Be Expanded

## Goal

Create a paper trading environment that tests strategies without connecting to
real funds or exchange trading permissions.

## Scope

- Simulate positions
- Simulate fills from public market data
- Track paper account equity
- Compare strategy assumptions with simulated outcomes
- Record paper trade reviews

## Non Goals

- No live orders
- No Trade API keys
- No Withdraw API keys
- No real fund movement
- No auto trading

## Safety Rules

- Label all paper trades clearly
- Keep paper performance separate from live performance
- Do not imply paper results predict real execution
- Do not connect trading permissions
- Do not hide slippage assumptions

## Acceptance Criteria

- Paper trade data model is defined
- Paper account behavior is documented
- Fill assumptions are explicit
- Review flow is supported
- Safety copy distinguishes paper from live trading
