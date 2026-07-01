# AlphaPilot V5 - Python Feature Engine

## Goal

基于当前 `trade-discipline-journal` V4.1 版本，开发 AlphaPilot V5：Python Feature Engine。

V5 的目标不是 AI，不是信号，不是自动交易。

V5 的目标是：

把 V3 本地 Feature Database 和 V4 公共行情服务连接起来，让后端使用 Python 统一计算真实市场 Feature，并让 App 可以把这些 Feature 写回本地 SQLite 的 `TradeFeatures` 表。

V5 要让 AlphaPilot 从“能保存 Feature 结构”升级为：

可以基于真实公共行情生成结构化 Feature 的研究基础设施。

---

## Current State

当前项目已经完成：

* V1.1：交易纪律、风险规则、Initial Setup、5x 杠杆限制
* V2：Quick Trade、Trade Detail、Mock Analysis、Snapshot、Timeline
* V3：本地 Feature Database、TradeFeatures 表、Data Quality 页面、CSV 导出
* V4：FastAPI 后端、CCXT、OKX 公共行情接口
* V4.1：后端已改用 CCXT unified public methods：

  * `fetch_ticker`
  * `fetch_ohlcv`

当前已经有：

* `api/main.py`
* `api/market_service.py`
* `api/requirements.txt`
* `src/feature-engine/featureEngine.ts`
* `src/feature-engine/csvExport.ts`
* `src/screens/DataQualityScreen.tsx`
* `src/services/marketDataService.ts`
* SQLite 表：`TradeFeatures`

V5 必须基于这些继续开发，不要推翻之前版本。

---

## Scope

V5 只做 Python Feature Engine。

包含：

1. 新增 Python feature engine 模块。
2. 使用 V4 后端已有的公共行情能力。
3. 从 OHLCV 计算技术指标。
4. 生成结构化 market features。
5. 新增后端 Feature API。
6. App 端调用后端 Feature API。
7. App 将后端生成的 features 写回本地 `TradeFeatures`。
8. Data Quality 页面增加“从后端刷新 Feature”的能力。
9. README 更新 V5 说明。
10. 保持 V1.1/V2/V3/V4/V4.1 功能不被破坏。

---

## Non Goals

V5 不做以下内容：

1. 不接真实 AI。
2. 不接 OpenAI / Claude / Gemini。
3. 不做 Signal Engine。
4. 不生成买卖信号。
5. 不做策略推荐。
6. 不做回测。
7. 不做 VectorBT。
8. 不做 Paper Trading。
9. 不做 Semi-Auto Trading。
10. 不做 Auto Trading。
11. 不接交易权限 API。
12. 不保存 API Key。
13. 不读取账户余额。
14. 不读取持仓。
15. 不下单。
16. 不撤单。
17. 不提供买卖建议。
18. 不预测未来价格。
19. 不承诺收益。

---

## Product Principle

V5 必须遵循 AlphaPilot 的核心原则：

1. Data before AI.
2. Research before automation.
3. Risk above signal.
4. AI never owns money.
5. Everything is feature.
6. Explainability before black-box output.
7. Evidence before opinion.

V5 只负责生成 Feature，不负责决定交易。

所有输出只能描述市场状态，不能变成交易建议。

---

## Architecture

推荐目录结构：

```text
api/
  main.py
  market_service.py
  feature_engine.py
  models.py
  requirements.txt

src/
  services/
    featureApiService.ts

src/feature-engine/
  featureEngine.ts
  csvExport.ts
```

说明：

* `api/market_service.py` 继续负责公共行情。
* `api/feature_engine.py` 新增，负责 Feature 计算。
* `api/main.py` 新增 Feature API endpoints。
* `src/services/featureApiService.ts` 新增，负责 App 调用后端 Feature API。
* `src/feature-engine/featureEngine.ts` 保留本地 fallback 能力。
* App 不能直接依赖 CCXT。
* App 不能直接计算复杂 Python 指标。

---

## Backend Requirements

### 1. Add Python Feature Engine

新增：

```text
api/feature_engine.py
```

功能：

从 OHLCV candles 计算基础 Feature。

输入：

* exchange
* symbol
* timeframe
* limit
* optional trade context

输出：

* price
* volume
* emaFast
* emaSlow
* macd
* macdSignal
* macdHistogram
* rsi
* atr
* change1h
* change4h
* change24h
* high24h
* low24h
* volatility
* trendDirection
* candlePattern
* dataQualityScore
* missingFields
* source
* generatedAt

### 2. Indicator Requirements

V5 可以使用纯 Python 实现指标，优先避免引入过重依赖。

必须实现：

* EMA
* MACD
* RSI
* ATR
* Volatility
* Trend Direction

可以先不用 pandas-ta。

如果使用第三方库，必须说明原因，并保持依赖轻量。

### 3. Indicator Definitions

#### EMA

计算：

