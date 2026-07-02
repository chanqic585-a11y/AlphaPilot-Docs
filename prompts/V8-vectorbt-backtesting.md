# AlphaPilot V8 - VectorBT Backtesting Research Lab

## Goal

基于当前 `trade-discipline-journal` V7 版本，开发 AlphaPilot V8：Backtesting Research Lab。

V8 的目标不是 AI，不是交易信号，不是自动交易。

V8 的目标是：

把 V5 Python Feature Engine、V6 Skill Engine、V7 Research Dashboard 的研究能力进一步推进到“历史规则验证”阶段。

V8 要让 AlphaPilot 从：

```text
能观察历史交易和研究结果
```

升级为：

```text
可以用公开历史行情，对研究规则进行历史回测，并输出统计报告
```

V8 只做历史研究验证。

V8 不决定用户下一笔交易。

V8 不生成实时买卖信号。

V8 不自动下单。

---

## Current State

当前项目已经完成：

* V1.1：交易纪律、风险规则、Initial Setup、5x 杠杆限制
* V2：Quick Trade、Trade Detail、Mock Analysis、Snapshot、Timeline
* V3：Feature Database、TradeFeatures 表、Data Quality 页面、CSV 导出
* V4：FastAPI 后端、CCXT、OKX 公共行情接口
* V4.1：后端已改用 CCXT unified public methods
* V5：Python Feature Engine，计算 EMA、MACD、RSI、ATR、volatility、trendDirection、dataQualityScore
* V5.1：修正 change1h / change4h 的 timeframe 语义
* V6：Skill Engine，5 个内置研究 Skill，SkillResults 表
* V6.1：SkillResults 增加 runGroupId，Skill Engine 页面优先展示最新批次
* V7：Research Dashboard，本地聚合 Trades、TradeFeatures、SkillResults

当前已有：

* `api/main.py`
* `api/market_service.py`
* `api/feature_engine.py`
* `src/research/`
* `src/skill-engine/`
* `src/services/marketDataService.ts`
* `src/services/featureApiService.ts`
* SQLite 本地数据表：

  * Trades
  * TradeFeatures
  * SkillResults
  * TradeAnalysis
  * TradeSnapshots

V8 必须基于这些继续开发，不要推翻之前版本。

---

## Scope

V8 只做 Backtesting Research Lab。

包含：

1. 后端新增 backtesting engine。
2. 使用 OKX 公共历史 OHLCV 数据。
3. 支持基础回测规则模板。
4. 支持用户选择 symbol、timeframe、limit、rule template。
5. 计算回测统计指标。
6. 返回结构化 backtest report。
7. App 端新增 Backtesting 页面。
8. My 页面新增 Backtesting 入口。
9. Backtesting 页面展示历史回测结果。
10. 支持保存回测结果到本地 SQLite。
11. README 更新 V8 说明。
12. 保持 V1.1～V7 功能不被破坏。

---

## Non Goals

V8 不做以下内容：

1. 不接真实 AI。
2. 不接 OpenAI / Claude / Gemini。
3. 不做 Signal Engine。
4. 不生成实时买卖信号。
5. 不提供买卖建议。
6. 不告诉用户下一笔交易该怎么做。
7. 不做 Paper Trading。
8. 不做 Semi-Auto Trading。
9. 不做 Auto Trading。
10. 不接交易权限 API。
11. 不保存 API Key。
12. 不读取交易所余额。
13. 不读取交易所持仓。
14. 不下单。
15. 不撤单。
16. 不预测未来价格。
17. 不承诺收益。
18. 不说某个策略未来有效。
19. 不使用刺激交易文案。

---

## Product Principle

V8 必须遵循 AlphaPilot 的核心原则：

1. Data before AI.
2. Research before automation.
3. Risk above signal.
4. AI never owns money.
5. Everything is feature.
6. Explainability before black-box output.
7. Evidence before opinion.

Backtesting 只能回答：

```text
这套规则在历史数据中表现如何？
```

不能回答：

```text
现在该不该买？
现在该不该卖？
下一笔怎么做？
```

---

## Architecture

推荐新增结构：

```text
api/
  backtesting_engine.py
  backtesting_models.py

src/
  services/
    backtestApiService.ts

  backtesting/
    backtestTypes.ts
    backtestMapper.ts

  screens/
    BacktestingScreen.tsx
```

