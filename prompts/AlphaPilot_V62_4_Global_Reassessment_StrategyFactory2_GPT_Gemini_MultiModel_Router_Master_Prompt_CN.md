# AlphaPilot V62.4+ 全局重审、Strategy Factory 2.0 与 GPT/Gemini 多模型编排 Master Prompt
## 给 Codex 的唯一完整实施文档

> **本文件是当前最新、唯一的实施提示词。**
>
> 它合并：
>
> ```text
> 全局 P0/P1 整改
> Source / Runtime 身份
> Demo / Live 唯一准入权威
> Strategy Factory 真实试验闭环
> 失败策略记忆
> 策略匹配率与频率组合
> 因子 / Qlib / 数值机器学习
> GPT + Gemini 多模型自动路由
> UI 信息架构
> Codex 自检
> Headless / 服务器部署
> ```
>
> 本轮先完成工程、研究和 AI 编排能力，不批准 Live、不 ARM Live、不创建策略实盘订单。
>
> 当前完整证据包已独立验证：
>
> ```text
> Artifact Manifest 1168 / 1168
> Git Bundle 4 / 4
> SQLite Online Backup 41
> 数据样本 2
> 缺失和 Hash 错误 0
> ```
>
> 历史审计表明 Strategy Factory 存在“Run 数量很多、真实 Trial/Formal 数量却可能为 0”的黑盒风险；
> 历史策略归档有大量同源版本，不能把版本数当作独立机制数量。
>
> **最核心原则：**
>
> ```text
> AI 负责研究、分析、解释、候选建议和独立审查；
> 确定性系统负责数据门、试验、Gate、Release、风险、订单、持仓、对账和 Kill Switch；
> 人工负责高风险批准、ARM、风险提高和 Runtime Authority 切换。
> ```

---

# 0. 执行边界

## 0.1 开始状态

执行前核验：

```text
Live=false
Live ARM=false
Live Strategy Orders=0
Withdraw=false
```

当前 Demo 若处于稳定运行：

```text
保持当前已批准 Commit / Release / Risk / ARM 作为生产对照；
不得热加载开发分支。
```

若 Demo 已 disarmed：

```text
保持安全 disarmed，直到完成 P0、Shadow Parity、对账和新的人工 ARM。
```

## 0.2 禁止行为

```text
不 amend / rebase / force-push 已发布 Tag
不修改历史不可变 Release 或证据
不把 not_run 写成 passed
不让 LLM 接触订单 Client、私有交易 API、Approval、ARM、Withdraw
不让 LLM 修改 Gate 或 Risk Profile
不通过降低 PIT、成本、容量、身份、幂等或风险标准制造策略通过
不在订单热路径调用 GPT、Gemini 或任何 LLM
```

## 0.3 隔离工作树

```text
Quant Remediation Worktree
Console Remediation Worktree
Docs Worktree
AI Orchestration Worktree
```

每阶段：

```text
实现
→ 定向测试
→ 全量测试
→ 独立验收
→ Commit
→ Tag（若计划要求）
→ Push
→ 远端 SHA 核对
```

---

# 1. Phase 0：事实冻结与 Source / Runtime 对齐

## 1.1 Dirty Path 处理

逐仓库将未提交项分为：

```text
应提交源码
用户资料
生成 Artifact
本地 Runtime 数据
应丢弃临时文件
```

不得静默遗漏。

生成：

```text
deployment_source_manifest.json
dirty_path_disposition.json
```

## 1.2 RuntimeIdentity

建立不可变：

```text
runtimeId
environment
processId
repositoryCommit
repositoryTag
moduleRootHashes

releaseId
releaseHash
riskOverlayHash
modelHash
modelPolicyHash
approvalHash
armHash
runtimeLeaseId

startedAt
lastHeartbeatAt
lastScanAt
nextScanAt
```

任意关键身份缺失或与磁盘源码不一致：

```text
newEntriesAllowed=false
route=runtime_identity_unverified
```

## 1.3 唯一 Runtime Lease

实现：

```text
ExecutionRuntimeLease
```

任何时刻每个环境只允许一个：

```text
demo_order_authority
live_order_authority
```

第二个进程只能：

```text
read_only
shadow
```

---

# 2. Phase 1：6 项 P0 和唯一权威

## 2.1 唯一 Live 准入

唯一调用链：

