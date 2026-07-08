# AlphaPilot V13.7.48 - Strategy Weakness Action Board

## Goal

Build a small local research layer on top of V13.7.47 replay scoring.

V13.7.48 should convert replay weakness labels into strategy repair action
items, so the user can see what to research next without reading every sample.

This version does not modify strategy logic, does not add trading permissions,
and does not create orders.

## Scope

- Add a backend module that reads closed-sample replay output.
- Convert warning and danger weakness labels into research action rows.
- Add `GET /api/weakness-action-board`.
- Add `GET /api/weakness-action-board/actions`.
- Add a web console panel named `策略弱点行动板`.
- Show action count, high-priority count, medium-priority count, blocked-upgrade
  count, and individual research tasks.
- Update Control Console README and docs.

## Action Mapping

Required mappings:

- `deep_adverse_excursion`: reduce adverse excursion exposure and review entry
  filters.
- `holding_too_long`: research time exits and holding-window limits.
- `stop_loss_like`: isolate stop-like losses by asset, timeframe, direction, and
  market state.
- `profit_not_captured`: research MFE capture and trailing exit candidates.
- `weak_favorable_path`: raise trigger quality and add momentum confirmation.
- `cost_drag_high`: reduce cost drag through liquidity, volume, and target-space
  filters.
- `path_missing`: block upgrade until replay fields are complete.

## Safety Boundary

V13.7.48 must remain local research only.

Forbidden:

- Trade API
- Withdraw API
- API key storage
- reading real accounts
- reading real positions
- creating orders
- exchange Dry-run
- automatic trading
- trading advice

## Acceptance Criteria

- `alphapilot_control_console/weakness_action_board.py` exists.
- `/api/weakness-action-board` returns summary and actions.
- `/api/weakness-action-board/actions` returns action rows.
- The web console shows the action board after closed-sample replay.
- README documents V13.7.48.
- Docs prompt exists.
- Python compileall passes.
- `node --check web/app.js` passes.
- `python -m alphapilot_control_console.http_app --smoke` passes.
- `git diff --check` passes.
- No trading capability is added.
