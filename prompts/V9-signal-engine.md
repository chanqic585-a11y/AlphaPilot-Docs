# AlphaPilot V9 - Signal Engine Research Boundary

## Goal

基于当前 `trade-discipline-journal` V8.1 版本，开发 AlphaPilot V9：Signal Engine Research Boundary。

V9 的目标不是自动交易，不是实盘喊单，不是买卖建议。

V9 的目标是：

把 `TradeFeatures`、`SkillResults`、`BacktestResults` 和 Research Dashboard 的聚合结果组合起来，生成结构化、可解释、可追溯的 Research Signal Candidates。

V9 要让 AlphaPilot 从：

```text
可以做 Feature、Skill、Research Dashboard、Backtesting
```

升级为：

```text
可以生成研究候选事件，并说明证据、风险、历史上下文和数据质量
```

重要边界：

```text
Signal Candidate ≠ Trading Advice
Signal Candidate ≠ Order
Signal Candidate ≠ Auto Trading
Signal Candidate ≠ Live Trade Signal
```

V9 只做研究候选事件。

V9 不做交易执行。

V9 不创建真实交易。

V9 不推荐用户买入、卖出、做多、做空。

---

## Current State

当前项目已经完成：

* V1.1：交易纪律、风险规则、Initial Setup、5x 杠杆限制
* V2：Quick Trade、Trade Detail、Mock Analysis、Snapshot、Timeline
* V3：Feature Database、TradeFeatures 表、Data Quality 页面、CSV 导出
* V4：FastAPI 后端、CCXT、OKX 公共行情接口
* V4.1：CCXT unified public methods
* V5：Python Feature Engine
* V5.1：change1h / change4h timeframe 语义修正
* V6：Skill Engine
* V6.1：SkillResults runGroupId
* V7：Research Dashboard
* V8：Backtesting Research Lab
* V8.1：BacktestResults 接入 Research Dashboard

当前已有本地数据表：

* Trades
* TradeFeatures
* SkillResults
* BacktestResults
* TradeAnalysis
* TradeSnapshots
* AccountSettings

当前已有核心模块：

* `src/feature-engine/`
* `src/skill-engine/`
* `src/backtesting/`
* `src/research/`
* `src/screens/ResearchDashboardScreen.tsx`
* `src/screens/BacktestingScreen.tsx`
* `src/db/database.ts`
* `src/db/repositories.ts`

V9 必须基于当前架构继续开发，不要推翻之前版本。

---

## Scope

V9 只做 Signal Engine 的研究边界版本。

包含：

1. 新增 Signal Candidate 类型。
2. 新增 SignalCandidates SQLite 表。
3. 新增本地 Signal Engine。
4. 新增本地 research candidate rules。
5. 从本地 `TradeFeatures`、`SkillResults`、`BacktestResults` 生成 Signal Candidates。
6. 每个 Signal Candidate 必须包含 evidence。
7. 每个 Signal Candidate 必须包含 risk notes。
8. 每个 Signal Candidate 必须包含 feature context。
9. 每个 Signal Candidate 必须包含 skill context。
10. 每个 Signal Candidate 必须包含 backtest context。
11. 每个 Signal Candidate 可以包含 confidenceScore，但它只能代表研究完整度，不代表盈利概率。
12. 新增 Signal Engine 页面。
13. My 页面新增 Signal Engine 入口。
14. Research Dashboard 新增 Signal Candidate Research section。
15. README 更新 V9 说明。
16. 保持 V1.1～V8.1 功能不被破坏。

---

## Non Goals

V9 不做以下内容：

1. 不接真实 AI。
2. 不接 OpenAI / Claude / Gemini。
3. 不调用后端生成 signal。
4. 不请求交易所。
5. 不做自动交易。
6. 不做 Paper Trading。
7. 不做 Semi-Auto Trading。
8. 不接 Trade API。
9. 不接 Withdraw API。
10. 不保存 API Key。
11. 不读取真实账户余额。
12. 不读取真实持仓。
13. 不下单。
14. 不撤单。
15. 不创建真实交易。
16. 不自动创建 planned trade。
17. 不推送实时交易通知。
18. 不告诉用户下一笔交易怎么做。
19. 不预测未来价格。
20. 不承诺收益。
21. 不把 Signal Candidate 命名为交易信号。
22. 不把 Signal Candidate 命名为入场指令。
23. 不把 Signal Candidate 命名为买卖建议。