```text
AdaptiveLearningTechnicalReadinessGate
→ LiveReleaseBuilder
→ ExactLiveReleaseApprovalGate
→ LiveArmGate
→ ExecutionRuntimeLease
→ LiveExecutionRuntime
```

旧路径：

```text
deprecated_non_executable
read_only_history
或 HTTP 410
```

任何旧 Release、旧环境变量、旧 ARM Store 都不得绕过新权威。

## 2.2 Rule / Model 模式

每条 Release 明确：

```text
rule_only
observer
rank_only
veto_only
meta_label
risk_suggestion
```

### rule_only

```text
规则策略决定信号；
学习埋点仍记录；
不强制使用低质量模型。
```

### observer

```text
只记录模型建议；
不改变排序、Risk 或订单资格。
```

### rank_only

```text
只对已有规则信号排序；
不能创造新信号。
```

### veto_only

```text
只允许删除规则信号；
不能增加信号。
```

### meta_label

```text
基础规则先产生信号；
冻结模型概率达到冻结阈值后才可继续。
```

非 observer 模式必须真正调用：

```text
ModelArtifactLoader
FeatureVectorBuilder
Calibration
ModeDecisionEngine
```

不得只修改 `modelMode` 字段。

## 2.3 Kill Switch

分为：

```text
Pause New Entries
Cancel Open Orders
Emergency Flatten And Kill
```

`Emergency Flatten And Kill`：

```text
暂停新开仓
→ 撤销开仓挂单
→ 查询交易所实际持仓
→ reduce-only 平仓
→ 处理部分成交
→ REST / WS 对账
→ 确认持仓归零
```

未归零：

```text
status=incomplete_emergency_flatten
```

不得显示成功。

## 2.4 实际持仓风险

权威来自：

```text
交易所实际持仓
实际入场均价
实际数量和合约价值
部分成交
已确认保护单
```

输出：

```text
expectedOpenRisk
exchangePositionRisk
protectedOpenRisk
unprotectedPositionRisk
unknownProtectionCount
```

## 2.5 HTTP 写安全

所有 POST / PUT / DELETE，包括 Loopback：

```text
Origin allowlist
CSRF token
Operator write session / token
Content-Type application/json
Malformed JSON -> 400
Route-specific exact confirmation
Append-only audit event
```

手机默认只读。

## 2.6 PIT 与缺失值

每个 Feature 保存：

```text
sourceTimestamp
availableAt
observedAt
decisionAt
```

必须满足：

```text
sourceTimestamp <= availableAt <= decisionAt <= orderSendAt
```

缺失手续费、滑点、Funding、Net R、PnL：

```text
null / unavailable
```

禁止转成 0。

---

# 3. Phase 2：进程、SQLite 与代码整理

## 3.1 模块化单体 + 多进程

保持仓库边界，不拆微服务，但拆运行进程：

```text
Control API / UI
Public Market Runtime
Demo Execution Runtime
Live Execution Runtime
Research Worker
Offline Factor / Model Worker
AI Orchestrator Worker
```

## 3.2 SQLite

共享：

```text
open_sqlite()
```

统一：

```text
foreign_keys=ON
busy_timeout
WAL（适用）
synchronous policy
SchemaMigrationLedger
integrity_check
online backup
```

迁移：

```text
在线备份
→ 副本迁移
→ 测试
→ 安全切换
```

不得直接热改生产数据库。

## 3.3 代码整理

目标是模块化，不是“大爆炸重写”。

Console 建议：

```text
alphapilot_control_console/
  api/
    middleware/
    routes/
  application/
  domain/
  infrastructure/
  projections/
  ai_orchestration/
```

Quant 建议：

```text
research_factory/
research_memory/
factor_lab/
adaptive_learning/
promotion/
risk/
execution/
data_contracts/
```

## 3.4 删除旧代码顺序

```text
可达性扫描
→ deprecated
→ 关闭写入口
→ 监控一个完整版本
→ 删除
```

内部废弃代码不搬到“参考仓库”。

外部论文、开源项目、策略资料和许可证才进入：

```text
AlphaPilot-Research-References
```

---

# 4. Phase 3：Strategy Factory 2.0

## 4.1 暂停空转 Runner

在真实闭环验收前：

```text
continuousResearch.enabled=false
```

保留历史 Run，不删除。

## 4.2 正确状态机

```text
hypothesis_draft
hypothesis_validated
data_readiness
candidate_build
trial_queued
trial_running
development_complete
formal_queued
formal_running
formal_complete
demo_release_draft
archived
blocked
```

