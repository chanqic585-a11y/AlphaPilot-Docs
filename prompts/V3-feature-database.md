# V3 Feature Database Prompt

在 V2 AI Trading Copilot 基础上，建立 Feature Database。

目标：不要只保存交易结果，而是保存每一笔交易发生时的市场特征，使产品未来可以进行长期模式分析、AI Memory、RAG、OCR、模型训练和报告生成。

## Feature Database

每一笔交易保存：

- tradeId
- 时间
- 币种
- 方向
- 入场价格
- 出场价格
- 成交量
- EMA
- MACD
- RSI
- ATR
- Open Interest
- Funding
- Fear & Greed
- 24 小时涨跌
- 上市时间
- 距离上市多久
- 市场波动率
- K 线形态
- 趋势
- 支撑
- 压力
- 最终盈亏
- 是否纪律亏损
- 是否按计划执行

## Query Goals

支持未来查询：

- 哪些 setupType 最赚钱。
- 哪些市场环境胜率最高。
- 哪些情绪最容易亏损。
- 哪些时间周期更适合用户。
- 哪些违规行为造成最大亏损。

## Future Extension

预留：

- 多交易所行情 Provider
- AI Provider
- OCR Provider
- Chart Screenshot Provider
- Local Model Provider
- Cloud Model Provider
- Data Export Provider
- PDF Report Provider

## Safety

Feature Database 只保存分析数据，不保存交易权限，不保存提现权限，不自动交易，不生成买卖建议。

