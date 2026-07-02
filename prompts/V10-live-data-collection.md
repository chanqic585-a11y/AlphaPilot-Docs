# AlphaPilot V10 - Live Data Collection

## Goal

基于当前 `trade-discipline-journal` V9 版本，开发 AlphaPilot V10：Live Data Collection。

V10 的目标不是 AI，不是 Signal Engine 升级，不是交易执行，不是自动交易。

V10 的目标是：

```text
可以持续或手动采集公共市场数据，并保存为本地研究样本，为 Feature Engine、Research Dashboard、Signal Candidate 和未来 Paper Trading 提供更丰富的数据输入。
```

V10 只做公共数据采集。

V10 不接交易权限。

V10 不保存 API Key。

V10 不读取账户余额。

V10 不读取持仓。

V10 不下单。

V10 不生成买卖建议。

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

当前已有：

* `api/main.py`
* `api/market_service.py`
* `api/feature_engine.py`
* `api/backtesting_engine.py`
* `src/services/marketDataService.ts`
* `src/services/featureApiService.ts`
* `src/services/backtestApiService.ts`
* `src/feature-engine/`
* `src/skill-engine/`
* `src/backtesting/`
* `src/research/`
* `src/signal-engine/`
* `src/screens/ResearchDashboardScreen.tsx`
* `src/screens/SignalEngineScreen.tsx`
* `src/db/database.ts`
* `src/db/repositories.ts`

当前本地 SQLite 已有：

* `Trades`
* `TradeFeatures`
* `SkillResults`
* `BacktestResults`
* `SignalCandidates`

V10 必须基于当前架构继续开发，不要推翻之前版本。

---

## Scope

V10 只做 Live Data Collection。

包含：

1. 后端新增公共市场数据采集模块。
2. 后端新增 live data endpoints。
3. App 新增 Live Data Collection 页面。
4. My 页面新增 Live Data Collection 入口。
5. 本地 SQLite 新增 MarketDataSnapshots 表。
6. 本地 SQLite 新增 LiveCollectionRuns 表。
7. App 可以手动触发一次公共数据采集。
8. App 可以采集多个 symbol。
9. App 可以显示最近采集结果。
10. App 可以显示数据质量。
11. Research Dashboard 可以显示 Live Data Collection summary。
12. V5 Feature Engine 可以优先使用最新 MarketDataSnapshots 作为补充数据源。
13. README 更新 V10 说明。
14. 保持 V1.1～V9 功能不被破坏。

---

## Non Goals

V10 不做以下内容：

1. 不接真实 AI。
2. 不升级 Signal Engine 为交易信号。
3. 不做 Paper Trading。
4. 不做 Semi-Auto Trading。
5. 不做 Auto Trading。
6. 不接 Trade API。
7. 不接 Withdraw API。
8. 不保存 API Key。
9. 不读取真实账户余额。
10. 不读取真实持仓。
11. 不下单。
12. 不撤单。
13. 不自动创建真实交易。
14. 不推送买卖通知。
15. 不预测未来价格。
16. 不承诺收益。
17. 不建议买入。
18. 不建议卖出。
19. 不建议做多。
20. 不建议做空。
21. 不做后台无限循环采集。
22. 不做高频采集。
23. 不绕过交易所限流。

---

## Product Principle

V10 必须遵循 AlphaPilot 核心原则：

1. Data before AI.
2. Research before automation.
3. Risk above signal.
4. AI never owns money.
5. Everything is feature.
6. Explainability before black-box output.
7. Evidence before opinion.

V10 新增原则：

```text
Collection before prediction.
Public data only.
No credentials.
No execution.
No hidden automation.
Respect rate limits.
```

---

## Live Data Collection Concept

V10 中的 Live Data Collection 是：

```text
采集公共市场数据，保存为本地研究样本。
```

它不是：

```text
实时交易系统
自动交易系统
喊单系统
信号推送系统
订单执行系统
```

Live Data Collection 只能回答：

```text
最近采集了哪些公共行情？
数据质量怎么样？
哪些字段缺失？
哪些 symbol 有可用样本？
Feature Engine 能否用这些样本补充字段？
```

不能回答：

```text
现在该买哪个？
现在该卖哪个？
该不该开仓？
该不该加仓？
```

---

## Recommended Architecture

新增后端文件：

```text
api/
  live_data_collector.py
  live_data_models.py
```

新增 App 文件：

```text
src/
  live-data/
    liveDataTypes.ts
    liveDataMapper.ts

  services/
    liveDataApiService.ts

  screens/
    LiveDataCollectionScreen.tsx
```

