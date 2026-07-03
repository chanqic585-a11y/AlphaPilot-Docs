# AlphaPilot V13 - Market-First AI Technical Analysis Rebuild

## Version Name

```text
AlphaPilot V13 - Market-First AI Technical Analysis Rebuild
```

中文版本名：

```text
AlphaPilot V13 - 实时行情优先的 AI 技术分析重构版
```

---

## Background

当前 V12.2 / V12.2.1 已经具备：

```text
AI Provider
Mock Provider
OpenAI-compatible Provider
AI Prompt Builder
AI Response Parser
AI Reports
AI Feedback
V12.1 Technical Analysis Knowledge Base
Research Dashboard
Paper Trading
Semi-Auto Design
Live Data Collection
OKX Public Market Monitor
```

但是当前产品主线已经偏离真实需求。

当前 App 更像：

```text
交易日记
风控规则
Paper Trading
执行边界
研究实验系统
AI 报告记录器
```

用户真正需要的是：

```text
输入币种
获取实时行情
自动计算技术指标
AI 根据实时数据和技术分析资料分析走势
给出中文分析建议
用户自己判断是否买入 / 开仓
开仓后再人工登记交易计划和复盘
```

因此 V13 不是 V12.2 的小补丁，而是一次产品主线纠偏。

V13 要把 AlphaPilot 从：

```text
交易日记 / 风控实验系统
```

调整为：

```text
实时行情驱动的中文 AI 技术分析助手
```

---

## Core User Requirement

用户明确想要的主流程是：

```text
输入币种
↓
获取交易所实时币种数据
↓
根据实时行情、K线、成交量、MACD、RSI、支撑压力等数据分析走势
↓
AI 给出中文分析结果和观察建议
↓
人工自己判断是否买入 / 开仓
↓
如果已经开仓，人工再登记交易计划
```

V13 必须围绕这个主流程开发。

---

## Product Direction

V13 的核心不是继续扩展旧的：

```text
OKX 公共行情监听
Execution Intent
Pre-trade Check
Dry Run
Signal Engine
Paper Trading
Feature Database
Skill Engine
Research Dashboard
```

这些功能可以保留，但必须从主页面移走，放入：

```text
高级功能
历史研究
旧版功能
```

V13 的主线必须变成：

```text
AI 行情分析
```

用户打开 App 后，第一眼应该看到：

```text
输入币种
选择交易所
选择K线周期
点击：获取行情并分析
```

而不是看到一堆开发者功能入口。

---

## V13 One-Sentence Definition

```text
V13 将 AlphaPilot 重构为实时行情优先的 AI 技术分析助手。用户输入币种、交易所和K线周期后，App 自动获取交易所公共实时行情和K线数据，自动计算 RSI、MACD、ATR、成交量、K线结构、支撑位、压力位等技术指标，并结合 V12.1 技术分析知识库生成中文 AI 分析建议。用户自行判断是否交易；开仓后再登记交易计划和复盘。
```

---

## Important Product Correction

当前旧功能 `OKX 公共行情监听` 不符合 V13 主流程。

旧逻辑：

```text
必须先有未复盘交易计划
↓
系统才监听对应币种
↓
监听目标主要是止盈 / 止损提醒
```

V13 新逻辑必须是：

```text
用户直接输入币种
↓
系统直接获取实时行情
↓
系统直接获取K线
↓
系统直接分析走势
```

因此：

```text
旧 OKX 公共行情监听可以保留为高级功能
但不能再作为实时行情分析主入口
```

---

## V13 Top-Level Goals

V13 必须完成以下目标：

1. 新增主入口：`AI 行情分析`。
2. 用户输入币种后，必须主动获取交易所公共实时行情。
3. 默认交易所为 OKX。
4. 默认交易对为 `币种/USDT`。
5. 支持用户输入 `BTC`、`ETH`、`SOL`、`BTC/USDT`、`BTC-USDT`。
6. 支持 K线周期选择。
7. 周期字段必须叫 `K线周期` 或 `分析周期`，不要叫 `Timeframe`。
8. 默认周期建议为 `1h`。
9. 系统必须获取最新 ticker。
10. 系统必须获取最近 OHLCV K线。
11. 系统必须自动计算技术指标。
12. 系统必须结合 V12.1 技术分析知识库。
13. 系统必须调用 V12.2 AI Provider 生成中文分析。
14. AI 分析必须基于刚获取的实时数据，而不是只读旧数据库。
15. AI 输出中文分析建议。
16. 用户自行判断是否交易。
17. 开仓后再登记交易计划。
18. 大幅简化 `我的` 页面。
19. 旧功能整理到 `高级功能`。
20. README 以 V13 作为新的主线版本记录。
21. 不删除旧数据。
22. 不新增真实交易能力。
23. 不接交易所私有 API。
24. 不保存交易所 API Key。
25. 不下单。
26. 不自动交易。

