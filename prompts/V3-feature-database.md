# V3 Feature Database prompt

## Goal

在 V2 AI Trading Copilot 基础上建立 Feature Database。

把每一笔交易转化为可查询、可导出、可长期分析的结构化数据。

目标不是只保存交易结果。

目标是保存每一笔交易发生时的市场特征。

这些数据让产品未来可以支持长期模式分析、AI Memory、RAG、OCR、模型训练和报告生成。

## Scope

V3 聚焦交易特征数据库和数据资产沉淀。

包含范围：

- 每笔交易的市场特征保存
- 每笔交易的执行结果保存
- 查询维度设计
- 未来扩展接口预留
- 数据导出方向预留
- SQLite 本地存储延续

## Requirements

### Feature Database

每一笔交易保存：

- `tradeId`
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

### Query goals

支持未来查询：

- 哪些 `setupType` 最赚钱
- 哪些市场环境胜率最高
- 哪些情绪最容易亏损
- 哪些时间周期更适合用户
- 哪些违规行为造成最大亏损

### Future extension

预留：

- 多交易所行情 Provider
- AI Provider
- OCR Provider
- Chart Screenshot Provider
- Local Model Provider
- Cloud Model Provider
- Data Export Provider
- PDF Report Provider

## Safety Rules

- Feature Database 只保存分析数据
- 不保存交易权限
- 不保存提现权限
- 不自动交易
- 不自动下单
- 不生成买卖建议
- 不保证收益
- 不因为指标缺失导致 App 崩溃

## Acceptance Criteria

- 每笔交易可以关联结构化 Feature 数据
- 市场特征字段支持缺失值
- 交易结果字段可以用于后续统计
- 查询目标在数据结构中有对应字段支撑
- 未来 Provider 接口方向明确
- 数据继续保存在本地 SQLite
- 文档清楚区分分析数据和交易权限
- TypeScript 类型在后续实现中保持清晰
- README 同步更新安全边界