---

## Product Principle

V9 必须遵循 AlphaPilot 的核心原则：

1. Data before AI.
2. Research before automation.
3. Risk above signal.
4. AI never owns money.
5. Everything is feature.
6. Explainability before black-box output.
7. Evidence before opinion.

V9 新增原则：

```text
Signal is research context.
Signal is not advice.
Signal is not execution.
Signal must be explainable.
Signal must be rejectable.
Signal must include risk notes.
Signal must include data quality.
```

---

## Signal Engine Concept

V9 中统一使用以下命名：

```text
Signal Candidate
Research Signal Candidate
Research Candidate
```

不要使用容易误导用户的命名，例如：

```text
Buy Signal
Sell Signal
Long Signal
Short Signal
Entry Signal
Trading Signal
Auto Signal
Execution Signal
```

Signal Candidate 的定义：

```text
当 Feature、Skill Results、Backtest Results 同时满足某些研究条件时，记录一个值得进一步观察和复盘的研究候选事件。
```

Signal Candidate 只能回答：

```text
这是不是一个值得进一步研究的候选事件？
证据是什么？
数据质量如何？
历史回测上下文是什么？
相关 Skill 结果是什么？
风险说明是什么？
为什么它只是 research-only？
```

Signal Candidate 不能回答：

```text
现在是否应该买入？
现在是否应该卖出？
应该开多少仓？
应该用几倍杠杆？
下一笔交易该怎么做？
```

---

## Recommended Architecture

新增目录：

```text
src/
  signal-engine/
    signalTypes.ts
    signalRules.ts
    signalRunner.ts
    signalMapper.ts
    signalRepository.ts

  screens/
    SignalEngineScreen.tsx
```

说明：

* `signalTypes.ts` 定义 Signal Candidate 类型。
* `signalRules.ts` 定义本地研究候选规则。
* `signalRunner.ts` 执行 candidate 生成流程。
* `signalMapper.ts` 把 candidate 映射到 SQLite 写入结构。
* `signalRepository.ts` 封装 SignalCandidates 的读取逻辑；也可以复用 `db/repositories.ts`。
* `SignalEngineScreen.tsx` 展示 Signal Engine 页面。

也需要更新：

```text
src/db/database.ts
src/db/repositories.ts
src/screens/MyScreen.tsx
src/screens/ResearchDashboardScreen.tsx
src/research/researchTypes.ts
src/research/researchRepository.ts
src/research/researchMetrics.ts
README.md
```

---

## Database Requirements

新增 SQLite 表：

```sql
CREATE TABLE IF NOT EXISTS SignalCandidates (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  signalId TEXT NOT NULL,
  signalVersion TEXT NOT NULL,
  runGroupId TEXT NOT NULL,
  symbol TEXT NOT NULL,
  marketType TEXT,
  direction TEXT,
  candidateType TEXT NOT NULL,
  status TEXT NOT NULL,
  confidenceScore REAL,
  qualityScore REAL,
  riskLevel TEXT NOT NULL,
  evidenceJson TEXT NOT NULL,
  backtestContextJson TEXT NOT NULL,
  skillContextJson TEXT NOT NULL,
  featureContextJson TEXT NOT NULL,
  riskNotesJson TEXT NOT NULL,
  summary TEXT NOT NULL,
  explanation TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

新增索引：

```sql
CREATE INDEX IF NOT EXISTS idx_signal_candidates_signal ON SignalCandidates(signalId);
CREATE INDEX IF NOT EXISTS idx_signal_candidates_run ON SignalCandidates(runGroupId);
CREATE INDEX IF NOT EXISTS idx_signal_candidates_symbol ON SignalCandidates(symbol);
CREATE INDEX IF NOT EXISTS idx_signal_candidates_type ON SignalCandidates(candidateType);
CREATE INDEX IF NOT EXISTS idx_signal_candidates_status ON SignalCandidates(status);
CREATE INDEX IF NOT EXISTS idx_signal_candidates_risk ON SignalCandidates(riskLevel);
CREATE INDEX IF NOT EXISTS idx_signal_candidates_created ON SignalCandidates(createdAt);
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
9. 不保存 API Key。
10. 不保存交易所私有数据。
11. 不保存真实账户数据。

如果更新 user_version，使用：

```text
PRAGMA user_version = 7;
```

---

## Signal Candidate Types

在 `src/signal-engine/signalTypes.ts` 中定义：