---

## Non Goals

V13 不做以下事情：

1. 不接 Trade API。
2. 不接 Withdraw API。
3. 不接交易所私有 API。
4. 不保存交易所 API Key。
5. 不读取真实账户余额。
6. 不读取真实持仓。
7. 不读取真实订单。
8. 不创建真实订单。
9. 不自动下单。
10. 不自动交易。
11. 不自动创建 SignalCandidate。
12. 不自动创建 ExecutionIntent。
13. 不自动创建 PaperOrder。
14. 不自动执行 Dry Run。
15. 不承诺收益。
16. 不预测确定性涨跌。
17. 不输出“稳赚”“必涨”“必跌”。
18. 不把 AI 分析当成交易指令。
19. 不删除旧表。
20. 不删除旧数据。
21. 不强行重写整个 App。
22. 不把旧功能直接删掉导致历史数据不可用。

---

## AI Advice Boundary

用户希望 AI 给出“建议”，但这个建议必须定义为：

```text
技术分析建议
观察建议
风险提醒
走势情景推演
```

不是：

```text
下单指令
买卖信号
仓位建议
杠杆建议
```

AI 可以输出：

```text
当前技术偏向：偏多 / 偏空 / 震荡 / 不明确
当前动能：增强 / 减弱 / 冲突
K线结构：强 / 弱 / 震荡 / 未确认
成交量：放大 / 萎缩 / 未确认
MACD：动能改善 / 动能减弱 / 信号冲突
RSI：偏强 / 偏弱 / 过热 / 超卖 / 背离风险
支撑位：接近 / 跌破 / 回踩 / 未确认
压力位：接近 / 突破 / 受阻 / 未确认
偏多情景
震荡情景
偏空情景
重点观察
风险提示
AI 分析建议
```

AI 不允许输出：

```text
现在买入
现在卖出
立即做多
立即做空
开仓
平仓
下单
仓位多少
杠杆多少
止损价多少
目标价多少
强烈买入
强烈卖出
```

推荐表达：

```text
当前更适合观察价格是否放量突破压力区，而不是直接给出买入指令。
```

```text
如果价格跌破关键支撑，并且成交量放大、MACD 动能继续走弱，则偏空情景的证据会增强。
```

```text
当前 AI 分析建议是：关注支撑压力区反应、成交量变化和 RSI / MACD 是否形成共振，最终是否交易由用户自行判断。
```

---

## K-line Period Meaning

V13 页面中的 `K线周期` 表示：

```text
AI 用哪一个 K线级别分析行情
```

不是：

```text
持仓时间
交易计划时间
开仓时间
```

示例：

```text
15m = 15分钟K线，偏短线观察
1h = 1小时K线，适合日内分析
4h = 4小时K线，适合波段分析
1d = 日线，适合趋势背景
```

V13 推荐默认：

```text
主分析周期：1h
辅助周期：15m + 4h
```

当用户选择 `1h` 时，系统可以自动获取：

```text
15m 作为短周期参考
1h 作为主周期
4h 作为大周期参考
```

如果实现时间有限，V13-A 先实现单周期分析；多周期可以作为 V13-B 扩展。

---

## New Main User Flow

V13 主流程：

```text
打开 App
↓
进入 AI 行情分析
↓
输入币种：BTC
↓
选择交易所：OKX
↓
选择K线周期：1h
↓
点击：获取行情并分析
↓
App 获取 OKX 实时 ticker
↓
App 获取 OKX K线 OHLCV
↓
App 自动计算技术指标
↓
App 检索 V12.1 技术知识库
↓
AI 输出中文分析建议
↓
用户自己判断是否交易
↓
如果已经开仓，点击：我已开仓，登记计划
```

---

## Main Page Design

V13 应该新增或重构一个主页面：

```text
src/screens/MarketFirstAnalysisScreen.tsx
```

中文页面标题：

```text
AI 行情分析
```

副标题：

```text
输入币种后，系统会自动获取实时行情、计算技术指标，并结合技术知识库生成 AI 分析建议。
```

输入区：

```text
币种
交易所
K线周期
```

默认值：

```text
币种：BTC
交易所：OKX
K线周期：1h
```

按钮：

```text
获取行情并分析
```

加载状态：

```text
正在获取 OKX 实时行情...
正在获取 K线数据...
正在计算技术指标...
正在检索技术知识库...
正在生成 AI 分析...
```

