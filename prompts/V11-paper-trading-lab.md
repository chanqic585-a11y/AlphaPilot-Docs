# AlphaPilot V11 - Paper Trading Lab

## Goal

基于当前 `trade-discipline-journal` V10 版本，开发 AlphaPilot V11：Paper Trading Lab。

V11 的目标不是实盘交易，不是自动交易，不是半自动交易，不是交易所接入。

V11 的目标是：

```text
可以用模拟资金，在本地环境中做纸面交易实验，并记录完整的模拟订单、模拟持仓、模拟成交、模拟盈亏和复盘结果
```

V11 只做 Paper Trading Lab。

V11 不接真实交易权限。

V11 不保存交易所 API Key。

V11 不读取真实账户余额。

V11 不读取真实持仓。

V11 不下真实订单。

V11 不自动交易。

V11 不生成买卖建议。

---

## Current State

当前项目已经完成：

* V1.1：交易纪律、风险规则、Initial Setup、5x 杠杆限制
* V2：Quick Trade、Trade Detail、Mock Analysis、Snapshot、Timeline
* V3：Feature Database
* V4：FastAPI + CCXT public market service
* V4.1：CCXT unified public methods
* V5：Python Feature Engine
* V5.1：change1h / change4h timeframe 语义修正
* V6：Skill Engine
* V6.1：SkillResults runGroupId
* V7：Research Dashboard
* V8：Backtesting Research Lab
* V8.1：BacktestResults 接入 Research Dashboard
* V9：Signal Engine Research Boundary
* V10：Live Data Collection，公共市场数据采集，本地 MarketDataSnapshots

当前已有本地 SQLite 表：

* Trades
* TradeFeatures
* SkillResults
* BacktestResults
* SignalCandidates
* LiveCollectionRuns
* MarketDataSnapshots

当前已有核心模块：

* `src/feature-engine/`
* `src/skill-engine/`
* `src/backtesting/`
* `src/research/`
* `src/signal-engine/`
* `src/live-data/`
* `src/screens/ResearchDashboardScreen.tsx`
* `src/screens/SignalEngineScreen.tsx`
* `src/screens/LiveDataCollectionScreen.tsx`
* `src/db/database.ts`
* `src/db/repositories.ts`

V11 必须基于当前架构继续开发，不要推翻之前版本。

---

## Scope

V11 只做 Paper Trading Lab。

包含：

1. 新增 Paper Trading 类型。
2. 新增本地模拟账户。
3. 新增本地模拟订单。
4. 新增本地模拟成交。
5. 新增本地模拟持仓。
6. 新增本地模拟权益曲线。
7. 新增 Paper Trading 页面。
8. My 页面新增 Paper Trading 入口。
9. 可以用模拟资金创建 paper order。
10. 可以用本地最新 MarketDataSnapshots 或用户输入价格进行模拟成交。
11. 可以更新 paper position 的 mark price。
12. 可以关闭 paper position。
13. 可以计算模拟盈亏、手续费、滑点、权益、回撤。
14. 可以把 SignalCandidate 作为 research context 关联到 paper order，但不能自动下单。
15. Research Dashboard 新增 Paper Trading Research 区块。
16. README 更新 V11 说明。
17. 保持 V1.1～V10 功能不被破坏。

---

## Non Goals

V11 不做以下内容：

1. 不接真实 AI。
2. 不接 OpenAI / Claude / Gemini。
3. 不接 Trade API。
4. 不接 Withdraw API。
5. 不保存 API Key。
6. 不读取真实账户余额。
7. 不读取真实持仓。
8. 不下真实订单。
9. 不撤真实订单。
10. 不连接交易所私有接口。
11. 不做真实自动交易。
12. 不做 Semi-Auto Trading。
13. 不做 Auto Trading。
14. 不从 SignalCandidate 自动创建真实交易。
15. 不从 SignalCandidate 自动创建 paper order。
16. 不推送买卖通知。
17. 不告诉用户下一笔交易该怎么做。
18. 不预测未来价格。
19. 不承诺收益。
20. 不建议买入。
21. 不建议卖出。
22. 不建议做多。
23. 不建议做空。
24. 不使用真实资金。
25. 不把 Paper Trading 结果包装成实盘收益。

