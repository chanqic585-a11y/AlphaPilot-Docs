# AlphaPilot V12 - Semi-Auto Trading Design Boundary

## Goal

基于当前 `trade-discipline-journal` V11.1 版本，开发 AlphaPilot V12：Semi-Auto Trading Design Boundary。

V12 是整个 AlphaPilot 路线中第一个接近“真实交易权限”的阶段，因此必须极度谨慎。

V12 的目标不是直接接真实交易所，不是直接保存 API Key，不是直接下真实订单。

V12 的目标是：

建立未来 Semi-Auto Trading 所需的安全边界、权限模型、风控流程、人工确认流程、审计链路和 dry-run 执行框架。

V12 要让 AlphaPilot 从：

```text
可以做 Paper Trading Lab 和本地模拟账户核算
```

升级为：

```text
可以生成 Execution Intent，完成 Pre-Trade Risk Check，进入 Manual Approval Workflow，并最终只执行 Paper/Dry-Run Simulation，不执行真实交易
```

V12 的最终边界：

```text
Execution Intent ≠ Real Order
Manual Approval ≠ Exchange Execution
Dry Run ≠ Live Trading
Semi-Auto Design ≠ Auto Trading
```

V12 只做设计边界和模拟执行链路。

V12 不接真实交易权限。

V12 不保存交易所 API Key。

V12 不读取真实账户余额。

V12 不读取真实持仓。

V12 不调用交易所 private endpoints。

V12 不下真实订单。

V12 不撤真实订单。

V12 不自动交易。

V12 不生成买卖建议。

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
* V10：Live Data Collection
* V11：Paper Trading Lab
* V11.1：Paper Trading Accounting Polish

当前已有本地 SQLite 表：

```text
Trades
TradeFeatures
SkillResults
BacktestResults
SignalCandidates
LiveCollectionRuns
MarketDataSnapshots
PaperAccounts
PaperOrders
PaperPositions
PaperFills
PaperEquitySnapshots
```

当前已有核心模块：

```text
src/feature-engine/
src/skill-engine/
src/backtesting/
src/research/
src/signal-engine/
src/live-data/
src/paper-trading/
src/screens/ResearchDashboardScreen.tsx
src/screens/SignalEngineScreen.tsx
src/screens/LiveDataCollectionScreen.tsx
src/screens/PaperTradingScreen.tsx
src/db/database.ts
src/db/repositories.ts
```

V12 必须基于当前架构继续开发，不要推翻之前版本。

---

## V12 Product Definition

V12 是：

```text
Semi-Auto Trading Design Boundary
```

它不是：

```text
Live Trading
Real Exchange Execution
Auto Trading
Trade API Integration
API Key Storage
Real Account Sync
```

V12 允许做：

1. Execution Intent 数据模型。
2. Pre-Trade Risk Check。
3. Manual Approval Workflow。
4. Approval Checklist。
5. Dry-Run / Paper Execution handoff。
6. Execution Audit Log。
7. Permission Boundary UI。
8. Research Dashboard integration。
9. README 安全边界说明。

V12 不允许做：

1. 不允许真实交易所 API Key 输入框。
2. 不允许保存 API Key。
3. 不允许调用 CCXT private methods。
4. 不允许调用 `createOrder`。
5. 不允许调用 `cancelOrder`。
6. 不允许调用 `fetchBalance`。
7. 不允许调用 `fetchPositions`。
8. 不允许读取真实账户。
9. 不允许读取真实持仓。
10. 不允许创建真实订单。
11. 不允许撤销真实订单。
12. 不允许自动下单。
13. 不允许把 SignalCandidate 变成交易建议。
14. 不允许把 Execution Intent 变成真实订单。

---

## Scope

V12 只做 Semi-Auto Trading 的设计边界版。

包含：

1. 新增 Execution Intent 类型。
2. 新增 Pre-Trade Risk Check 类型。
3. 新增 Manual Approval 类型。
4. 新增 Execution Audit Event 类型。
5. 新增本地 SQLite 表：

   * ExecutionIntents
   * PreTradeRiskChecks
   * ManualApprovals
   * ExecutionAuditEvents
   * ExecutionDryRuns
6. 新增 Execution Boundary / Semi-Auto Design 页面。
7. My 页面新增 Semi-Auto Design 入口。
8. 可以从 SignalCandidate、PaperPosition、BacktestResult 或用户手动输入创建 Execution Intent。
9. 创建 Execution Intent 必须是用户手动操作。
10. Execution Intent 必须经过 Pre-Trade Risk Check。
11. Pre-Trade Risk Check 必须包含硬性风险规则。
12. Manual Approval 必须包含多项确认 checklist。
13. Manual Approval 通过后，只能触发 Paper/Dry-Run Simulation。
14. Dry-Run Simulation 可以复用 V11 Paper Trading 的模拟订单逻辑。
15. Dry-Run 不能调用真实交易所。
16. Research Dashboard 新增 Semi-Auto Design / Execution Intent Research 区块。
17. README 更新 V12 说明。
18. 保持 V1.1～V11.1 功能不被破坏。

