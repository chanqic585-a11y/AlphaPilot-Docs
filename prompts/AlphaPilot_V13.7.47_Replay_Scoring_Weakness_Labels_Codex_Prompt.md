# AlphaPilot V13.7.47 - Replay Scoring and Weakness Labels

## Goal

Build an explainable review layer on top of V13.7.46 estimated sandbox sample
paths.

The goal is to help the user understand why local sandbox samples won or lost:

- profit not captured
- adverse excursion too deep
- stop-like losses
- favorable path too weak
- cost drag
- holding window too long
- cleaner entry path

This version must remain research-only.

## Non Goals

Do not add:

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
- buy/sell advice

## Scope

1. Add a deterministic scoring module.
2. Score each closed-sample replay record.
3. Add weakness labels and a primary weakness label.
4. Add strategy-level score summaries.
5. Add global top warning/danger labels.
6. Show replay score and weakness labels in the web console.
7. Keep the V13.7.46 estimated path boundary clear.

## Acceptance

- `/api/closed-sample-replay` returns review score fields.
- Samples show `reviewScore`, `reviewRatingLabel`, `primaryWeaknessLabel`, and
  `weaknessLabels`.
- Strategy summaries show average review score and top weakness labels.
- The UI displays the score and weakness labels clearly.
- No trading capability is added.
