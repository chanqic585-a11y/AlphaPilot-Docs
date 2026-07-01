# AlphaPilot V7 - Research Dashboard

## Goal

基于当前 `trade-discipline-journal` V6.1 版本，开发 AlphaPilot V7：Research Dashboard。

V7 的目标不是 AI，不是信号，不是自动交易。

V7 的目标是：

把 V3 Feature Database、V5 Python Feature Engine、V6 Skill Engine 的结果整合成一个研究型 Dashboard，让用户能够系统观察自己的交易行为、市场环境、入场质量、风险纪律和复盘模式。

V7 要让 AlphaPilot 从“能生成 Skill Results”升级为：

可以把交易数据、Feature 数据、Skill Results 聚合成可解释、可筛选、可复盘的研究看板。

---

## Current State

当前项目已经完成：

* V1.1：交易纪律、风险规则、Initial Setup、5x 杠杆限制
* V2：Quick Trade、Trade Detail、Mock Analysis、Snapshot、Timeline
* V3：本地 Feature Database、TradeFeatures 表、Data Quality 页面、CSV 导出
* V4：FastAPI 后端、CCXT、OKX 公共行情接口
* V4.1：后端已改用 CCXT unified public methods
* V5：Python Feature Engine，计算 EMA、MACD、RSI、ATR、volatility、trendDirection、dataQualityScore
* V5.1：修正 change1h / change4h 的 timeframe 语义
* V6：Skill Engine，5 个内置研究 Skill，SkillResults 表
* V6.1：SkillResults 增加 runGroupId，Skill Engine 页面优先展示最新批次结果

当前已有核心数据：

* `Trades`
* `TradeFeatures`
* `TradeAnalysis`
* `TradeSnapshots`
* `SkillResults`
* `AccountSettings`

V7 必须基于这些继续开发，不要推翻之前版本。

---

## Scope

V7 只做 Research Dashboard。

包含：

1. 新增 Research Dashboard 页面。
2. 新增研究统计聚合逻辑。
3. 聚合 TradeFeatures。
4. 聚合 SkillResults。
5. 聚合交易复盘结果。
6. 支持按时间、交易对、setupType、skill label、discipline loss 过滤。
7. 展示研究摘要卡片。
8. 展示 Feature 分布。
9. 展示 Skill Results 分布。
10. 展示纪律亏损模式。
11. 展示 setup 表现。
12. 展示数据质量。
13. 展示最近研究观察。
14. My 页面新增 Research Dashboard 入口。
15. README 更新 V7 说明。
16. 保持 V1.1～V6.1 功能不被破坏。

---

## Non Goals

V7 不做以下内容：

1. 不接真实 AI。
2. 不接 OpenAI / Claude / Gemini。
3. 不做 Signal Engine。
4. 不生成买卖信号。
5. 不生成交易建议。
6. 不预测未来价格。
7. 不做 VectorBT 回测。
8. 不做 Paper Trading。
9. 不做 Semi-Auto Trading。
10. 不做 Auto Trading。
11. 不接交易权限 API。
12. 不保存 API Key。
13. 不读取交易所余额。
14. 不读取交易所持仓。
15. 不下单。
16. 不撤单。
17. 不推荐买入、卖出、做多、做空。
18. 不承诺收益。
19. 不使用刺激交易的 UI 文案。

---

## Product Principle

V7 必须遵循 AlphaPilot 的核心原则：

1. Data before AI.
2. Research before automation.
3. Risk above signal.
4. AI never owns money.
5. Everything is feature.
6. Explainability before black-box output.
7. Evidence before opinion.

Research Dashboard 只能帮助用户理解历史行为和数据质量。

Research Dashboard 不能变成交易信号页面。

---

## Recommended Architecture

推荐新增目录：

```text id="v7_structure"
src/
  research/
    researchTypes.ts
    researchRepository.ts
    researchMetrics.ts
    researchFilters.ts
    researchSummary.ts

  screens/
    ResearchDashboardScreen.tsx
```

说明：

