# AlphaPilot V13.4.28 - Market Data Coverage Repair and Public Data Expansion Skeleton

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.28。

本版本的目标是：**基于 V13.4.27 Market Regime + Data Integrity Review 的结果，优先修复本地 OHLCV 覆盖问题，并建立 Funding Rate / Open Interest / Spread Proxy 等公开数据扩展层的 skeleton，为后续 Bear Regime Short Strategy 和 Regime-Aware Research 做准备。**

本版本不是策略实现版。
本版本不是调参版。
本版本不是策略回测版。
本版本不是 Dry-run 版。
本版本不是实盘版。
本版本不接真实 API Key。
本版本不自动交易。
本版本不创建真实订单。

允许使用公开行情数据。
禁止使用私有交易 API。
禁止读取真实账户。
禁止创建真实订单。

完成后请输出完整自检报告。

---

# 1. Version Name

```text
AlphaPilot V13.4.28 - Market Data Coverage Repair and Public Data Expansion Skeleton
```

中文版本名：

```text
AlphaPilot V13.4.28 - 市场数据覆盖修复与公开数据扩展骨架版
```

---

# 2. Background

当前已完成：

```text
V13.4.27：Market Regime and Data Integrity Review
```

V13.4.27 结果摘要：

```text
timerange: 20260101-
timeframes: 1h, 4h
pairCount: 30
pairTimeframeCount: 60
validCount: 55
warningCount: 1
invalidCount: 0
missingFileCount: 4
averageMissingRatePct: 0.0
totalInvalidOhlcRows: 0
totalDuplicateTimestamps: 0
totalExtremeReturnRows: 1
BTC dominant regimes: bear, sideways, bull
```

V13.4.27 结论：

```text
本地 OHLCV 没发现明显 OHLC 破坏。
研究可用，但存在覆盖 warning。
近期 long-only 技术策略失败，更像是 bear / high-volatility 市场环境 + alpha 验证不足共同导致。
不能简单归因成单个参数问题或数据坏了。
```

V13.4.27 已知问题：

```text
BTC 2025-10-01 本地没有近邻 candle，报告已写 warning。
latest breadth proxy 尾部部分时点 pairCount 降到 3。
本地样本末端覆盖不完全。
后续 V13.4.28 数据扩展应优先补齐。
```

因此 V13.4.28 的首要任务是：

```text
先修复数据覆盖，再扩展 Funding / OI / Spread Proxy 数据骨架。
```

---

# 3. Core Goal

V13.4.28 的核心目标：

```text
1. 读取 V13.4.27 数据完整性报告
2. 定位 missingFileCount=4 的 pair/timeframe
3. 定位 breadth proxy 尾部 pairCount 降到 3 的覆盖问题
4. 使用公开数据补齐 Top30 1h / 4h OHLCV
5. 重新运行数据完整性检查
6. 输出 data coverage repair report
7. 建立 Funding Rate schema
8. 建立 Open Interest schema
9. 建立 Spread / Orderbook proxy schema
10. 建立公开数据源 registry / collector skeleton
11. 不运行策略回测
12. 不写策略
13. 不进入 Dry-run
```

一句话目标：

```text
先把研究数据地基补齐，再开始熊市做空、Funding/OI、低频主流币研究。
```

---

# 4. Required Mindset

请遵守以下原则：

```text
1. 数据覆盖修复优先于继续策略研究
2. 公开行情数据可以下载
3. 私有交易数据禁止接入
4. API Key 禁止保存
5. 真实账户禁止读取
6. 不因为数据缺口小就忽略 coverage warning
7. 不能伪造已下载数据
8. 不能把 skeleton 当成真实数据采集完成
9. 不运行 backtest
10. 不进入 Dry-run
```

---

# 5. Non Goals

V13.4.28 不做：

```text
不实现交易策略
不实现做空策略
不修改任何策略参数
不运行 Freqtrade backtest
不进入 Dry-run
不实盘
不接真实 API Key
不接 Trade API
不接 Withdraw API
不读取真实账户
不读取真实持仓
不创建真实订单
不自动交易
不修改 AlphaPilot Mobile App
不接新闻系统
不接机器学习
不接多模型决策
```

允许做：

```text
使用公开 OHLCV 数据下载 / 补齐
读取本地数据质量报告
新增数据扩展 schema
新增公开数据 collector skeleton
新增 data source registry
生成 coverage repair report
生成 market data expansion summary
更新 README / docs
```

---

# 6. Precondition

执行前必须确认：

```text
1. v13.4.27 tag 已存在
2. reports/v13_4_27_market_regime_data_integrity_report.json 存在
3. reports/v13_4_27_data_quality_by_pair.json 存在
4. reports/v13_4_27_btc_regime_labels.json 存在
5. 当前工作区干净
```