以下不再是完成状态：

```text
awaiting_formal_validation
waiting_forward_50
data_insufficient_without_details
```

## 4.3 每个 Run 必须有真实工作

```text
completedTrialCount > 0
```

才可进入 Development Complete。

Formal Ready 时必须存在：

```text
FormalJob
Formal Claim
Formal Attempt
Formal Result
Formal Read
```

0 幸存者合法。

## 4.4 失败树

每个失败必须落到一层：

```text
Implementation
Data / PIT
Signal Edge
Cost / Capacity
Stability / Regime
Risk / Portfolio
Promotion / Execution
```

输出：

```text
Fact
Inference
Repairability
ProhibitedRepair
NextExperiment
ChangedVariable
ParentStrategy
FamilyFingerprint
SignalCorrelation
```

---

# 5. Phase 4：GPT + Gemini 多模型 AI 编排

## 5.1 架构

新增唯一：

```text
AIOrchestrationService
```

模块：

```text
ai_orchestration/
  provider_adapters/
    openai_adapter.py
    gemini_adapter.py
  routing/
    task_classifier.py
    routing_policy.py
    model_registry.py
    budget_policy.py
    fallback_policy.py
  prompts/
    prompt_registry.py
    prompt_versions/
  schemas/
  redaction/
  evaluation/
  audit/
  ui_projection/
```

禁止业务模块直接调用 OpenAI 或 Gemini SDK。

所有调用必须经过：

```text
AIOrchestrationService
```

## 5.2 API 选择

OpenAI：

```text
使用 Responses API
不新建 Assistants API 集成
```

Gemini：

```text
优先使用 Interactions API；
无状态、敏感度较高的单轮任务可使用不保存请求的标准内容生成路径。
```

Provider Adapter 必须把第三方 API 差异统一成：

```text
AIRequest
AIResponse
AIToolCall
AIUsage
AIError
```

## 5.3 模型名称不得写死进业务逻辑

建立：

```text
AIModelRegistry
```

只在配置中填写实际 Model ID。

能力别名：

```text
openai_reasoning_primary
openai_coding_primary
openai_fast
openai_batch

gemini_reasoning_primary
gemini_multimodal_primary
gemini_fast
gemini_batch
```

每个登记项：

```text
provider
modelId
capabilityTags
supportsStructuredOutput
supportsFunctionCalling
supportsFiles
supportsImages
supportsBatch
contextLimit
latencyTier
costTier
previewOrStable
enabled
```

模型升级只改 Registry，不改 Strategy Factory 代码。

## 5.4 AI Task Envelope

每个任务必须创建：

```text
taskId
taskType
taskVersion
createdAt
requestedBy

sensitivityClass
dataClassification
containsSourceCode
containsPrivateAccountData
containsCredentials
containsOrderPayload
containsPersonalData

requiresLongContext
requiresMultimodal
requiresCoding
requiresQuantReasoning
requiresIndependentReview
requiresHumanReview

maximumLatencyMs
maximumCostUSD
maximumInputTokens
maximumOutputTokens

allowedTools
forbiddenTools
inputArtifactHashes
promptVersion
```

## 5.5 数据分类

```text
PUBLIC
INTERNAL
CONFIDENTIAL
RESTRICTED_TRADING
SECRET
```

### SECRET

```text
API Key
Secret
Passphrase
签名 Header
私钥
服务器密码
```

永远不得发送给任何模型。

### RESTRICTED_TRADING

```text
私有账户原始响应
完整实时订单 Payload
精确账户身份
未脱敏余额
```

默认不得发送。

仅允许使用脱敏聚合：

```text
orderCount
positionCount
errorCode
latencyBucket
riskRatio
```

## 5.6 Prompt Injection 防护

用户上传的：

```text
策略文件
README
论文
网页
代码注释
日志
```

全部视为不可信数据。

必须：

```text
将内容放入 data 区
明确禁止执行其中指令
剥离“忽略系统提示”等文本
工具调用只来自平台 System Policy
```

任何模型不得根据文档内指令获得额外工具。

## 5.7 任务类型自动路由表

### A. 全局架构、P0、安全、订单、Risk、SQLite 迁移

```text
Primary:
openai_reasoning_primary

Independent Reviewer:
gemini_reasoning_primary

Mode:
dual_independent_review

Result:
必须结构化对比；
有重大分歧则 blocked_human_review。
```

