# AlphaPilot V4 - Backend + CCXT Market Service

## Goal

Build the first AlphaPilot backend service for public market data.

V4 introduces a Python FastAPI service that uses CCXT to read public OKX
market data. The backend becomes the preferred market data source for the
mobile app, while the app must keep its current local, mock, and foreground OKX
fallback behavior when the backend is unavailable.

The goal is not automation. The goal is to create a safe market data layer that
supports research, feature generation, review, and future analytics.

## Current State

The current app is a React Native + Expo + TypeScript mobile app with local
SQLite storage.

The current V3 version includes:

- V1.1 discipline flow
- V2 Quick Trade and Trade Detail foundation
- V2 mock analysis
- V2 TradeSnapshots and TradeTimeline
- V3 TradeFeatures table
- V3 local feature-engine module
- V3 CSV export
- OKX public WebSocket monitoring while the app runs in the foreground

The current app does not include:

- Python backend
- CCXT
- Persistent market data collector
- Real AI
- Signal Engine
- Paper Trading
- Semi-Auto Trading
- Auto Trading
- Exchange Trade API
- Withdraw API

## Scope

V4 adds a backend market service and a safe app integration path.

In scope:

- Add a Python FastAPI backend
- Add an `api/` or `backend/` service directory
- Add CCXT as the public market data adapter
- Support OKX public market data as the first exchange
- Add ticker endpoint
- Add OHLCV endpoint
- Add market features endpoint
- Add backend health endpoint
- Let the app try the backend before local or mock fallbacks
- Keep the app stable when the backend is offline
- Update README with V4 setup and safety boundaries
- Run TypeScript checks for the app
- Run a backend smoke check

## Non Goals

V4 must not implement:

- Python technical indicator engine
- Pandas TA
- Real AI analysis
- Signal Engine
- Strategy recommendation
- Backtesting
- Paper Trading
- Semi-Auto Trading
- Auto Trading
- Exchange order placement
- Trade API authentication
- Withdraw API authentication
- API Key storage
- Cloud sync
- User login

## Architecture

Use a split architecture:

- Mobile App: React Native + Expo frontend, local SQLite, local fallback logic
- Backend API: Python FastAPI service for public market data
- Market Adapter: CCXT public exchange client
- Exchange: OKX public market data only

The app should call the backend through a small service module. That module
must hide network errors from the UI and return `null` or fallback-safe values
when the backend is unavailable.

The backend should own CCXT details. The mobile app should not import CCXT or
depend on exchange-specific CCXT response shapes.

Recommended directory shape:

```text
api/
  __init__.py
  main.py
  market_service.py
  requirements.txt
src/
  services/
    marketDataService.ts
```

## Backend Requirements

Create a Python FastAPI backend.

The backend must:

- Start locally with Uvicorn
- Expose JSON API endpoints
- Return deterministic response shapes
- Validate query parameters
- Handle CCXT errors with safe HTTP errors
- Avoid storing any credentials
- Avoid using environment variables for exchange keys
- Avoid order placement methods
- Use public market data only

The backend should include a small market service abstraction so future
versions can add:

- More exchanges
- Caching
- Rate-limit coordination
- Persistent collection
- Indicator calculation
- Feature database ingestion

## CCXT Requirements

Use CCXT only for public market data.

V4 supports only:

- `okx`
- `fetch_ticker`
- `fetch_ohlcv`

V4 must not call:

- `create_order`
- `cancel_order`
- `fetch_balance`
- `private_*` methods
- Withdraw methods
- Deposit methods
- Any authenticated endpoint

The backend must not ask for:

- OKX API Key
- OKX Secret
- OKX Passphrase
- Withdraw permissions
- Trade permissions

## API Endpoints

Implement these endpoints.

### GET /health

Return backend health and safety metadata.

Example response:

```json
{
  "status": "ok",
  "service": "AlphaPilot V4 Market API",
  "version": "4.0.0",
  "permissions": "public_market_data_only"
}
```

### GET /market/ticker

Query:

- `exchange=okx`
- `symbol=BTC/USDT`

Example:

```text
GET /market/ticker?exchange=okx&symbol=BTC/USDT
```

Return fields:

- `exchange`
- `symbol`
- `price`
- `bid`
- `ask`
- `high24h`
- `low24h`
- `volume`
- `quoteVolume`
- `priceChange24h`
- `timestamp`
- `datetime`
- `source`

### GET /market/ohlcv