```ts
export type SignalCandidateType =
  | 'trend_pullback_research'
  | 'mean_reversion_research'
  | 'volatility_breakout_research'
  | 'setup_replay_research'
  | 'discipline_review_research';

export type SignalCandidateStatus =
  | 'research_only'
  | 'needs_more_data'
  | 'rejected_by_risk'
  | 'archived';

export type SignalRiskLevel =
  | 'low'
  | 'medium'
  | 'high'
  | 'unknown';

export interface SignalCandidate {
  id: number;
  signalId: string;
  signalVersion: string;
  runGroupId: string;
  symbol: string;
  marketType: string | null;
  direction: string | null;
  candidateType: SignalCandidateType;
  status: SignalCandidateStatus;
  confidenceScore: number | null;
  qualityScore: number | null;
  riskLevel: SignalRiskLevel;
  evidenceJson: string;
  backtestContextJson: string;
  skillContextJson: string;
  featureContextJson: string;
  riskNotesJson: string;
  summary: string;
  explanation: string;
  source: string;
  createdAt: string;
}
```

新增输入输出类型：

```ts
export interface SignalCandidateDraft {
  signalId: string;
  signalVersion: string;
  runGroupId: string;
  symbol: string;
  marketType: string | null;
  direction: string | null;
  candidateType: SignalCandidateType;
  status: SignalCandidateStatus;
  confidenceScore: number | null;
  qualityScore: number | null;
  riskLevel: SignalRiskLevel;
  evidence: Record<string, unknown>;
  backtestContext: Record<string, unknown>;
  skillContext: Record<string, unknown>;
  featureContext: Record<string, unknown>;
  riskNotes: string[];
  summary: string;
  explanation: string;
  source: string;
}

export interface SignalRunSummary {
  latestRunGroupId: string | null;
  totalCandidates: number;
  latestRunCandidates: number;
  averageConfidenceScore: number;
  needsMoreDataCount: number;
  rejectedByRiskCount: number;
  highRiskCount: number;
  latestRunAt: string | null;
}
```

---

## Built-in Research Candidate Rules

V9 至少实现 4 个 research candidate rules。

所有规则都必须是本地研究规则。

所有规则都只能生成研究候选事件。

所有规则都不能生成交易建议。

---

### 1. Trend Pullback Candidate

ID:

```text
trend_pullback_candidate_v1
```

Candidate Type:

```text
trend_pullback_research
```

参考数据：

* TradeFeatures:

  * symbol
  * marketType
  * direction
  * trend
  * rsi
  * ema
  * macd
  * atr
  * marketVolatility
  * dataQualityScore
  * source
* SkillResults:

  * entry_quality_v1
  * market_context_v1
* BacktestResults:

  * ema_trend_pullback_v1

候选条件示例：

* feature quality >= 60
* trend is not unknown
* market context skill is available
* related backtest exists
* related backtest tradeCount >= 5

输出要求：

* confidenceScore
* qualityScore
* riskLevel
* evidence
* featureContext
* skillContext
* backtestContext
* riskNotes
* summary
* explanation

Summary 文案必须研究导向，例如：

```text
Trend pullback research candidate found from feature, skill, and backtest context.
```

不能使用任何交易执行或交易建议文案。

---

### 2. Mean Reversion Candidate

ID:

```text
mean_reversion_candidate_v1
```

Candidate Type:

```text
mean_reversion_research
```

参考数据：

* TradeFeatures:

  * rsi
  * trend
  * marketVolatility
  * dataQualityScore
* SkillResults:

  * market_context_v1
* BacktestResults:

  * rsi_mean_reversion_v1

候选条件示例：

* rsi exists
* rsi is in an extreme or near-extreme research zone
* feature quality >= 60
* related backtest exists
* related backtest sample is not empty

说明：

RSI 只用于研究上下文，不能被解释为实时交易动作。

允许文案：

```text
RSI condition matched the historical research template context.
```

不允许把 RSI 条件解释成实时买入或卖出动作。

---

### 3. Volatility Breakout Candidate

ID:

```text
volatility_breakout_candidate_v1
```

Candidate Type:

```text
volatility_breakout_research
```

参考数据：

* TradeFeatures:

  * marketVolatility
  * change24h
  * volume
  * setupType
  * dataQualityScore
* SkillResults:

  * setup_pattern_v1
  * market_context_v1
* BacktestResults:

  * volatility_breakout_research_v1