* `researchTypes.ts` 定义 Research Dashboard 类型。
* `researchRepository.ts` 从本地 SQLite 读取 Trades / TradeFeatures / SkillResults。
* `researchMetrics.ts` 计算聚合指标。
* `researchFilters.ts` 处理筛选逻辑。
* `researchSummary.ts` 生成研究摘要文案。
* `ResearchDashboardScreen.tsx` 展示研究看板。

尽量保持纯 TypeScript，不新增重型图表库。

V7 第一版可以使用：

* cards
* lists
* simple bars
* grouped sections
* text-based distribution

不要为了图表引入复杂依赖。

---

## Data Sources

V7 主要使用本地 SQLite 数据。

### Trades

使用字段：

* id
* symbol
* marketType
* direction
* leverage
* entryPrice
* stopLossPrice
* takeProfitPrice
* positionSize
* setupType
* emotionBefore
* isFollowingSystem
* status
* exitPrice
* pnl
* followedPlan
* movedStopLoss
* addedPosition
* earlyTakeProfit
* impulsiveTrade
* lossType
* createdAt
* reviewedAt

### TradeFeatures

使用字段：

* tradeId
* source
* symbol
* marketType
* direction
* tradeStatus
* entryTime
* currentPrice
* volume
* ema
* macd
* rsi
* atr
* change24h
* marketVolatility
* candlePattern
* trend
* setupType
* setupConfidence
* finalPnl
* isDisciplineLoss
* followedPlan
* dataQualityScore
* missingFieldsJson
* generatedAt

### SkillResults

使用字段：

* runGroupId
* skillId
* skillName
* skillVersion
* tradeId
* symbol
* category
* score
* label
* summary
* explanation
* evidenceJson
* outputJson
* source
* createdAt

---

## Research Dashboard Sections

V7 Research Dashboard 至少包含以下模块。

---

### 1. Research Overview

显示核心研究摘要：

* total trades
* reviewed trades
* feature rows
* skill results
* average feature quality
* average skill score
* discipline loss count
* latest skill run time

文案必须是研究导向：

可以写：

```text id="safe_research_copy"
This dashboard summarizes historical trades, feature quality, and research skill observations.
```

禁止写：

```text id="unsafe_research_copy"
This dashboard tells you what to buy next.
```

---

### 2. Data Quality Panel

展示：

* average dataQualityScore
* backend enriched rows
* local fallback rows
* missing field count
* most common missing fields
* latest feature generatedAt

目的：

帮助用户知道数据是否足够用于研究。

如果数据不足，显示：

```text id="data_quality_warning"
Data quality is limited. Generate or refresh TradeFeatures before drawing conclusions.
```

---

### 3. Skill Results Panel

基于 SkillResults 聚合：

* total skill results
* latest run results
* average score
* warning count
* danger count
* results by skillId
* results by label
* latest runGroupId

显示方式：

* Skill name
* count
* average score
* most common label

要求：

* 优先使用 V6.1 最新 runGroupId 的结果。
* 同时可以显示 all-time summary，但必须明确区分 latest run 和 all-time。

---

### 4. Discipline Panel

聚合纪律相关结果：

* discipline_loss count
* strategy_loss count
* no_loss count
* incomplete_review count
* followedPlan rate
* impulsiveTrade count
* movedStopLoss count
* addedPosition count
* earlyTakeProfit count

说明：

这个模块用于复盘行为模式，不责备用户，不刺激交易。

文案示例：

```text id="discipline_copy"
Discipline patterns describe past behavior. Use them to improve review quality, not to force new trades.
```

---

### 5. Setup Pattern Panel

按 setupType 聚合：

* trade count
* reviewed count
* win count
* loss count
* average pnl
* discipline loss count
* average skill score
* average feature quality

支持 setupType：

* new_listing_pullback_breakout
* volume_breakout_previous_high
* extreme_fear_rebound
* other
* unknown

注意：

这不是策略推荐，只是历史分类统计。

禁止输出：

```text id="setup_forbidden"
Best setup to trade now
You should use this setup
Strong setup signal
```