如果不满足：

```text
停止 V13.4.28
提示先完成 V13.4.27
不要继续
```

---

# 7. Repository Scope

主要修改：

```text
D:\Codex-Workspace\AlphaPilot-Quant-Engine
```

可修改 Docs 仓库归档 prompt：

```text
D:\Codex-Workspace\AlphaPilot-Docs
```

不要修改 App 仓库主逻辑：

```text
D:\Codex-Workspace\trade-discipline-journal
```

---

# 8. Phase 1 - Data Coverage Repair

## 8.1 Read Data Quality Report

读取：

```text
reports/v13_4_27_data_quality_by_pair.json
reports/v13_4_27_market_regime_data_integrity_report.json
```

提取：

```text
missing files
warning pairs
invalid pairs
tail coverage drop
timeframe coverage
```

输出：

```text
dataCoverageGaps
```

---

## 8.2 Identify Missing Pair/Timeframe Files

必须明确输出：

```text
missingPairTimeframes
```

字段：

```text
pair
timeframe
expectedPath
reason
repairPlanned
```

如果报告中没有明确 pair 名称，要从数据目录扫描推断。

不要只写：

```text
missingFileCount = 4
```

必须列出是哪 4 个。

---

## 8.3 Repair OHLCV Coverage

允许执行公开数据下载。

优先使用现有脚本：

```powershell
.\scripts\download_data.ps1 -UseTop30 -Timeframes "1h,4h" -Timerange "20260101-" -Run
```

如果已支持 `-Prepend`，对于覆盖不足的旧文件使用：

```powershell
.\scripts\download_data.ps1 -UseTop30 -Timeframes "1h,4h" -Timerange "20260101-" -Prepend -Run
```

如果只需修复特定 pair，可以使用：

```powershell
.\scripts\download_data.ps1 -Pairs "PAIR1,PAIR2" -Timeframes "1h,4h" -Timerange "20260101-" -Run
```

要求：

```text
1. 只下载公开历史行情
2. 不使用 API Key
3. 不接私有接口
4. 下载失败必须记录原因
5. 不伪造成功
```

---

## 8.4 Re-run Integrity Review

补齐后重新运行：

```powershell
python -m alphapilot.reports.generate_market_regime_data_integrity_review
```

但不要覆盖 V13.4.27 的正式报告。

V13.4.28 应生成自己的修复后报告：

```text
reports/v13_4_28_data_coverage_repair_report.json
reports/v13_4_28_data_coverage_repair_summary.md
reports/v13_4_28_post_repair_data_quality_by_pair.json
```

如果复用同一个 generator，需要增加参数或复制输出为 V13.4.28 文件名。

---

## 8.5 Coverage Success Criteria

修复后目标：

```text
missingFileCount = 0
invalidCount = 0
averageMissingRatePct 可接受
tail breadth pairCount 不应异常降到 3，除非有明确数据截止原因
```

如果仍有缺口：

```text
写入 unresolvedCoverageGaps
```

不影响 skeleton 设计，但必须说明。

---

# 9. Phase 2 - Data Expansion Schema

新增 package：

```text
alphapilot/data_expansion/
```

---

## 9.1 Funding Rate Schema

新增：

```text
alphapilot/data_expansion/funding_rate_schema.py
```

字段：

```text
timestamp
pair
exchange
marketType
fundingRate
nextFundingTime
fundingRateZScore
fundingRatePercentile
fundingRegime
source
fetchedAt
qualityFlags
```

fundingRegime：

```text
normal
long_crowded
short_crowded
extreme_long
extreme_short
unavailable
```

用途：

```text
risk filter
regime feature
short / long crowdedness
```

---

## 9.2 Open Interest Schema

新增：

```text
alphapilot/data_expansion/open_interest_schema.py
```

字段：

```text
timestamp
pair
exchange
marketType
openInterest
openInterestValue
oiChange1h
oiChange4h
oiChange24h
priceChange1h
priceChange4h
oiPriceRegime
source
fetchedAt
qualityFlags
```

oiPriceRegime：

```text
price_up_oi_up
price_up_oi_down
price_down_oi_up
price_down_oi_down
flat_or_unknown
unavailable
```

用途：

```text
判断趋势是否有新资金推动
判断上涨是否空头平仓
判断下跌是否新空进场
```

---

## 9.3 Spread / Orderbook Proxy Schema

新增：

```text
alphapilot/data_expansion/orderbook_proxy_schema.py
```

字段：

```text
timestamp
pair
exchange
marketType
bid
ask
mid
spreadPct
top5BidDepth
top5AskDepth
top10BidDepth
top10AskDepth
depthImbalance
depthAvailable
source
fetchedAt
qualityFlags
```

用途：

```text
execution reality
liquidity gate
slippage stress
order size limits
```

