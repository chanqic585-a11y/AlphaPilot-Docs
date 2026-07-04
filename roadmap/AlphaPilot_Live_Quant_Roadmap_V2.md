# AlphaPilot Live Quant Roadmap V2

## Positioning

AlphaPilot 的最终方向是实盘量化交易系统，但 V13.1.3 仍然只稳定手机端控制台、行情缓存、AI 分析入口、交易计划记录和异动提醒链路。

AlphaPilot Mobile App 的定位：

- 手机端行情观察和 AI 技术分析控制台。
- 用户手动交易计划、盈亏和交易习惯记录入口。
- 后续 Quant Engine 的人工确认、风险查看和运行状态面板。

AlphaPilot Quant Engine 的定位：

- 未来独立后端服务。
- 负责更长周期数据采集、策略研究、回测、Dry-run、风控和受控执行工作流。
- 不由 V13.1.3 实现。

## Layered Roadmap

1. Data layer: public market data, OHLCV cache, ticker cache, indicator cache, instrument metadata, unit metadata, precision metadata.
2. Feature layer: technical indicators, support/resistance zones, data quality, market structure context.
3. Research layer: AI analysis, knowledge retrieval, trade habit records, pattern statistics, alert history.
4. Backtest layer: historical validation of candidate rules before any execution design.
5. Dry-run layer: simulated execution environment with no exchange private API.
6. Controlled execution layer: proposal, risk gate, human gate, execution gate, audit log.
7. Multi-model judgment layer: future cross-checking for research conclusions, never direct order ownership.

## Version Path

- V13.1.3: fix Android runtime issues and document the live quant roadmap.
- V13.2 to V13.6: improve mobile market UI, chart readability, alert reliability, trade habit fields, and local statistics.
- V14: begin AlphaPilot-Quant-Engine design only after the mobile control plane is stable.
- Later versions: run backtest and Dry-run first, then consider strictly controlled live execution.

## Safety Boundary

- V13.1.3 does not implement strategy lab, Freqtrade, backtesting changes, real trading, Trade API, Withdraw API, API Key storage, account reads, position reads, order creation, or automatic trading.
- AI can organize research context, but it never owns money and never directly creates execution.
- Any future live capability must pass risk gates and human confirmation before execution is considered.