需要更新：

```text
api/main.py
api/market_service.py
src/db/database.ts
src/db/repositories.ts
src/screens/MyScreen.tsx
src/research/researchTypes.ts
src/research/researchRepository.ts
src/research/researchMetrics.ts
src/screens/ResearchDashboardScreen.tsx
src/feature-engine/featureEngine.ts
README.md
```

---

## Backend Requirements

### 1. Add Live Data Collector

新增：

```text
api/live_data_collector.py
```

职责：

1. 使用已有 `MarketService`。
2. 只调用公共行情。
3. 支持 OKX。
4. 支持 ticker。
5. 支持 OHLCV。
6. 尝试支持 public funding rate。
7. 尝试支持 public open interest。
8. 如果 funding / open interest 不稳定或不可用，返回 null，不伪造。
9. 计算 dataQualityScore。
10. 返回 missingFields。
11. 不使用 API Key。
12. 不调用 private endpoints。
13. 不下单。

---

### 2. Public Data Fields

每个 live snapshot 建议返回：

```json
{
  "exchange": "okx",
  "symbol": "BTC/USDT",
  "timeframe": "1h",
  "price": 100000,
  "volume24h": 123456,
  "change24h": 2.5,
  "high24h": 101000,
  "low24h": 98000,
  "fundingRate": 0.0001,
  "openInterest": 12345,
  "candleCount": 200,
  "latestCandleTime": 1710000000000,
  "dataQualityScore": 85,
  "missingFields": [],
  "source": "live_data_collector_v10",
  "collectedAt": "ISO_DATE"
}
```

字段说明：

* `fundingRate` 可以为 null。
* `openInterest` 可以为 null。
* 不允许伪造 funding / openInterest。
* 缺失字段必须写入 `missingFields`。
* `dataQualityScore` 应根据字段完整度计算。

---

### 3. Backend Endpoints

在 `api/main.py` 新增：

```text
GET /live/sources
GET /live/snapshot
GET /live/collect
```

### GET /live/sources

返回支持的数据源：

```json
{
  "sources": [
    {
      "exchange": "okx",
      "publicOnly": true,
      "supportsTicker": true,
      "supportsOhlcv": true,
      "supportsFundingRate": true,
      "supportsOpenInterest": true,
      "source": "live_data_collector_v10"
    }
  ]
}
```

### GET /live/snapshot

示例：

```text
/live/snapshot?exchange=okx&symbol=BTC/USDT&timeframe=1h&limit=200
```

返回单个 symbol 的 snapshot。

### GET /live/collect

示例：

```text
/live/collect?exchange=okx&symbols=BTC/USDT,ETH/USDT,SOL/USDT&timeframe=1h&limit=200
```

返回多个 symbol 的 snapshots。

要求：

1. 只支持 GET。
2. 只支持 public data。
3. `symbols` 最多 20 个。
4. `limit` 限制在 50～500。
5. 单个 symbol 失败时，不影响其他 symbol。
6. 每个失败项返回 error，但 App 不崩溃。
7. 不保存服务器端数据。
8. 不使用 API Key。
9. 不调用 private endpoints。

---

## Backend Data Quality

`dataQualityScore` 建议计算：

```text
base = 100
缺少 price: -30
缺少 volume24h: -15
缺少 OHLCV candles: -25
candleCount < 50: -20
缺少 fundingRate: -5
缺少 openInterest: -5
```

最低 0，最高 100。

注意：

* funding / openInterest 缺失是轻微扣分。
* price / OHLCV 缺失是严重扣分。
* 不要用假数据填补。

---

## App Types

新增：

```text
src/live-data/liveDataTypes.ts
```

至少定义：

```ts
export interface LiveDataSource {
  exchange: string;
  publicOnly: boolean;
  supportsTicker: boolean;
  supportsOhlcv: boolean;
  supportsFundingRate: boolean;
  supportsOpenInterest: boolean;
  source: 'live_data_collector_v10';
}

export interface LiveMarketSnapshot {
  exchange: string;
  symbol: string;
  timeframe: string;
  price: number | null;
  volume24h: number | null;
  change24h: number | null;
  high24h: number | null;
  low24h: number | null;
  fundingRate: number | null;
  openInterest: number | null;
  candleCount: number;
  latestCandleTime: number | null;
  dataQualityScore: number;
  missingFields: string[];
  source: 'live_data_collector_v10';
  collectedAt: string;
}

export interface LiveCollectionResult {
  exchange: string;
  symbols: string[];
  timeframe: string;
  total: number;
  successCount: number;
  failureCount: number;
  snapshots: LiveMarketSnapshot[];
  errors: Array<{
    symbol: string;
    error: string;
  }>;
  source: 'live_data_collector_v10';
  collectedAt: string;
}
```