---

### 6. Market Context Panel

基于 TradeFeatures 聚合：

* trend distribution
* rsi buckets
* volatility buckets
* change24h buckets
* data quality buckets

建议分桶：

RSI:

```text id="rsi_buckets"
below_30
30_to_50
50_to_70
above_70
unknown
```

Volatility:

```text id="volatility_buckets"
low
normal
high
extreme
unknown
```

Trend:

```text id="trend_buckets"
up
down
flat
unknown
```

---

### 7. Recent Research Observations

展示最近 SkillResults：

* skillName
* symbol
* label
* score
* summary
* explanation
* createdAt

要求：

* 默认显示最新 runGroupId 的结果。
* 如果没有 runGroupId，显示最近所有结果。
* 不显示交易建议。
* 不显示买卖信号。

---

## Filters

V7 Research Dashboard 支持基础筛选。

至少实现：

```text id="v7_filters"
timeRange:
  all
  last_7_days
  last_30_days
  last_90_days

symbol:
  all
  specific symbol from local trades

setupType:
  all
  known setup types

resultType:
  all
  discipline_loss
  strategy_loss
  no_loss
  unreviewed
```

可以用简单按钮或卡片实现，不需要复杂下拉组件。

筛选应用到：

* Trades
* TradeFeatures
* SkillResults

如果 SkillResults 没有 tradeId，则保留在 all 模式，但在 trade-linked filters 下可以排除。

---

## Research Types

在 `src/research/researchTypes.ts` 中建议定义：

```text id="research_types"
export type ResearchTimeRange =
  | 'all'
  | 'last_7_days'
  | 'last_30_days'
  | 'last_90_days';

export interface ResearchFilters {
  timeRange: ResearchTimeRange;
  symbol: string;
  setupType: string;
  resultType: string;
}

export interface ResearchOverview {
  totalTrades: number;
  reviewedTrades: number;
  featureRows: number;
  skillResults: number;
  averageFeatureQuality: number;
  averageSkillScore: number;
  disciplineLossCount: number;
  latestSkillRunAt: string | null;
}

export interface BucketItem {
  label: string;
  count: number;
  percent: number;
}

export interface SetupPatternResearchRow {
  setupType: string;
  tradeCount: number;
  reviewedCount: number;
  winCount: number;
  lossCount: number;
  averagePnl: number;
  disciplineLossCount: number;
  averageSkillScore: number;
  averageFeatureQuality: number;
}

export interface SkillResearchRow {
  skillId: string;
  skillName: string;
  count: number;
  averageScore: number;
  mostCommonLabel: string | null;
}

export interface ResearchDashboardData {
  filters: ResearchFilters;
  overview: ResearchOverview;
  dataQuality: {
    averageQualityScore: number;
    missingFieldCount: number;
    commonMissingFields: BucketItem[];
    backendRows: number;
    localRows: number;
  };
  skillSummary: {
    latestRunGroupId: string | null;
    latestRunResultCount: number;
    allTimeResultCount: number;
    averageScore: number;
    warningCount: number;
    dangerCount: number;
    bySkill: SkillResearchRow[];
    byLabel: BucketItem[];
  };
  discipline: {
    disciplineLossCount: number;
    strategyLossCount: number;
    noLossCount: number;
    incompleteReviewCount: number;
    followedPlanRate: number;
    impulsiveTradeCount: number;
    movedStopLossCount: number;
    addedPositionCount: number;
    earlyTakeProfitCount: number;
  };
  setupPatterns: SetupPatternResearchRow[];
  marketContext: {
    trendBuckets: BucketItem[];
    rsiBuckets: BucketItem[];
    volatilityBuckets: BucketItem[];
    change24hBuckets: BucketItem[];
    qualityBuckets: BucketItem[];
  };
  recentObservations: Array<{
    id: number;
    skillName: string;
    symbol: string | null;
    label: string | null;
    score: number | null;
    summary: string;
    explanation: string;
    createdAt: string;
  }>;
}
```

