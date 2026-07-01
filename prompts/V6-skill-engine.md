# AlphaPilot V6 - Skill Engine

## Goal

基于当前 `trade-discipline-journal` V5.1 版本，开发 AlphaPilot V6：Skill Engine。

V6 的目标不是 AI，不是信号，不是自动交易。

V6 的目标是：

把交易研究方法、复盘方法、Feature 分析方法封装成可复用、可版本化、可解释的 Skill。

Skill Engine 是 AlphaPilot 未来 AI Copilot、Research Dashboard、Backtesting、Signal Engine 的基础。

V6 要让 AlphaPilot 从“能生成 Feature”升级为：

可以把 Feature 输入到一组可复用研究 Skill 中，得到结构化、可解释、非交易建议的研究输出。

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

当前已经有：

* `api/main.py`
* `api/market_service.py`
* `api/feature_engine.py`
* `src/feature-engine/featureEngine.ts`
* `src/services/featureApiService.ts`
* `src/screens/DataQualityScreen.tsx`
* SQLite 表：`TradeFeatures`

V6 必须基于这些继续开发，不要推翻之前版本。

---

## Scope

V6 只做 Skill Engine。

包含：

1. 新增 Skill 定义结构。
2. 新增 Skill 执行器。
3. 新增内置基础 Skills。
4. Skills 输入来自 `TradeFeatures`、`Trades`、`TradeAnalysis`、`TradeSnapshots`。
5. Skill 输出必须结构化。
6. Skill 输出必须可解释。
7. Skill 输出必须保存到本地 SQLite。
8. 新增 Skill Results 数据表。
9. 新增 Skill 页面或入口。
10. Data Quality / My 页面可以进入 Skill Engine。
11. README 更新 V6 说明。
12. 保持 V1.1/V2/V3/V4/V5 功能不被破坏。

---

## Non Goals

V6 不做以下内容：

1. 不接真实 AI。
2. 不接 OpenAI / Claude / Gemini。
3. 不做 Signal Engine。
4. 不生成买卖信号。
5. 不生成交易建议。
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
17. 不预测未来价格。
18. 不承诺收益。
19. 不推荐用户买入、卖出、做多、做空。

---

## Product Principle

V6 必须遵循 AlphaPilot 的核心原则：

1. Data before AI.
2. Research before automation.
3. Risk above signal.
4. AI never owns money.
5. Everything is feature.
6. Explainability before black-box output.
7. Evidence before opinion.

V6 只负责把研究方法封装成 Skill。

Skill 只能输出研究观察和质量评分。

Skill 不能输出交易指令。

---

## Skill Engine Concept

Skill 是一个可复用研究模块。

每个 Skill 必须有：

* id
* name
* version
* description
* category
* input schema
* output schema
* safety level
* explanation
* run function

Skill 的作用是：

把已有交易数据和 Feature 数据转成结构化研究结果。

Skill 不负责下单。

Skill 不负责预测。

Skill 不负责推荐买卖。

---

## Recommended Architecture

推荐目录结构：

```text id="v6_structure"
src/
  skill-engine/
    skillTypes.ts
    skillRegistry.ts
    skillRunner.ts
    builtInSkills.ts
    skillResultMapper.ts

  screens/
    SkillEngineScreen.tsx

  db/
    database.ts
    repositories.ts

  types.ts
```

说明：

* `skillTypes.ts` 定义 Skill、SkillInput、SkillOutput、SkillResult。
* `skillRegistry.ts` 管理所有 Skill。
* `skillRunner.ts` 负责执行 Skill。
* `builtInSkills.ts` 放内置 Skills。
* `skillResultMapper.ts` 负责把 Skill 输出映射为数据库记录。
* `SkillEngineScreen.tsx` 展示 Skill 列表、运行结果和最近结果。

---

## Database Requirements

新增 SQLite 表：