---

## Non Goals

V12 不做以下内容：

1. 不接真实 AI。
2. 不接 OpenAI / Claude / Gemini。
3. 不接交易所私有接口。
4. 不接 Trade API。
5. 不接 Withdraw API。
6. 不保存 API Key。
7. 不保存 API Secret。
8. 不保存 Passphrase。
9. 不读取真实账户余额。
10. 不读取真实持仓。
11. 不读取真实订单。
12. 不下真实订单。
13. 不撤真实订单。
14. 不修改真实订单。
15. 不做实盘成交。
16. 不做 Paper Trading 之外的真实执行。
17. 不做 Semi-Auto live execution。
18. 不做 Auto Trading。
19. 不做后台自动交易。
20. 不做高频交易。
21. 不做信号推送下单。
22. 不从 SignalCandidate 自动创建真实订单。
23. 不从 ExecutionIntent 自动创建真实订单。
24. 不告诉用户下一笔真实交易怎么做。
25. 不预测未来价格。
26. 不承诺收益。
27. 不建议买入。
28. 不建议卖出。
29. 不建议做多。
30. 不建议做空。
31. 不使用真实资金。
32. 不把 Dry-Run 结果包装成实盘收益。

---

## Product Principle

V12 必须遵循 AlphaPilot 核心原则：

1. Data before AI.
2. Research before automation.
3. Risk above signal.
4. AI never owns money.
5. Everything is feature.
6. Explainability before black-box output.
7. Evidence before opinion.

V12 新增原则：

```text
Boundary before permission.
Approval before execution.
Audit before automation.
Dry-run before live.
No credentials in V12.
No private exchange API in V12.
No real order in V12.
Manual confirmation is mandatory.
User owns every decision.
```

---

## Key Concept

### Execution Intent

Execution Intent 是“用户想要测试或未来可能执行的交易意图草稿”。

它不是订单。

它不是建议。

它不是信号。

它不是交易指令。

它不能被自动执行。

定义：

```text
Execution Intent is a local research-and-approval artifact that describes a possible execution plan for review, risk checking, and dry-run simulation only.
```

中文定义：

```text
Execution Intent 是一个本地研究和审批对象，用于描述一个可能的执行计划，并进行风控检查、人工确认和模拟执行。它不是订单，不会触发真实交易。
```

### Manual Approval

Manual Approval 是人工确认流程。

它不是交易授权。

它不是交易所授权。

它不能触发真实订单。

V12 中 Manual Approval 通过后，只能允许：

```text
Run Paper/Dry-Run Simulation
```

不能允许：

```text
Send Real Order
```

### Dry Run

Dry Run 是本地模拟执行。

Dry Run 可以调用 V11 Paper Trading 的模拟逻辑。

Dry Run 不可以调用交易所 private endpoints。

Dry Run 不可以创建真实订单。

---

## Recommended Architecture

新增目录：