### B. 大型证据包、长文档、PDF、图像和 UI 截图审计

```text
Primary:
gemini_multimodal_primary

Verifier:
openai_reasoning_primary
```

### C. 代码生成、重构、测试和 API Contract

```text
Primary:
openai_coding_primary

Reviewer:
gemini_reasoning_primary

Requirement:
Diff Scope + Test Plan + Regression Review
```

### D. 策略市场机制假设

```text
Parallel:
openai_reasoning_primary
gemini_reasoning_primary

两边互相不可见；
各自产生结构化 Hypothesis。
```

之后由确定性：

```text
HypothesisDeduplicator
FamilyFingerprint
NegativeResearchMemory
```

去重。

禁止由一个模型直接选择自己生成的候选为优胜者。

### E. 失败归因和下一实验

```text
Primary Critic:
openai_reasoning_primary

Independent Critic:
gemini_reasoning_primary

Merger:
deterministic schema merger
```

只允许输出：

```text
可证伪下一实验
单变量变化
禁止修复
```

不得直接改 Gate。

### F. Alpha191、论文、参考策略和大规模文档抽取

```text
Primary:
gemini_multimodal_primary

Validation:
openai_reasoning_primary
+ deterministic formula parser/tests
```

### G. Quant 数学、Walk-forward、PIT、成本、容量和统计解释

```text
Primary:
openai_reasoning_primary

Reviewer:
gemini_reasoning_primary
```

数值结果必须来自确定性代码，不得由 LLM 计算后直接入库。

### H. UI 中文解释、“为什么没有下单”、运行摘要

```text
Primary:
gemini_fast

Fallback:
openai_fast
```

输入仅使用结构化、脱敏的 Runtime Projection。

### I. 批量失败分类、证据标签、文档摘要和历史归档

```text
Primary:
gemini_batch 或 openai_batch

Selection:
由成本、队列和 Provider Health 决定。
```

只用于非紧急任务。

### J. 订单热路径、风控热路径、Release / Approval / ARM

```text
LLM ROUTING:
FORBIDDEN
```

任何以下路径：

```text
signal
risk
order
position
exit
reconciliation
kill_switch
approval
arm
```

都不能等待 GPT/Gemini。

## 5.8 双模型共识

关键任务不采用简单多数投票。

输出：

```text
providerA.findings
providerB.findings
agreement
disagreement
missingEvidence
confidence
```

机械路线：

```text
agree_high_confidence
agree_low_confidence
disagree_noncritical
disagree_critical
```

`disagree_critical`：

```text
blocked_human_review
```

AI不能自行决定 Live 或资金风险。

## 5.9 结构化输出

所有自动流程必须使用 JSON Schema。

输出先经过：

```text
JSON parse
schema validation
enum validation
semantic validation
artifact reference validation
hash validation
```

结构正确但语义错误也必须拒绝。

禁止把自然语言直接作为状态机输入。

## 5.10 Tool Allowlist

### 研究 AI 可使用

```text
read_artifact
search_research_memory
read_source_snapshot
read_data_manifest
request_deterministic_backtest
request_factor_bench
request_static_analysis
```

### 永久禁止

```text
read_secret
read_raw_private_account_response
place_order
cancel_order
modify_position
approve_release
arm_runtime
change_risk
withdraw
transfer
```

AI只能创建：

```text
proposal
draft
analysis
review
```

不能创建交易状态变更。

## 5.11 API Key 与数据保存

```text
OPENAI_API_KEY
GEMINI_API_KEY
```

仅来自：

```text
环境变量
OS Secret Store
服务器 Secret Manager
```

不得写入：

```text
SQLite
JSON
日志
证据 ZIP
前端
Git
```

Provider 调用默认：

```text
store=false / no logging
```

在 API 支持的情况下关闭服务端持久化。

日志中只保留：

```text
redactedPromptHash
inputArtifactHashes
provider
modelId
latency
tokenUsage
cost
status
responseHash
```

原始 Prompt / Response 是否保留由单独的数据政策控制，默认不保留敏感原文。

## 5.12 Batch 与缓存

非紧急大任务允许使用 Provider Batch API。

要求：

```text
batchManifestHash
每条 requestKey 唯一
防止重复提交
结果逐条校验
失败可恢复
```

Gemini Batch 创建非幂等，因此平台必须用本地：

```text
BatchSubmissionLedger
```

保证同一 Manifest 不重复提交。

