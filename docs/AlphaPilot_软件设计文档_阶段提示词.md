# AlphaPilot 软件设计文档 + Codex 分阶段提示词

## 0. 项目最终定位

项目名称建议：AlphaPilot

产品定位：不是交易日记 App，不是自动下单机器人，也不是预测行情工具。

它应该是：个人量化研究平台 + AI 交易副驾驶 + 小资金实盘数据采集系统。

长期目标：先收集真实交易数据；再收集市场特征数据；再分析用户真正赚钱的模式；再做信号筛选；再做小资金半自动交易；最后才考虑自动交易。

核心原则：AI 不直接控制账户；风控层永远拥有最高权限；交易执行层必须和 AI 分析层隔离；任何实盘交易必须小资金、可追踪、可关闭、可审计。

## 1. 总体架构

请将项目逐步重构为以下结构：

```text
alpha-pilot/
├── app/                 React Native / Expo 移动端
├── api/                 FastAPI 或 NestJS API 服务
├── collector/           行情采集服务，接 OKX / Binance / Bybit
├── feature-engine/      技术指标、市场特征、交易特征生成
├── skill-engine/        可插拔交易 Skill 系统
├── signal-engine/       候选信号生成，不自动下单
├── risk-engine/         风控系统，最高权限
├── execution-engine/    未来实盘下单模块，默认关闭
├── research/            Python 量化研究、回测、数据分析
├── ml/                  机器学习模型实验
├── datasets/            导出的历史交易数据和市场数据
├── docs/                架构文档、数据结构、风控规则
└── prompts/             每个版本给 Codex 的开发提示词
```

## 2. 技术路线

移动端：React Native + Expo + TypeScript。

本地存储：SQLite。

后端：FastAPI 优先，原因是未来量化研究、Pandas、机器学习、指标计算都更适合 Python。

数据库：早期 SQLite，中期 PostgreSQL，缓存 Redis。

交易所接口：V2-V3 使用 OKX 公共行情；V4 引入 CCXT 统一交易所接口；V5 以后支持 OKX / Binance / Bybit。

技术指标：早期 TypeScript 简单指标；中期 Python 服务使用 Pandas TA / TA-Lib；不要在 App 里长期维护复杂指标计算。

研究和回测：VectorBT、Backtrader；Freqtrade 作为设计参考，不要直接塞进 App。

机器学习：LightGBM / XGBoost / Random Forest，优先使用表格特征模型，不要一开始就依赖 LLM。

AI：LLM 只负责解释、总结、辅助分析，不要让 LLM 直接下单。

## 3. 实盘小资金原则

用户希望通过小资金实盘收集真实数据，这是合理的。原因是模拟交易和真实交易心态不同，真实交易能记录真实决策、真实滑点、真实情绪、真实执行。

但必须遵守：初期只做人工确认；AI 只分析，不自动交易；小资金交易必须独立账户；每笔交易必须自动记录特征；最大单笔风险默认 1%-2%；最大日亏损默认 3%；杠杆最高 5 倍；永远禁止提现权限 API Key；实盘功能默认关闭；所有实盘行为必须有日志。

## 4. Skill 系统设计

AlphaPilot 需要支持导入交易 Skill。Skill 不是自动交易策略，而是一组可插拔的分析能力。

示例 Skill：First Pullback Skill、Volume Breakout Skill、EMA Trend Skill、RSI Divergence Skill、Funding + OI Skill、New Coin Launch Skill、Liquidity Sweep Skill、Order Block Skill、Fair Value Gap Skill。

Skill 文件格式建议使用 JSON 或 YAML。

Skill 必须只输出：形态识别、可信度、风险提示、需要观察的数据。不能输出：立即买入、一定上涨、保证收益、自动下单。