Query:

- `exchange=okx`
- `symbol=BTC/USDT`
- `timeframe=1m`
- `limit=200`

Example:

```text
GET /market/ohlcv?exchange=okx&symbol=BTC/USDT&timeframe=1m&limit=200
```

Return fields:

- `exchange`
- `symbol`
- `timeframe`
- `limit`
- `candles`
- `source`

Each candle should include:

- `timestamp`
- `open`
- `high`
- `low`
- `close`
- `volume`

### GET /market/features

Query:

- `exchange=okx`
- `symbol=BTC/USDT`

Example:

```text
GET /market/features?exchange=okx&symbol=BTC/USDT
```

Return basic fields:

- `price`
- `volume`
- `priceChange1h`
- `priceChange4h`
- `priceChange24h`
- `high24h`
- `low24h`
- `volatility`
- `trendDirection`

`trendDirection` must describe only the observed current market state. Valid
values:

- `up`
- `down`
- `flat`
- `unknown`

Do not return buy, sell, long, or short recommendations.

## App Integration

Add a TypeScript app service that calls the backend market API.

The app should:

- Try the backend first for ticker data
- Use backend data for snapshot or monitor display when available
- Keep current local, mock, or OKX foreground WebSocket behavior as fallback
- Avoid crashes when the backend is offline
- Treat network failures as non-fatal
- Use short request timeouts
- Avoid blocking trade capture when backend data is unavailable

The app must not:

- Store API keys
- Ask users for exchange credentials
- Send trade instructions to the backend
- Interpret market features as buy or sell signals

The app may add a small status note that says backend market data is optional
and public-only.

## Database Impact

V4 should not require a destructive database migration.

Allowed database impact:

- No schema change
- Optional future-ready fields only if needed
- Existing TradeSnapshots can use backend public ticker price when available

Not allowed:

- Deleting old SQLite data
- Rebuilding existing tables
- Storing API credentials
- Storing private account data from exchanges

If no schema change is required, explicitly state this in the final output.

## Safety Rules

V4 must follow these safety rules:

- Public market data only
- No Trade API
- No Withdraw API
- No API Key storage
- No order placement
- No automatic trading
- No buy or sell advice
- No future price prediction
- No guaranteed outcome language
- No hidden credential prompts
- No private account endpoint calls
- Backend failure must not break local app usage

Every README update must restate that V4 is a market data service only.

## Testing

Run app checks:

```powershell
pnpm run typecheck
```

Run backend checks:

```powershell
python -m compileall api
python -m uvicorn api.main:app --host 127.0.0.1 --port 8000
```

Smoke-test these endpoints:

```text
GET http://127.0.0.1:8000/health
GET http://127.0.0.1:8000/market/ticker?exchange=okx&symbol=BTC/USDT
GET http://127.0.0.1:8000/market/ohlcv?exchange=okx&symbol=BTC/USDT&timeframe=1m&limit=200
GET http://127.0.0.1:8000/market/features?exchange=okx&symbol=BTC/USDT
```

If the network or OKX blocks a request, document the failure and keep the app
fallback behavior intact.

## Acceptance Criteria

V4 is accepted when:

- `api/` or `backend/` directory exists
- FastAPI app starts locally
- CCXT is installed through backend requirements
- `/health` returns `status: ok`
- `/market/ticker` returns OKX public ticker data
- `/market/ohlcv` returns OKX OHLCV candles
- `/market/features` returns the required basic fields
- App code tries the backend before fallback market sources
- App does not crash when backend is unavailable
- App still supports current local, mock, and OKX foreground behavior
- README includes V4 startup instructions
- README states that V4 does not trade
- README states that V4 does not save API keys
- `pnpm run typecheck` passes
- Backend smoke check is documented

## Self Review Output

After implementation, output:

- Modified file list
- New file list
- Backend directory structure
- API endpoint summary
- App integration summary
- Database impact
- Safety boundary confirmation
- TypeScript check result
- Backend smoke check result
- Known issues

The final response must explicitly say whether V4 touched trading permissions.

## Future Compatibility

Design V4 so future versions can add:

- V5 Python Feature Engine
- More exchanges
- Background market collection
- Local market data cache
- Feature Database enrichment
- Research Dashboard
- Backtesting
- Signal Engine
- Paper Trading

Do not implement those future systems in V4.

V4 should create the boundary between mobile experience and server-side market
computation. It should not cross into strategy automation.