* emaFast 默认 12
* emaSlow 默认 26

#### MACD

计算：

* MACD Line = EMA12 - EMA26
* Signal Line = EMA9 of MACD Line
* Histogram = MACD Line - Signal Line

#### RSI

默认周期：

* 14

#### ATR

默认周期：

* 14

#### Volatility

可以使用：

```text
(high - low) / close * 100
```

或者基于 candle close returns 的近似波动率。

必须在代码里保持简单清晰。

#### Trend Direction

只能返回：

```text
up
down
flat
unknown
```

不能返回：

```text
buy
sell
long
short
strong buy
strong sell
```

### 4. Data Quality

Feature Engine 必须计算 `dataQualityScore`。

建议逻辑：

* 核心字段存在则加分
* OHLCV 数量足够则加分
* 指标成功计算则加分
* 缺失字段写入 `missingFields`

如果数据不足，不要伪造。

缺失值必须返回 null。

---

## Backend API Endpoints

在 `api/main.py` 中新增：

### GET /features/market

示例：

```text
GET /features/market?exchange=okx&symbol=BTC/USDT&timeframe=1h&limit=200
```

返回：

```json
{
  "exchange": "okx",
  "symbol": "BTC/USDT",
  "timeframe": "1h",
  "limit": 200,
  "price": 100000,
  "volume": 12345,
  "emaFast": 99800,
  "emaSlow": 99100,
  "macd": 120,
  "macdSignal": 90,
  "macdHistogram": 30,
  "rsi": 62,
  "atr": 850,
  "change1h": 0.4,
  "change4h": 1.2,
  "change24h": 3.8,
  "high24h": 102000,
  "low24h": 97000,
  "volatility": 4.9,
  "trendDirection": "up",
  "candlePattern": "none",
  "dataQualityScore": 86,
  "missingFields": [],
  "source": "python_feature_engine_v5",
  "generatedAt": "ISO_DATE"
}
```

### GET /features/trade-context

示例：

```text
GET /features/trade-context?exchange=okx&symbol=BTC/USDT&direction=long&entryPrice=100000&timeframe=1h&limit=200
```

返回 market features，并额外返回交易上下文字段：

* entryPrice
* distanceFromEntryPercent
* direction
* trendAlignment

`trendAlignment` 只能描述：

```text
aligned
against
neutral
unknown
```

不能输出买卖建议。

---

## App Integration

新增：

```text
src/services/featureApiService.ts
```

功能：

1. 调用后端 `/features/market`
2. 调用后端 `/features/trade-context`
3. 后端不可用时返回 null
4. 使用短超时
5. 不阻塞 Quick Trade
6. 不让 App 崩溃
7. 不上传隐私数据
8. 不发送 API Key
9. 不发送账户信息
10. 不发送下单指令

---

## TradeFeatures Integration

当前 V3 已有 `TradeFeatures` 表。

V5 需要把后端 Feature Engine 的结果写入 `TradeFeatures`。

要求：

1. 保留现有本地 feature generation fallback。
2. 增加后端 feature enrichment。
3. 如果后端可用，优先使用后端 Feature。
4. 如果后端不可用，继续使用 V3 本地 Feature。
5. 不删除已有 `TradeFeatures` 行。
6. 使用 upsert。
7. 不重建表。
8. 不破坏旧数据。

---

## Database Impact

如果现有 `TradeFeatures` 字段足够，则不要改数据库结构。

如果必须新增字段，使用 safe migration。

允许新增字段：

* emaFast
* emaSlow
* macdSignal
* macdHistogram
* change1h
* change4h
* backendFeatureSource
* backendGeneratedAt

但如果可以复用当前字段：

* ema
* macd
* rsi
* atr
* volume
* change24h
* marketVolatility
* trend

则优先复用，避免过早扩大表结构。

数据库要求：

* 不删除旧数据
* 不重建表
* 不清空表
* 不保存 API Key
* 不保存交易所私有数据
* 不保存账户余额
* 不保存持仓

---

## Data Quality Page

更新：

```text
src/screens/DataQualityScreen.tsx
```

新增能力：

1. 显示 V5 Python Feature Engine 状态。
2. 增加按钮：

   * Generate Local Features
   * Refresh From Backend
   * Export CSV
3. 后端不可用时显示明确提示：

   * “Backend Feature Engine unavailable. Local fallback is still available.”
4. 显示：

   * total trades
   * feature rows
   * average data quality
   * backend enriched rows
   * missing fields
   * latest generated time

不要让后端失败导致页面崩溃。

---

## Feature Flow

V5 推荐数据流：

```text
User opens Data Quality
↓
Tap Refresh From Backend
↓
App loads trades from SQLite
↓
For each trade:
  call /features/trade-context
  if backend returns data:
    map backend features to TradeFeatures
    upsert TradeFeatures
  else:
    use local V3 feature engine fallback
↓
Refresh summary
↓
Show Data Quality result
```