候选条件示例：

* marketVolatility exists
* volume exists
* related backtest exists
* setup or skill context supports breakout research category
* data quality is not too low

输出：

* volatility evidence
* setup evidence
* backtest sample context
* risk notes
* research-only explanation

---

### 4. Setup Replay Candidate

ID:

```text
setup_replay_candidate_v1
```

Candidate Type:

```text
setup_replay_research
```

参考数据：

* TradeFeatures:

  * setupType
  * setupConfidence
  * dataQualityScore
  * finalPnl
  * followedPlan
* SkillResults:

  * setup_pattern_v1
  * discipline_loss_review_v1
* BacktestResults:

  * setup_pattern_replay_v1

候选条件示例：

* setupType is not unknown
* feature quality >= 50
* setup pattern skill exists
* related backtest exists or missing backtest is clearly noted

如果缺少 backtest：

* 可以生成 `needs_more_data`
* riskNotes 需要说明缺少 backtest context
* confidenceScore 应降低

---

### 5. Discipline Review Candidate

ID:

```text
discipline_review_candidate_v1
```

Candidate Type:

```text
discipline_review_research
```

参考数据：

* TradeFeatures:

  * isDisciplineLoss
  * followedPlan
  * finalPnl
  * dataQualityScore
* SkillResults:

  * risk_discipline_v1
  * discipline_loss_review_v1
* Trades:

  * lossType
  * impulsiveTrade
  * movedStopLoss
  * addedPosition
  * earlyTakeProfit

候选条件示例：

* discipline loss evidence exists
* risk discipline skill has caution or violation label
* review quality has missing fields or incomplete review

说明：

这个 candidate 只用于复盘纪律问题，不用于触发新交易。

---

## Candidate Scoring

Signal Candidate 可以有 `confidenceScore`，但必须解释清楚：

```text
confidenceScore is a research completeness score, not a probability of profit.
```

建议评分来源：

```text
Feature quality: 0-30
Skill context quality: 0-25
Backtest sample quality: 0-25
Risk notes completeness: 0-20
```

总分：0-100。

如果数据不足：

* status = `needs_more_data`
* riskLevel = `unknown`
* confidenceScore 应降低
* riskNotesJson 必须记录缺失项

如果风险条件不合格：

* status = `rejected_by_risk`
* 不删除 candidate
* 保留原因用于研究

---

## Quality Score

`qualityScore` 可以基于：

* TradeFeature dataQualityScore
* SkillResults 是否存在
* BacktestResults 是否存在
* Backtest tradeCount
* Backtest sample quality
* JSON 解析是否成功

要求：

1. 不伪造数据。
2. 缺失数据必须写入 riskNotes。
3. 缺失数据不能用默认好结果替代。
4. 质量不足时使用 `needs_more_data`。

---

## Risk Level

Risk level 建议规则：

```text
low:
  qualityScore >= 75
  backtest exists
  risk notes count <= 3

medium:
  qualityScore >= 50
  has some missing context

high:
  data quality low
  backtest drawdown high
  discipline risk exists

unknown:
  key data missing
```

注意：

`low` 不是低交易风险。

`low` 只表示研究数据上下文相对完整。

UI 必须说明：

```text
Risk level describes research context limitations, not trade safety.
```

---

## Risk Notes

每个 Signal Candidate 必须包含 `riskNotesJson`。

至少包含：

* historical research only
* not trading advice
* not an order
* no execution permission
* backtest sample limitation
* data quality limitation
* user owns all decisions

示例：

```json
[
  "Historical research candidate only.",
  "Backtest result describes selected sample only.",
  "This is not trading advice.",
  "No order will be created from this candidate.",
  "User owns all final decisions."
]
```

---

## Data Flow

```text
User opens Signal Engine
↓
Tap Run Signal Research
↓
Load local TradeFeatures
↓
Load local SkillResults
↓
Load local BacktestResults
↓
Apply local research candidate rules
↓
Generate SignalCandidate drafts
↓
Save SignalCandidates to SQLite
↓
Show latest run summary and recent candidates
```

要求：

1. 不请求交易所。
2. 不请求后端。
3. 不接 AI。
4. 不下单。
5. 不创建 trade。
6. 只读取本地 SQLite。
7. 生成的是研究候选事件。
8. 失败时 App 不崩溃。

---

## Repository Requirements

在 `src/db/repositories.ts` 中新增：

