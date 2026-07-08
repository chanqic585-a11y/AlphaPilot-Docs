# AlphaPilot V13.7.46 - Sandbox Sample Path Instrumentation

## Goal

Upgrade the local Control Console closed-sample replay view so sandbox samples
can show an estimated review path instead of only an R outcome.

V13.7.46 is not a trading execution release.

It must not add:

- API key storage
- Trade API
- Withdraw API
- exchange testnet orders
- real account reads
- real position reads
- order creation
- exchange Dry-run
- live trading
- automatic trading

## Scope

Implement a local-only sample path instrumentation layer:

1. Read local public OHLCV cache files.
2. Estimate entry time and price.
3. Estimate exit time and price.
4. Infer research direction.
5. Estimate market regime.
6. Calculate MFE / MAE in R.
7. Calculate path outcome in R.
8. Add fee and slippage estimates.
9. Add holding time and replay candle window.
10. Surface the fields in `GET /api/closed-sample-replay`.
11. Surface the fields in the web console replay panel.

## Required Boundary

Every enriched sample must clearly indicate:

```text
actualExchangeFill = false
instrumentationMode = estimated_from_local_ohlcv_cache
```

The UI must not present estimated OHLCV paths as real exchange fills.

## Acceptance

- Closed-sample replay returns estimated path fields when local OHLCV cache is
  available.
- Old logs can be enriched dynamically without rewriting old data.
- New sandbox logs can include the same estimated path fields.
- The existing sample de-duplication schema remains unchanged.
- The web console displays estimated path state, path R, MFE, MAE, fee R,
  slippage R, and holding time.
- Safety boundary remains unchanged.
