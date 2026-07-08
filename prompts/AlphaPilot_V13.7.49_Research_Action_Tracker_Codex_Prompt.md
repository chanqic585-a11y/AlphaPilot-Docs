# AlphaPilot V13.7.49 - Research Action Tracker

## Goal

Build a local task-tracking layer for V13.7.48 strategy weakness action items.

The user should be able to mark each weakness action as todo, in progress,
needs more samples, resolved, or archived from the local control console.

This version remains research-only.

## Scope

- Add local state storage for weakness action tasks.
- Merge task status into `/api/weakness-action-board`.
- Add `GET /api/weakness-action-board/tasks`.
- Add `POST /api/weakness-action-task`.
- Add task status counters to the web console.
- Add status and priority filters to the web console.
- Add action buttons:
  - start processing
  - needs more samples
  - mark resolved
  - archive
- Store local notes for each action.
- Update README and docs.

## Non Goals

- Do not modify strategy logic automatically.
- Do not start testnet.
- Do not start Dry-run.
- Do not connect exchange private APIs.
- Do not save API keys.
- Do not read real account balances or positions.
- Do not create orders.
- Do not enable automatic trading.

## Safety Boundary

Completing or archiving a research action does not approve a strategy for
trading. It only records local research progress.

## Acceptance Criteria

- Local task state is stored under `weaknessActionTasks`.
- `/api/weakness-action-board` includes task status fields.
- `/api/weakness-action-board/tasks` returns saved task state.
- `/api/weakness-action-task` updates task state.
- The web console can filter weakness actions by status and priority.
- The web console can update task status.
- Python compileall passes.
- `node --check web/app.js` passes.
- `python -m alphapilot_control_console.http_app --smoke` passes.
- `git diff --check` passes.
- No trading capability is added.