大证据包可使用 Provider 支持的缓存，但：

```text
不得缓存 SECRET / RESTRICTED_TRADING
必须记录 cache policy
```

## 5.13 Fallback 与 Circuit Breaker

Provider Health：

```text
healthy
degraded
rate_limited
unavailable
disabled
```

非关键任务：

```text
Primary fail
→ same-provider alternate
→ other-provider fallback
```

关键双审任务：

```text
任一 Provider 不可用
→ blocked_waiting_independent_review
```

不得降级成单模型后自动执行。

## 5.14 成本与预算

每任务配置：

```text
maximumCostUSD
maximumTokens
maximumRetries
```

每日/每月：

```text
providerBudget
taskTypeBudget
researchCampaignBudget
```

超预算：

```text
paused_budget_exhausted
```

不得自动切换到更昂贵模型绕过预算。

## 5.15 AI 审计账本

每次调用记录：

```text
aiRunId
taskId
taskType
provider
modelId
promptVersion
inputArtifactHashes
redactionPolicyHash
toolCalls
startedAt
completedAt
latencyMs
inputTokens
outputTokens
estimatedCost
rawResponseHash
structuredOutputHash
schemaStatus
semanticStatus
humanReviewStatus
```

AI输出不得覆盖旧版本。

---

# 6. Phase 5：AI Research Governor

## 6.1 职责

```text
读取参考资料
读取失败策略记忆
生成新机制假设
检查重复家族
生成 Candidate DSL 草稿
生成测试草稿
解释失败
提出单变量下一实验
生成 Run Card
```

## 6.2 Negative Research Memory

回填历史策略和失败资产：

```text
strategyId
familyId
parentVersion
hypothesis
failureLayer
metrics
cost
capacity
stability
regime
familyFingerprint
signalCorrelation
prohibitedRepeats
```

AI每次生成候选前必须检索。

## 6.3 禁止“梯度追赢”

AI不能：

```text
看到结果后不断降低 Gate
连续修改多个变量
读取 Locked OOS 后调参
把一个正月份包装成策略成功
```

只允许：

```text
One-Variable-At-A-Time
```

## 6.4 AI 输出只是研究建议

AI输出必须转换为：

```text
HypothesisDraft
CandidateDraft
ExperimentDraft
```

只有确定性 Validator 通过后才进入真实试验。

---

# 7. Phase 6：匹配率、宽币池和频率组合

## 7.1 当前 Control Release 不变

当前三策略组合不原地改：

```text
组件
Definition Hash
冷却
ExitPolicy
历史证据
```

## 7.2 Matchability Service

逐策略/组件输出：

```text
requestedUniverse
publicUniverse
exchangeTradable
eligibleUniverse
componentCompatible
lookbackReady
dataReady
evaluated
rawSignals
identityPassed
cooldownRejected
riskRejected
orderEligible
orders
fills
closedTrades
```

UI：

```text
请求 Top200
合格 N
策略兼容 M
实际评估 K
信号 S
可下单 O
```

## 7.3 Broad-Universe Successor

为窄币池组件创建：

```text
Top25
Top50
Top100
Top200
```

离散、预注册的新 Candidate。

动态依据：

```text
成交额
盘口深度
历史完整性
容量
执行身份
```

不硬编码币名。

## 7.4 独立机制

每轮最多：

```text
4–6 个机制家族
每家族 1–2 候选
```

优先：

```text
趋势回调 / 恢复
波动 / 区间扩张
Funding / Basis 均值回复
横截面相对强弱
冲击 / 清算恢复
压缩释放
```

组合槽位：

```text
low_frequency >= 1
medium_frequency >= 1
high_frequency <= 1 experimental
```

高频必须真实验证：

```text
费用
滑点
Funding
延迟
部分成交
容量
```

---

# 8. Phase 7：因子、Qlib 与数值 ML

## 8.1 因子

```text
真实 OKX PIT Feature Panel
Alpha101
Alpha191 Compatibility
Crypto-native Funding / Basis / OI / Liquidity
IC / Rank IC
Decay
Turnover
Regime
Redundancy
```

因子挖掘：

```text
有界
Trial Ledger
FDR / PBO
```

## 8.2 数值模型

第一批：

```text
线性模型
树模型
Qlib 可复现 Campaign
```

用途：

```text
rank_only
veto_only
meta_label
risk_suggestion
```

LLM不直接替代这些模型进入实时交易决策。

验证：