说明：

* `api/backtesting_engine.py` 负责后端历史回测。
* `api/backtesting_models.py` 定义回测输入输出结构。
* `api/main.py` 新增 backtesting endpoints。
* `src/services/backtestApiService.ts` 负责 App 调用后端。
* `src/backtesting/backtestTypes.ts` 定义 App 端类型。
* `src/backtesting/backtestMapper.ts` 负责把后端结果映射到本地数据结构。
* `src/screens/BacktestingScreen.tsx` 展示回测研究页面。

---

## Backend Requirements

### 1. Add Backtesting Engine

新增：

```text
api/backtesting_engine.py
```

职责：

1. 通过 `MarketService.fetch_ohlcv()` 获取 OKX 公共 OHLCV。
2. 基于 OHLCV 计算研究规则的历史入场/出场。
3. 计算统计指标。
4. 返回结构化结果。
5. 不调用任何私有交易所接口。
6. 不读取账户。
7. 不下单。

---

### 2. VectorBT Requirement

V8 文件名是 `V8-vectorbt-backtesting.md`，因此优先尝试使用 VectorBT 或兼容的 vectorized 回测方式。

要求：

1. 如果 `vectorbt` 可以稳定安装和运行，可以加入到 `api/requirements.txt`。
2. 如果 `vectorbt` 与当前 Python 环境、依赖、Windows 或 Expo 工作流冲突，不要强行引入。
3. 如果不能稳定使用 `vectorbt`，可以使用纯 Python / pandas 风格的轻量回测实现作为 fallback。
4. 不允许因为 vectorbt 依赖问题阻塞 V8。
5. README 必须说明实际采用的是：

   * vectorbt implementation
   * 或 lightweight Python fallback implementation

优先级：

```text
稳定可运行 > 框架名义完整
```

---

### 3. Backtest Input

后端接口需要支持：

```text
exchange: okx
symbol: BTC/USDT
timeframe: 1h
limit: 300
ruleId: ema_trend_pullback_v1
initialCapital: 10000
riskPerTradePercent: 1
feeRate: 0.0005
slippageRate: 0.0005
```

所有参数必须有安全默认值。

限制：

* `exchange` 第一版只支持 `okx`
* `limit` 建议限制在 50～1000
* `riskPerTradePercent` 建议限制在 0.1～5
* `feeRate` 和 `slippageRate` 不能为负
* 不使用真实账户余额
* `initialCapital` 只是研究用模拟资金，不是交易账户

---

## Backtest Rule Templates

V8 至少实现 4 个规则模板。

这些规则是历史研究模板，不是实时信号。

### 1. EMA Trend Pullback

ID:

```text
ema_trend_pullback_v1
```

逻辑示例：

* EMA fast > EMA slow
* RSI 在中性区间
* price 接近 EMA fast
* 出场用：

  * 固定持有 N 根 candle
  * 或 ATR stop / take profit

输出必须说明：

```text
This is a historical rule template, not a live signal.
```

---

### 2. RSI Mean Reversion

ID:

```text
rsi_mean_reversion_v1
```

逻辑示例：

* RSI < 30 作为历史入场条件
* RSI 回到 50 或固定持有 N 根 candle 作为出场条件
* 只做历史统计

禁止输出：

```text
RSI below 30 means buy now
```

---

### 3. Volatility Breakout Research

ID:

```text
volatility_breakout_research_v1
```

逻辑示例：

* 当前 close 突破过去 N 根 high
* ATR 或 volatility 高于阈值
* 出场用固定持有 N 根 candle 或 ATR stop

说明：

这是历史突破研究模板，不是买入建议。

---

### 4. Setup Pattern Replay

ID:

```text
setup_pattern_replay_v1
```

逻辑示例：

* 尝试把 V7 里的 setupType 概念转成历史规则模板
* 可先支持：

  * volume_breakout_previous_high
  * extreme_fear_rebound 的简化版本
* 如果缺少某些字段，例如 fearGreed、listingTime，必须返回 null 或 unknown，不得伪造。

---

## Backtest Output

后端返回结构建议：

