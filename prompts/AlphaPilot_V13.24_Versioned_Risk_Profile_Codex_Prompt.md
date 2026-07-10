# AlphaPilot V13.24 - Versioned Risk Profile

## Goal

Replace fixed account, strategy, position, leverage, and loss assumptions with
immutable, checksummed, environment-specific RiskProfile versions while
preserving a separate code-reviewed SafetyEnvelope.

## Required behavior

- Support `local_forward`, `okx_demo`, `live_canary`, and `live_standard`.
- New settings create new versions; old records are never overwritten.
- Activation and rollback are append-only and auditable.
- Release contracts bind the exact RiskProfile id and hash.
- Evaluate account-level strategy, position, symbol, direction, correlation,
  notional, leverage, loss, drawdown, cooldown, data, and liquidity limits.
- Keep `reward/risk >= 2R`.
- Routine UI cannot raise the SafetyEnvelope.
- Configuration or activation never grants order permission.

## Safety boundary

V13.24 does not store raw credentials, expose Withdraw, create Live orders, or
enable Live execution. It prepares the bounded configuration contract only.