---

## Product Principle

V11 必须遵循 AlphaPilot 核心原则：

1. Data before AI.
2. Research before automation.
3. Risk above signal.
4. AI never owns money.
5. Everything is feature.
6. Explainability before black-box output.
7. Evidence before opinion.

V11 新增原则：

```text
Paper first.
Simulation only.
Manual action only.
No credentials.
No execution.
No exchange account.
No hidden automation.
```

---

## Paper Trading Concept

V11 中的 Paper Trading 是：

```text
用模拟资金和本地公共市场数据进行研究型交易实验。
```

它不是：

```text
真实交易
自动交易
交易建议
实盘信号执行
交易所账户同步
```

Paper Trading 只能回答：

```text
如果我在模拟环境中按某个研究想法创建纸面订单，结果如何？
模拟订单是否符合风险规则？
模拟持仓的纸面盈亏如何？
某个 Signal Candidate 被用于模拟实验后表现如何？
```

不能回答：

```text
现在是否应该买入？
现在是否应该卖出？
应该用真实资金开仓吗？
应该用几倍杠杆实盘交易？
```

---

## Recommended Architecture

新增目录：

```text
src/
  paper-trading/
    paperTypes.ts
    paperAccount.ts
    paperExecution.ts
    paperRisk.ts
    paperMapper.ts
    paperRepository.ts

  screens/
    PaperTradingScreen.tsx
```

需要更新：

```text
src/db/database.ts
src/db/repositories.ts
src/screens/MyScreen.tsx
src/research/researchTypes.ts
src/research/researchRepository.ts
src/research/researchMetrics.ts
src/screens/ResearchDashboardScreen.tsx
README.md
```

说明：

* `paperTypes.ts` 定义模拟账户、模拟订单、模拟持仓、模拟成交、权益曲线类型。
* `paperAccount.ts` 负责初始化和计算模拟账户。
* `paperExecution.ts` 负责模拟成交逻辑。
* `paperRisk.ts` 负责模拟订单风险检查。
* `paperMapper.ts` 负责 JSON 映射与 SQLite input。
* `paperRepository.ts` 可以封装 repository，也可以直接复用 `db/repositories.ts`。
* `PaperTradingScreen.tsx` 展示 Paper Trading Lab。

---

## Database Requirements

V11 新增本地 SQLite 表。

### 1. PaperAccounts

```sql
CREATE TABLE IF NOT EXISTS PaperAccounts (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  accountId TEXT NOT NULL,
  name TEXT NOT NULL,
  initialEquity REAL NOT NULL,
  cashBalance REAL NOT NULL,
  equity REAL NOT NULL,
  realizedPnl REAL NOT NULL,
  unrealizedPnl REAL NOT NULL,
  maxDrawdownPercent REAL NOT NULL,
  maxLeverage INTEGER NOT NULL,
  status TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL,
  updatedAt TEXT NOT NULL
);
```

### 2. PaperOrders

```sql
CREATE TABLE IF NOT EXISTS PaperOrders (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  orderId TEXT NOT NULL,
  accountId TEXT NOT NULL,
  signalCandidateId INTEGER,
  symbol TEXT NOT NULL,
  marketType TEXT NOT NULL,
  direction TEXT NOT NULL,
  orderType TEXT NOT NULL,
  status TEXT NOT NULL,
  requestedPrice REAL,
  filledPrice REAL,
  quantity REAL NOT NULL,
  leverage INTEGER NOT NULL,
  feeRate REAL NOT NULL,
  slippageRate REAL NOT NULL,
  stopLossPrice REAL,
  takeProfitPrice REAL,
  notionalValue REAL,
  marginUsed REAL,
  feePaid REAL,
  riskAmount REAL,
  riskPercent REAL,
  reasonJson TEXT NOT NULL,
  riskCheckJson TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL,
  filledAt TEXT,
  cancelledAt TEXT
);
```

### 3. PaperPositions