```json
{
  "ruleId": "ema_trend_pullback_v1",
  "ruleName": "EMA Trend Pullback",
  "exchange": "okx",
  "symbol": "BTC/USDT",
  "timeframe": "1h",
  "limit": 300,
  "initialCapital": 10000,
  "finalEquity": 10450,
  "totalReturnPercent": 4.5,
  "maxDrawdownPercent": 6.2,
  "tradeCount": 12,
  "winRate": 58.3,
  "averageWin": 180,
  "averageLoss": -110,
  "profitFactor": 1.42,
  "expectancy": 37.5,
  "sharpeLike": 0.8,
  "feeRate": 0.0005,
  "slippageRate": 0.0005,
  "entries": 12,
  "exits": 12,
  "equityCurve": [
    {
      "timestamp": 1710000000000,
      "equity": 10000
    }
  ],
  "trades": [
    {
      "entryTime": 1710000000000,
      "exitTime": 1710020000000,
      "entryPrice": 68000,
      "exitPrice": 69000,
      "pnl": 120,
      "returnPercent": 1.2,
      "holdingCandles": 6,
      "exitReason": "fixed_holding"
    }
  ],
  "dataQuality": {
    "candleCount": 300,
    "missingFields": [],
    "qualityScore": 90
  },
  "warnings": [
    "Historical backtest only. Not trading advice."
  ],
  "source": "backtesting_engine_v8",
  "generatedAt": "ISO_DATE"
}
```

如果没有交易：

```json
{
  "tradeCount": 0,
  "warnings": [
    "No historical rule matches found in this sample.",
    "This does not mean the rule is invalid or valid."
  ]
}
```

---

## Backtest Metrics

至少计算：

1. initialCapital
2. finalEquity
3. totalReturnPercent
4. maxDrawdownPercent
5. tradeCount
6. winRate
7. averageWin
8. averageLoss
9. profitFactor
10. expectancy
11. fee impact
12. slippage impact
13. equityCurve
14. trade list
15. dataQuality score

注意：

这些指标只描述历史样本表现。

不能写：

```text
This strategy will work
```

只能写：

```text
This rule produced these historical results on the selected sample.
```

---

## Backend API Endpoints

在 `api/main.py` 中新增：

### GET /backtest/rules

返回可用规则模板。

示例：

```json
{
  "rules": [
    {
      "ruleId": "ema_trend_pullback_v1",
      "ruleName": "EMA Trend Pullback",
      "description": "Historical EMA trend pullback research template.",
      "source": "backtesting_engine_v8"
    }
  ]
}
```

### GET /backtest/run

示例：

```text
GET /backtest/run?exchange=okx&symbol=BTC/USDT&timeframe=1h&limit=300&ruleId=ema_trend_pullback_v1&initialCapital=10000&riskPerTradePercent=1
```

返回 backtest report。

要求：

1. 只支持 GET。
2. 不接账户。
3. 不接 API Key。
4. 不下单。
5. 不保存服务器端结果。
6. 后端失败时返回安全错误。
7. App 端不能崩溃。

---

## App Integration

新增：

```text
src/services/backtestApiService.ts
```

功能：

1. 调用 `/backtest/rules`
2. 调用 `/backtest/run`
3. 后端不可用时返回 null
4. 不阻塞 App
5. 不上传 API Key
6. 不上传账户信息
7. 不发送交易指令
8. 使用 `requestMarketApi` 复用 V4/V5 后端 base URL 和 timeout

---

## App Types

新增：

```text
src/backtesting/backtestTypes.ts
```

至少定义：

```text
BacktestRule
BacktestRequest
BacktestReport
BacktestTrade
BacktestEquityPoint
BacktestDataQuality
```

---

## Database Requirements

V8 可以新增本地表保存回测结果。

新增 SQLite 表：