错误状态：

```text
获取行情失败，请检查网络或稍后重试。
OKX 数据暂不可用。
K线数据不足，无法完成完整分析。
AI 分析失败，已生成本地技术摘要。
```

---

## V13 Home / My Page Simplification

当前 `我的` 页面入口过多，用户不知道有什么用。

V13 必须整理主页面入口。

建议主入口只保留：

```text
AI 行情分析
历史 AI 分析
我的交易计划
技术知识库
高级功能
设置
```

旧功能放入 `高级功能` 页面：

```text
行情提醒 / 旧版监听
Feature Database
Skill Engine
Research Dashboard
Backtesting
Signal Engine
Live Data Collection
Paper Trading
Semi-Auto Design
Knowledge Base 原入口
旧版 AI 分析
```

注意：

```text
不要删除旧功能
不要删除旧数据
不要破坏已有页面
只是从主页面隐藏，统一放入高级功能
```

可以新增：

```text
src/screens/AdvancedFeaturesScreen.tsx
```

高级功能页说明：

```text
这些是历史研究和实验功能，普通行情分析不需要进入。
```

---

## Market Data Requirements

V13 必须新增一个独立的市场数据分析链路。

不要继续依赖：

```text
未复盘交易计划
listMonitorableTrades()
旧 OKX 监听
止损止盈提醒
```

新增目录建议：

```text
src/market-analysis/
  marketAnalysisTypes.ts
  marketSymbol.ts
  marketRealtimeClient.ts
  marketIndicatorEngine.ts
  marketStructureAnalyzer.ts
  marketAnalysisRepository.ts
  marketAnalysisRunner.ts
```

---

## Market Symbol Normalization

新增：

```text
src/market-analysis/marketSymbol.ts
```

要求支持用户输入：

```text
BTC
btc
BTC/USDT
BTC-USDT
BTC-USDT-SWAP
ETH
SOL
DOGE
```

统一解析为：

```text
baseSymbol: BTC
quoteSymbol: USDT
displaySymbol: BTC/USDT
okxSpotInstId: BTC-USDT
ccxtSymbol: BTC/USDT
```

默认：

```text
quote = USDT
marketType = spot
exchange = okx
```

V13 先支持 OKX spot 公共行情。

合约 / 永续可以后续再做，不要在 V13-A 复杂化。

---

## Market Realtime Client

新增：

```text
src/market-analysis/marketRealtimeClient.ts
```

职责：

```text
根据币种、交易所、K线周期，主动获取实时 ticker 和 OHLCV K线
```

必须支持：

```text
fetchRealtimeTicker(input)
fetchRealtimeOhlcv(input)
fetchMarketAnalysisData(input)
```

输入：

```ts
{
  exchange: 'okx';
  symbol: string;
  timeframe: '5m' | '15m' | '1h' | '4h' | '1d';
  limit?: number;
}
```

输出：

```ts
{
  exchange: 'okx';
  symbol: 'BTC/USDT';
  instrumentId: 'BTC-USDT';
  timeframe: '1h';
  ticker: {
    lastPrice: number;
    change24h: number | null;
    volume24h: number | null;
    high24h: number | null;
    low24h: number | null;
    timestamp: string;
  };
  ohlcv: Array<{
    timestamp: string;
    open: number;
    high: number;
    low: number;
    close: number;
    volume: number;
  }>;
  dataSource: 'direct_okx_public' | 'backend_market_api' | 'fallback';
  fetchedAt: string;
}
```

### Data Source Strategy

V13 可以保留现有 backend market API，但不能只依赖它。

优先级建议：

```text
1. Direct OKX public REST from app
2. Existing backend market API fallback
3. Clear failure message
```

原因：

```text
手机真机使用时，本地 127.0.0.1 / localhost / 10.0.2.2 很容易不可用
用户需要的是直接输入币种就能获取行情
```

如果 direct OKX public REST 在某些环境失败，必须给明确错误，而不是静默失败。

---

## Technical Indicator Engine

新增：

```text
src/market-analysis/marketIndicatorEngine.ts
```

基于 OHLCV 自动计算：

```text
RSI
MACD
ATR
成交量变化
K线方向
趋势方向
支撑位
压力位
波动率状态
```

V13-A 至少必须实现：

```text
RSI 14
MACD 12/26/9
ATR 14
成交量均值对比
最近 N 根 K线涨跌结构
简单支撑位
简单压力位
趋势方向
```

不需要引入复杂依赖，优先使用 TypeScript 直接计算。

### Indicator Output