```ts
createSignalCandidate(input)
createSignalCandidates(inputs)
listSignalCandidates(limit?)
listSignalCandidatesByRunGroupId(runGroupId)
listLatestSignalCandidates(limit?)
getLatestSignalRunSummary()
```

`getLatestSignalRunSummary()` 至少返回：

* latestRunGroupId
* totalCandidates
* latestRunCandidates
* averageConfidenceScore
* needsMoreDataCount
* rejectedByRiskCount
* highRiskCount
* latestRunAt

---

## Signal Mapper Requirements

新增：

```text
src/signal-engine/signalMapper.ts
```

负责：

1. 把 `SignalCandidateDraft` 转成 repository input。
2. 将 evidence / context / riskNotes 序列化为 JSON。
3. 确保空数组和空对象可以安全保存。
4. 不丢失 source / createdAt / runGroupId。

---

## Signal Runner Requirements

新增：

```text
src/signal-engine/signalRunner.ts
```

至少提供：

```ts
runSignalResearch()
listAvailableSignalRules()
```

`runSignalResearch()` 返回：

```ts
{
  runGroupId: string;
  totalCandidates: number;
  createdCandidates: number;
  needsMoreDataCount: number;
  rejectedByRiskCount: number;
}
```

要求：

1. 每次运行生成统一 runGroupId。
2. 同一批 candidates 使用相同 runGroupId。
3. 不删除旧 candidates。
4. 最新页面优先展示最新 runGroupId 的 candidates。
5. 没有足够数据时返回空结果或 needs_more_data candidates，但不能崩溃。

---

## Signal Engine UI

新增页面：

```text
src/screens/SignalEngineScreen.tsx
```

入口：

```text
我的 -> Signal Engine
```

页面内容：

### Header

Title:

```text
Signal Engine
```

Subtitle:

```text
Research candidates built from features, skills, and backtests.
```

### Safety Note

必须显示：

```text
Signal candidates are research context only. They are not trading advice, orders, or automation.
```

### Summary Cards

* Total Candidates
* Latest Run Candidates
* Avg Confidence
* Needs More Data
* Rejected By Risk
* High Risk

### Actions

* Run Signal Research
* Refresh Candidates

### Candidate List

每个 candidate 显示：

* candidateType
* symbol
* confidenceScore
* qualityScore
* riskLevel
* status
* summary
* explanation
* createdAt

### Evidence Preview

可以显示：

* feature quality
* related skill labels
* related backtest rule
* risk notes count

### Empty State

如果没有 candidates，显示：

```text
No signal candidates yet. Run Signal Research after generating features, skills, and saved backtests.
```

---

## Research Dashboard Integration

V9 需要把 SignalCandidates 接入 Research Dashboard。

新增 section：

```text
Signal Candidate Research
```

显示：

* total candidates
* latest run candidates
* average confidence
* by candidateType
* by status
* by riskLevel
* recent candidates

必须显示说明：

```text
Signal candidates are research artifacts. They do not trigger trades.
```

Research Dashboard 只能读取本地 SignalCandidates。

Research Dashboard 不运行 signal research。

Research Dashboard 不请求后端。

---

## My Screen Integration

更新：

```text
src/screens/MyScreen.tsx
```

新增入口：

```text
Signal Engine
```

描述：

```text
基于 Feature、Skill 和 Backtest 生成研究候选事件，不执行交易。
```

---

## README Update

更新 `README.md`，新增 V9 说明。

必须写明：

* V9 是 Signal Engine Research Boundary。
* V9 生成 Signal Candidates。
* Signal Candidate 是研究候选事件。
* Signal Candidate 不是买卖建议。
* Signal Candidate 不是交易指令。
* Signal Candidate 不是订单。
* V9 不接 AI。
* V9 不请求交易所。
* V9 不接交易权限。
* V9 不自动下单。
* V9 不读取交易所账户。
* V9 只读取本地 SQLite。
* V9 读取 TradeFeatures、SkillResults、BacktestResults。
* confidenceScore 是研究完整度，不是盈利概率。
* riskLevel 是研究上下文限制，不是交易安全等级。

---

## Database Impact

V9 新增 `SignalCandidates` 表。

要求：

1. safe migration。
2. 不删除旧表。
3. 不删除旧数据。
4. 不修改 BacktestResults。
5. 不修改 SkillResults。
6. 不修改 TradeFeatures。
7. 不修改 Trades。