```sql
CREATE TABLE IF NOT EXISTS BacktestResults (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  runId TEXT NOT NULL,
  ruleId TEXT NOT NULL,
  ruleName TEXT NOT NULL,
  symbol TEXT NOT NULL,
  exchange TEXT NOT NULL,
  timeframe TEXT NOT NULL,
  limitCount INTEGER NOT NULL,
  initialCapital REAL NOT NULL,
  finalEquity REAL,
  totalReturnPercent REAL,
  maxDrawdownPercent REAL,
  tradeCount INTEGER,
  winRate REAL,
  profitFactor REAL,
  expectancy REAL,
  reportJson TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

新增索引：

```sql
CREATE INDEX IF NOT EXISTS idx_backtest_results_run ON BacktestResults(runId);
CREATE INDEX IF NOT EXISTS idx_backtest_results_rule ON BacktestResults(ruleId);
CREATE INDEX IF NOT EXISTS idx_backtest_results_symbol ON BacktestResults(symbol);
CREATE INDEX IF NOT EXISTS idx_backtest_results_created ON BacktestResults(createdAt);
```

Migration 要求：

1. 使用 `CREATE TABLE IF NOT EXISTS`
2. 不删除旧表
3. 不重建旧表
4. 不删除旧数据
5. 不修改 Trades
6. 不修改 TradeFeatures
7. 不修改 SkillResults
8. 不保存 API Key
9. 不保存交易所私有数据
10. 不保存真实账户数据

如果更新 user_version，使用：

```text
PRAGMA user_version = 6;
```

---

## Backtesting Screen

新增页面：

```text
src/screens/BacktestingScreen.tsx
```

入口：

```text
我的 -> Backtesting
```

页面内容：

1. Header

   * Title: Backtesting Research
   * Subtitle: Historical rule testing using public market data.

2. Safety Note

   * “Backtests are historical research only. They are not trading advice.”

3. Controls

   * symbol
   * timeframe
   * limit
   * rule template
   * initial capital
   * risk per trade
   * fee rate
   * slippage rate

4. Actions

   * Load Rules
   * Run Backtest
   * Save Result
   * Refresh Saved Results

5. Result Summary Cards

   * Total Return
   * Max Drawdown
   * Trade Count
   * Win Rate
   * Profit Factor
   * Expectancy

6. Warnings

   * historical only
   * sample size
   * no guarantee
   * data limitations

7. Equity Curve

   * simple text/list or lightweight bar/list
   * no chart dependency required

8. Trade List

   * entryTime
   * exitTime
   * pnl
   * returnPercent
   * exitReason

9. Saved Results

   * recent saved backtest reports

UI 可以用现有组件：

* Screen
* Button
* StatCard
* Text
* View
* TextInput if available

不要为了 V8 引入复杂图表库。

---

## My Screen Integration

更新：

```text
src/screens/MyScreen.tsx
```

新增入口：

```text
Backtesting
```

描述：

```text
用公开历史行情验证研究规则，查看历史表现统计。
```

---

## Repository Requirements

在 `src/db/repositories.ts` 中新增：

```text
createBacktestResult(input)
listBacktestResults(limit?)
getBacktestResultByRunId(runId)
```

类型可放在 `src/types.ts` 或 `src/backtesting/backtestTypes.ts`。

要求：

1. 只保存回测报告。
2. 不保存 API Key。
3. 不保存真实账户。
4. 不保存交易所私有数据。
5. 不触发任何交易动作。

---

## Safety Rules

V8 必须遵守：

1. Public market data only.
2. No AI.
3. No Signal Engine.
4. No buy/sell advice.
5. No long/short recommendation.
6. No future prediction.
7. No guaranteed outcome.
8. No Trade API.
9. No Withdraw API.
10. No API Key.
11. No real account balance.
12. No positions.
13. No order placement.
14. No cancel order.
15. No paper trading.
16. No auto trading.
17. Backtest output is historical research only.
18. Backtest result is not a trading command.
19. User always owns final decisions.

禁止出现以下英文文案：

```text
buy now
sell now
go long
go short
open position
close position immediately
guaranteed profit
sure win
strong buy
strong sell
best coin to buy
next trade
trade this setup now
profitable strategy guaranteed
```

禁止出现以下中文文案：

```text
建议买入
建议卖出
立即做多
立即做空
稳赚
必涨
必跌
强烈买入
强烈卖出
下一个交易机会
现在交易这个形态
保证盈利
稳赚策略
```

允许出现：

```text
historical rule
historical sample
research-only
backtest result
past performance
not trading advice
```

---

## README Update

更新 `README.md`，新增 V8 说明。

必须写明：

* V8 是 Backtesting Research Lab。
* V8 使用 OKX 公共 OHLCV。
* V8 用于历史规则验证。
* V8 不接真实 AI。
* V8 不生成实时信号。
* V8 不自动交易。
* V8 不接交易权限。
* V8 不保存 API Key。
* V8 不提供买卖建议。
* V8 回测结果只代表所选历史样本。
* V8 需要后端运行。
* 如果使用 vectorbt，说明依赖。
* 如果使用 lightweight fallback，也说明原因。

---

## Testing

完成后运行：

### App

```powershell
pnpm run typecheck
pnpm exec expo config --type public
```

### Backend

```powershell
python -m compileall api
```

启动后端：

```powershell
.venv-api\Scripts\python.exe -m uvicorn api.main:app --host 0.0.0.0 --port 8000
```

Smoke test：

```powershell
Invoke-WebRequest -UseBasicParsing "http://127.0.0.1:8000/health"
Invoke-WebRequest -UseBasicParsing "http://127.0.0.1:8000/backtest/rules"
Invoke-WebRequest -UseBasicParsing "http://127.0.0.1:8000/backtest/run?exchange=okx&symbol=BTC%2FUSDT&timeframe=1h&limit=300&ruleId=ema_trend_pullback_v1&initialCapital=10000&riskPerTradePercent=1"
```

Manual check：

1. App 能启动。
2. 我的页面能进入 Backtesting。
3. 后端未启动时页面不崩溃。
4. 后端启动后能加载 rules。
5. 能运行 backtest。
6. 能显示统计卡片。
7. 能显示 warnings。
8. 能显示 equity curve 简表。
9. 能显示 trade list。
10. 能保存 backtest result。
11. 能查看 saved results。
12. Data Quality 页面仍然正常。
13. Skill Engine 页面仍然正常。
14. Research Dashboard 页面仍然正常。
15. Quick Trade 仍然正常。
16. Trade Detail 仍然正常。
17. 没有交易建议文案。
18. 没有交易权限相关实现。

---

## Acceptance Criteria

V8 完成后必须满足：

1. `api/backtesting_engine.py` 存在。
2. `/backtest/rules` 存在。
3. `/backtest/run` 存在。
4. 至少 4 个 backtest rule templates。
5. 支持 OKX 公共 OHLCV。
6. 不接 API Key。
7. 不接 Trade API。
8. 不接 Withdraw API。
9. 不下单。
10. App 新增 `backtestApiService.ts`。
11. App 新增 Backtesting 页面。
12. My 页面有 Backtesting 入口。
13. 可以运行历史回测。
14. 可以展示 total return。
15. 可以展示 max drawdown。
16. 可以展示 trade count。
17. 可以展示 win rate。
18. 可以展示 profit factor。
19. 可以展示 expectancy。
20. 可以展示 warnings。
21. 可以保存 BacktestResults。
22. BacktestResults 使用 safe migration。
23. README 更新 V8。
24. `pnpm run typecheck` 通过。
25. `pnpm exec expo config --type public` 通过。
26. `python -m compileall api` 通过。
27. 后端 smoke test 通过或明确记录外部网络原因。
28. 不接真实 AI。
29. 不做 Signal Engine。
30. 不生成买卖建议。
31. 不接交易权限。
32. 不自动下单。

---

## Self Review Output

完成后请输出：

1. 修改文件列表
2. 新增文件列表
3. 后端 backtesting engine 说明
4. 是否使用 vectorbt；如果没有使用，说明 fallback 原因
5. 新增 API endpoint 说明
6. Rule templates 列表
7. Backtest metrics 说明
8. App Backtesting 页面说明
9. My 页面入口说明
10. 数据库 migration 说明
11. BacktestResults 保存逻辑说明
12. 后端不可用时 App fallback 行为
13. 安全边界确认
14. `pnpm run typecheck` 结果
15. `pnpm exec expo config --type public` 结果
16. `python -m compileall api` 结果
17. 后端 smoke test 结果
18. 已知问题
19. 下一步 V9 建议

---

## Future Compatibility

V8 必须为 V9/V10/V11 做准备：

* V9 Signal Engine 可以参考 V8 回测结果，但 V8 不实现 Signal。
* V10 Live Data Collection 可以提供更多历史样本和实时样本。
* V11 Paper Trading 可以复用回测规则，但 V8 不做 Paper Trading。
* 未来 AI Copilot 可以读取 BacktestResults，但 V8 不接 AI。

V8 只完成 Backtesting Research Lab。