Quick Trade 可以继续使用 V4 后端 ticker 保存 snapshot，但不要因为 V5 后端失败影响交易创建。

---

## Mapping Rules

后端 Feature 到 `TradeFeatures` 的建议映射：

```text
backend.price -> currentPrice
backend.volume -> volume
backend.emaFast or emaSlow -> ema
backend.macd -> macd
backend.rsi -> rsi
backend.atr -> atr
backend.change24h -> change24h
backend.volatility -> marketVolatility
backend.trendDirection -> trend
backend.candlePattern -> candlePattern
backend.dataQualityScore -> dataQualityScore
backend.missingFields -> missingFieldsJson
backend.generatedAt -> generatedAt
```

如果字段不存在，写 null。

不要伪造数据。

---

## UI Rules

Data Quality 页面风格继续保持：

* 简洁
* 克制
* 专业
* 研究导向
* 不刺激交易
* 不显示买卖建议
* 不显示“强烈看多/强烈看空”

可以使用文案：

```text
Python Feature Engine converts public market data into structured research features.
```

不要使用文案：

```text
AI says buy
AI says sell
Strong signal
Guaranteed opportunity
```

---

## Safety Rules

V5 必须遵守：

1. Public market data only.
2. No API Key.
3. No Trade API.
4. No Withdraw API.
5. No private endpoints.
6. No account balance.
7. No positions.
8. No order placement.
9. No cancel order.
10. No auto trading.
11. No buy/sell advice.
12. No price prediction.
13. No guaranteed outcome.
14. Feature is not signal.
15. Signal is not order.
16. User always owns decisions.

---

## README Update

更新 `README.md`，新增 V5 说明：

必须写明：

* V5 是 Python Feature Engine
* V5 使用公共行情计算 Feature
* V5 不接真实 AI
* V5 不生成信号
* V5 不自动交易
* V5 不接交易权限
* V5 不保存 API Key
* 如何运行后端
* 如何测试 `/features/market`
* 如何测试 `/features/trade-context`
* App 端后端不可用时会 fallback

---

## Testing

完成后运行：

### App

```powershell
pnpm run typecheck
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
Invoke-WebRequest -UseBasicParsing "http://127.0.0.1:8000/features/market?exchange=okx&symbol=BTC%2FUSDT&timeframe=1h&limit=200"
Invoke-WebRequest -UseBasicParsing "http://127.0.0.1:8000/features/trade-context?exchange=okx&symbol=BTC%2FUSDT&direction=long&entryPrice=100000&timeframe=1h&limit=200"
```

如果 OKX 或网络不可用，记录失败原因，但 App fallback 必须正常。

---

## Acceptance Criteria

V5 完成后必须满足：

1. `api/feature_engine.py` 存在。
2. `/features/market` 存在。
3. `/features/trade-context` 存在。
4. 后端可以计算 EMA。
5. 后端可以计算 MACD。
6. 后端可以计算 RSI。
7. 后端可以计算 ATR。
8. 后端可以计算 volatility。
9. 后端可以输出 trendDirection。
10. 后端可以输出 dataQualityScore。
11. 后端缺失字段返回 null，不伪造。
12. App 新增 `featureApiService.ts`。
13. Data Quality 页面可以从后端刷新 Feature。
14. 后端不可用时 App 不崩溃。
15. 本地 V3 Feature Engine fallback 保留。
16. TradeFeatures 可以被后端 Feature upsert 更新。
17. README 已更新 V5。
18. `pnpm run typecheck` 通过。
19. `python -m compileall api` 通过。
20. 后端 smoke test 结果已记录。
21. 没有真实 AI。
22. 没有 Signal Engine。
23. 没有 Trade API。
24. 没有 Withdraw API。
25. 没有 API Key。
26. 没有自动下单。
27. 没有买卖建议。

---

## Self Review Output

完成后请输出：

1. 修改文件列表
2. 新增文件列表
3. 后端新增模块说明
4. 新增 API endpoint 说明
5. 指标计算说明
6. App 集成说明
7. Data Quality 页面更新说明
8. 数据库是否 migration
9. TradeFeatures 映射说明
10. fallback 行为说明
11. 安全边界确认
12. `pnpm run typecheck` 结果
13. `python -m compileall api` 结果
14. 后端 smoke test 结果
15. 已知问题
16. 下一步 V6 建议

---

## Future Compatibility

V5 必须为 V6/V7/V8 做准备：

* V6 Skill Engine 可以复用 Feature Engine 输出
* V7 Research Dashboard 可以展示 Feature 分布
* V8 Backtesting 可以使用相同 Feature 定义
* V9 Signal Engine 可以基于已经验证过的 Feature
* V10 Live Data Collection 可以复用后端 feature pipeline

但 V5 不要提前实现这些阶段。

V5 只完成 Python Feature Engine。
