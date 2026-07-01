# AlphaPilot Roadmap

## Product Direction

AlphaPilot evolves from **Trade Discipline Journal** into an AI trading copilot.

The product is not a trading terminal. It does not predict prices, recommend buys or sells, connect trade permissions, save withdraw permissions, or place orders automatically.

The core sentence:

> A trader's biggest enemy is not the market. It is themselves.

The product therefore focuses on four themes:

- Discipline
- Emotion
- Risk
- Execution

## Stage V1.1 - Stabilize Current App

Goal: make the existing React Native + Expo app reliable, local-first, and discipline-focused.

Scope:

- Keep all data in SQLite.
- Keep no-login and no-cloud-sync mode.
- Enforce local risk rules.
- Add initial capital setup.
- Simplify navigation.
- Add pre-trade checklist.
- Keep OKX public market alerting as read-only.

Success criteria:

- The app runs in Expo/EAS Android APK.
- TypeScript checks pass.
- Users can create plans, review trades, view statistics, and receive foreground price alerts.
- No trading API permissions are requested or stored.

## Stage V2 - AI Trading Copilot

Goal: reposition the product as **Trade Coach AI** / **Trading Copilot**.

The experience should feel like Apple Health, Notion, Linear, or Arc Browser:

- Clean
- Minimal
- Restrained
- White / gray / deep blue
- Small orange warnings only when needed

The product should reduce manual input. A trader should be able to create a new trade record in about 10 seconds by entering only:

- Symbol
- Direction
- Entry price
- Position size
- Leverage, max 5x

After the user starts recording, the app should collect market context, calculate risk, generate objective analysis, detect patterns, monitor alerts, and prepare a later review.

V2 must not generate buy/sell recommendations. It can describe current objective conditions, such as trend, volatility, volume, RSI, MACD, ATR, support, resistance, and risk metrics.

## Stage V3 - Feature Database

Goal: turn every trade into structured training data.

For every trade, save market features such as:

- Time
- Symbol
- Direction
- Volume
- EMA
- MACD
- RSI
- ATR
- Open interest
- Funding
- Fear and Greed
- Listing age
- Market volatility
- Pattern type
- Trend state
- Final PnL

This database supports future AI memory, pattern mining, RAG, OCR, local models, cloud models, exports, and annual PDF reports.

## Non-Negotiable Safety Rules

- No automatic trading.
- No automatic orders.
- No price prediction.
- No guaranteed profit language.
- No buy/sell recommendations.
- No trade-permission API keys.
- No withdraw-permission API keys.