第一版可以只做 schema，不拉取真实 orderbook。

---

## 9.4 Liquidation Schema

新增：

```text
alphapilot/data_expansion/liquidation_schema.py
```

字段：

```text
timestamp
pair
longLiquidationUsd
shortLiquidationUsd
totalLiquidationUsd
liquidationImbalance
liquidationZScore
source
fetchedAt
qualityFlags
```

用途：

```text
极端清算行情识别
risk / avoid regime
```

第一版只做 schema。

---

## 9.5 Market Regime Proxy Schema

新增：

```text
alphapilot/data_expansion/market_regime_proxy_schema.py
```

字段：

```text
timestamp
btcTrendRegime
ethTrendRegime
btcVolatilityRegime
altcoinBreadthProxy
marketRiskState
source
fetchedAt
qualityFlags
```

用途：

```text
regime-aware strategy
bear / bull / sideways / crash
```

---

# 10. Data Source Registry

新增：

```text
alphapilot/data_expansion/data_source_registry.py
```

定义：

```text
DataSourceDefinition
DataSourceCapability
DATA_SOURCE_REGISTRY
```

至少包含：

```text
okx_public_ohlcv
okx_public_funding_rate
okx_public_open_interest
okx_public_ticker
okx_public_orderbook_snapshot
local_freqtrade_ohlcv
```

每个数据源字段：

```text
sourceId
requiresApiKey
usesPrivateEndpoint
supportsHistorical
supportsRealtime
status
notes
```

必须设置：

```text
requiresApiKey = false
usesPrivateEndpoint = false
```

除非只是未来占位，且必须 status = planned。

---

# 11. Public Collector Skeleton

新增：

```text
alphapilot/data_expansion/public_data_collector_skeleton.py
```

只做 skeleton，不必须真实调用 API。

接口：

```python
class PublicDataCollector:
    def fetch_funding_rate(...)
    def fetch_open_interest(...)
    def fetch_orderbook_snapshot(...)
    def fetch_ticker(...)
```

默认行为：

```text
raise NotImplementedError
```

或：

```text
return unavailable result
```

但要有清晰注释：

```text
No private API
No trading endpoint
No API key
```

如果实现 public endpoint smoke fetch，必须：

```text
只调用公开接口
不需要 API Key
不保存密钥
失败写 warning
```

本版本建议先 skeleton，不强制 smoke fetch。

---

# 12. Data Quality Schema

新增：

```text
alphapilot/data_expansion/data_quality_schema.py
```

字段：

```text
sourceId
pair
timeframe
timerange
completenessPct
missingIntervals
duplicateTimestamps
invalidRows
source
fetchedAt
timezone
symbolMapping
contractType
qualityStatus
warnings
```

qualityStatus：

```text
valid
warning
invalid
unavailable
```

---

# 13. Report Generator

新增：

```text
alphapilot/reports/generate_market_data_expansion_report.py
alphapilot/reports/market_data_expansion_schema.py
```

输出：

```text
reports/v13_4_28_market_data_expansion_report.json
reports/v13_4_28_market_data_expansion_summary.md
```

报告包含：

```text
coverageRepair
fundingRateSchema
openInterestSchema
orderbookProxySchema
liquidationSchema
marketRegimeProxySchema
dataSourceRegistry
collectorSkeleton
nextStepRecommendation
dryRunApproved=false
liveTradingApproved=false
```

---

# 14. Docs To Add

新增：

```text
docs/V13.4.28-market-data-coverage-repair-expansion.md
docs/funding-rate-data-design.md
docs/open-interest-data-design.md
docs/orderbook-spread-proxy-design.md
docs/market-data-source-registry.md
docs/data-quality-requirements.md
```

README 增加：

```text
## V13.4.28 Market Data Coverage Repair and Public Data Expansion
```

中文说明：

```text
V13.4.28 基于 V13.4.27 数据完整性报告，优先修复本地 OHLCV 覆盖问题，并新增 Funding Rate、Open Interest、Spread/Orderbook Proxy 等公开数据扩展骨架。
本版本不写策略、不回测、不进入 Dry-run、不接真实 API Key。
```

---

# 15. Output Files

必须生成：

```text
reports/v13_4_28_data_coverage_repair_report.json
reports/v13_4_28_data_coverage_repair_summary.md
reports/v13_4_28_post_repair_data_quality_by_pair.json
reports/v13_4_28_market_data_expansion_report.json
reports/v13_4_28_market_data_expansion_summary.md
```

如果无法修复所有缺口：

```text
报告 unresolvedCoverageGaps
```

不要伪造修复成功。

---