```ts
export interface MarketTechnicalIndicators {
  rsi14: number | null;
  macd: {
    macd: number | null;
    signal: number | null;
    histogram: number | null;
  };
  atr14: number | null;
  volume: {
    latestVolume: number | null;
    averageVolume20: number | null;
    volumeRatio: number | null;
    volumeState: '放量' | '缩量' | '正常' | '未知';
  };
  priceStructure: {
    latestClose: number | null;
    recentHigh: number | null;
    recentLow: number | null;
    candleBias: '偏强' | '偏弱' | '震荡' | '未知';
  };
  supportResistance: {
    support: number | null;
    resistance: number | null;
    method: string;
  };
  trend: {
    direction: '上升' | '下降' | '震荡' | '未知';
    strength: number;
    description: string;
  };
  dataQuality: {
    candleCount: number;
    enoughForRsi: boolean;
    enoughForMacd: boolean;
    enoughForAtr: boolean;
    score: number;
    notes: string[];
  };
}
```

---

## Simple Support / Resistance Method

V13-A 先实现简单可靠的方法，不要过度复杂。

建议：

```text
support = 最近 50 根 K线中较明显的低点区域
resistance = 最近 50 根 K线中较明显的高点区域
```

可以先用：

```text
support = recent lows 的低分位数，或最近 swing low
resistance = recent highs 的高分位数，或最近 swing high
```

必须在 UI 中说明：

```text
支撑压力为算法估算区域，仅用于技术分析参考。
```

不要伪装成绝对精确价格。

---

## Market Structure Analyzer

新增：

```text
src/market-analysis/marketStructureAnalyzer.ts
```

职责：

```text
把 ticker、OHLCV、指标结果转成 AI 可读的中文市场结构摘要
```

输出：

```ts
export interface MarketStructureSummary {
  symbol: string;
  exchange: string;
  timeframe: string;
  summaryText: string;
  klineSummary: string;
  volumeSummary: string;
  rsiSummary: string;
  macdSummary: string;
  atrSummary: string;
  supportResistanceSummary: string;
  trendSummary: string;
  riskNotes: string[];
}
```

---

## Market Analysis Database

V13 可以新增轻量表，保存每次实时行情分析结果。

新增表：

```text
MarketAnalysisRuns
MarketAnalysisSnapshots
MarketIndicatorSnapshots
```

### MarketAnalysisRuns

