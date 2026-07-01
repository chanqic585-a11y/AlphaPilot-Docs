# AlphaPilot V8 - vectorbt backtesting

Status: Draft / To Be Expanded

## Goal

Add a Python backtesting layer that can test research hypotheses against
historical public market data before any trading automation exists.

## Scope

- Introduce vectorbt-based research workflows
- Define historical data requirements
- Define backtest input and output contracts
- Record assumptions and limitations
- Prepare reports for Research Dashboard integration

## Non Goals

- No live trading
- No paper trading
- No automatic order routing
- No strategy recommendation to users
- No guaranteed future performance

## Safety Rules

- Backtests must show assumptions
- Backtests must separate historical performance from future expectations
- Backtests must not override Risk Engine rules
- Backtests must not create live orders
- Backtests must expose data quality limitations

## Acceptance Criteria

- Backtest module boundary is defined
- Input and output schemas are documented
- Example backtest flow is reproducible
- Results include risk and drawdown fields
- Documentation states that backtests are research only