```text
示例：
{
  "name": "First Pullback",
  "version": "1.0.0",
  "description": "识别新币上市后的首次缩量回踩突破形态",
  "requiredFeatures": ["ema20", "ema60", "volumeChange1h", "atr", "priceChange24h"],
  "rules": [
    {"field": "trendDirection", "operator": "equals", "value": "up"},
    {"field": "volumeChange1h", "operator": "greaterThan", "value": 1.5},
    {"field": "rsi", "operator": "lessThan", "value": 75}
  ],
  "output": {
    "setupType": "first_pullback",
    "riskWarnings": ["RSI 过高时可能存在追高风险", "成交量衰减时形态失效"]
  }
}
```

## 5. 版本路线图

### V1.1：当前 App 基础修正

修正现有交易记录 App 的基础问题。

```text
请基于当前 trade-discipline-journal 项目做 V1.1 修正。
1. 将所有杠杆上限统一为 5 倍。
2. 修改 README、表单校验、UI 文案、类型定义。
3. 首次启动增加 Initial Setup 页面，让用户设置初始本金，默认建议 15000。
4. 简化底部导航为：首页、交易、监控、我的。
5. 在 OKX Monitor 页面顶部明确提示：当前版本只在 App 前台运行时提醒，后台或锁屏可能停止。
6. 保留 SQLite。
7. 不接交易权限。
8. 不自动下单。
9. TypeScript 必须无报错。
10. 更新 README。
```

### V2：AI Trading Copilot，减少输入

用户 10 秒内记录一笔交易。用户只输入币种、方向、入场价、仓位、杠杆，其余内容由系统自动分析。

```text
请开发 AlphaPilot V2：AI Trading Copilot。
核心目标：减少输入，自动分析。
新增 Quick Trade 页面。用户只需要输入：symbol、direction: long / short、entryPrice、positionSize、leverage，最高 5 倍。保存后自动创建交易记录。
新增 AI Analysis 模块，但当前阶段先用规则引擎，不接大模型。
自动分析：当前趋势、成交量状态、波动率、RSI、MACD、EMA20 / EMA60、支撑位、压力位、可能形态、形态可信度、风险提示。
注意：不要给买卖建议；不要写“建议买入”；不要写“一定上涨”；只能写“当前市场状态显示”。
新增数据库表 MarketSnapshots：id, tradeId, symbol, timestamp, price, volume, rsi, macd, macdSignal, ema20, ema60, atr, trend, volatility, support, resistance, createdAt。
新增数据库表 TradeAnalysis：id, tradeId, setupType, confidence, marketSummary, setupSummary, riskSummary, support, resistance, suggestedStopLoss, target1, target2, createdAt。
验收标准：用户可以 10 秒内创建交易；创建交易后自动生成分析；不要求填写情绪和长文本；不自动下单；OKX 公共行情正常更新；TypeScript 无报错；README 更新。
```

### V3：Feature Database，建立核心资产

每笔交易自动生成结构化特征，这是以后量化研究、AI 分析、信号引擎、自动交易的基础。

```text
请开发 AlphaPilot V3：Feature Database。
新增 TradeFeatures 表：id, tradeId, symbol, direction, entryHour, entryWeekday, sessionType, isWeekend, priceChange1h, priceChange4h, priceChange24h, volumeChange1h, volumeChange4h, atr, volatilityLevel, trendDirection, trendStrength, rsi, macd, macdSignal, macdHistogram, ema20, ema60, ema200, priceAboveEma20, priceAboveEma60, emaTrend, setupType, setupConfidence, isBreakout, isPullback, isRange, isChasingHigh, isReversal, stopLossDistancePercent, takeProfitDistancePercent, rrRatio, maxRiskPercent, liquidationRiskLevel, result, isHighQualityWin, isHighQualityLoss, isDisciplineLoss, exitReason, createdAt, updatedAt。
新增 feature-engine 模块：generateTradeFeatures(trade, marketSnapshots)。
要求：缺失数据允许 null；不允许因为指标缺失崩溃；所有 Feature 有 TypeScript 类型；生成后的 Feature 可导出 CSV；新增 Export 页面，支持导出 trades.csv、features.csv、snapshots.csv；新增 Data Quality 页面，显示缺失率；TypeScript 无报错；README 更新。
```

