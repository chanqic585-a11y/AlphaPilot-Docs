# AlphaPilot prompts

This directory stores versioned engineering prompts for AlphaPilot. Run the
prompts in order. Each stage depends on the safety boundaries and data contracts
from earlier stages.

## Execution order

1. `V1.1-fix-current-app.md`: stabilize the current mobile app and discipline
   workflow.
2. `V2-ai-trading-copilot.md`: move the product toward an AI Trading Copilot
   foundation without real AI or trading permissions.
3. `V3-feature-database.md`: create the local Feature Database for structured
   trade and market context.
4. `V4-backend-ccxt-market-service.md`: add a Python FastAPI backend with CCXT
   public OKX market data.
5. `V5-python-feature-engine.md`: move feature calculation toward Python.
6. `V6-skill-engine.md`: define reusable research skills.
7. `V7-research-dashboard.md`: expose research and quality views.
8. `V8-vectorbt-backtesting.md`: add reproducible backtesting.
9. `V9-signal-engine.md`: define explainable research signals.
10. `V10-live-data-collection.md`: collect public live market data.
11. `V11-paper-trading-lab.md`: simulate trading without real funds.
12. `V12-semi-auto-trading.md`: design human-approved trading workflows.
13. `V13-auto-trading-design.md`: document automation requirements only.

## Safety boundary

The prompt sequence follows this order:

```text
Research
Signal
Risk
Execution
Review
```

Do not jump directly to automatic trading. Each prompt must keep the AlphaPilot
Engineering Principles in `docs/01-Engineering-Principles.md`.