```sql
CREATE TABLE IF NOT EXISTS PaperPositions (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  positionId TEXT NOT NULL,
  accountId TEXT NOT NULL,
  openingOrderId TEXT NOT NULL,
  symbol TEXT NOT NULL,
  marketType TEXT NOT NULL,
  direction TEXT NOT NULL,
  status TEXT NOT NULL,
  quantity REAL NOT NULL,
  entryPrice REAL NOT NULL,
  markPrice REAL NOT NULL,
  leverage INTEGER NOT NULL,
  marginUsed REAL NOT NULL,
  feePaid REAL NOT NULL,
  realizedPnl REAL NOT NULL,
  unrealizedPnl REAL NOT NULL,
  returnPercent REAL NOT NULL,
  stopLossPrice REAL,
  takeProfitPrice REAL,
  source TEXT NOT NULL,
  openedAt TEXT NOT NULL,
  updatedAt TEXT NOT NULL,
  closedAt TEXT
);
```

### 4. PaperFills

```sql
CREATE TABLE IF NOT EXISTS PaperFills (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  fillId TEXT NOT NULL,
  accountId TEXT NOT NULL,
  orderId TEXT NOT NULL,
  positionId TEXT,
  symbol TEXT NOT NULL,
  direction TEXT NOT NULL,
  fillType TEXT NOT NULL,
  price REAL NOT NULL,
  quantity REAL NOT NULL,
  notionalValue REAL NOT NULL,
  feePaid REAL NOT NULL,
  slippagePaid REAL NOT NULL,
  realizedPnl REAL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

### 5. PaperEquitySnapshots

```sql
CREATE TABLE IF NOT EXISTS PaperEquitySnapshots (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  accountId TEXT NOT NULL,
  equity REAL NOT NULL,
  cashBalance REAL NOT NULL,
  realizedPnl REAL NOT NULL,
  unrealizedPnl REAL NOT NULL,
  drawdownPercent REAL NOT NULL,
  openPositionCount INTEGER NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

索引：

```sql
CREATE INDEX IF NOT EXISTS idx_paper_accounts_account ON PaperAccounts(accountId);
CREATE INDEX IF NOT EXISTS idx_paper_orders_account ON PaperOrders(accountId);
CREATE INDEX IF NOT EXISTS idx_paper_orders_status ON PaperOrders(status);
CREATE INDEX IF NOT EXISTS idx_paper_orders_symbol ON PaperOrders(symbol);
CREATE INDEX IF NOT EXISTS idx_paper_orders_created ON PaperOrders(createdAt);
CREATE INDEX IF NOT EXISTS idx_paper_positions_account ON PaperPositions(accountId);
CREATE INDEX IF NOT EXISTS idx_paper_positions_status ON PaperPositions(status);
CREATE INDEX IF NOT EXISTS idx_paper_positions_symbol ON PaperPositions(symbol);
CREATE INDEX IF NOT EXISTS idx_paper_positions_updated ON PaperPositions(updatedAt);
CREATE INDEX IF NOT EXISTS idx_paper_fills_order ON PaperFills(orderId);
CREATE INDEX IF NOT EXISTS idx_paper_fills_position ON PaperFills(positionId);
CREATE INDEX IF NOT EXISTS idx_paper_fills_created ON PaperFills(createdAt);
CREATE INDEX IF NOT EXISTS idx_paper_equity_account ON PaperEquitySnapshots(accountId);
CREATE INDEX IF NOT EXISTS idx_paper_equity_created ON PaperEquitySnapshots(createdAt);
```

Migration 要求：

1. 使用 `CREATE TABLE IF NOT EXISTS`。
2. 不删除旧表。
3. 不重建旧表。
4. 不删除旧数据。
5. 不修改 Trades。
6. 不修改 TradeFeatures。
7. 不修改 SkillResults。
8. 不修改 BacktestResults。
9. 不修改 SignalCandidates。
10. 不修改 MarketDataSnapshots。
11. 不保存 API Key。
12. 不保存真实账户数据。
13. 不保存交易所私有数据。

如果更新 user_version，使用：

```text
PRAGMA user_version = 9;
```

---

## Paper Trading Types

在 `src/paper-trading/paperTypes.ts` 中定义：

```ts
export type PaperOrderDirection = 'long' | 'short';
export type PaperOrderType = 'market' | 'limit';
export type PaperOrderStatus = 'draft' | 'filled' | 'cancelled' | 'rejected';
export type PaperPositionStatus = 'open' | 'closed';
export type PaperAccountStatus = 'active' | 'archived';
```

也要补充：

* `PaperAccount`
* `PaperOrder`
* `PaperPosition`
* `PaperFill`
* `PaperEquitySnapshot`
* `PaperTradingSummary`
* `PaperOrderRiskCheck`

---

## Paper Account Rules

V11 第一版默认创建一个本地模拟账户：

```text
accountId: default_paper_account
name: AlphaPilot Paper Account
initialEquity: 10000
maxLeverage: 5
source: paper_trading_lab_v11
```

要求：

1. 如果没有 paper account，进入页面时可以创建默认账户。
2. 模拟账户只存在本地 SQLite。
3. 不连接真实交易所账户。
4. 不同步真实余额。
5. 不允许 maxLeverage > 5。
6. 所有资金字段都是模拟数据。

---

## Paper Order Rules

Paper order 是模拟订单，不是真实订单。

创建 paper order 时要求：

1. symbol 必填。
2. direction 只能是 long 或 short。
3. orderType 可以是 market 或 limit。
4. quantity 必须 > 0。
5. leverage 必须 1～5。
6. requestedPrice 必须 > 0，或者使用最新 MarketDataSnapshot.price。
7. feeRate >= 0。
8. slippageRate >= 0。
9. 如果 stopLossPrice 存在，必须能计算 riskAmount。
10. riskPercent 建议不能超过账户权益的 2%。
11. 如果 riskPercent > 2%，订单可以标记为 rejected。
12. rejected 的订单也可以保存，用于研究，但不能生成 position。
13. filled 的订单才生成 position 和 fill。

---

## Paper Execution Rules

### Market order

如果 orderType = market：

1. 优先使用最新 `MarketDataSnapshots.price`。
2. 如果没有 snapshot，允许用户输入 requestedPrice。
3. long 模拟成交价：

   * `filledPrice = basePrice * (1 + slippageRate)`

4. short 模拟成交价：

   * `filledPrice = basePrice * (1 - slippageRate)`

5. fee:

   * `feePaid = notionalValue * feeRate`

6. margin:

   * `marginUsed = notionalValue / leverage`

### Limit order

第一版可以简化：

1. 如果 requestedPrice 存在，直接按 requestedPrice 模拟成交。
2. 不做订单簿模拟。
3. 不做真实撮合。
4. README 必须写明 V11 limit order 是 simplified simulation。

---

## Paper Position Rules

创建 position 后：

1. status = open。
2. markPrice 初始等于 filledPrice。
3. unrealizedPnl 初始考虑 fee 后计算。
4. 后续可以点击 Update Marks。
5. Update Marks 使用最新本地 MarketDataSnapshots。
6. 如果没有最新 snapshot，可以允许用户手动输入 markPrice。
7. Close Position 只做本地模拟平仓。
8. Close Position 生成 PaperFill。
9. Close Position 更新 realizedPnl。
10. Close Position 更新 account equity。
11. Close Position 不调用交易所。

---

## PnL Calculation

### Long

```text
unrealizedPnl = (markPrice - entryPrice) * quantity - feePaid
```

### Short

```text
unrealizedPnl = (entryPrice - markPrice) * quantity - feePaid
```

### Return Percent

```text
returnPercent = unrealizedPnl / marginUsed * 100
```

### Account Equity

```text
equity = cashBalance + unrealizedPnl
```

### Drawdown

```text
drawdownPercent = (peakEquity - currentEquity) / peakEquity * 100
```

要求：

1. 避免除以 0。
2. 所有计算必须注明是模拟。
3. 不使用真实账户余额。
4. 不和交易所账户同步。

---

## Signal Candidate Integration

V11 可以读取 SignalCandidates 作为研究上下文。

要求：

1. Paper Trading 页面可以显示最近 Signal Candidates。
2. 用户可以选择一个 SignalCandidate 作为 paper order 的 research context。
3. 选择 SignalCandidate 不等于创建订单。
4. 不允许自动从 SignalCandidate 创建 paper order。
5. 创建 paper order 必须由用户手动点击。
6. order 的 `signalCandidateId` 可以保存候选事件 id。
7. UI 必须写明：

```text
Signal Candidate is research context only. Paper orders are manual simulations.
```

---

## MarketDataSnapshots Integration

V11 必须使用 V10 的本地公共数据样本。

要求：

1. 创建 paper order 时可以读取 `getLatestMarketSnapshotBySymbol(symbol)`。
2. Update Marks 时可以读取最新 MarketDataSnapshots。
3. 如果 snapshot 不存在，显示提示：

   * “No local market snapshot available. Collect live data or enter a manual simulation price.”

4. 不请求后端。
5. 不调用交易所。
6. 不触发 live collection。
7. 只读取本地 SQLite。

---

## Repository Requirements

在 `src/db/repositories.ts` 中新增：

```ts
createPaperAccount(input)
getDefaultPaperAccount()
updatePaperAccount(input)
createPaperOrder(input)
updatePaperOrder(input)
listPaperOrders(limit?)
listPaperOrdersByAccount(accountId, limit?)
createPaperPosition(input)
updatePaperPosition(input)
listPaperPositions(limit?)
listOpenPaperPositions(accountId)
listClosedPaperPositions(accountId, limit?)
createPaperFill(input)
listPaperFills(limit?)
createPaperEquitySnapshot(input)
listPaperEquitySnapshots(accountId, limit?)
getPaperTradingSummary()
```

`getPaperTradingSummary()` 至少返回：

```ts
{
  accountId: string | null;
  equity: number;
  cashBalance: number;
  realizedPnl: number;
  unrealizedPnl: number;
  openPositionCount: number;
  closedPositionCount: number;
  totalOrders: number;
  rejectedOrders: number;
  winCount: number;
  lossCount: number;
  maxDrawdownPercent: number;
  latestUpdatedAt: string | null;
}
```

---

## Paper Engine Requirements

新增：

```text
src/paper-trading/paperExecution.ts
```

至少提供：

```ts
ensureDefaultPaperAccount()
createAndFillPaperOrder(input)
updateOpenPaperPositionMarks()
closePaperPosition(input)
calculatePaperTradingSummary()
```

要求：

1. 所有函数只读写本地 SQLite。
2. 不请求后端。
3. 不调用交易所。
4. 不读取真实账户。
5. 不下真实订单。
6. 每次模拟成交都写入 PaperFill。
7. 每次账户权益变化都写入 PaperEquitySnapshots。
8. 风险检查失败时可以保存 rejected order，但不生成 position。

---

## Paper Risk Checks

新增：

```text
src/paper-trading/paperRisk.ts
```

至少检查：

1. leverage <= 5。
2. quantity > 0。
3. price > 0。
4. notionalValue > 0。
5. marginUsed <= account.equity。
6. riskPercent <= 2%，如果能计算。
7. stopLoss 合理：

   * long 的 stopLossPrice < entryPrice
   * short 的 stopLossPrice > entryPrice

8. paper account active。
9. 使用模拟资金。
10. 不接真实账户。

返回：

```ts
{
  passed: boolean;
  severity: 'ok' | 'warning' | 'rejected';
  notes: string[];
  riskAmount: number | null;
  riskPercent: number | null;
}
```

---

## Paper Trading Screen

新增页面：

```text
src/screens/PaperTradingScreen.tsx
```

入口：

```text
我的 -> Paper Trading
```

页面内容：

### Header

Title:

```text
Paper Trading Lab
```

Subtitle:

```text
Manual simulation using local research data and simulated capital.
```

### Safety Note

必须显示：

```text
Paper Trading uses simulated capital only. It does not use API keys, account data, orders, or trading permissions.
```

### Account Summary

显示：

* initialEquity
* equity
* cashBalance
* realizedPnl
* unrealizedPnl
* maxDrawdownPercent
* maxLeverage

### Create Paper Order

输入：

* symbol
* direction
* orderType
* quantity
* leverage
* requestedPrice
* stopLossPrice
* takeProfitPrice
* feeRate
* slippageRate
* optional signalCandidateId

按钮：

* Load Latest Local Price
* Create Simulated Order
* Refresh Paper State

### Open Positions

显示：

* symbol
* direction
* quantity
* entryPrice
* markPrice
* unrealizedPnl
* returnPercent
* leverage
* openedAt

操作：

* Update Marks
* Close Position

### Orders

显示：

* symbol
* direction
* status
* requestedPrice
* filledPrice
* riskPercent
* createdAt

### Fills

显示：

* symbol
* fillType
* price
* quantity
* feePaid
* realizedPnl
* createdAt

### Equity Curve

第一版可以用简单列表，不需要图表库。

显示最近 20 条 PaperEquitySnapshots。

---

## Research Dashboard Integration

Research Dashboard 新增 section：

```text
Paper Trading Research
```

显示：

* paper equity
* realizedPnl
* unrealizedPnl
* openPositionCount
* closedPositionCount
* totalOrders
* rejectedOrders
* winCount
* lossCount
* maxDrawdownPercent
* recent paper fills
* recent paper equity snapshots

必须显示说明：

```text
Paper Trading results are simulated research records only. They are not real trading performance.
```

要求：

1. 只读本地 SQLite。
2. 不请求后端。
3. 不调用交易所。
4. 空数据不崩溃。

---

## My Screen Integration

更新：

```text
src/screens/MyScreen.tsx
```

新增入口：

```text
Paper Trading
```

描述：

```text
使用模拟资金进行本地纸面交易实验，不接真实交易权限。
```

---

## README Update

更新 `README.md`，新增 V11 说明。

必须写明：

* V11 是 Paper Trading Lab。
* V11 使用模拟资金。
* V11 使用本地 SQLite。
* V11 可以读取 MarketDataSnapshots 作为本地模拟价格来源。
* V11 可以关联 SignalCandidate 作为研究上下文。
* SignalCandidate 不会自动创建订单。
* Paper order 是模拟订单，不是真实订单。
* Paper position 是模拟持仓，不是真实持仓。
* Paper fill 是模拟成交，不是真实成交。
* V11 不接真实 AI。
* V11 不接 Trade API。
* V11 不接 Withdraw API。
* V11 不保存 API Key。
* V11 不读取账户余额。
* V11 不读取持仓。
* V11 不下真实订单。
* V11 不自动交易。
* V11 不提供买卖建议。

---

## Safety Rules

V11 必须遵守：

1. Simulated capital only.
2. Local SQLite only.
3. No real AI.
4. No Trade API.
5. No Withdraw API.
6. No API Key.
7. No real account balance.
8. No real positions.
9. No real order placement.
10. No real order cancellation.
11. No semi-auto trading.
12. No auto trading.
13. No buy/sell advice.
14. No long/short recommendation.
15. No future prediction.
16. No guaranteed outcome.
17. Signal Candidate is research context only.
18. Paper order must be manual.
19. Paper order must not create real trade.
20. User owns all decisions.

禁止出现以下英文文案：

```text
buy now
sell now
go long
go short
open real position
close real position
guaranteed profit
sure win
strong buy
strong sell
best coin to buy
next trade
trade this setup now
profitable strategy guaranteed
signal says buy
signal says sell
```

禁止出现以下中文文案：

```text
建议买入
建议卖出
立即做多
立即做空
开真实仓位
平真实仓位
稳赚
必涨
必跌
强烈买入
强烈卖出
下一个交易机会
现在交易这个形态
保证盈利
稳赚策略
信号建议买入
信号建议卖出
```

允许出现：

```text
paper order
simulated order
paper position
simulated position
paper fill
simulated fill
simulated capital
research-only
not trading advice
not a real order
```

中文允许：

```text
纸面订单
模拟订单
纸面持仓
模拟持仓
纸面成交
模拟成交
模拟资金
仅用于研究
不是交易建议
不是真实订单
```

---

## Testing

完成后运行：

```powershell
pnpm run typecheck
pnpm exec expo config --type public
```

如果没有修改后端 `api/`，不需要运行：

```powershell
python -m compileall api
```

手动检查：

1. App 能启动。
2. 我的页面能进入 Paper Trading。
3. 没有 paper account 时能创建默认模拟账户。
4. 没有 MarketDataSnapshots 时页面不崩溃。
5. 可以手动输入 price 创建模拟订单。
6. 有 MarketDataSnapshots 时可以 Load Latest Local Price。
7. leverage > 5 会被拒绝。
8. quantity <= 0 会被拒绝。
9. riskPercent > 2% 时订单被 rejected 或 warning。
10. filled paper order 会生成 paper position。
11. paper order 会生成 paper fill。
12. Update Marks 能更新 unrealizedPnl。
13. Close Position 能生成 close fill。
14. Close Position 能更新 realizedPnl。
15. Equity Snapshot 能保存。
16. Research Dashboard 显示 Paper Trading Research section。
17. Signal Engine 页面仍然正常。
18. Live Data Collection 页面仍然正常。
19. Backtesting 页面仍然正常。
20. Data Quality 页面仍然正常。
21. Quick Trade 仍然正常。
22. Trade Detail 仍然正常。
23. 没有真实交易权限调用。
24. 没有 API Key。
25. 没有买卖建议文案。
26. 没有真实下单相关实现。

---

## Acceptance Criteria

V11 完成后必须满足：

1. `src/paper-trading/` 目录存在。
2. `paperTypes.ts` 存在。
3. `paperExecution.ts` 存在。
4. `paperRisk.ts` 存在。
5. `paperMapper.ts` 存在。
6. `PaperTradingScreen.tsx` 存在。
7. My 页面有 Paper Trading 入口。
8. SQLite 新增 PaperAccounts。
9. SQLite 新增 PaperOrders。
10. SQLite 新增 PaperPositions。
11. SQLite 新增 PaperFills。
12. SQLite 新增 PaperEquitySnapshots。
13. migration 使用 `CREATE TABLE IF NOT EXISTS`。
14. `PRAGMA user_version = 9`。
15. 可以创建默认模拟账户。
16. 可以创建模拟订单。
17. 可以拒绝不合格模拟订单。
18. 可以生成模拟持仓。
19. 可以生成模拟成交。
20. 可以更新模拟持仓 mark price。
21. 可以关闭模拟持仓。
22. 可以计算模拟 realizedPnl。
23. 可以计算模拟 unrealizedPnl。
24. 可以保存模拟权益快照。
25. 可以关联 SignalCandidate 作为 research context。
26. SignalCandidate 不会自动创建订单。
27. Research Dashboard 显示 Paper Trading Research section。
28. `pnpm run typecheck` 通过。
29. `pnpm exec expo config --type public` 通过。
30. 不接真实 AI。
31. 不接 Trade API。
32. 不接 Withdraw API。
33. 不保存 API Key。
34. 不读取真实账户。
35. 不下真实订单。
36. 不自动交易。
37. 不生成买卖建议。

---

## Self Review Output

完成后请输出：

1. 修改文件列表
2. 新增文件列表
3. 新增数据库表说明
4. Paper Trading 类型说明
5. Paper Account 说明
6. Paper Order 说明
7. Paper Position 说明
8. Paper Fill 说明
9. Paper Equity Snapshot 说明
10. SignalCandidate 关联说明
11. MarketDataSnapshots 价格来源说明
12. Paper Trading 页面说明
13. Research Dashboard 集成说明
14. My 页面入口说明
15. 安全边界确认
16. 是否修改后端
17. 是否新增 migration
18. `pnpm run typecheck` 结果
19. `pnpm exec expo config --type public` 结果
20. 安全文案扫描结果
21. 已知问题
22. 下一步 V12 建议

---

## Future Compatibility

V11 为 V12 做准备：

* V12 Semi-Auto Trading 以后必须仍然要求用户手动确认。
* V12 如果接真实交易权限，必须有单独设计、权限隔离、风控校验和人工确认。
* V11 不做 Semi-Auto Trading。
* V11 不做 Auto Trading。
* V11 不接真实交易权限。
* V11 不保存真实 API Key。
* V11 不下真实订单。

V11 只完成 Paper Trading Lab。