### V4：后端化 + CCXT 接入

App 不再承担复杂行情与指标计算，引入后端服务和 CCXT。

```text
请开发 AlphaPilot V4：Backend + CCXT Market Service。
请新增 api/ 和 collector/ 目录。技术栈：FastAPI + Python。引入 ccxt。
当前只允许使用公共行情接口，不接 Trade 权限，不下单。
后端提供接口：GET /health；GET /market/ticker?exchange=okx&symbol=BTC/USDT；GET /market/ohlcv?exchange=okx&symbol=BTC/USDT&timeframe=1m&limit=200；GET /market/features?exchange=okx&symbol=BTC/USDT。
Feature 返回：price, volume, priceChange1h, priceChange4h, priceChange24h, high24h, low24h, volatility, atr, rsi, ema20, ema60, macd, trendDirection。
App 端修改：优先从后端获取市场数据；后端不可用时退回当前 OKX WebSocket；保留本地 SQLite；README 增加后端启动说明；不实现交易功能；不保存 API Key。
验收标准：FastAPI 可以启动；CCXT 可以获取 OKX 行情；App 可以调用后端接口；TypeScript 和 Python 基础检查通过；不涉及真实交易权限。
```

### V5：Python Feature Engine + Pandas TA

把复杂技术指标计算迁移到 Python。

```text
请开发 AlphaPilot V5：Python Feature Engine。
在 feature-engine/ 中实现 Python 指标计算服务。引入 pandas 和 pandas-ta。如果 pandas-ta 安装失败，允许先实现自定义 fallback 指标函数。
新增接口 POST /features/calculate。输入：symbol, timeframe, ohlcv[]。输出：rsi, macd, macdSignal, macdHistogram, ema20, ema60, ema200, atr, volumeChange, volatility, trendDirection, trendStrength。
新增模块：feature_engine/indicators.py、feature_engine/trend.py、feature_engine/volatility.py、feature_engine/schema.py。
要求：指标计算独立于 App；App 只负责展示；所有返回结构稳定；缺失数据返回 null；README 说明如何运行 Python Feature Engine；添加简单单元测试；不接交易权限。
```

### V6：Skill Engine，可导入交易思路

建立 Skill 插件系统，让用户可以导入现成交易思路。

```text
请开发 AlphaPilot V6：Skill Engine。
目标：支持通过 JSON / YAML 导入交易 Skill。新增 skill-engine/。
Skill 文件结构：name, version, description, requiredFeatures, rules, output, riskWarnings。
新增默认 Skills：First Pullback、Volume Breakout、EMA Trend、RSI Overheated Risk、New Coin High Volatility、Funding + OI Watch。
Skill 运行结果：setupType, confidence, matchedRules, failedRules, riskWarnings。
注意：Skill 只能识别形态和风险，不能输出买入卖出，不能自动下单。
App 新增 Skill 页面：已安装 Skills、导入 Skill、查看 Skill 规则、启用 / 禁用 Skill。支持从本地 JSON 导入。未来预留 GitHub URL 导入接口，但本版不强制实现。
验收标准：可以导入 Skill JSON；可以运行 Skill 检测当前交易；可以显示匹配规则和风险提示；默认 Skills 可用；TypeScript / Python 无报错；README 增加 Skill 规范。
```

### V7：Research Dashboard，分析真实优势

分析用户实盘小资金交易数据，找出在哪些条件下更容易赚钱或亏钱。