```sql
CREATE TABLE IF NOT EXISTS MarketAnalysisRuns (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  runId TEXT NOT NULL,
  exchange TEXT NOT NULL,
  symbol TEXT NOT NULL,
  instrumentId TEXT NOT NULL,
  timeframe TEXT NOT NULL,
  status TEXT NOT NULL,
  dataSource TEXT NOT NULL,
  errorMessage TEXT,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

### MarketAnalysisSnapshots

```sql
CREATE TABLE IF NOT EXISTS MarketAnalysisSnapshots (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  runId TEXT NOT NULL,
  exchange TEXT NOT NULL,
  symbol TEXT NOT NULL,
  instrumentId TEXT NOT NULL,
  timeframe TEXT NOT NULL,
  tickerJson TEXT NOT NULL,
  ohlcvJson TEXT NOT NULL,
  fetchedAt TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

### MarketIndicatorSnapshots

```sql
CREATE TABLE IF NOT EXISTS MarketIndicatorSnapshots (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  runId TEXT NOT NULL,
  symbol TEXT NOT NULL,
  instrumentId TEXT NOT NULL,
  timeframe TEXT NOT NULL,
  indicatorsJson TEXT NOT NULL,
  structureSummaryJson TEXT NOT NULL,
  dataQualityScore REAL NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

Indexes：

```sql
CREATE INDEX IF NOT EXISTS idx_market_analysis_runs_run ON MarketAnalysisRuns(runId);
CREATE INDEX IF NOT EXISTS idx_market_analysis_runs_symbol ON MarketAnalysisRuns(symbol);
CREATE INDEX IF NOT EXISTS idx_market_analysis_runs_created ON MarketAnalysisRuns(createdAt);
CREATE INDEX IF NOT EXISTS idx_market_analysis_snapshots_run ON MarketAnalysisSnapshots(runId);
CREATE INDEX IF NOT EXISTS idx_market_indicator_snapshots_run ON MarketIndicatorSnapshots(runId);
CREATE INDEX IF NOT EXISTS idx_market_indicator_snapshots_symbol ON MarketIndicatorSnapshots(symbol);
```

Migration：

```text
使用 CREATE TABLE IF NOT EXISTS
不删除旧表
不删除旧数据
PRAGMA user_version = 14
```

---

## Market Analysis Runner

新增：

```text
src/market-analysis/marketAnalysisRunner.ts
```

主函数：

```ts
runMarketFirstAIAnalysis(input: {
  symbol: string;
  exchange: 'okx';
  timeframe: '5m' | '15m' | '1h' | '4h' | '1d';
  providerName?: AIProviderName;
  apiKey?: string;
}): Promise<{
  run: MarketAnalysisRun;
  marketSnapshot: MarketAnalysisSnapshot;
  indicatorSnapshot: MarketIndicatorSnapshot;
  aiReport: AIAnalysisReport;
}>
```

流程：

```text
1. normalize symbol
2. fetch realtime ticker
3. fetch OHLCV
4. validate data quality
5. calculate RSI / MACD / ATR / volume / support / resistance / trend
6. save MarketAnalysisRun
7. save MarketAnalysisSnapshot
8. save MarketIndicatorSnapshot
9. retrieve V12.1 technical knowledge chunks
10. build AI prompt based on realtime market data
11. call AI provider
12. parse AI response
13. save AIAnalysisReport
14. save evidence links
15. return result
```

关键要求：

```text
AI 分析必须基于本次刚获取的 MarketAnalysisSnapshot 和 MarketIndicatorSnapshot
不能只读取旧 MarketDataSnapshots
不能只读取旧 TradeFeatures
```

---

## AI Prompt Update

V13 必须新增 Market-first AI Prompt。

可以新增：

```text
src/ai-analysis/aiMarketPromptBuilder.ts
```

或者扩展现有：

```text
aiPromptBuilder.ts
```

但必须明确区分 V12.2 旧 Prompt 和 V13 Market-first Prompt。

Prompt 重点：

```text
你是中文 AI 技术分析助手。
你现在分析的是实时行情数据，而不是交易日记。
请基于最新 ticker、OHLCV、RSI、MACD、ATR、成交量、支撑位、压力位、趋势结构，以及技术分析知识库引用，输出中文分析建议。
```

必须包含：

```text
symbol
exchange
timeframe
last price
24h change
24h volume
OHLCV summary
RSI
MACD
ATR
volume state
support
resistance
trend
data quality
knowledge chunks
```

输出结构：

```ts
export interface MarketFirstAIAnalysisOutput {
  language: 'zh-CN';
  technicalBias: '偏多' | '偏空' | '震荡' | '不明确';
  adviceLevel: '积极观察' | '谨慎观察' | '等待确认' | '风险较高';
  summary: string;
  marketOverview: string;
  klineAnalysis: string;
  volumeAnalysis: string;
  rsiAnalysis: string;
  macdAnalysis: string;
  atrAnalysis: string;
  supportAnalysis: string;
  resistanceAnalysis: string;
  trendAnalysis: string;
  indicatorConfluence: string;
  indicatorConflict: string;
  bullishScenario: string;
  neutralScenario: string;
  bearishScenario: string;
  watchPoints: string[];
  riskWarnings: string[];
  humanDecisionReminder: string;
  evidenceChunkIds: string[];
  dataQualityNotes: string[];
}
```

`humanDecisionReminder` 必须包含：

```text
AI 只提供技术分析建议，最终是否交易由用户自行判断。
```

---

## AI Output UI

AI 行情分析页面必须显示：

```text
实时行情
K线周期
最新价格
24h 涨跌幅
24h 成交量
数据来源
数据更新时间
技术指标
RSI
MACD
ATR
成交量状态
趋势方向
支撑位
压力位
AI 分析建议
总体技术偏向
建议级别
行情概况
K线分析
成交量分析
RSI 分析
MACD 分析
ATR 分析
支撑位分析
压力位分析
指标共振
指标冲突
偏多情景
震荡情景
偏空情景
重点观察
风险提醒
人工判断提醒
```

不要显示大量英文。

---

## Trading Plan After Opening

V13 必须新增或重构“开仓后登记计划”。

页面名称：

```text
我的交易计划
```

按钮：

```text
我已开仓，登记计划
```

说明：

```text
如果你已经在交易所手动开仓，可以在这里登记交易计划、理由和后续观察点。
```

字段：

```text
币种
方向
交易所
开仓价格
开仓时间
仓位备注
我为什么开仓
关联 AI 分析报告
我关注的支撑位
我关注的压力位
失效条件
后续观察计划
复盘备注
```

注意：

```text
这是开仓后的计划登记
不是开仓前审批
不是自动交易
不是下单功能
```

可以复用旧 Trades 表，也可以新增更清晰的表：

```text
ManualTradePlans
```

如果复用旧 Trades 会导致字段语义混乱，建议新增：

```sql
CREATE TABLE IF NOT EXISTS ManualTradePlans (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  planId TEXT NOT NULL,
  symbol TEXT NOT NULL,
  exchange TEXT NOT NULL,
  direction TEXT NOT NULL,
  entryPrice REAL,
  entryTime TEXT,
  positionNote TEXT,
  entryReason TEXT NOT NULL,
  linkedAIReportId TEXT,
  supportLevel REAL,
  resistanceLevel REAL,
  invalidationCondition TEXT,
  observationPlan TEXT,
  reviewNote TEXT,
  status TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL,
  updatedAt TEXT NOT NULL
);
```

Indexes：

```sql
CREATE INDEX IF NOT EXISTS idx_manual_trade_plans_plan ON ManualTradePlans(planId);
CREATE INDEX IF NOT EXISTS idx_manual_trade_plans_symbol ON ManualTradePlans(symbol);
CREATE INDEX IF NOT EXISTS idx_manual_trade_plans_status ON ManualTradePlans(status);
CREATE INDEX IF NOT EXISTS idx_manual_trade_plans_created ON ManualTradePlans(createdAt);
```

---

## UI Simplification Requirements

V13 必须整理 `MyScreen`。

当前很多功能对用户造成干扰。

V13 主菜单建议：

```text
AI 行情分析
历史 AI 分析
我的交易计划
技术知识库
高级功能
设置
```

### AI 行情分析

主功能。

### 历史 AI 分析

显示过往 AI 行情分析报告。

### 我的交易计划

开仓后登记和复盘。

### 技术知识库

V12.1 Knowledge Base，可以保留。

### 高级功能

放旧功能。

### 设置

AI Provider、API Key 本次运行说明、默认交易所、默认周期等。

---

## Advanced Features

新增：

```text
src/screens/AdvancedFeaturesScreen.tsx
```

包含：

```text
旧版行情提醒
Feature Database
Skill Engine
Research Dashboard
Backtesting
Signal Engine
Live Data Collection
Paper Trading
Semi-Auto Design
旧版 AI 分析
```

高级功能页顶部说明：

```text
这些是历史研究和实验功能。普通行情分析只需要使用 AI 行情分析。
```

---

## Repository Cleanup / Version Record

用户要求“仓库里面无用的版本也可以整理一下，以 V13 版本为重新开发的版本进行记录编写”。

因此需要整理 README 和 Docs。

### README Must Add

新增章节：

```text
## V13 Current Mainline
```

内容：

```text
V13 is the new current product mainline.

The app is now focused on:

- 输入币种
- 获取交易所公共实时行情
- 获取K线数据
- 自动计算技术指标
- 使用 V12.1 技术知识库作为分析资料
- 使用 AI 生成中文技术分析建议
- 用户自行判断是否交易
- 开仓后登记交易计划
```

### Legacy Versions Section

把旧版本整理为：

```text
## Legacy Research Milestones
```

说明：

```text
V1.1 - V12.2.1 are preserved as historical research milestones.
They are not the main user flow after V13.
Old functions remain available under Advanced Features.
```

中文：

```text
V1.1 到 V12.2.1 作为历史研究里程碑保留。
V13 开始，App 主线调整为实时行情驱动的 AI 技术分析。
旧功能不会删除，但会收纳到高级功能中。
```

### Do Not Delete

不要删除：

```text
旧 commits
旧 prompts
旧数据库表
旧页面源码
旧研究数据
```

可以整理：

```text
README 结构
MyScreen 菜单结构
Docs prompts index
高级功能入口
旧功能说明
```

如果 Docs 仓库有 prompt index，可以新增：

```text
V13 = Current Mainline
V1.1 - V12.2.1 = Legacy Research Milestones
```

---

## Files To Add

建议新增：

```text
src/market-analysis/marketAnalysisTypes.ts
src/market-analysis/marketSymbol.ts
src/market-analysis/marketRealtimeClient.ts
src/market-analysis/marketIndicatorEngine.ts
src/market-analysis/marketStructureAnalyzer.ts
src/market-analysis/marketAnalysisRepository.ts
src/market-analysis/marketAnalysisRunner.ts

src/ai-analysis/aiMarketPromptBuilder.ts

src/screens/MarketFirstAnalysisScreen.tsx
src/screens/ManualTradePlanScreen.tsx
src/screens/AdvancedFeaturesScreen.tsx
```

---

## Files To Modify

需要修改：

```text
src/db/database.ts
src/db/repositories.ts
src/screens/MyScreen.tsx
src/screens/AIAnalysisScreen.tsx
src/screens/ResearchDashboardScreen.tsx
src/ai-analysis/aiTypes.ts
src/ai-analysis/aiResponseParser.ts
src/ai-analysis/aiRunner.ts
README.md
```

如有必要修改：

```text
src/services/marketDataService.ts
src/services/priceAlerts.ts
src/types.ts
```

但不要破坏旧功能。

---

## Safety Requirements

V13 必须保持：

```text
只用公共行情
不使用交易所私有接口
不保存交易所 API Key
不请求交易权限
不请求提现权限
不下单
不自动交易
不读取真实账户
不读取真实持仓
```

所有页面底部只需简短中文说明：

```text
AI 只提供技术分析建议，最终是否交易由用户自行判断。
```

不要再堆叠大量英文安全说明。

---

## Error Handling Requirements

V13 必须清楚处理错误：

### 行情获取失败

显示：

```text
获取 OKX 行情失败，请检查网络或稍后重试。
```

### K线不足

显示：

```text
K线数据不足，部分指标无法计算。
```

### RSI 不足

显示：

```text
K线数量不足，暂时无法计算 RSI。
```

### MACD 不足

显示：

```text
K线数量不足，暂时无法计算 MACD。
```

### AI 失败

显示：

```text
AI 分析失败，已生成本地技术摘要。
```

### 数据来源不可用

显示：

```text
当前数据来源不可用，请重试或切换网络。
```

不要静默失败。

---

## Testing Requirements

完成后运行：

```powershell
pnpm run typecheck
pnpm exec expo config --type public
git diff --check
```

如果修改 `api/`：

```powershell
python -m compileall api
```

如果未修改 `api/`，说明不需要运行 compileall。

---

## Manual Test Checklist

必须手动验证：

1. App 启动。
2. 首页 / 我的页面入口明显简化。
3. 能进入 `AI 行情分析`。
4. 默认币种 BTC。
5. 默认交易所 OKX。
6. 默认 K线周期 1h。
7. 点击 `获取行情并分析`。
8. App 显示正在获取行情。
9. App 能获取 OKX ticker。
10. App 能获取 OKX OHLCV。
11. App 能显示最新价格。
12. App 能显示 24h 涨跌幅。
13. App 能显示成交量。
14. App 能计算 RSI。
15. App 能计算 MACD。
16. App 能计算 ATR。
17. App 能估算支撑位。
18. App 能估算压力位。
19. App 能显示趋势结构。
20. App 能检索 V12.1 技术知识库。
21. App 能生成 AI 中文分析建议。
22. AI 报告不出现明确买入 / 卖出指令。
23. AI 报告包含偏多 / 偏空 / 震荡 / 不明确。
24. AI 报告包含重点观察。
25. AI 报告包含风险提醒。
26. 能保存 MarketAnalysisRun。
27. 能保存 MarketAnalysisSnapshot。
28. 能保存 MarketIndicatorSnapshot。
29. 能保存 AIAnalysisReport。
30. 能进入 `我的交易计划`。
31. 能点击 `我已开仓，登记计划`。
32. 能保存开仓后交易计划。
33. 能关联 AI 分析报告。
34. 旧功能在高级功能里仍能进入。
35. 旧数据不丢失。
36. `OKX 公共行情监听` 不再作为主入口。
37. 页面基本中文化。
38. 手机小屏幕可阅读。
39. 错误提示清楚。
40. OpenAI-compatible provider 仍可用。
41. Mock provider 仍可用。
42. 不保存 raw AI API Key。
43. 不出现交易所 API Key 输入。
44. 不创建订单。
45. 不自动交易。
46. 不创建 ExecutionIntent。
47. 不创建 SignalCandidate。
48. 不执行 Dry Run。

---

## Static Safety Scan

扫描以下关键词：

```text
apiSecret
passphrase
exchangeKey
withdraw
createOrder
cancelOrder
fetchBalance
fetchPositions
private
trade api
withdraw api
place order
execute trade
buy now
sell now
go long
go short
建议买入
建议卖出
立即买入
立即卖出
立即做多
立即做空
开仓指令
平仓指令
仓位建议
杠杆建议
稳赚
必涨
必跌
```

允许：

```text
本次运行 API Key
OpenAI-compatible API Key
AI Provider API Key
防御性过滤列表中的禁用词
否定语境中的安全说明
用户手动登记“已开仓”
```

不允许：

```text
交易所 API Key
交易所 secret
真实下单
自动下单
自动创建订单
自动创建执行意图
AI 输出买卖指令
AI mock 输出买卖指令
```

---

## Acceptance Criteria

V13 完成后必须满足：

1. 新增 `MarketFirstAnalysisScreen.tsx`。
2. 新增 `market-analysis/` 目录。
3. 支持输入币种。
4. 支持选择交易所，默认 OKX。
5. 支持选择 K线周期，默认 1h。
6. 点击按钮后主动获取实时行情。
7. 不依赖旧交易计划监听。
8. 不依赖 `listMonitorableTrades()` 才能获取行情。
9. 能获取 ticker。
10. 能获取 OHLCV。
11. 能计算 RSI。
12. 能计算 MACD。
13. 能计算 ATR。
14. 能分析成交量。
15. 能估算支撑位。
16. 能估算压力位。
17. 能判断趋势方向。
18. 能保存 MarketAnalysisRuns。
19. 能保存 MarketAnalysisSnapshots。
20. 能保存 MarketIndicatorSnapshots。
21. `PRAGMA user_version = 14`。
22. AI 分析基于本次实时行情数据。
23. AI 分析结合 V12.1 Knowledge Base。
24. AI 输出中文。
25. AI 输出技术偏向。
26. AI 输出分析建议。
27. AI 输出重点观察。
28. AI 输出风险提醒。
29. AI 不输出明确买入卖出指令。
30. 新增或重构 `我的交易计划`。
31. 支持开仓后登记计划。
32. 交易计划可以关联 AI 报告。
33. `我的` 页面主入口大幅简化。
34. 旧功能进入 `高级功能`。
35. README 标记 V13 为 Current Mainline。
36. README 标记 V1.1-V12.2.1 为 Legacy Research Milestones。
37. 不删除旧数据。
38. 不删除旧表。
39. 不接 Trade API。
40. 不接 Withdraw API。
41. 不读取真实账户。
42. 不读取真实持仓。
43. 不创建订单。
44. 不自动交易。
45. 不自动创建 SignalCandidate。
46. 不自动创建 ExecutionIntent。
47. 不执行 Dry Run。
48. `pnpm run typecheck` 通过。
49. `pnpm exec expo config --type public` 通过。
50. `git diff --check` 通过。

---

## Development Priority

请按以下顺序开发：

```text
1. 新增 market-analysis 类型和 symbol normalization
2. 新增实时行情 client
3. 新增 OHLCV 获取
4. 新增技术指标计算
5. 新增市场结构摘要
6. 新增 MarketAnalysis 数据表和 repository
7. 新增 Market-first AI Prompt
8. 新增 MarketAnalysisRunner
9. 新增 AI 行情分析页面
10. 新增开仓后交易计划页面
11. 简化 MyScreen
12. 新增 AdvancedFeaturesScreen
13. README 重构 V13 Current Mainline
14. 测试
15. 安全扫描
```

---

## Codex Execution Instruction

请严格按照本 Prompt 执行 V13。

这是 V13 架构纠偏版本，不是 V12.2 的小补丁。

核心目标只有一个：

```text
输入币种 → 获取实时行情 → 自动计算技术指标 → AI 分析走势 → 给出中文分析建议 → 人工自己判断 → 开仓后登记计划
```

请不要继续围绕旧的“未复盘交易计划监听”做主线开发。

旧功能可以保留，但必须从主页面整理到高级功能。

完成后请输出完整 Self Review。

---

## Self Review Output

完成后请输出：

1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除请写无
4. 是否新增数据库表
5. 是否更新 `PRAGMA user_version`
6. MarketAnalysisRuns 说明
7. MarketAnalysisSnapshots 说明
8. MarketIndicatorSnapshots 说明
9. ManualTradePlans 说明
10. 实时行情获取方式说明
11. 是否支持 direct OKX public data
12. 是否仍依赖本地 backend
13. 是否仍依赖未复盘交易计划
14. 技术指标计算说明
15. RSI 说明
16. MACD 说明
17. ATR 说明
18. 支撑压力估算说明
19. AI Prompt 更新说明
20. AI 是否基于本次实时行情数据
21. AI 是否结合 V12.1 Knowledge Base
22. AI 输出边界说明
23. AI 行情分析页面说明
24. 我的交易计划页面说明
25. MyScreen 简化说明
26. AdvancedFeaturesScreen 说明
27. README V13 Current Mainline 说明
28. Legacy Research Milestones 说明
29. 是否删除旧数据
30. 是否删除旧功能
31. 是否接 Trade API
32. 是否接 Withdraw API
33. 是否读取真实账户
34. 是否创建真实订单
35. 是否自动交易
36. 是否自动创建 ExecutionIntent
37. 是否自动创建 SignalCandidate
38. 是否执行 Dry Run
39. `pnpm run typecheck` 结果
40. `pnpm exec expo config --type public` 结果
41. `git diff --check` 结果
42. 安全扫描结果
43. 已知问题
44. 手机真机测试建议
45. 下一版本建议