```sql id="skill_results_table"
CREATE TABLE IF NOT EXISTS SkillResults (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  skillId TEXT NOT NULL,
  skillName TEXT NOT NULL,
  skillVersion TEXT NOT NULL,
  tradeId INTEGER,
  symbol TEXT,
  category TEXT NOT NULL,
  score REAL,
  label TEXT,
  summary TEXT NOT NULL,
  explanation TEXT NOT NULL,
  evidenceJson TEXT NOT NULL,
  outputJson TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL,
  FOREIGN KEY (tradeId) REFERENCES Trades(id)
);
```

新增索引：

```sql id="skill_results_indexes"
CREATE INDEX IF NOT EXISTS idx_skill_results_skill ON SkillResults(skillId);
CREATE INDEX IF NOT EXISTS idx_skill_results_trade ON SkillResults(tradeId);
CREATE INDEX IF NOT EXISTS idx_skill_results_symbol ON SkillResults(symbol);
CREATE INDEX IF NOT EXISTS idx_skill_results_created ON SkillResults(createdAt);
```

数据库要求：

1. 使用 `CREATE TABLE IF NOT EXISTS`。
2. 不删除旧表。
3. 不重建旧表。
4. 不删除旧数据。
5. 不破坏 `TradeFeatures`。
6. 不破坏 `Trades`。
7. 不保存 API Key。
8. 不保存交易所私有数据。
9. 不保存账户余额。
10. 不保存持仓。

如果需要更新 `PRAGMA user_version`，更新为：

```text id="v6_user_version"
PRAGMA user_version = 4;
```

---

## TypeScript Types

在 `src/types.ts` 中新增或扩展：

```text id="skill_types"
export type SkillCategory =
  | 'entry_quality'
  | 'risk'
  | 'discipline'
  | 'market_context'
  | 'review'
  | 'research';

export type SkillSeverity =
  | 'info'
  | 'warning'
  | 'danger'
  | 'success';

export interface SkillResult {
  id: number;
  skillId: string;
  skillName: string;
  skillVersion: string;
  tradeId: number | null;
  symbol: string | null;
  category: SkillCategory;
  score: number | null;
  label: string | null;
  summary: string;
  explanation: string;
  evidenceJson: string;
  outputJson: string;
  source: string;
  createdAt: string;
}
```

Skill 输出结构建议：

```text id="skill_output"
export interface SkillOutput {
  skillId: string;
  skillName: string;
  skillVersion: string;
  tradeId?: number;
  symbol?: string;
  category: SkillCategory;
  score?: number | null;
  label?: string | null;
  summary: string;
  explanation: string;
  evidence: Record<string, unknown>;
  output: Record<string, unknown>;
  source: string;
}
```

---

## Built-in Skills

V6 至少实现 5 个内置 Skill。

### 1. Entry Quality Skill

ID:

```text id="entry_quality_skill"
entry_quality_v1
```

目标：

评估入场质量。

输入：

* Trade
* TradeFeature
* TradeAnalysis
* TradeSnapshot

参考字段：

* riskPercent
* leverage
* stopLossDistance
* rrRatio
* trend
* rsi
* atr
* marketVolatility
* setupType
* dataQualityScore

输出：

* score: 0-100
* label:

  * excellent
  * good
  * weak
  * risky
  * unknown
* summary
* explanation
* evidence

禁止输出：

* buy
* sell
* long now
* short now
* enter now

---

### 2. Risk Discipline Skill

ID:

```text id="risk_discipline_skill"
risk_discipline_v1
```

目标：

评估交易是否符合风险纪律。

参考字段：

* leverage
* positionSize
* stopLossPrice
* entryPrice
* account risk settings
* maxRiskPerTradePercent
* maxDailyLossPercent

输出：

* score: 0-100
* label:

  * within_rules
  * caution
  * rule_violation
  * unknown
* explanation
* evidence

禁止输出：

* 建议加仓
* 建议补仓
* 建议扩大杠杆

---

### 3. Market Context Skill

ID:

```text id="market_context_skill"
market_context_v1
```

目标：

把 V5 Feature Engine 的结果转成市场状态摘要。