```text
请开发 AlphaPilot V7：Research Dashboard。
基于 TradeFeatures 和真实交易结果生成研究分析。新增 Research 页面。
统计：总交易数、胜率、平均盈利、平均亏损、Profit Factor、最大回撤、最大连续亏损、平均持仓时间、按 setupType 分析、按交易时间分析、按 volatilityLevel 分析、按 trendDirection 分析、按 Skill 分析。
新增 Insight Engine。规则：样本少于 20 笔，不允许给强结论；样本少于 50 笔，只能给弱提示；样本超过 100 笔，才允许给较明确建议；所有洞察必须基于真实数据。
示例：你在 First Pullback 形态下交易 46 笔，胜率 63%，Profit Factor 1.7。样本不足时提示：样本不足，暂不生成结论。
验收标准：可以按 Feature 分组分析；可以生成洞察；样本不足时不乱说；图表正常显示；README 更新。
```

### V8：VectorBT / Backtesting Research

开始做策略研究和回测，引入 vectorbt。

```text
请开发 AlphaPilot V8：Research Backtesting。
在 research/ 中引入 Python 回测环境。优先使用 vectorbt。
目标：使用历史 OHLCV 数据测试 Skill 规则组合。
新增 research/backtest_vectorbt.py、research/data_loader.py、research/strategy_rules.py、research/report_generator.py。
支持：加载历史 K 线 CSV；加载 Skill JSON；将 Skill 规则转换为入场条件；回测收益曲线；输出总收益、最大回撤、胜率、Profit Factor、交易次数、平均持仓时间。
注意：回测结果只用于研究，不能直接作为实盘信号。README 必须说明过拟合风险。
验收标准：可以对一个 Skill 做历史回测；可以输出 backtest_report.json；可以导出图表或 CSV；不影响 App 正常运行。
```

### V9：Signal Engine，候选机会扫描

基于用户历史优势 + 当前市场特征，扫描候选机会。不是自动交易。

```text
请开发 AlphaPilot V9：Signal Engine。
Signal Engine 只生成候选机会。禁止输出立即买入、立即卖出、保证上涨、保证盈利。
Signal 结构：id, symbol, exchange, timestamp, signalType, confidence, matchedSkill, matchedFeatures, userHistoricalStats, riskWarnings, marketSummary, status: candidate / watched / dismissed / traded, createdAt。
信号来源：当前市场 Feature、已安装 Skill、用户历史交易统计、风险过滤器。
生成逻辑：如果某个 Skill 在用户历史中样本数 > 30、胜率 > 55%、Profit Factor > 1.3，并且当前市场匹配该 Skill，则生成 candidate。
App 新增 Signals 页面：候选机会、匹配原因、历史表现、风险提示、一键记录为交易、忽略、加入观察。
验收标准：可以扫描 Watchlist；可以生成候选 Signal；Signal 有风险提示；可以转为交易记录；不自动下单；README 更新。
```

### V10：小资金实盘数据采集模式

正式支持用户用小资金实盘交易采集数据。只读同步，不下单。

```text
请开发 AlphaPilot V10：Live Data Collection Mode。
目标：支持小资金实盘数据采集。只接交易所 Read 权限 API。禁止 Trade 权限，禁止 Withdraw 权限。
新增 Exchange Account 页面。用户可以添加 OKX API Key。
要求：只允许 Read 权限；检测到 Trade 权限时弹出风险提示；检测到 Withdraw 权限必须拒绝保存；API Secret 本地加密保存；README 明确安全风险；推荐用户绑定 IP。
功能：自动读取当前持仓；自动读取历史成交；自动同步开仓价、仓位、方向；自动生成交易记录；自动补充 MarketSnapshots；自动生成 TradeFeatures。
注意：本阶段不允许下单，不允许撤单，不允许设置止损，只读同步。
验收标准：能连接 OKX Read API；能同步持仓；能同步历史成交；能生成交易记录；不具备下单能力；README 更新。
```

### V11：Paper Trading，可选但不强制

用于测试 Signal Engine，不用于替代真实交易数据。