```text
Purged Walk-forward
Calibration
Base vs Base+AI
Cost-adjusted Lift
Cross-symbol Stability
Regime Stability
Inference Latency
```

没有增量价值：

```text
保持 rule_only
```

---

# 9. Phase 8：ForwardTask 唯一化

废弃模糊的：

```text
需要向前验证 50
```

建立：

```text
taskId
releaseId
releaseHash
startedAt
status
closedTradeCount
effectiveSampleSize
symbolCoverage
regimeCoverage
costCompleteness
nextAction
```

UI必须有：

```text
开始 / 暂停 / 继续
当前进度
剩余证据
阻塞原因
下一步
```

30/50/100 只作为 Review 提示，不自动晋级。

---

# 10. Phase 9：UI V3

## 10.1 Strategy Factory

显示：

```text
AI Provider / Model
假设
数据
候选
Trial
Formal
失败树
预算
下一步
AI 解释
研究记忆命中
```

AI解释必须区分：

```text
Fact
Inference
Recommendation
Unverified
```

## 10.2 AI 控制页

增加：

```text
Provider Health
模型 Registry
Task Queue
Routing Policy
Token / Cost
失败重试
Dual Review 分歧
Prompt Version
```

默认用户只看到：

```text
任务
状态
当前模型
成本
结果
```

高级详情折叠。

## 10.3 Demo

显示：

```text
账户
策略
扫描漏斗
信号
拒绝
订单
持仓
PnL
延迟
错误
```

## 10.4 Live

首页只显示交易操作。

Readiness、Hash、AI审计移入“系统与审计”。

---

# 11. Phase 10：Codex 自检与开发纪律

每阶段写代码前输出：

```text
Fact Map
Entry-point Call Graph
Authority Map
Mutation Surface
Expected Invariants
Test Plan
Rollback Plan
```

## 11.1 Diff Budget

预注册：

```text
允许修改的文件
预计新增文件
最大 Diff 规模
```

超出必须解释并停在 Review。

## 11.2 Disconnect Test

断开真实调用链后，测试必须失败。

例如：

```text
删除 Model Runtime 注入
→ rank_only 测试必须失败
```

## 11.3 Mutation Test

移除关键 Gate、CSRF、Lease、PIT 检查后，测试必须失败。

## 11.4 AI Router 测试

至少：

```text
任务分类
敏感数据阻止
Provider Fallback
Dual Review
结构化输出
Prompt Injection
Cost Budget
Batch 幂等
Provider Timeout
Schema 错误
Semantic 错误
Tool Denial
日志脱敏
```

## 11.5 强制工具

```text
pytest + branch coverage
ruff
pyright / mypy
bandit / semgrep
vulture
radon
pip-audit / npm audit
Playwright
```

未运行保持 `not_run`。

---

# 12. Phase 11：AI 编排上线顺序

## Stage 1：Offline Read-only

```text
只处理历史证据
不改变 Factory 状态
```

## Stage 2：Shadow Critic

```text
对真实失败结果生成解释
但不改变下一实验
```

## Stage 3：Suggestion Mode

```text
AI 提议下一实验
人工或确定性 Policy 接受
```

## Stage 4：Bounded Autonomous Research

```text
AI 可管理研究队列
只能在预注册预算内
不能改变 Gate
不能自动 Demo
```

## Stage 5：Auto-Demo Draft

```text
AI 可生成 Demo Release 草稿
确定性 Gate 决定是否合格
```

是否自动进入 Experimental Demo 由单独 Policy 决定。

任何阶段：

```text
AI 绝不自动 Live
```

---

# 13. Phase 12：Headless 与服务器

P0/P1 稳定后：

```text
Windows Service / Task Scheduler
无网页两周期验收
自动重启先对账
固定 IP 服务器 Shadow
多轮 Parity
人工 Runtime Authority Cutover
```

服务器与本机不得 Active-Active。

---

# 14. 人工门

## Gate A：Demo Runtime ARM

```text
APPROVE DEMO RUNTIME ARM
<runtimeIdentityHash>
<releaseHash>
<riskHash>
```

## Gate B：Runtime Authority Cutover

```text
APPROVE RUNTIME AUTHORITY CUTOVER
<cutoverHash>
```

## Gate C：Auto-Demo Policy

```text
APPROVE AUTO DEMO POLICY
<policyHash>
```

## Gate D：Live Release

