# AlphaPilot V13.8.1 - Testnet Readiness UI Boundary

## Goal

Add a visible but disabled Testnet readiness center to AlphaPilot Control
Console.

This patch prepares the product direction for future testnet work without
adding any trading capability.

## Scope

1. Add a read-only Testnet design boundary payload.
2. Add `GET /api/testnet-design-boundary`.
3. Show a greyed Testnet readiness center in the web console.
4. Display missing controls before testnet can be considered.
5. Display future connection sequence.
6. Display disabled action buttons.
7. Update README and docs.

## Non Goals

Do not add:

- API key input
- API key storage
- Trade API
- Withdraw API
- real account reads
- real position reads
- real order creation
- testnet order creation
- exchange Dry-run
- live trading
- automatic trading

## Required Greyed Actions

The UI may show these actions only as disabled buttons:

- input API Key
- connect Testnet
- create Testnet order
- exchange Dry-run

## Safety Copy

The UI must clearly say:

```text
V13.8.1 is a readiness UI only. API Key input, Testnet connection, order
creation, and exchange Dry-run are disabled.
```

## Acceptance Criteria

1. `/api/testnet-design-boundary` exists.
2. Console shows Testnet readiness center.
3. Testnet buttons are disabled.
4. API key input is not available.
5. No Trade API or Withdraw API is added.
6. No real or testnet order can be created.
7. README documents the boundary.
8. Checks pass.