# 16. Commands To Run

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m compileall alphapilot
```

数据补齐：

```powershell
.\scripts\download_data.ps1 -UseTop30 -Timeframes "1h,4h" -Timerange "20260101-" -Run
```

如支持 Prepend：

```powershell
.\scripts\download_data.ps1 -UseTop30 -Timeframes "1h,4h" -Timerange "20260101-" -Prepend -Run
```

生成报告：

```powershell
python -m alphapilot.reports.generate_market_data_expansion_report
python -m alphapilot.reports.generate_market_regime_data_integrity_review
```

安全检查：

```powershell
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

如果现有脚本路径或参数不同，以当前仓库实际脚本为准，但必须记录差异。

---

# 17. Safety Requirements

必须保持：

```text
不接真实 Trade API
不接 Withdraw API
不保存真实 API Key
不读取真实账户
不读取真实持仓
不创建真实订单
不自动交易
不执行 Dry-run
不运行策略 backtest
不修改 AlphaPilot Mobile App
不修改策略代码
```

允许：

```text
下载公开 OHLCV 数据
读取本地公开行情
新增数据扩展 schema
新增公开数据 collector skeleton
生成数据质量报告
```

---

# 18. Acceptance Criteria

完成后必须满足：

```text
1. 已读取 V13.4.27 data quality report
2. 已列出 missingPairTimeframes
3. 已尝试补齐 Top30 1h/4h OHLCV
4. 已生成 data coverage repair report
5. 已生成 post-repair data quality by pair
6. 已生成 market data expansion report
7. funding_rate_schema.py 已创建
8. open_interest_schema.py 已创建
9. orderbook_proxy_schema.py 已创建
10. liquidation_schema.py 已创建
11. market_regime_proxy_schema.py 已创建
12. data_source_registry.py 已创建
13. public_data_collector_skeleton.py 已创建
14. data_quality_schema.py 已创建
15. README / docs 更新
16. 不写策略
17. 不运行 backtest
18. 不进入 Dry-run
19. 不接真实 API Key
20. 不接 Trade API
21. 不接 Withdraw API
22. 不读取真实账户
23. 不读取真实持仓
24. 不创建真实订单
25. 不自动交易
26. compileall 通过
27. validate_config 通过
28. safety scan 通过
29. git diff --check 通过
```

如果数据补齐无法执行：

```text
可以提交 skeleton + blocked coverage report
但不要声称 coverage repair 成功
是否打 tag 取决于 report 是否完整说明失败原因
```

---

# 19. Commit And Tag

如果检查通过：

```bash
git add .
git commit -m "Add V13.4.28 market data coverage repair and expansion skeleton"
git push
git tag v13.4.28
git push origin v13.4.28
```

Docs 仓库归档 prompt 后：

```bash
git add .
git commit -m "Document V13.4.28 market data coverage repair"
git push
git tag v13.4.28-docs
git push origin v13.4.28-docs
```

---

# 20. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. 输入 V13.4.27 data quality report 路径
5. missingPairTimeframes
6. 是否执行数据补齐
7. 数据补齐命令
8. unresolvedCoverageGaps
9. post-repair validCount / warningCount / invalidCount / missingFileCount
10. tail breadth coverage 是否改善
11. funding schema 说明
12. open interest schema 说明
13. orderbook proxy schema 说明
14. liquidation schema 说明
15. market regime proxy schema 说明
16. data source registry 说明
17. collector skeleton 说明
18. data coverage report 路径
19. expansion report 路径
20. 是否写策略
21. 是否运行 backtest
22. 是否进入 Dry-run
23. 是否保存真实 API Key
24. 是否接 Trade API
25. 是否接 Withdraw API
26. 是否读取真实账户
27. 是否读取真实持仓
28. 是否创建真实订单
29. 是否自动交易
30. compileall 结果
31. validate_config 结果
32. safety scan 结果
33. git diff --check 结果
34. commit hash
35. push 结果
36. tag 结果
37. 已知问题
38. 下一步建议
```

---

# 21. Next Version Preview

V13.4.28 完成后，下一步建议根据结果选择。

如果 coverage repair 成功：

```text
V13.4.29 - Bear Regime Short Strategy Specification
```

目标：

```text
设计熊市做空模块，只写规格，不写策略代码，不回测。
```

如果 coverage 仍有问题：

```text
V13.4.29 - Data Coverage Fix Follow-up
```

如果用户想优先合约增强数据：

```text
V13.4.29 - Funding Rate and Open Interest Public Data Collector
```

推荐默认：

```text
V13.4.29 - Bear Regime Short Strategy Specification
```

因为 V13.4.27 已确认近期市场包含 bear / high-volatility，用户也明确提出熊市应有做空机会。

---

# 22. Final Instruction

请严格执行 V13.4.28。

本版本只做市场数据覆盖修复和公开数据扩展骨架。

不要写策略。

不要调参。

不要运行 backtest。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。