---

## App Service

新增：

```text
src/services/liveDataApiService.ts
```

功能：

```ts
fetchLiveSources()
fetchLiveSnapshot(request)
collectLiveSnapshots(request)
```

要求：

1. 复用 `requestMarketApi`。
2. 后端不可用时返回 null。
3. 不上传 API Key。
4. 不上传账户信息。
5. 不发送交易指令。
6. 不调用交易所 private API。
7. App 不能因为后端不可用崩溃。

---

## Database Requirements

V10 新增两个本地表。

### 1. LiveCollectionRuns

```sql
CREATE TABLE IF NOT EXISTS LiveCollectionRuns (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  runGroupId TEXT NOT NULL,
  exchange TEXT NOT NULL,
  symbolsJson TEXT NOT NULL,
  timeframe TEXT NOT NULL,
  totalSymbols INTEGER NOT NULL,
  successCount INTEGER NOT NULL,
  failureCount INTEGER NOT NULL,
  status TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

索引：

```sql
CREATE INDEX IF NOT EXISTS idx_live_runs_group ON LiveCollectionRuns(runGroupId);
CREATE INDEX IF NOT EXISTS idx_live_runs_created ON LiveCollectionRuns(createdAt);
CREATE INDEX IF NOT EXISTS idx_live_runs_status ON LiveCollectionRuns(status);
```

### 2. MarketDataSnapshots

```sql
CREATE TABLE IF NOT EXISTS MarketDataSnapshots (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  runGroupId TEXT NOT NULL,
  exchange TEXT NOT NULL,
  symbol TEXT NOT NULL,
  timeframe TEXT NOT NULL,
  price REAL,
  volume24h REAL,
  change24h REAL,
  high24h REAL,
  low24h REAL,
  fundingRate REAL,
  openInterest REAL,
  candleCount INTEGER NOT NULL,
  latestCandleTime INTEGER,
  dataQualityScore REAL NOT NULL,
  missingFieldsJson TEXT NOT NULL,
  payloadJson TEXT NOT NULL,
  source TEXT NOT NULL,
  collectedAt TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

索引：

```sql
CREATE INDEX IF NOT EXISTS idx_market_snapshots_run ON MarketDataSnapshots(runGroupId);
CREATE INDEX IF NOT EXISTS idx_market_snapshots_symbol ON MarketDataSnapshots(symbol);
CREATE INDEX IF NOT EXISTS idx_market_snapshots_collected ON MarketDataSnapshots(collectedAt);
CREATE INDEX IF NOT EXISTS idx_market_snapshots_quality ON MarketDataSnapshots(dataQualityScore);
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
10. 不保存 API Key。
11. 不保存账户数据。

如果更新 user_version，使用：

```text
PRAGMA user_version = 8;
```

---

## Repository Requirements

在 `src/db/repositories.ts` 中新增：

```ts
createLiveCollectionRun(input)
createMarketDataSnapshot(input)
createMarketDataSnapshots(inputs)
listLiveCollectionRuns(limit?)
listMarketDataSnapshots(limit?)
listMarketDataSnapshotsBySymbol(symbol, limit?)
listLatestMarketDataSnapshots(limit?)
getLatestMarketSnapshotBySymbol(symbol)
getLiveDataSummary()
```

`getLiveDataSummary()` 至少返回：

```ts
{
  totalRuns: number;
  totalSnapshots: number;
  latestRunAt: string | null;
  latestRunGroupId: string | null;
  latestSuccessCount: number;
  latestFailureCount: number;
  averageDataQualityScore: number;
  symbolsTracked: number;
}
```

---

## Live Data Mapper

新增：

```text
src/live-data/liveDataMapper.ts
```

负责：

1. 把 `LiveCollectionResult` 映射成 `LiveCollectionRuns` input。
2. 把 `LiveMarketSnapshot` 映射成 `MarketDataSnapshots` input。
3. 序列化 `missingFieldsJson`。
4. 序列化 `payloadJson`。
5. 生成统一 `runGroupId`。
6. 确保同一次 collect 的 run 和 snapshots 使用同一个 runGroupId。

---

## Live Data Collection Screen

新增页面：

```text
src/screens/LiveDataCollectionScreen.tsx
```

入口：

```text
我的 -> Live Data Collection
```

页面内容：

### Header

Title:

```text
Live Data Collection
```

Subtitle:

```text
Collect public market data for research features.
```

### Safety Note

必须显示：

```text
Live data collection uses public market data only. It does not use API keys, account data, orders, or trading permissions.
```

### Controls

至少支持：

* symbols
* timeframe
* limit
* exchange

默认：

```text
exchange: okx
symbols: BTC,ETH,SOL
timeframe: 1h
limit: 200
```

### Actions

* Load Sources
* Collect Now
* Refresh Saved Snapshots

### Summary Cards

* Total Runs
* Total Snapshots
* Latest Success
* Latest Failures
* Avg Quality
* Symbols Tracked

### Latest Run

显示：

* runGroupId
* exchange
* timeframe
* successCount
* failureCount
* createdAt

### Recent Snapshots

显示：

* symbol
* price
* change24h
* fundingRate
* openInterest
* candleCount
* dataQualityScore
* collectedAt
* missingFields

### Empty State

如果没有 snapshots：

```text
No live data snapshots yet. Collect public market data to create local research samples.
```

---

## Feature Engine Integration

V10 需要轻量接入 V3/V5 Feature 生成流程。

目标：

当 `generateTradeFeature` 或 `generateTradeFeatureFromBackend` 生成 TradeFeature 时，可以读取本地最新 `MarketDataSnapshots` 作为补充数据源。

建议：

1. 在 `src/feature-engine/featureEngine.ts` 中读取 `getLatestMarketSnapshotBySymbol(symbol)`。
2. 如果 snapshot 存在，可以补充：

   * currentPrice
   * volume
   * change24h
   * funding
   * openInterest
   * dataQualityScore
3. 不要覆盖更高质量的 Python Feature Engine 字段。
4. 不要伪造缺失字段。
5. `missingFieldsJson` 要反映仍然缺失的字段。
6. Feature source 可以记录：

   * `python_feature_engine_v5`
   * `local_feature_engine_v3`
   * `live_data_collector_v10`
   * 或组合 source，例如 `python_feature_engine_v5+live_data_collector_v10`

要求：

* 如果没有 live snapshot，不影响原流程。
* Quick Trade 不崩溃。
* Data Quality 页面不崩溃。
* Feature generation 不请求后端。
* 只读取本地 SQLite。

---

## Research Dashboard Integration

Research Dashboard 新增 section：

```text
Live Data Collection
```

显示：

* total runs
* total snapshots
* latest run time
* symbols tracked
* average quality
* latest success count
* latest failure count
* recent snapshots

必须显示说明：

```text
Live data snapshots are public market data samples for research only. They are not trading signals.
```

要求：

1. 只读本地 SQLite。
2. 不请求后端。
3. 不运行 collect。
4. 不调用交易所。
5. 空数据不崩溃。

---

## My Screen Integration

更新：

```text
src/screens/MyScreen.tsx
```

新增入口：

```text
Live Data Collection
```

描述：

```text
采集 OKX 公共市场数据，保存为本地研究样本，不接交易权限。
```

---

## README Update

更新 `README.md`，新增 V10 说明。

必须写明：

* V10 是 Live Data Collection。
* V10 只采集公共市场数据。
* V10 第一版只支持 OKX public data。
* V10 可以采集 ticker / OHLCV / funding / open interest。
* funding / open interest 不可用时保存 null，不伪造。
* V10 保存 MarketDataSnapshots 到本地 SQLite。
* V10 保存 LiveCollectionRuns 到本地 SQLite。
* V10 不接真实 AI。
* V10 不做交易信号。
* V10 不接 Trade API。
* V10 不接 Withdraw API。
* V10 不保存 API Key。
* V10 不读取账户余额。
* V10 不读取持仓。
* V10 不下单。
* V10 不自动交易。
* V10 不提供买卖建议。

---

## Safety Rules

V10 必须遵守：

1. Public market data only.
2. No real AI.
3. No Signal Engine upgrade.
4. No buy/sell advice.
5. No long/short recommendation.
6. No future prediction.
7. No guaranteed outcome.
8. No Trade API.
9. No Withdraw API.
10. No API Key.
11. No account balance.
12. No positions.
13. No order placement.
14. No cancel order.
15. No paper trading.
16. No auto trading.
17. No hidden background trading.
18. No high-frequency collection.
19. Respect exchange rate limits.
20. User owns all decisions.

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
signal says buy
signal says sell
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
信号建议买入
信号建议卖出
```

允许出现：

```text
public market data
research sample
live snapshot
data quality
missing fields
not trading advice
not an order
research-only
```

中文允许：

```text
公共市场数据
研究样本
实时快照
数据质量
缺失字段
不是交易建议
不是订单
仅用于研究
```

---

## Backend Testing

完成后运行：

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
Invoke-WebRequest -UseBasicParsing "http://127.0.0.1:8000/live/sources"
Invoke-WebRequest -UseBasicParsing "http://127.0.0.1:8000/live/snapshot?exchange=okx&symbol=BTC%2FUSDT&timeframe=1h&limit=200"
Invoke-WebRequest -UseBasicParsing "http://127.0.0.1:8000/live/collect?exchange=okx&symbols=BTC%2FUSDT,ETH%2FUSDT,SOL%2FUSDT&timeframe=1h&limit=200"
```

如果外部网络或 OKX 不可访问，必须明确记录原因。

---

## App Testing

完成后运行：

```powershell
pnpm run typecheck
pnpm exec expo config --type public
```

手动检查：

1. App 能启动。
2. 我的页面能进入 Live Data Collection。
3. 后端未启动时页面不崩溃。
4. 后端启动后可以 Load Sources。
5. 可以 Collect Now。
6. 可以保存 LiveCollectionRuns。
7. 可以保存 MarketDataSnapshots。
8. Recent Snapshots 能显示。
9. Research Dashboard 能显示 Live Data Collection section。
10. Data Quality 页面仍然正常。
11. Feature generation 仍然正常。
12. Signal Engine 页面仍然正常。
13. Backtesting 页面仍然正常。
14. Quick Trade 仍然正常。
15. Trade Detail 仍然正常。
16. 没有交易建议文案。
17. 没有交易权限调用。
18. 没有下单相关实现。

---

## Acceptance Criteria

V10 完成后必须满足：

1. `api/live_data_collector.py` 存在。
2. `api/live_data_models.py` 存在。
3. `/live/sources` 存在。
4. `/live/snapshot` 存在。
5. `/live/collect` 存在。
6. 后端只使用 public market data。
7. 后端不使用 API Key。
8. 后端不调用 private endpoints。
9. App 新增 `src/live-data/liveDataTypes.ts`。
10. App 新增 `src/live-data/liveDataMapper.ts`。
11. App 新增 `src/services/liveDataApiService.ts`。
12. App 新增 `LiveDataCollectionScreen.tsx`。
13. My 页面有 Live Data Collection 入口。
14. SQLite 新增 `LiveCollectionRuns` 表。
15. SQLite 新增 `MarketDataSnapshots` 表。
16. migration 使用 `CREATE TABLE IF NOT EXISTS`。
17. `PRAGMA user_version = 8`。
18. 可以采集单个 symbol snapshot。
19. 可以采集多个 symbols snapshots。
20. 可以保存 collection run。
21. 可以保存 market data snapshots。
22. 可以显示 live data summary。
23. Research Dashboard 显示 Live Data Collection section。
24. Feature Engine 可以读取最新 local market snapshot 作为补充数据。
25. 后端不可用时 App 不崩溃。
26. `pnpm run typecheck` 通过。
27. `pnpm exec expo config --type public` 通过。
28. `python -m compileall api` 通过。
29. smoke test 通过，或明确记录外部网络原因。
30. 不接 AI。
31. 不接交易权限。
32. 不保存 API Key。
33. 不下单。
34. 不自动交易。
35. 不生成买卖建议。

---

## Self Review Output

完成后请输出：

1. 修改文件列表
2. 新增文件列表
3. 后端 live data collector 说明
4. 新增 API endpoints 说明
5. 采集字段说明
6. funding / openInterest 不可用时的处理说明
7. 新增数据库表说明
8. Live Data Collection 页面说明
9. My 页面入口说明
10. Research Dashboard 集成说明
11. Feature Engine 集成说明
12. 后端不可用时 App fallback 行为
13. 安全边界确认
14. `pnpm run typecheck` 结果
15. `pnpm exec expo config --type public` 结果
16. `python -m compileall api` 结果
17. smoke test 结果
18. 安全文案扫描结果
19. 已知问题
20. 下一步 V11 建议

---

## Future Compatibility

V10 为 V11/V12 做准备：

* V11 Paper Trading 可以读取 MarketDataSnapshots 作为研究输入。
* V11 仍然不能接真实交易权限。
* V12 Semi-Auto Trading 以后也必须手动确认，且要经过严格风控。
* V10 不做 Paper Trading。
* V10 不做 Semi-Auto Trading。
* V10 不做 Auto Trading。
* V10 不做交易执行。

V10 只完成 Live Data Collection。