可以按实际代码适当简化，但必须保留核心信息。

---

## Repository Requirements

可以直接复用现有 repository 函数，也可以新增：

```text id="research_repo_functions"
listResearchTrades()
listResearchTradeFeatures()
listResearchSkillResults()
listLatestResearchSkillResults()
```

要求：

1. 只读本地 SQLite。
2. 不修改交易数据。
3. 不删除数据。
4. 不上传数据。
5. 不请求后端。
6. 不调用交易所。
7. 不接 AI。

Research Dashboard 是本地研究聚合层。

---

## Metrics Requirements

新增：

```text id="research_metrics_file"
src/research/researchMetrics.ts
```

至少实现：

1. `buildResearchDashboardData(filters)`
2. `calculateOverview(...)`
3. `calculateDataQuality(...)`
4. `calculateSkillSummary(...)`
5. `calculateDisciplineSummary(...)`
6. `calculateSetupPatterns(...)`
7. `calculateMarketContext(...)`
8. `buildRecentObservations(...)`

要求：

* 缺失数据不崩溃。
* 空数据返回 0 或空数组。
* 百分比保留 0-100。
* 平均值要避免除以 0。
* 所有分桶必须包含 unknown。

---

## UI Requirements

新增页面：

```text id="research_screen"
src/screens/ResearchDashboardScreen.tsx
```

入口：

```text id="research_entry"
我的 -> Research Dashboard
```

页面内容：

1. Header

   * Title: Research Dashboard
   * Subtitle: Historical research view built from trades, features, and skill results.

2. Safety Note

   * “Research Dashboard summarizes historical data. It does not provide trading advice.”

3. Filters

   * time range
   * symbol
   * setup type
   * result type

4. Overview Cards

   * Trades
   * Reviewed
   * Features
   * Skill Results
   * Avg Feature Quality
   * Avg Skill Score
   * Discipline Losses

5. Data Quality Section

6. Skill Results Section

7. Discipline Section

8. Setup Pattern Section

9. Market Context Section

10. Recent Research Observations

UI 可以用现有 `Button`、`StatCard`、`Screen`、`Text`、`View`。

不要新增复杂图表库。

简单 bar 可以用 `View` 宽度模拟，但必须避免复杂样式导致崩溃。

---

## My Screen Integration

更新：

```text id="my_screen_v7"
src/screens/MyScreen.tsx
```

新增入口：

```text id="research_dashboard_entry"
Research Dashboard
```

描述：

```text id="research_dashboard_desc"
聚合交易、Feature 和 Skill Results，查看历史研究模式与数据质量。
```

---

## Trade Detail Integration

V7 不强制修改 Trade Detail。

如果改动很小，可以在 Trade Detail 的 Skill Results 区域增加：

```text id="trade_detail_v7_optional"
View in Research Dashboard
```

但这不是必须项。

---

## Database Impact

V7 默认不需要新增数据库表。

允许不做 migration。

如果确实需要保存 dashboard preference，可以新增轻量表，但第一版不建议。

V7 应优先做只读聚合。

数据库要求：

1. 不删除旧表。
2. 不重建旧表。
3. 不删除旧数据。
4. 不修改 SkillResults。
5. 不修改 TradeFeatures。
6. 不保存 API Key。
7. 不保存交易所私有数据。
8. 不保存账户余额。
9. 不保存持仓。

---

## Safety Rules

V7 必须遵守：

1. No AI.
2. No Signal Engine.
3. No buy/sell advice.
4. No long/short recommendation.
5. No price prediction.
6. No guaranteed outcome.
7. No Trade API.
8. No Withdraw API.
9. No API Key.
10. No order placement.
11. No cancel order.
12. No account balance from exchange.
13. No positions from exchange.
14. No auto trading.
15. Research output is historical observation only.
16. Dashboard result is not a trading command.
17. User always owns final decisions.

禁止出现以下英文文案：

```text id="v7_forbidden_en"
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
```

禁止出现以下中文文案：

```text id="v7_forbidden_cn"
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
```

---