```text
请开发 AlphaPilot V11：Paper Trading Lab。
目标：用于测试 Signal Engine，不用于替代真实交易数据。
新增 Paper Account：initialBalance, currentBalance, maxRiskPerTrade, maxDailyLoss。
新增 Paper Orders：signalId, symbol, direction, entryPrice, stopLoss, takeProfit, positionSize, leverage, status, pnl。
功能：Signal 可以转为 Paper Trade；根据实时行情更新盈亏；触及止盈止损自动关闭模拟单；统计 Paper 策略表现。
README 必须说明：Paper Trading 不能代表真实交易心态，它只用于测试策略逻辑，不用于证明一定盈利。
```

### V12：Semi-Auto Trading，小资金半自动

第一个真实交易执行阶段，默认关闭，必须用户确认。

```text
请开发 AlphaPilot V12：Semi-Auto Trading。
这是实盘交易功能，必须极其谨慎，默认关闭。
新增 Semi-Auto Mode。默认 enabled=false，requireManualConfirm=true。
流程：Signal Engine 生成候选机会；Risk Engine 检查；用户手动确认；Execution Engine 下单。
要求：API Key 必须有 Trade 权限；API Key 必须没有 Withdraw 权限；检测到 Withdraw 权限必须拒绝；用户必须手动开启 Semi-Auto Mode；每笔订单必须用户确认；单笔风险不得超过设置；当日亏损不得超过设置；连续亏损触发停止；下单后必须设置止损；如果止损设置失败，立即报警并停止 Semi-Auto Mode。
新增 RealOrders 表：id, signalId, exchange, symbol, direction, entryPrice, stopLoss, takeProfit, positionSize, leverage, orderId, status, pnl, createdAt, updatedAt。
新增 AuditLogs 表：id, eventType, payload, createdAt。
验收标准：默认不能实盘交易；必须用户手动开启；必须用户手动确认订单；Risk Engine 可以拦截订单；所有实盘行为有日志；断线、异常、止损失败时自动停止；README 明确风险。
```

### V13：Auto Trading 设计文档，不开发

只设计，不开发自动交易代码。

```text
请只设计 AlphaPilot V13 Auto Trading，不要写实盘自动交易代码。
输出文档：Auto Trading 架构、Risk Engine 最高权限设计、Kill Switch 设计、Order Engine 设计、审计日志设计、API 安全设计、回滚机制、测试计划、上线前检查清单。
Kill Switch 必须包括：单日亏损超限、单周亏损超限、最大回撤超限、连续亏损超限、WebSocket 断线、API 异常、止损单失败、订单状态不同步、市场极端波动、用户手动停止。
自动交易必须满足条件后才能进入：小资金实盘数据不少于 500 笔；Signal Engine 稳定运行；Research Dashboard 显示策略有正期望；最大回撤可接受；风控系统稳定；用户明确确认。
要求：不要实现自动交易代码，只输出设计文档。
```

## 6. 开源项目导入时间表

V4：导入 CCXT，目的：统一交易所行情接口。

V5：导入 Pandas / Pandas TA / TA-Lib，目的：技术指标计算。

V6：建立 Skill Engine，目的：导入交易思路，不直接导入大型策略系统。

V8：导入 VectorBT，目的：回测 Skill 和策略组合。

V9：参考 Freqtrade 的策略结构和风控思想，不要直接把 Freqtrade 嵌入 App。

V12：参考 Freqtrade / CCXT 的订单执行经验，但必须自己写 Risk Engine 和 Audit Log。

## 7. 最终提醒

AlphaPilot 最值钱的不是 AI。最值钱的是：真实交易数据、市场特征数据、交易结果标签、用户自己的优势模型。

不要急着自动交易。先让系统知道：你到底在哪些条件下赚钱。

当数据足够多以后，AI 才真正有意义。自动交易不是起点，自动交易是长期数据验证后的结果。