参考字段：

* trend
* rsi
* atr
* macd
* volume
* change24h
* marketVolatility
* dataQualityScore

输出：

* label:

  * trending
  * ranging
  * volatile
  * low_quality_data
  * unknown
* summary
* explanation
* evidence

注意：

只能描述市场状态，不能判断未来涨跌。

---

### 4. Discipline Loss Review Skill

ID:

```text id="discipline_loss_review_skill"
discipline_loss_review_v1
```

目标：

识别纪律亏损相关行为。

参考字段：

* followedPlan
* movedStopLoss
* addedPosition
* earlyTakeProfit
* impulsiveTrade
* lossType
* reviewNote
* nextImprovement

输出：

* label:

  * discipline_loss
  * strategy_loss
  * no_loss
  * incomplete_review
* score
* summary
* explanation
* evidence

用途：

帮助复盘，不责备用户，不刺激交易。

---

### 5. Setup Pattern Skill

ID:

```text id="setup_pattern_skill"
setup_pattern_v1
```

目标：

根据 setupType 和 Feature 记录归类交易形态。

参考字段：

* setupType
* trend
* rsi
* atr
* volume
* marketVolatility
* candlePattern
* finalPnl
* followedPlan

输出：

* label:

  * new_listing_pullback_breakout
  * volume_breakout_previous_high
  * extreme_fear_rebound
  * other
  * unknown
* summary
* explanation
* evidence

注意：

这只是归类，不是信号。

---

## Skill Runner Requirements

新增：

```text id="skill_runner_files"
src/skill-engine/skillRunner.ts
src/skill-engine/skillRegistry.ts
src/skill-engine/builtInSkills.ts
```

Skill Runner 需要支持：

1. `runSkillForTrade(skillId, tradeId)`
2. `runAllSkillsForTrade(tradeId)`
3. `runAllSkillsForAllTrades()`
4. `listAvailableSkills()`

运行流程：

```text id="skill_flow"
Load trade
↓
Load latest TradeFeature
↓
Load latest TradeAnalysis
↓
Load latest TradeSnapshot
↓
Build SkillInput
↓
Run Skill
↓
Create SkillResult
↓
Save to SkillResults
```

如果某些数据缺失：

* 不要报错崩溃
* 输出 `unknown`
* evidence 中记录 missing fields
* summary 中说明数据不足

---

## Repositories

在 `src/db/repositories.ts` 中新增：

```text id="skill_repo_functions"
createSkillResult(input)
listSkillResults(limit?)
listSkillResultsByTradeId(tradeId)
listSkillResultsBySkillId(skillId)
getSkillResultSummary()
```

`getSkillResultSummary()` 返回：

* totalResults
* totalSkills
* latestRunAt
* averageScore
* warningCount
* dangerCount

如果暂时没有 warning/danger，可以根据 label 简单统计。

---

## Skill Engine UI

新增页面：

```text id="skill_screen"
src/screens/SkillEngineScreen.tsx
```

入口：

建议放在：

```text id="skill_entry"
我的 -> Skill Engine
```

页面内容：

1. Header

   * Title: Skill Engine
   * Subtitle: Reusable research skills built on Feature Database.

2. Safety Note

   * “Skills produce research observations, not trading advice.”

3. Summary Cards

   * Available Skills
   * Skill Results
   * Avg Score
   * Latest Run

4. Actions

   * Run All Skills
   * Refresh Results

5. Available Skills List

   * Skill name
   * category
   * description
   * version

6. Recent Skill Results

   * skillName
   * symbol
   * score
   * label
   * summary
   * createdAt

UI 文案必须克制、研究导向。

不要使用刺激交易的文案。

---

## My Screen Integration

更新：

```text id="my_screen_update"
src/screens/MyScreen.tsx
```

新增入口：

```text id="skill_engine_entry"
Skill Engine
```

描述：

```text id="skill_engine_desc"
运行可复用研究技能，分析入场质量、风险纪律、市场状态和复盘行为。
```

---