---

## Safety Rules

V9 必须遵守：

1. No real AI.
2. No OpenAI / Claude / Gemini.
3. No backend signal generation.
4. No exchange request.
5. No Trade API.
6. No Withdraw API.
7. No API Key.
8. No real account balance.
9. No positions.
10. No order placement.
11. No cancel order.
12. No paper trading.
13. No auto trading.
14. No buy or sell advice.
15. No long or short recommendation.
16. No price prediction.
17. No guaranteed outcome.
18. No next-trade recommendation.
19. Signal Candidate is research context only.
20. Signal Candidate must not create trades automatically.
21. User always owns final decisions.

Avoid any UI wording that tells the user to buy, sell, open a position, close a position, use leverage, or expect guaranteed profit.

Allowed wording:

```text
research candidate
signal candidate
historical context
evidence
risk notes
needs more data
not trading advice
not an order
research-only
```

中文允许：

```text
研究候选事件
信号候选
历史上下文
证据
风险说明
需要更多数据
不是交易建议
不是订单
仅用于研究
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
2. 我的页面能进入 Signal Engine。
3. 没有 Feature / Skill / Backtest 数据时不崩溃。
4. 点击 Run Signal Research 不崩溃。
5. 缺少数据时 candidate 状态为 needs_more_data 或不生成，并有说明。
6. 有数据时能生成 SignalCandidates。
7. SignalCandidates 能保存到 SQLite。
8. Signal Engine 页面能显示最新 candidates。
9. Research Dashboard 能显示 Signal Candidate Research section。
10. Backtesting 页面仍然正常。
11. Research Dashboard 原有 sections 仍然正常。
12. Skill Engine 页面仍然正常。
13. Data Quality 页面仍然正常。
14. Quick Trade 仍然正常。
15. Trade Detail 仍然正常。
16. 没有买卖建议文案。
17. 没有交易权限调用。
18. 没有下单相关实现。

---

## Acceptance Criteria

V9 完成后必须满足：

1. `src/signal-engine/` 目录存在。
2. `signalTypes.ts` 存在。
3. `signalRules.ts` 存在。
4. `signalRunner.ts` 存在。
5. `signalMapper.ts` 存在。
6. `SignalCandidates` SQLite 表存在。
7. SignalCandidates 使用 safe migration。
8. 至少 4 个 research candidate rules。
9. Signal Engine 页面存在。
10. My 页面有 Signal Engine 入口。
11. 可以运行 Signal Research。
12. 可以保存 SignalCandidates。
13. 可以显示 candidate summary。
14. 可以显示 recent candidates。
15. 每个 candidate 有 evidenceJson。
16. 每个 candidate 有 backtestContextJson。
17. 每个 candidate 有 skillContextJson。
18. 每个 candidate 有 featureContextJson。
19. 每个 candidate 有 riskNotesJson。
20. 每个 candidate 有 summary 和 explanation。
21. Research Dashboard 显示 Signal Candidate Research section。
22. README 更新 V9。
23. `pnpm run typecheck` 通过。
24. `pnpm exec expo config --type public` 通过。
25. 不接真实 AI。
26. 不请求交易所。
27. 不接交易权限。
28. 不生成买卖建议。
29. 不自动下单。
30. 不创建真实交易。
31. Signal Candidate 明确是 research-only。

---

## Self Review Output

完成后请输出：

1. 修改文件列表
2. 新增文件列表
3. 新增数据库表说明
4. Signal Candidate 类型说明
5. 内置 candidate rules 列表
6. Signal Runner 数据流说明
7. Signal Engine 页面说明
8. Research Dashboard 集成说明
9. My 页面入口说明
10. 安全边界确认
11. 是否修改后端
12. 是否新增 migration
13. `pnpm run typecheck` 结果
14. `pnpm exec expo config --type public` 结果
15. 安全文案扫描结果
16. 已知问题
17. 下一步 V10 建议

---

## Future Compatibility

V9 为 V10/V11/V12 做准备：

* V10 Live Data Collection 可以提供更多实时 feature 输入。
* V11 Paper Trading 可以使用 Signal Candidates 作为研究输入，但 V9 不做 Paper Trading。
* V12 Semi-Auto Trading 可以要求用户手动确认，但 V9 不做半自动交易。
* 未来 AI Copilot 可以解释 Signal Candidates，但 V9 不接 AI。

V9 只完成 Signal Engine Research Boundary。