```text
APPROVE LIVE RELEASE
<releaseHash>
<modelPolicyHash>
<riskHash>
```

## Gate E：Live ARM

```text
APPROVE LIVE ARM
<armRequestHash>
```

AI不能代替用户完成这些 Gate。

---

# 15. API 与配置

## 15.1 Provider Config

```text
OPENAI_API_KEY
GEMINI_API_KEY
```

环境变量。

配置文件只保存：

```text
enabled
model alias
timeout
budget
routing priority
data policy
```

## 15.2 Platform API

```text
GET  /api/ai/providers
GET  /api/ai/models
GET  /api/ai/tasks
GET  /api/ai/tasks/<id>
GET  /api/ai/routing-policy
GET  /api/ai/usage

POST /api/ai/tasks
POST /api/ai/tasks/<id>/cancel
POST /api/ai/tasks/<id>/retry
POST /api/ai/routing-policy/draft
POST /api/ai/routing-policy/confirm
```

写接口继续使用统一安全 Middleware。

---

# 16. 必须交付的证据

最终只交付一个 Evidence ZIP 和 SHA-256。

至少：

```text
source_runtime_identity/
authority_retirement/
http_security/
kill_switch_flatten/
actual_risk/
sqlite_migrations/

strategy_factory/
real_trial_pilot/
negative_research_memory/
failure_tree/
matchability_frequency/

ai_orchestration/
provider_contracts.json
model_registry.json
routing_policy.json
task_type_matrix.json
redaction_audit.json
prompt_injection_tests.json
dual_review_audit.json
fallback_audit.json
batch_idempotency_audit.json
cost_budget_audit.json
ai_call_ledger_sample.json
structured_output_validation.json

factor_model/
forward_task/
ui_real_api/
headless_server/
tests_static_analysis/

artifact_manifest.json
final_closeout_cn.md
```

未运行：

```text
status=not_run
reason=<明确原因>
```

---

# 17. 最终验收条件

## 17.1 Strategy Factory

```text
completedTrialCount > 0
真实 Data Gate
真实 Backtest Job
真实 Formal Job（当候选合格时）
每个失败有具体归因
AI先检索负向研究记忆
```

## 17.2 GPT/Gemini Router

```text
业务代码无直接 Provider SDK 调用
所有调用经过 AIOrchestrationService
敏感数据阻止
关键任务双审
批量任务可路由
Provider 故障可恢复
成本可控
日志脱敏
订单路径无 LLM
```

## 17.3 AI 价值

至少对一组历史失败策略证明：

```text
AI 归因比旧“数据不足/未通过”更具体
AI 能指出证据引用
AI 能提出单变量下一实验
AI 不重复历史禁止模式
```

## 17.4 安全

```text
Live=false
Live ARM=false
Live Strategy Orders=0
Withdraw=false
```

除非后续单独通过 Gate D 和 Gate E。

---

# 18. Codex 最终回复模板

```text
AlphaPilot Global Reassessment + Multi-Model AI Orchestration 完成

Source / Runtime:
- Commits:
- Tags:
- Runtime Identity:
- Demo:
- Live:
- Withdraw:

P0:
- Fixed:
- Remaining:

Strategy Factory:
- Real trials:
- Formal jobs:
- Failure attribution:
- Forward tasks:
- Matchability:
- New mechanism families:

AI Orchestration:
- OpenAI adapter:
- Gemini adapter:
- Routing task types:
- Dual review:
- Batch:
- Redaction:
- Prompt injection:
- Cost:
- Provider fallback:
- Forbidden trading tools:

Factor / ML:
- Factor bench:
- Qlib:
- Models:
- Demo modes:
- Lift vs rule baseline:

UI:
- Factory:
- AI control:
- Demo:
- Live:

Tests:
- Coverage:
- Static analysis:
- E2E:
- Mutation / Disconnect:

Git:
Evidence ZIP:
SHA-256:
Current route:
```

---

# 19. 最终原则

```text
GPT 与 Gemini 不是两个可以直接操纵交易的“交易员”。
```

```text
它们是：
研究者、批评者、代码审查者、文档分析器和解释器。
```

```text
任务分发由确定性 Router 完成；
关键任务使用双模型独立审查；
交易核心永远不等待 LLM。
```

```text
AI 输出必须结构化、验证、版本化、可审计、可回滚。
```

```text
没有真实数据、真实 Trial、真实模型增量价值，
就不能因为 AI 给出漂亮解释而晋级策略。
```