## Trade Detail Integration

如果当前 `TradeDetailScreen.tsx` 结构允许，添加 Skill Results section。

要求：

* 显示最近与该 trade 相关的 Skill Results。
* 不影响页面加载。
* 如果结果为空，显示：

  * “No skill results yet.”
* 不强制实现复杂筛选。

如果改动风险大，可以只在 Skill Engine 页面展示，Trade Detail 集成留到 V6.1。

---

## Safety Rules

V6 必须遵守：

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
12. No account balance.
13. No positions.
14. No auto trading.
15. Skill output is research observation only.
16. Skill result is not a trading command.
17. User always owns final decisions.

Skill 输出中禁止出现：

```text id="forbidden_phrases"
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
```

中文也禁止：

```text id="forbidden_cn"
建议买入
建议卖出
立即做多
立即做空
稳赚
必涨
必跌
强烈买入
强烈卖出
```

---

## README Update

更新 `README.md`，新增 V6 说明：

必须写明：

* V6 是 Skill Engine
* Skill 是可复用研究模块
* Skill 输入来自 Feature Database 和交易记录
* Skill 输出是研究观察，不是交易建议
* V6 不接真实 AI
* V6 不生成信号
* V6 不自动交易
* V6 不接交易权限
* V6 不保存 API Key

---

## Testing

完成后运行：

```powershell id="v6_typecheck"
pnpm run typecheck
```

如果有后端文件未改，可以不运行后端检查。

如果修改了 api，则运行：

```powershell id="v6_backend_check"
python -m compileall api
```

手动检查：

1. App 能启动。
2. 我的页面能进入 Skill Engine。
3. Skill Engine 页面能显示内置 Skills。
4. 点击 Run All Skills 不崩溃。
5. SkillResults 表有数据。
6. 没有交易建议文案。
7. Data Quality 页面仍然正常。
8. Quick Trade 仍然正常。
9. Review 仍然正常。

---

## Acceptance Criteria

V6 完成后必须满足：

1. `src/skill-engine/` 目录存在。
2. `skillTypes.ts` 存在。
3. `skillRegistry.ts` 存在。
4. `skillRunner.ts` 存在。
5. `builtInSkills.ts` 存在。
6. 至少 5 个内置 Skills。
7. `SkillResults` SQLite 表存在。
8. `SkillResults` 使用 safe migration。
9. Repository 层可以保存 SkillResult。
10. Repository 层可以读取 SkillResult。
11. My 页面有 Skill Engine 入口。
12. Skill Engine 页面存在。
13. 可以运行全部 Skills。
14. 可以显示最近 Skill Results。
15. 缺失数据时不崩溃。
16. 缺失数据会输出 unknown 或 low data quality。
17. Skill 输出包含 explanation。
18. Skill 输出包含 evidenceJson。
19. README 更新 V6。
20. `pnpm run typecheck` 通过。
21. 不接真实 AI。
22. 不做 Signal Engine。
23. 不生成买卖建议。
24. 不接 Trade API。
25. 不接 Withdraw API。
26. 不保存 API Key。
27. 不自动下单。

---

## Self Review Output

完成后请输出：

1. 修改文件列表
2. 新增文件列表
3. 新增数据库表说明
4. 新增 Skill 列表
5. 每个 Skill 的输入输出说明
6. Skill Runner 数据流说明
7. Skill Engine 页面说明
8. My 页面入口说明
9. Trade Detail 是否集成
10. 安全边界确认
11. `pnpm run typecheck` 结果
12. 后端是否改动
13. 已知问题
14. 下一步 V7 建议

---

## Future Compatibility

V6 必须为 V7/V8/V9 做准备：

* V7 Research Dashboard 可以聚合 Skill Results。
* V8 Backtesting 可以复用 Skill 定义。
* V9 Signal Engine 可以参考 Skill Results，但 V6 不实现 Signal。
* 未来 AI Copilot 可以读取 Skill Results，但 V6 不接 AI。

V6 只完成 Skill Engine。