```text
src/
  execution-boundary/
    executionTypes.ts
    executionRules.ts
    executionRisk.ts
    executionApproval.ts
    executionMapper.ts
    executionRunner.ts
    executionRepository.ts

  screens/
    SemiAutoDesignScreen.tsx
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

* `executionTypes.ts` 定义 ExecutionIntent、PreTradeRiskCheck、ManualApproval、ExecutionAuditEvent、ExecutionDryRun。
* `executionRules.ts` 定义 execution boundary 规则。
* `executionRisk.ts` 执行 pre-trade risk checks。
* `executionApproval.ts` 处理 approval checklist。
* `executionMapper.ts` 负责 SQLite input / JSON serialization。
* `executionRunner.ts` 负责 intent -> risk check -> approval -> dry-run 的流程。
* `executionRepository.ts` 可以封装 repository，也可以直接复用 `db/repositories.ts`。
* `SemiAutoDesignScreen.tsx` 是 V12 页面。

---

## Database Requirements

V12 新增 5 张本地 SQLite 表。

### 1. ExecutionIntents

```sql
CREATE TABLE IF NOT EXISTS ExecutionIntents (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  intentId TEXT NOT NULL,
  sourceType TEXT NOT NULL,
  sourceId TEXT,
  symbol TEXT NOT NULL,
  marketType TEXT NOT NULL,
  direction TEXT NOT NULL,
  orderType TEXT NOT NULL,
  requestedPrice REAL,
  quantity REAL NOT NULL,
  leverage INTEGER NOT NULL,
  stopLossPrice REAL,
  takeProfitPrice REAL,
  maxRiskPercent REAL NOT NULL,
  thesisJson TEXT NOT NULL,
  evidenceJson TEXT NOT NULL,
  status TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL,
  updatedAt TEXT NOT NULL
);
```

### 2. PreTradeRiskChecks

```sql
CREATE TABLE IF NOT EXISTS PreTradeRiskChecks (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  checkId TEXT NOT NULL,
  intentId TEXT NOT NULL,
  passed INTEGER NOT NULL,
  severity TEXT NOT NULL,
  riskScore REAL NOT NULL,
  riskPercent REAL,
  notionalValue REAL,
  marginRequired REAL,
  feeEstimate REAL,
  slippageEstimate REAL,
  maxLossEstimate REAL,
  dataQualityScore REAL,
  marketSnapshotAgeMinutes REAL,
  checksJson TEXT NOT NULL,
  blockersJson TEXT NOT NULL,
  warningsJson TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

### 3. ManualApprovals

```sql
CREATE TABLE IF NOT EXISTS ManualApprovals (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  approvalId TEXT NOT NULL,
  intentId TEXT NOT NULL,
  riskCheckId TEXT,
  approvalStatus TEXT NOT NULL,
  checklistJson TEXT NOT NULL,
  userNotes TEXT,
  approvedFor TEXT NOT NULL,
  approvedAt TEXT,
  rejectedAt TEXT,
  expiredAt TEXT,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL,
  updatedAt TEXT NOT NULL
);
```

### 4. ExecutionDryRuns

```sql
CREATE TABLE IF NOT EXISTS ExecutionDryRuns (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  dryRunId TEXT NOT NULL,
  intentId TEXT NOT NULL,
  approvalId TEXT,
  paperOrderId TEXT,
  paperPositionId TEXT,
  dryRunStatus TEXT NOT NULL,
  resultJson TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

### 5. ExecutionAuditEvents

```sql
CREATE TABLE IF NOT EXISTS ExecutionAuditEvents (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  eventId TEXT NOT NULL,
  intentId TEXT,
  eventType TEXT NOT NULL,
  eventStatus TEXT NOT NULL,
  message TEXT NOT NULL,
  payloadJson TEXT NOT NULL,
  source TEXT NOT NULL,
  createdAt TEXT NOT NULL
);
```

### Indexes

```sql
CREATE INDEX IF NOT EXISTS idx_execution_intents_intent ON ExecutionIntents(intentId);
CREATE INDEX IF NOT EXISTS idx_execution_intents_symbol ON ExecutionIntents(symbol);
CREATE INDEX IF NOT EXISTS idx_execution_intents_status ON ExecutionIntents(status);
CREATE INDEX IF NOT EXISTS idx_execution_intents_created ON ExecutionIntents(createdAt);

CREATE INDEX IF NOT EXISTS idx_pre_trade_checks_check ON PreTradeRiskChecks(checkId);
CREATE INDEX IF NOT EXISTS idx_pre_trade_checks_intent ON PreTradeRiskChecks(intentId);
CREATE INDEX IF NOT EXISTS idx_pre_trade_checks_created ON PreTradeRiskChecks(createdAt);

CREATE INDEX IF NOT EXISTS idx_manual_approvals_approval ON ManualApprovals(approvalId);
CREATE INDEX IF NOT EXISTS idx_manual_approvals_intent ON ManualApprovals(intentId);
CREATE INDEX IF NOT EXISTS idx_manual_approvals_status ON ManualApprovals(approvalStatus);
CREATE INDEX IF NOT EXISTS idx_manual_approvals_created ON ManualApprovals(createdAt);

CREATE INDEX IF NOT EXISTS idx_execution_dry_runs_dry ON ExecutionDryRuns(dryRunId);
CREATE INDEX IF NOT EXISTS idx_execution_dry_runs_intent ON ExecutionDryRuns(intentId);
CREATE INDEX IF NOT EXISTS idx_execution_dry_runs_created ON ExecutionDryRuns(createdAt);

CREATE INDEX IF NOT EXISTS idx_execution_audit_intent ON ExecutionAuditEvents(intentId);
CREATE INDEX IF NOT EXISTS idx_execution_audit_type ON ExecutionAuditEvents(eventType);
CREATE INDEX IF NOT EXISTS idx_execution_audit_created ON ExecutionAuditEvents(createdAt);
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
11. 不修改 PaperTrading 表结构。
12. 不保存 API Key。
13. 不保存真实账户数据。
14. 不保存交易所私有数据。

如果更新 user_version，使用：

```text
PRAGMA user_version = 11;
```

---

## Types

新增：

```text
src/execution-boundary/executionTypes.ts
```

### Enums / Unions

```ts
export type ExecutionIntentSourceType =
  | 'manual'
  | 'signal_candidate'
  | 'paper_position'
  | 'backtest_result'
  | 'research_note';

export type ExecutionIntentStatus =
  | 'draft'
  | 'risk_checked'
  | 'blocked_by_risk'
  | 'needs_manual_approval'
  | 'approved_for_dry_run'
  | 'dry_run_completed'
  | 'cancelled'
  | 'expired';

export type ExecutionDirection = 'long' | 'short';

export type ExecutionOrderType = 'market' | 'limit';

export type PreTradeRiskSeverity =
  | 'ok'
  | 'warning'
  | 'blocked';

export type ManualApprovalStatus =
  | 'pending'
  | 'approved_for_dry_run'
  | 'rejected'
  | 'expired';

export type ApprovedFor =
  | 'dry_run_only';

export type ExecutionDryRunStatus =
  | 'not_started'
  | 'completed'
  | 'failed';

export type ExecutionAuditEventType =
  | 'intent_created'
  | 'risk_check_run'
  | 'risk_blocked'
  | 'approval_created'
  | 'approval_approved_for_dry_run'
  | 'approval_rejected'
  | 'dry_run_started'
  | 'dry_run_completed'
  | 'dry_run_failed'
  | 'intent_cancelled';

export type ExecutionAuditEventStatus =
  | 'info'
  | 'warning'
  | 'blocked'
  | 'success'
  | 'error';
```

### ExecutionIntent

```ts
export interface ExecutionIntent {
  id: number;
  intentId: string;
  sourceType: ExecutionIntentSourceType;
  sourceId: string | null;
  symbol: string;
  marketType: string;
  direction: ExecutionDirection;
  orderType: ExecutionOrderType;
  requestedPrice: number | null;
  quantity: number;
  leverage: number;
  stopLossPrice: number | null;
  takeProfitPrice: number | null;
  maxRiskPercent: number;
  thesisJson: string;
  evidenceJson: string;
  status: ExecutionIntentStatus;
  source: string;
  createdAt: string;
  updatedAt: string;
}
```

### PreTradeRiskCheck

```ts
export interface PreTradeRiskCheck {
  id: number;
  checkId: string;
  intentId: string;
  passed: boolean;
  severity: PreTradeRiskSeverity;
  riskScore: number;
  riskPercent: number | null;
  notionalValue: number | null;
  marginRequired: number | null;
  feeEstimate: number | null;
  slippageEstimate: number | null;
  maxLossEstimate: number | null;
  dataQualityScore: number | null;
  marketSnapshotAgeMinutes: number | null;
  checksJson: string;
  blockersJson: string;
  warningsJson: string;
  source: string;
  createdAt: string;
}
```

### ManualApproval

```ts
export interface ManualApproval {
  id: number;
  approvalId: string;
  intentId: string;
  riskCheckId: string | null;
  approvalStatus: ManualApprovalStatus;
  checklistJson: string;
  userNotes: string | null;
  approvedFor: ApprovedFor;
  approvedAt: string | null;
  rejectedAt: string | null;
  expiredAt: string | null;
  source: string;
  createdAt: string;
  updatedAt: string;
}
```

### ExecutionDryRun

```ts
export interface ExecutionDryRun {
  id: number;
  dryRunId: string;
  intentId: string;
  approvalId: string | null;
  paperOrderId: string | null;
  paperPositionId: string | null;
  dryRunStatus: ExecutionDryRunStatus;
  resultJson: string;
  source: string;
  createdAt: string;
}
```

### ExecutionAuditEvent

```ts
export interface ExecutionAuditEvent {
  id: number;
  eventId: string;
  intentId: string | null;
  eventType: ExecutionAuditEventType;
  eventStatus: ExecutionAuditEventStatus;
  message: string;
  payloadJson: string;
  source: string;
  createdAt: string;
}
```

### Summary

```ts
export interface ExecutionBoundarySummary {
  totalIntents: number;
  draftCount: number;
  riskCheckedCount: number;
  blockedByRiskCount: number;
  pendingApprovalCount: number;
  approvedForDryRunCount: number;
  dryRunCompletedCount: number;
  cancelledCount: number;
  averageRiskScore: number;
  latestIntentAt: string | null;
}
```

---

## Source Constant

Use:

```ts
export const EXECUTION_BOUNDARY_SOURCE = 'execution_boundary_v12';
```

All V12 records must use this source.

---

## State Machine

Execution Intent 状态流必须严格限制：

```text
draft
  ↓ run risk check
risk_checked
  ↓ if blocked
blocked_by_risk

risk_checked
  ↓ if not blocked
needs_manual_approval

needs_manual_approval
  ↓ user manually approves checklist
approved_for_dry_run

approved_for_dry_run
  ↓ user manually clicks Run Dry Run
dry_run_completed

any state
  ↓ user cancels
cancelled

stale approval
  ↓ expires
expired
```

禁止出现：

```text
approved_for_live
live_order_sent
real_order_created
exchange_order_filled
auto_executed
```

---

## Execution Intent Rules

Execution Intent 创建规则：

1. 必须由用户手动点击创建。
2. 可以选择 sourceType：

   * manual
   * signal_candidate
   * paper_position
   * backtest_result
   * research_note
3. sourceId 可选。
4. symbol 必填。
5. direction 必填，只允许 long / short。
6. orderType 必填，只允许 market / limit。
7. quantity 必须 > 0。
8. leverage 必须 1～5。
9. maxRiskPercent 默认 1%，最大不能超过 2%。
10. stopLossPrice 强烈建议填写。
11. thesisJson 必须说明这是 research / dry-run context。
12. evidenceJson 可以包含 SignalCandidate / Backtest / Feature / MarketDataSnapshot 上下文。
13. status 初始为 `draft`。
14. 创建后写入 audit event：`intent_created`。

重要：

Execution Intent 不能自动创建 paper order。

Execution Intent 不能自动创建 real order。

Execution Intent 不能触发任何交易所 API。

---

## Pre-Trade Risk Check

新增：

```text
src/execution-boundary/executionRisk.ts
```

至少实现：

```ts
runPreTradeRiskCheck(intentId: string): Promise<PreTradeRiskCheck>
```

风险检查必须读取本地数据：

```text
ExecutionIntent
PaperAccount
MarketDataSnapshots
SignalCandidates optional
BacktestResults optional
TradeFeatures optional
```

不能请求后端。

不能调用交易所。

不能读取真实账户。

### Required Checks

至少包含以下检查：

#### 1. Leverage Check

```text
leverage <= 5
```

超过 5 必须 blocked。

#### 2. Quantity Check

```text
quantity > 0
```

不满足必须 blocked。

#### 3. Price Check

Market order:

* 优先使用最新本地 MarketDataSnapshots.price。
* 如果没有本地 price，允许使用 requestedPrice。
* 两者都没有则 blocked。

Limit order:

* requestedPrice 必须 > 0。

#### 4. Simulated Account Check

读取本地 PaperAccount。

检查：

```text
marginRequired + feeEstimate <= availableBalance
```

不满足必须 blocked。

#### 5. Max Risk Percent Check

如果 stopLossPrice 可用：

```text
riskPercent <= maxRiskPercent
```

如果 riskPercent > maxRiskPercent，必须 blocked。

如果没有 stopLossPrice：

* severity 至少 warning。
* checklist 必须强制用户确认 no stop loss risk。
* 可以选择 blocked，也可以 warning。
* 推荐 V12 第一版：没有 stopLossPrice 时 blocked，避免误用。

#### 6. Stop Loss Side Check

long:

```text
stopLossPrice < referencePrice
```

short:

```text
stopLossPrice > referencePrice
```

不满足必须 blocked。

#### 7. Market Data Freshness Check

读取最新 `MarketDataSnapshots`。

如果 snapshot 太旧：

```text
age > 60 minutes
```

severity = warning 或 blocked。

推荐：

* age <= 15 min: ok
* 15 < age <= 60: warning
* age > 60: blocked

如果没有 snapshot：

* 可以使用手动 requestedPrice
* 但必须 warning
* 如果 market order 没有 requestedPrice，blocked

#### 8. Data Quality Check

如果 MarketDataSnapshot.dataQualityScore < 60：

* severity = warning

如果 < 40：

* blocked

#### 9. Signal Candidate Check

如果 sourceType = signal_candidate：

必须检查：

* SignalCandidate 存在
* status 不是 rejected_by_risk
* riskLevel 不是 high
* confidenceScore 不为空

如果 SignalCandidate riskLevel = high：

* blocked 或 warning
* 推荐 V12 第一版：blocked

#### 10. Paper Account Boundary Check

必须确认：

```text
This is paper/dry-run only.
No real account is connected.
No API key exists.
No private endpoint is called.
```

这些必须写入 checksJson。

---

## Risk Score

riskScore 0～100。

建议：

```text
0-30: low research risk
31-60: medium research risk
61-80: high research risk
81-100: blocked
```

注意：

riskScore 是研究和执行准备风险，不是市场涨跌预测。

riskScore 不能解释为盈利概率。

UI 必须写明：

```text
Risk score describes execution-readiness risk in a dry-run workflow. It is not a profit probability.
```

---

## Manual Approval Workflow

新增：

```text
src/execution-boundary/executionApproval.ts
```

至少实现：

```ts
createManualApproval(intentId: string, riskCheckId: string): Promise<ManualApproval>
approveForDryRun(approvalId: string, checklist: ManualApprovalChecklist, userNotes?: string): Promise<ManualApproval>
rejectApproval(approvalId: string, userNotes?: string): Promise<ManualApproval>
```

### Approval Checklist

Manual Approval checklist 必须包含：

```ts
export interface ManualApprovalChecklist {
  understandsDryRunOnly: boolean;
  confirmsNoRealOrder: boolean;
  confirmsNoApiKey: boolean;
  confirmsNoExchangeAccount: boolean;
  reviewedSymbol: boolean;
  reviewedDirection: boolean;
  reviewedQuantity: boolean;
  reviewedLeverage: boolean;
  reviewedStopLoss: boolean;
  reviewedRiskPercent: boolean;
  reviewedFeesAndSlippage: boolean;
  acceptsSimulationLimitations: boolean;
}
```

所有字段必须为 true，才允许 approvalStatus = `approved_for_dry_run`。

任何字段 false：

```text
approvalStatus = pending
```

或者 reject。

### approvedFor

V12 中唯一允许：

```text
dry_run_only
```

禁止：

```text
live_order
real_order
exchange_execution
```

---

## Dry Run Execution

新增：

```text
src/execution-boundary/executionRunner.ts
```

至少实现：

```ts
createExecutionIntent(input): Promise<ExecutionIntent>
runRiskCheckForIntent(intentId): Promise<PreTradeRiskCheck>
createApprovalForIntent(intentId): Promise<ManualApproval>
approveIntentForDryRun(approvalId, checklist, userNotes?): Promise<ManualApproval>
runDryRunForApprovedIntent(intentId): Promise<ExecutionDryRun>
cancelExecutionIntent(intentId): Promise<ExecutionIntent>
```

### Dry Run Rules

`runDryRunForApprovedIntent` 只能做：

```text
Create simulated paper order using V11 Paper Trading logic
```

不能做：

```text
Send real order
Call exchange private endpoint
Call backend trade endpoint
Use API key
Read real balance
Read real positions
```

Dry Run 成功后：

1. 调用 V11 `createAndFillPaperOrder(...)`。
2. 创建 `ExecutionDryRun`。
3. 写入 `ExecutionAuditEvent`。
4. 更新 ExecutionIntent status = `dry_run_completed`。
5. 不创建真实订单。

Dry Run 失败后：

1. 创建 failed ExecutionDryRun。
2. 写入 audit event。
3. 不影响真实账户，因为不存在真实账户。

---

## Repository Requirements

在 `src/db/repositories.ts` 中新增：

```ts
createExecutionIntent(input)
updateExecutionIntentStatus(intentId, status)
getExecutionIntentByIntentId(intentId)
listExecutionIntents(limit?)
listExecutionIntentsByStatus(status, limit?)

createPreTradeRiskCheck(input)
listPreTradeRiskChecksByIntent(intentId, limit?)
getLatestPreTradeRiskCheck(intentId)

createManualApproval(input)
updateManualApproval(input)
getManualApprovalByApprovalId(approvalId)
listManualApprovals(limit?)
listManualApprovalsByIntent(intentId)

createExecutionDryRun(input)
listExecutionDryRuns(limit?)
listExecutionDryRunsByIntent(intentId)

createExecutionAuditEvent(input)
listExecutionAuditEvents(limit?)
listExecutionAuditEventsByIntent(intentId, limit?)

getExecutionBoundarySummary()
```

`getExecutionBoundarySummary()` 至少返回：

```ts
{
  totalIntents: number;
  draftCount: number;
  riskCheckedCount: number;
  blockedByRiskCount: number;
  pendingApprovalCount: number;
  approvedForDryRunCount: number;
  dryRunCompletedCount: number;
  cancelledCount: number;
  averageRiskScore: number;
  latestIntentAt: string | null;
}
```

---

## Audit Log Requirements

每个关键动作必须写 audit event：

1. intent_created
2. risk_check_run
3. risk_blocked
4. approval_created
5. approval_approved_for_dry_run
6. approval_rejected
7. dry_run_started
8. dry_run_completed
9. dry_run_failed
10. intent_cancelled

Audit message 必须清楚说明：

```text
This workflow is dry-run only and does not create real orders.
```

---

## Semi-Auto Design Screen

新增页面：

```text
src/screens/SemiAutoDesignScreen.tsx
```

入口：

```text
我的 -> Semi-Auto Design
```

页面标题：

```text
Semi-Auto Design
```

副标题：

```text
Manual approval workflow for dry-run execution boundaries.
```

### Safety Notice

必须显示：

```text
V12 is a design boundary. It does not use API keys, exchange accounts, private endpoints, or real orders.
```

必须显示：

```text
Approved intents can only run Paper/Dry-Run simulations. They cannot place real trades.
```

必须显示：

```text
Execution Intent is not trading advice and not an order.
```

---

## UI Sections

### 1. Boundary Status

显示：

* Real API Key: Disabled
* Private Exchange API: Disabled
* Real Orders: Disabled
* Withdraw Permission: Disabled
* Auto Trading: Disabled
* Dry Run: Enabled
* Paper Simulation: Enabled

### 2. Create Execution Intent

字段：

* sourceType
* sourceId optional
* symbol
* direction
* orderType
* requestedPrice
* quantity
* leverage
* stopLossPrice
* takeProfitPrice
* maxRiskPercent
* thesis / notes

按钮：

```text
Create Intent
```

不能有：

```text
Place Order
Send Order
Trade Now
Execute Live
```

### 3. Intent List

显示：

* intentId
* symbol
* direction
* quantity
* leverage
* status
* createdAt

Actions：

* Run Risk Check
* Create Approval
* Cancel Intent

### 4. Risk Check Panel

显示：

* passed
* severity
* riskScore
* riskPercent
* notionalValue
* marginRequired
* feeEstimate
* slippageEstimate
* dataQualityScore
* marketSnapshotAgeMinutes
* blockers
* warnings

如果 blocked：

```text
Blocked by local risk rules. Dry-run approval is not available.
```

### 5. Manual Approval Checklist

显示 checklist。

所有 checkbox 必须 true 才能 approve。

按钮：

```text
Approve for Dry Run
Reject Approval
```

不能有：

```text
Approve Live Trade
Approve Real Order
```

### 6. Dry Run Panel

只有 approvalStatus = approved_for_dry_run 时显示：

```text
Run Paper Dry-Run
```

点击后调用 V11 Paper Trading simulation。

结果显示：

* dryRunStatus
* paperOrderId
* paperPositionId
* result summary

### 7. Audit Log

显示最近 audit events：

* eventType
* eventStatus
* message
* createdAt

---

## Research Dashboard Integration

Research Dashboard 新增 section：

```text
Semi-Auto Design Boundary
```

显示：

* total intents
* draft count
* risk checked count
* blocked by risk count
* pending approval count
* approved for dry run count
* dry run completed count
* cancelled count
* average risk score
* latest intent time
* recent audit events
* recent dry runs

必须显示：

```text
V12 records describe dry-run execution boundaries only. They are not real orders or trading performance.
```

要求：

1. 只读本地 SQLite。
2. 不请求后端。
3. 不调用交易所。
4. 不读取真实账户。
5. 不触发 dry-run。
6. 空数据不崩溃。

---

## My Screen Integration

更新：

```text
src/screens/MyScreen.tsx
```

新增入口：

```text
Semi-Auto Design
```

描述：

```text
设计半自动交易的风控、人工确认和 dry-run 审计流程，不接真实交易权限。
```

---

## README Update

更新 `README.md`，新增 V12 说明。

必须写明：

* V12 是 Semi-Auto Trading Design Boundary。
* V12 不是 live trading。
* V12 不是 real exchange execution。
* V12 不保存 API Key。
* V12 不接 Trade API。
* V12 不接 Withdraw API。
* V12 不读取真实账户余额。
* V12 不读取真实持仓。
* V12 不下真实订单。
* V12 不撤真实订单。
* V12 不做自动交易。
* V12 不提供买卖建议。
* V12 新增 ExecutionIntents。
* V12 新增 PreTradeRiskChecks。
* V12 新增 ManualApprovals。
* V12 新增 ExecutionDryRuns。
* V12 新增 ExecutionAuditEvents。
* V12 approval 只允许 `dry_run_only`。
* V12 dry run 只能调用 Paper Trading simulation。
* V12 是为未来更严格的权限隔离做准备。

---

## Forbidden Implementation Details

V12 中禁止出现真实执行实现。

禁止新增：

```text
apiKey
apiSecret
passphrase
exchangeSecret
privateKey
withdrawPermission
tradePermission
realOrder
liveOrder
placeOrder
sendOrder
executeLive
```

禁止调用或封装：

```text
ccxt.createOrder
ccxt.cancelOrder
ccxt.fetchBalance
ccxt.fetchPositions
ccxt.fetchOrders
exchange.createOrder
exchange.cancelOrder
exchange.fetchBalance
exchange.fetchPositions
```

允许在 README 或 safety copy 中以否定形式出现：

```text
No API key
No real order
No Trade API
No private endpoint
```

但代码中不能实现这些功能。

---

## Safety Rules

V12 必须遵守：

1. Local SQLite only.
2. Dry-run only.
3. Paper simulation only.
4. No real AI.
5. No Trade API.
6. No Withdraw API.
7. No API Key.
8. No API Secret.
9. No passphrase.
10. No private endpoint.
11. No real account balance.
12. No real positions.
13. No real order placement.
14. No real order cancellation.
15. No semi-auto live trading.
16. No auto trading.
17. No buy/sell advice.
18. No long/short recommendation.
19. No future prediction.
20. No guaranteed outcome.
21. Signal Candidate is research context only.
22. Execution Intent is not an order.
23. Manual Approval is for dry-run only.
24. User owns all decisions.

禁止出现以下英文文案：

```text
buy now
sell now
go long
go short
open real position
close real position
place real order
send real order
execute live trade
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
下真实订单
发送真实订单
执行实盘交易
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
execution intent
manual approval
dry-run only
paper simulation
risk check
audit log
not trading advice
not a real order
no API key
no private endpoint
```

中文允许：

```text
执行意图
人工确认
仅 dry-run
纸面模拟
风控检查
审计日志
不是交易建议
不是真实订单
不使用 API Key
不调用私有接口
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
2. 旧数据库升级不崩溃。
3. 我的页面能进入 Semi-Auto Design。
4. Boundary Status 显示真实交易权限全部 Disabled。
5. 可以创建 Execution Intent。
6. 创建 intent 后写入 audit event。
7. leverage > 5 会被 risk check blocked。
8. quantity <= 0 会被 risk check blocked。
9. 没有 stopLoss 时 blocked 或至少 warning；推荐 blocked。
10. riskPercent > maxRiskPercent 时 blocked。
11. MarketDataSnapshot 太旧时 blocked。
12. SignalCandidate high risk 时 blocked。
13. blocked intent 不能 approval。
14. risk check passed 后可以创建 manual approval。
15. checklist 未全部确认时不能 approve。
16. checklist 全部确认后只能 approved_for_dry_run。
17. approved_for_dry_run 后可以 Run Paper Dry-Run。
18. Dry Run 会生成 ExecutionDryRun。
19. Dry Run 可以调用 V11 paper simulation。
20. Dry Run 不调用后端。
21. Dry Run 不调用交易所。
22. Dry Run 不创建真实订单。
23. Audit Log 记录完整流程。
24. Research Dashboard 显示 Semi-Auto Design Boundary section。
25. Paper Trading 页面仍然正常。
26. Signal Engine 页面仍然正常。
27. Live Data Collection 页面仍然正常。
28. Backtesting 页面仍然正常。
29. Data Quality 页面仍然正常。
30. Quick Trade 仍然正常。
31. Trade Detail 仍然正常。
32. 没有 API Key 字段。
33. 没有真实交易权限调用。
34. 没有真实下单相关实现。
35. 没有买卖建议文案。

---

## Static Safety Scan

完成后请执行或手动检查：

```text
apiKey
apiSecret
passphrase
createOrder
cancelOrder
fetchBalance
fetchPositions
fetchOrders
place real order
send real order
execute live
live order
real order placement
withdraw
```

要求：

* 不应该出现真实实现。
* 如果 README 中出现，必须是明确否定语境。
* 如果安全文案中出现，必须是明确禁止语境。
* 不能存在真实 private API 调用。

---

## Acceptance Criteria

V12 完成后必须满足：

1. `src/execution-boundary/` 目录存在。
2. `executionTypes.ts` 存在。
3. `executionRules.ts` 存在。
4. `executionRisk.ts` 存在。
5. `executionApproval.ts` 存在。
6. `executionMapper.ts` 存在。
7. `executionRunner.ts` 存在。
8. `SemiAutoDesignScreen.tsx` 存在。
9. My 页面有 Semi-Auto Design 入口。
10. SQLite 新增 ExecutionIntents。
11. SQLite 新增 PreTradeRiskChecks。
12. SQLite 新增 ManualApprovals。
13. SQLite 新增 ExecutionDryRuns。
14. SQLite 新增 ExecutionAuditEvents。
15. migration 使用 `CREATE TABLE IF NOT EXISTS`。
16. `PRAGMA user_version = 11`。
17. 可以手动创建 Execution Intent。
18. Execution Intent 不会自动创建订单。
19. 可以运行 Pre-Trade Risk Check。
20. 风控 blocked 时不能 approval。
21. 风控通过时可以创建 Manual Approval。
22. checklist 必须全部确认才能 approved_for_dry_run。
23. approvedFor 只能是 dry_run_only。
24. approved_for_dry_run 后只能 Run Paper Dry-Run。
25. Dry Run 只能调用 V11 paper simulation。
26. Dry Run 不调用真实交易所。
27. Dry Run 不创建真实订单。
28. Audit Log 记录完整流程。
29. Research Dashboard 显示 Semi-Auto Design Boundary section。
30. README 更新 V12。
31. `pnpm run typecheck` 通过。
32. `pnpm exec expo config --type public` 通过。
33. 不接真实 AI。
34. 不接 Trade API。
35. 不接 Withdraw API。
36. 不保存 API Key。
37. 不读取真实账户。
38. 不读取真实持仓。
39. 不下真实订单。
40. 不撤真实订单。
41. 不自动交易。
42. 不生成买卖建议。

---

## Self Review Output

完成后请输出：

1. 修改文件列表
2. 新增文件列表
3. 新增数据库表说明
4. ExecutionIntent 类型说明
5. PreTradeRiskCheck 类型说明
6. ManualApproval 类型说明
7. ExecutionDryRun 类型说明
8. ExecutionAuditEvent 类型说明
9. 状态机说明
10. Pre-Trade Risk Check 规则说明
11. Manual Approval checklist 说明
12. Dry Run 如何复用 Paper Trading 的说明
13. Audit Log 说明
14. Semi-Auto Design 页面说明
15. Research Dashboard 集成说明
16. My 页面入口说明
17. README 更新说明
18. 安全边界确认
19. 是否修改后端
20. 是否新增 API Key 字段
21. 是否调用交易所 private endpoint
22. 是否存在真实下单实现
23. `pnpm run typecheck` 结果
24. `pnpm exec expo config --type public` 结果
25. 安全文案 / 交易权限关键词扫描结果
26. 已知问题
27. 下一步 V12.1 或 V13 建议

---

## Future Compatibility

V12 为未来 V12.1 / V13 做准备：

* V12.1 可以继续完善权限隔离设计，但仍不接真实 API Key。
* V12.2 可以做 read-only exchange account design，但必须单独设计权限和安全存储。
* V13 才能讨论 auto trading design，而且必须仍然是 design-only，不能直接上线真实自动交易。
* 任何真实交易权限都必须经过单独版本、单独审计、单独风控、单独人工确认。
* Withdrawal permission 永远不允许。
* Auto trading 永远不能默认开启。

V12 只完成 Semi-Auto Trading Design Boundary。