## README Update

更新 `README.md`，新增 V7 说明。

必须写明：

* V7 是 Research Dashboard。
* V7 聚合 Trades、TradeFeatures、SkillResults。
* V7 展示历史研究观察。
* V7 不接真实 AI。
* V7 不生成信号。
* V7 不自动交易。
* V7 不接交易权限。
* V7 不保存 API Key。
* V7 不提供买卖建议。
* V7 默认不需要后端运行。
* V7 默认只读取本地 SQLite。

---

## Testing

完成后运行：

```powershell id="v7_typecheck"
pnpm run typecheck
```

同时运行：

```powershell id="v7_expo_config"
pnpm exec expo config --type public
```

手动检查：

1. App 能启动。
2. 我的页面能进入 Research Dashboard。
3. Research Dashboard 空数据不崩溃。
4. 有交易但无 Feature 时不崩溃。
5. 有 Feature 但无 SkillResults 时不崩溃。
6. 有 SkillResults 时能显示 latest run summary。
7. Filters 能切换。
8. Overview Cards 数据合理。
9. Data Quality Section 显示合理。
10. Skill Results Section 显示合理。
11. Discipline Section 显示合理。
12. Setup Pattern Section 显示合理。
13. Market Context Section 显示合理。
14. Recent Research Observations 显示合理。
15. Skill Engine 页面仍然正常。
16. Data Quality 页面仍然正常。
17. Quick Trade 仍然正常。
18. Trade Detail 仍然正常。
19. 没有交易建议文案。
20. 没有 AI / Signal / Trading API 相关实现。

如果未修改后端 `api/`，不需要运行：

```powershell id="v7_backend_check"
python -m compileall api
```

---

## Acceptance Criteria

V7 完成后必须满足：

1. `src/research/` 目录存在。
2. `researchTypes.ts` 存在。
3. `researchMetrics.ts` 存在。
4. `ResearchDashboardScreen.tsx` 存在。
5. My 页面有 Research Dashboard 入口。
6. Research Dashboard 可以加载本地数据。
7. 空数据不崩溃。
8. Overview Cards 存在。
9. Data Quality Section 存在。
10. Skill Results Section 存在。
11. Discipline Section 存在。
12. Setup Pattern Section 存在。
13. Market Context Section 存在。
14. Recent Research Observations 存在。
15. 至少支持 timeRange 筛选。
16. 至少支持 symbol 筛选。
17. 至少支持 setupType 筛选。
18. 至少支持 resultType 筛选。
19. Latest run 和 all-time summary 明确区分。
20. 不新增数据库表，或如新增必须 safe migration。
21. README 更新 V7。
22. `pnpm run typecheck` 通过。
23. `pnpm exec expo config --type public` 通过。
24. 不接真实 AI。
25. 不做 Signal Engine。
26. 不生成买卖建议。
27. 不接 Trade API。
28. 不接 Withdraw API。
29. 不保存 API Key。
30. 不自动下单。

---

## Self Review Output

完成后请输出：

1. 修改文件列表
2. 新增文件列表
3. Research Dashboard 页面说明
4. My 页面入口说明
5. 数据源说明
6. 聚合指标说明
7. 筛选器说明
8. 空数据处理说明
9. 是否新增数据库 migration
10. README 更新说明
11. 安全边界确认
12. `pnpm run typecheck` 结果
13. `pnpm exec expo config --type public` 结果
14. 后端是否改动
15. 已知问题
16. 下一步 V8 建议

---

## Future Compatibility

V7 必须为 V8/V9 做准备：

* V8 Backtesting 可以复用 Research Dashboard 的 setup / feature / skill 聚合逻辑。
* V9 Signal Engine 可以参考 Research Dashboard 的历史研究结果，但 V7 不实现 Signal。
* 未来 AI Copilot 可以读取 Research Dashboard 的聚合结果，但 V7 不接 AI。
* V10 Live Data Collection 可以把更多实时数据接入研究看板，但 V7 不做 live pipeline。

V7 只完成 Research Dashboard。
