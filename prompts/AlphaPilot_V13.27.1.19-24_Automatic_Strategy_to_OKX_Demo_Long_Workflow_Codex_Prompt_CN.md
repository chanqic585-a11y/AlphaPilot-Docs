# AlphaPilot V13.27.1.19–V13.27.1.24
# 数据能力驱动的自动策略研究工厂、正式验证与 OKX Demo 准入长工作流

## 给 Codex 的完整长工作流提示词

> **项目目标**
>
> 在不修改 V18.3 历史证据、不复活 S01、不降低 Gate、不读取 Locked OOS、
> 不制造通过策略的前提下，搭建并运行一条可重复、可恢复、有限预算的自动研究流水线：
>
> ```text
> 数据能力审计
> → 独立市场机制生成
> → 历史失败去重
> → 候选实现
> → 快速事件预筛
> → 正式 Walk-forward 与统计审计
> → 不可变 Release
> → 精确 Hash 人工批准
> → OKX Demo 准入与 ARM
> ```
>
> **主要成功终态**
>
> ```text
> 至少 1 条候选获得允许进入 Demo 的证据等级，
> 生成不可变 Release，
> 由用户批准精确 Release Hash，
> 成功导入并 ARM 到 OKX 策略验证 Demo，
> 完成至少一个正常调度扫描周期。
> ```
>
> **合法安全终态**
>
> ```text
> 在冻结预算内没有候选达到 Demo 准入条件，
> 所有候选被可信归档，
> Campaign 自动关闭并输出下一轮数据/市场机制建议。
> ```
>
> 不得承诺一定找到盈利策略；不得为了达到 Demo 目标无限生成候选、
> 降低 Gate、复用已查看 Holdout 或调参救活失败策略。

---

# 0. 当前权威基线

V18.3 已完整关闭：

```text
Campaign:
advisory_r_v18_3_s01_fold_ranking_evidence_correction_aa74e9a2bff29616

Formal result commit:
3e3f85e4309ef4719149768b906c862704e1e027

Closeout commit:
f9f36f420496a9cbd59bc079bbcdf7796bac6c5a

Tag:
v13.27.1.18.3

Route:
archive_s01_current_version
```

S01 当前版本：

```text
永久归档
不得原地修改
不得继续 V18.4
不得通过放宽 Ranking / Capacity / Gate 恢复
```

V18.3 已证明可复用的基础能力：

```text
CandidateAdapter
Formal Validation Core 与候选解耦
版本化 Policy Object
Formal Event Disposition
不可变 Ranking Evidence
真实信号结构认证
正式 Freqtrade Runtime
Canonical Event Identity
PIT Portfolio Context
资本竞争与仓位路径
五折顺序验证
一次性正式运行
Artifact Hash / Manifest
机械路由
零 OOS / 零 Release / 零订单安全路径
```

本长工作流必须复用这些能力，不得重写第二套正式验证引擎。

---

# 1. 总体版本与阶段

建议按以下版本执行；如标签已占用，先审计并使用下一个未占用版本，
但阶段语义不得改变。

| 版本 | 阶段 | 主要交付 |
|---|---|---|
| V13.27.1.19 | 数据能力与自动编排基础 | Data Capability Matrix、Data Profiles、Program Ledger、自动恢复 |
| V13.27.1.20 | 新假设与候选生成 | 有界独立家族、CandidateSpec、Adapter、负样本去重 |
| V13.27.1.21 | 自动预筛与正式 Campaign 冻结 | 事件预筛、幸存者、比较面板、正式预注册 |
| V13.27.1.22 | 正式验证与机械路由 | Walk-forward、资本竞争、成本、基准、统计、Research/Formal Pass |
| V13.27.1.23 | Release 与 Console 准入 | 不可变 Release、导入、精确 Hash 批准请求 |
| V13.27.1.24 | OKX Demo Cutover | Demo Universe、批准 Overlay、ARM、首个正常扫描周期 |

每个阶段：

```text
实现
→ 测试
→ Commit
→ Push
→ Tag（仅阶段收口）
→ Artifact Manifest
→ 机械 Gate
→ 自动进入下一阶段或安全停止
```

---

# 2. 自动化边界

## 2.1 可自动执行

Codex 可自动完成：

```text
数据审计
数据 Profile 建立
市场机制资料整理
候选生成与去重
CandidateAdapter / Freqtrade 实现
结构认证
事件预筛
正式预注册
远端冻结
五折 Walk-forward
成本压力
资本竞争
简单基准
统计审计
机械归档
Release 生成
Console 导入
Demo 准入准备
```

## 2.2 唯一强制人工门

必须保留：

```text
用户对精确 Release Hash 的明确批准
```

批准前：

```text
approved=false
Demo ARM=false
策略订单=0
```

不得使用：

```text
“自动批准最优策略”
“预先批准未来未知 Hash”
“管理员默认同意”
```

## 2.3 不得自动执行

```text
Live ARM
Live API
实盘订单
Withdraw
API Key 持久化
根据 Demo 结果自动晋级实盘
```

---

# 3. 程序级终态

整个长工作流只允许以下顶层终态：

```text
completed_demo_admission
```

至少一条 Release 获得精确 Hash 批准并进入策略验证 Demo。

```text
completed_research_forward_demo_admission
```

没有 Clean Locked OOS，但一条强研究候选经显式批准进入隔离的
Research-Forward Demo；不能晋级 Live。

```text
completed_zero_qualified_candidates
```

有限预算耗尽，0 条候选达到 Demo 准入。

```text
blocked_data_capability
blocked_remote_freeze
blocked_formal_engine
blocked_demo_environment
blocked_waiting_exact_release_approval
```

禁止：

```text
无限循环直到通过
```

---

# 4. 程序预算

新增：

```text
ProgramBudget
```

建议冻结：

```text
maximumResearchCampaigns = 3
maximumFamiliesPerCampaign = 8
maximumInitialVariantsPerFamily = 2
maximumInitialCandidatesPerCampaign = 16
maximumStructuralRevisionPerFamily = 1
maximumFormalCandidatesPerCampaign = 6
maximumFullBacktestsPerCampaign = 48
maximumFullBacktestsAcrossProgram = 144
maximumDemoReleasesPerCampaign = 3
```

规则：

```text
每个家族最多一次预注册结构修订；
结果读取后不得继续调当前版本；
没有新数据或新机制时不得自动启动下一 Campaign；
达到程序总预算必须停止。
```

输出：

```text
program_budget.json
program_budget_ledger.jsonl
budget_consumption_summary.json
```

---

# 5. 长流程可恢复编排器

新增：

```text
alphapilot/research_factory/
  program_types.py
  program_state.py
  program_ledger.py
  orchestrator.py
  stage_gates.py
  resume.py
  artifact_paths.py
```

建议脚本：

```text
scripts/run_automatic_strategy_to_demo_program.ps1
```

命令：

```powershell
powershell -ExecutionPolicy Bypass `
  -File scripts\run_automatic_strategy_to_demo_program.ps1 `
  -ProgramSpec "research\programs\<programId>.json" `
  -Resume
```

要求：

```text
append-only ledger
每阶段幂等
结果阶段有 one-shot token
失败可从最近安全 Checkpoint 恢复
不得重复已读取结果
不得覆盖 Hash 地址 Artifact
```

状态至少包括：

```text
program_created
baseline_frozen
data_capability_ready
hypotheses_frozen
candidates_certified
prefilter_completed
formal_campaign_frozen
formal_validation_completed
release_ready
demo_waiting_approval
demo_armed
completed
blocked
```

每次恢复必须输出：

```text
previousCheckpoint
nextAllowedStage
oneShotClaimsConsumed
resultReadCount
```

---

# 6. 全局不可变安全边界

所有阶段必须：

```text
不保存 raw API Key / Secret / Passphrase
不输出私有签名请求头
不接 Withdraw
不接 Live 交易
不读取 Live 账户和仓位
不创建 Live 订单

缺失数据保持 null / unavailable
不把缺失值填 0
不从当前 TopN 回填历史 PIT
不结果后删币
不结果后改 Gate
不使用 Martingale
不扩大初始止损
不无限加仓
不强制生成 Winner
```

OKX Demo 凭证：

```text
仅由当前进程注入
不写磁盘
所有私有请求必须 x-simulated-trading: 1
```

---

# 7. Advisory-R 统一规则

后续候选继续使用：

```text
riskMetric = R
targetRGateMode = advisory
minimumTargetR = null
initialStopMayWiden = false
exitPolicyRequired = true
```

允许：

```text
fixed_r
partial_then_trailing
structure_or_time
hybrid
```

不得重新引入：

```text
targetR >= 2 的候选生成硬门
<2R 回测前拒绝
```

但以下仍为硬门：

```text
成本后正期望
PF
样本充足
最大回撤
正 Walk-forward Fold
1.5x 成本压力
基准增量
集中度
实现 Parity
OOS 纪律
不可变 Release
```

---

# 第一阶段：V13.27.1.19
# 数据能力矩阵与自动研究基础

## 8. V18.3 基线冻结

只读核验：

```text
v13.27.1.18.3 Tag
Formal Result Commit
Closeout Commit
Formal Result Manifest Hash
65/65 Hash
S01 route=archive_s01_current_version
Locked OOS access=0
Release=0
ARM=false
Orders=0
```

生成：

```text
reports/automatic_research_program/<programId>/
  baseline_identity.json
  v18_3_reusable_core_manifest.json
  v18_3_archived_candidate_reference.json
```

不得修改 V18.3。

增加一项非结果审计：

```text
structural_certification_vs_formal_event_delta_audit.json
```

只解释结构认证 924 事件与正式 884 事件的输入差异；不得重跑 S01 或改变归档结论。

---

## 9. 数据能力矩阵

新增：

```text
alphapilot/research_factory/data_capability.py
alphapilot/research_factory/data_profiles.py
alphapilot/research_factory/field_semantics.py
alphapilot/research_factory/available_at.py
```

扫描：

```text
D:\Codex-Workspace\回测数据
AlphaPilot Data Manifests
现有不可变 Snapshot
前向采集目录
```

维度：

```text
exchange
marketType
instrumentId
timeframe
field
start
end
rowCount
coveragePct
unit
source
availableAtRule
causal
pointInTime
hash
```

至少检查：

```text
OHLCV
base volume
quote volume
contract volume
quote turnover
VWAP（可计算/不可计算）
BTC returns
residual / beta inputs
Funding
OI
Basis
spot/perp
liquidation
orderbook
instrument state
PIT universe
```

输出：

```text
data_capability_matrix.parquet
data_capability_matrix.csv
data_capability_summary.json
field_semantics_registry.json
data_gap_queue.json
```

---

## 10. Data Profile

建立候选生成前使用的不可变 Data Profile。

建议至少：

### 10.1 `ohlcv_core_directional_v1`

```text
策略类型：directional_event
周期：1h / 4h
字段：OPEN/HIGH/LOW/CLOSE
Volume：可选
Universe：冻结核心币池
用途：第一批自动候选主 Profile
```

### 10.2 `ohlcv_verified_turnover_v1`

```text
字段：OHLCV + 已认证 Quote Turnover
Universe：只依据数据语义、在假设生成前冻结
不得依据策略收益选择
```

### 10.3 `future_derivatives_v1`

```text
Funding / OI / Basis / Liquidation / Orderbook
用途：前向研究
历史覆盖不足时不得生成正式历史候选
```

每个 Profile 输出：

```text
profileId
profileHash
universeHash
fieldSet
coverage
timeframes
commonCutoff
availableAt
knownLimitations
```

---

## 11. Candidate Data Gate

每条候选生成前必须声明：

```text
requiredFields
optionalFields
dataProfileId
timeframes
minimumHistory
availableAtRules
missingDataPolicy
```

建议硬门：

```text
必需字段在冻结 Universe 和验证区间的覆盖率 >= 95%
字段单位明确
因果 availableAt 明确
没有 post-entry 数据
共同有效历史满足 Split
```

不足：

```text
data_blocked_before_candidate_creation
```

不生成 Candidate ID，不消耗正式 Trial 或 Backtest 预算。

严禁再次出现：

```text
先生成策略
→ 跑到正式阶段
→ 发现所有 Ranking 必需字段不可用
```

---

## 12. 第一阶段退出门

必须满足：

```text
Data Capability Matrix 完整
至少 1 个可用于 directional_event 的 Profile ready
所有必需字段缺失可机器识别
Program Ledger 可恢复
V18.3 原证据 Hash 不变
```

否则：

```text
blocked_data_capability
```

---

# 第二阶段：V13.27.1.20
# 独立市场机制与候选自动生成

## 13. 研究资料输入

必须读取：

```text
186 个策略身份 / 72 个历史家族
失败归因
Negative Research Rules
可复用组件
Advisory-R 规则
Alpha 因子手册
现有市场机制研究文档
```

允许 Codex 搜索：

```text
论文
交易所官方文档
高质量开源研究仓库
市场微观结构资料
```

要求：

```text
记录来源
只提取机制与研究方法
不复制许可证不兼容代码
不开启外部依赖黑箱
```

---

## 14. 第一批只支持 `directional_event`

为了尽快让首条新策略进入 Demo，本 Program 第一批不得扩展新的执行类型。

正式支持：

```text
directional_event
```

暂不作为本 Program 候选：

```text
pair_relative_value
cross_sectional_portfolio
carry_basis
```

它们进入后续能力队列，不阻塞第一批。

优先周期：

```text
4h
1h
```

15m 只有在：

```text
成本数据充分
预期频率有上界
预筛成本后仍有明显优势
```

时才允许。

5m 默认禁用。

---

## 15. 机制家族生成

每个 Campaign 自动生成最多 8 个独立家族。

允许机制来源示例，不是强制名单：

```text
趋势状态中的结构回踩延续
波动压缩后的方向释放
BTC 冲击后的因果滞后反应
波动冲击后的方向不对称
跨时段流动性转换
残差极端后的因果恢复
趋势失败后的结构反转
市场宽度/联动变化下的方向过滤
```

每个家族必须：

```text
有市场机制
有反证条件
有简单基准
有数据 Profile
与历史失败家族有实质差异
不是堆指标
```

---

## 16. HypothesisSpec

每个家族生成：

```text
hypothesisId
familyId
displayNameZh
marketMechanism
whyCryptoSpecific
falsificationCondition
strategyType
direction
timeframe
requiredDataProfile
requiredFields
optionalFields
simpleBenchmark
expectedTradeFrequency
knownFailureModes
historicalOverlap
sourceReferences
```

必须通过：

```text
historical_family_semantic_dedup
```

输出：

```text
hypothesis_inventory.json
hypothesis_novelty_matrix.csv
historical_overlap_audit.json
```

规则：

```text
与失败家族只是阈值微调
→ duplicate / rejected

旧归档结构性负边际
→ 必须 materially different hypothesis
```

---

## 17. CandidateSpec

每个家族最多 2 个初始候选。

CandidateSpec 至少包含：

```text
candidateId
familyId
hypothesisId
strategyDefinitionHash

entryDefinition
marketRegime
initialStop
exitPolicy
maximumHoldBars
direction
timeframe

requiredDataProfile
requiredFields
simpleBenchmark
capitalPolicyProfile
GatePolicy
```

每条策略只保留：

```text
一个核心 Setup
```

其他指标只能：

```text
确认
过滤
否决
排序
```

---

## 18. CandidateAdapter 与 Freqtrade 实现

每个候选自动生成：

```text
CandidateAdapter
Freqtrade Strategy Adapter
独立 Simple Benchmark
Synthetic Fixture
Real-Signal Structural Certification
```

硬要求：

```text
Formal Core 不修改
不 import 候选专用模块
Artifact 路径动态
CandidateAdapter Contract 统一
ExitPolicy 使用权威引擎
```

新增候选时必须输出：

```text
coreEngineChangedForCandidate=false
```

否则：

```text
implementation_invalid
```

---

## 19. 候选生成退出门

所有候选必须先通过：

```text
数据 Gate
新颖性 Gate
CandidateAdapter Contract
真实信号结构认证
Event Disposition
Ranking/PIT Evidence 记录
Freqtrade Fixture Parity
```

未通过者：

```text
不进入事件预筛
```

---

# 第三阶段：V13.27.1.21
# 自动事件预筛与正式 Campaign 冻结

## 20. 快速事件预筛

使用：

```text
8–12 个代表币
冻结 Data Profile
冻结候选
冻结 ExitPolicy
冻结基础成本
冻结简单基准
```

预筛运行：

```text
原始事件
成本后 Gross/Net R
PF
事件数
MFE/MAE
退出原因
基础成本
1.5x 成本
简单基准增量
```

不得读取 Locked OOS。

---

## 21. 预筛 Gate

优先复用仓库现有批准的 `PrefilterGatePolicy`。

如果缺失某个字段：

```text
在任何结果前冻结
```

不得事后改阈值。

至少要求：

```text
样本达到预注册下限
PF > 1
平均净 R > 0
总净 R > 0
基础成本后为正
1.5x 成本不能显著崩溃
明显优于简单基准
没有单币/单月极端集中
```

输出：

```text
prefilter_results.json
prefilter_metric_matrix.csv
prefilter_gate_matrix.csv
prefilter_failure_attribution.json
```

---

## 22. 幸存者限制

每 Campaign：

```text
maximumFormalCandidates = 6
maximumPerFamily = 1
```

同家族多个版本：

```text
只保留预注册的确定性代表
其余保留为已读 Trial
```

0 条幸存是合法结果。

---

## 23. 统计比较面板必须在正式结果前冻结

对全部可比预筛候选建立：

```text
candidate_daily_return_panel_plan.json
campaign_multiple_testing_scope.json
```

冻结：

```text
候选集合
统一日期
统一成本
统一资本政策 Profile
各自简单基准
Trial 计数规则
```

为：

```text
BH / BY
DSR
PBO
White Reality Check
SPA
```

准备输入。

不得正式结果后追补候选面板。

---

## 24. 正式预注册

对每个幸存者生成独立 Campaign，同时保存共同父 Campaign。

必须绑定：

```text
Candidate Hash
Data Profile Hash
Universe Hash
Split Hash
Cost Hash
Capital Policy Hash
Benchmark Hash
Statistical Policy Hash
Gate Hash
Runtime Hash
I/O Guard Hash
Candidate Panel Hash
```

顺序：

```text
代码 Commit / Push
→ 预注册 Commit / Push
→ Future Locked OOS Identity
→ one-shot Authorization
```

---

# 第四阶段：V13.27.1.22
# 正式验证与 Research/Formal Pass

## 25. 正式运行前认证

每个候选必须通过：

```text
真实信号结构认证
Runtime loaded
Canonical Identity 100%
Event Disposition 100%
Ranking Evidence Record 100%
PIT Context 100%
Capital Decision 100%
Position Size 100%
Exit-leg Fixture 100%
```

未通过：

```text
implementation_invalid
```

不得消耗正式结果 Claim。

---

## 26. 正式五折顺序验证

复用 V18.3 Formal Core。

每候选一次性运行：

```text
5 Fold
Purge
Embargo
资本竞争
Base Cost
1.5x Cost
2x Cost
Funding actual/stress/unavailable
Simple Benchmark
```

必须输出每折：

```text
tradeCount
PF
averageNetR
totalNetR
maximumDrawdown
benchmarkIncrement
costStress
```

---

## 27. 正式账户路径

必须模拟：

```text
最大并发
总开放风险
单币风险
同方向风险
相关簇风险
组合 Beta
容量
重复信号
确定性竞争排序
```

候选缺少某个可选资本字段：

```text
按已冻结 Policy 稳定拒绝
```

不得分类为实现失败，除非证据记录本身缺失。

---

## 28. 经济 Gate

优先复用 V18.3 已冻结正式 Gate，不降低。

至少：

```text
5/5 Fold 完整
PF >= 1.05
averageNetR > 0
totalNetR > 0
maximumDrawdown <= 25%
positiveFoldCount >= 3

1.5x:
PF >= 1.00
averageNetR > 0
totalNetR > 0

simple benchmark:
overall incrementalNetR > 0
positiveIncrementFoldCount >= 3
```

Funding 缺失：

```text
不得填 0
```

---

## 29. 统计审计

在共同冻结面板可用时运行：

```text
Newey-West
BH / BY
Deflated Sharpe Ratio
PBO
White Reality Check
SPA
Bootstrap CI
```

不可用：

```text
status=unavailable
exactReason
```

不得拼凑。

---

## 30. 结果等级

### 30.1 `formal_pass`

需要：

```text
经济 Gate
统计 Gate
实现 Gate
Clean Locked OOS 或已批准的正式 OOS 证据
Funding 证据要求
```

### 30.2 `research_pass_no_clean_holdout`

需要：

```text
历史 Walk-forward 经济 Gate 通过
实现 Parity 通过
统计 Gate 通过或按预注册状态可接受
但 Clean Locked OOS 尚未成熟
```

### 30.3 `research_pass_funding_unavailable`

需要：

```text
除真实 Funding 外其他历史经济 Gate 通过
Funding 保持 unavailable
```

### 30.4 失败

```text
prefilter_failed
formal_economic_failed
statistical_failed
capital_infeasible
implementation_invalid
data_blocked
archived
```

当前版本失败后不得修当前 Candidate。

---

## 31. Campaign 失败后的自动下一轮

若 0 条 Demo 准入候选：

```text
分析失败矩阵
```

只有以下条件才自动启动下一 Campaign：

```text
Program Budget 未耗尽
存在至少一个 materially different hypothesis
所需 Data Profile 已 ready
不复用同一失败家族的微调版本
```

下一 Campaign 必须：

```text
新 Campaign ID
新 Hypothesis IDs
新 Candidate IDs
新 Trial Ledger
```

不得：

```text
降低 Gate
放宽资本政策
删除亏损币
继续调当前失败版本
```

---

# 第五阶段：V13.27.1.23
# 不可变 Release 与 Demo 准入等级

## 32. 两类 Demo Release

为解决 Clean Future OOS 需要时间的问题，定义两个严格隔离的 Demo 证据等级。

---

## 32.1 标准策略验证 Demo

仅允许：

```text
formal_pass
```

Release：

```text
releasePurpose = strategy_forward_validation
evidenceClass = demo_strategy_validation
strategyQualification = true
livePromotionEligible = false
approvalRequired = true
approved = false
```

---

## 32.2 Research-Forward Demo

仅在没有 Clean Locked OOS/Funding，但历史研究证据充分时允许。

必须满足：

```text
research_pass_no_clean_holdout
或
research_pass_funding_unavailable

所有历史经济 Gate 通过
所有实现 Parity 通过
没有未解决 implementation blocker
明确用户批准该证据等级
```

Release：

```text
releasePurpose = research_forward_validation
evidenceClass = demo_research_forward

strategyQualification = false
formalPass = false
livePromotionEligible = false
forwardReviewEligible = true

approvalRequired = true
approved = false
```

Research-Forward Demo：

```text
不能生成 Live Candidate
不能自动升级 Formal Pass
只能收集真实 Demo 前向闭合交易
```

---

## 33. Immutable Release

每个 Campaign 最多 3 个 Release。

Release 必须包含：

```text
releaseId
releaseHash
campaignId
candidateId
strategyId
familyId

strategyDefinitionHash
exitPolicyHash
dataProfileHash
dataManifestHash
preregistrationHash
costModelHash
capitalPolicyHash
benchmarkHash
formalGateHash
backtestReportHash

releasePurpose
evidenceClass
approvalRequired
approved=false
environment=demo
createdAt
```

任何源 Artifact Hash 变化：

```text
拒绝生成
```

---

## 34. Release 排名

只在同一证据等级内排序。

确定性依据：

```text
formal/research status
1.5x PF
averageNetR
positive folds
drawdown
benchmark increment
concentration
sample size
evidence completeness
candidateId tiebreak
```

禁止：

```text
事后人工选看起来最赚钱的一条
```

---

## 35. Demo Risk Overlay

Demo 可使用更严格、但不能更宽松的风险 Overlay。

Research-Forward 建议：

```text
maximumConcurrentPositions = 1
maximumOpenRisk <= 0.10% equity
no adding
no averaging
no martingale
bounded retry
exact frozen exits
```

标准策略验证 Demo：

```text
使用 Release 冻结风险
同时受 Console Demo 安全上限约束
```

Overlay：

```text
不能改变信号
不能改变 ExitPolicy
只能降低仓位/并发
必须独立 Hash
```

---

# 第六阶段：V13.27.1.24
# Console 导入、精确批准与 OKX Demo ARM

## 36. Console 导入

Console 只从配置允许的 Quant 报告路径读取：

```text
Hash 已验证 Release
```

导入状态：

```text
demo_waiting_approval
```

不得：

```text
自动 approved
自动 ARM
自动创建订单
```

输出：

```text
release_import_audit.json
release_store_record.json
```

---

## 37. 精确 Hash 批准请求

生成：

```text
demo_approval_request.md
demo_approval_request.json
```

展示：

```text
Release Hash
Candidate
证据等级
正式/研究状态
5 Fold
成本压力
回撤
基准增量
统计状态
Funding/OOS 限制
Demo Risk Overlay
禁止 Live
```

然后强制暂停：

```text
blocked_waiting_exact_release_approval
```

用户必须回复精确 Release Hash。

批准记录：

```text
append-only
绑定 exact releaseHash
绑定 riskOverlayHash
绑定 approver
绑定 timestamp
```

---

## 38. OKX Demo Universe

批准后，使用：

```text
公共 Point-in-Time Universe
∩
Authenticated OKX Demo Instruments
```

必须精确匹配：

```text
USDT SWAP
```

禁止：

```text
现货替换永续
USDC 替换 USDT
Coin-margined 替换
仅按 base 模糊匹配
私有接口失败后回退公共列表
```

输出：

```text
demo_universe_count
intersection_count
release_instruments_eligible
excluded_reasons
stale
```

---

## 39. 工程烟测隔离

如果 OKX Demo 订单链路尚未通过工程烟测：

```text
先执行 engineering_smoke
```

它只能证明：

```text
发现
下单
查询
撤销/平仓
对账
```

不能：

```text
计入策略胜率
计入 Forward Evidence
晋级策略
```

工程烟测 Ledger 与策略 Demo Ledger 分离。

---

## 40. Strategy Validation Demo ARM

批准和 Universe Gate 通过后：

```text
导入 exact Release
加载 CandidateAdapter / Runtime
校验 Release Hash
校验 Approval Hash
校验 Demo Risk Overlay
校验 x-simulated-trading:1
ARM Strategy Validation Runtime
```

不得开启：

```text
Live
Withdraw
Live Account
Live Position
```

---

## 41. 长工作流成功终止条件

### 41.1 标准成功

```text
at leastOneApprovedRelease = true
releasePurpose = strategy_forward_validation
releaseHashLoaded = true
approvalHashMatched = true
demoArm = true
runtimeHealthy = true
firstScheduledScanCompleted = true
liveTradingEnabled = false
orderCount may be 0 if no signal
```

终态：

```text
completed_demo_admission
```

### 41.2 Research-Forward 成功

```text
releasePurpose = research_forward_validation
strategyQualification = false
livePromotionEligible = false
exact approval = true
Demo ARM = true
firstScheduledScanCompleted = true
```

终态：

```text
completed_research_forward_demo_admission
```

不得强制制造信号或合成订单。

---

# 42. 进入 Demo 后立即停止本 Program

本长工作流到：

```text
Release 已批准
Demo 已 ARM
首个正常扫描周期完成
```

即结束。

不在本 Program 继续：

```text
等待 30 笔闭合交易
等待 100 笔闭合交易
实盘 Canary
策略自动进化
```

这些属于下一 Program：

```text
Demo Forward Evidence and Strategy Evolution
```

---

# 43. Demo 后续证据规则（只建立，不等待）

可以建立但不执行长期等待：

```text
0–29 closed trades:
collecting

30–99:
preliminary_review_ready

>=100:
serious_review_ready
```

只有策略 Demo 的真实闭合交易计数。

不计入：

```text
工程烟测
Shadow
本地历史
Legacy Release
开放订单
开放仓位
```

任何门槛都不能自动晋级 Live。

---

# 44. 自动研究总控机械路由

每条候选只允许：

```text
data_blocked
hypothesis_duplicate
implementation_invalid
prefilter_failed
formal_economic_failed
statistical_failed
capital_infeasible
archived

research_pass_no_clean_holdout
research_pass_funding_unavailable
formal_pass

immutable_release_ready
demo_waiting_approval
demo_validation_running
```

程序不得创建未定义状态。

---

# 45. 测试要求

## 45.1 Research Factory

```text
Program budget
Resume
One-shot
Artifact immutability
Data Gate
Historical dedup
CandidateAdapter generic path
No core modification
```

## 45.2 Formal Validation

```text
Event disposition
Ranking/PIT evidence
Runtime
Identity
Exit legs
Capital
Position size
Five folds
Costs
Benchmarks
Statistics
```

## 45.3 Release

```text
Only eligible status creates Release
At most 3
Canonical JSON
Hash immutability
No automatic approval
Advisory-R compatibility
```

## 45.4 Demo

```text
Exact USDT SWAP
Authenticated intersection
Credentials not persisted
Approval exact Hash
Risk Overlay cannot widen
Demo/Live isolation
Engineering smoke isolation
No duplicate/orphan
```

---

# 46. 验证命令

Quant：

```powershell
python -m pytest tests/research_factory -q --import-mode=importlib
python -m pytest tests/research_screening -q --import-mode=importlib
python -m pytest tests/formal_validation -q --import-mode=importlib
python -m pytest tests/statistical_validation -q --import-mode=importlib
python -m pytest tests/exit_policy -q --import-mode=importlib
python -m pytest tests -q --import-mode=importlib

python -m compileall alphapilot
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

Console：

```powershell
python -m pytest tests -q
python -m compileall alphapilot_control_console
node --check web/app.js
python -m alphapilot_control_console.http_app --smoke
git diff --check
```

---

# 47. Git 与提交纪律

每个阶段：

```text
独立 worktree
保护用户原有改动
不 reset
不 amend 历史 Tag
不 force-push
结果前 Commit / Push
预注册前远端核验
```

最少提交顺序：

```text
1. Freeze V18.3 baseline and build data-capability contracts
2. Add resumable automatic research-program orchestration
3. Freeze independent market hypotheses and candidate definitions
4. Implement candidate adapters without modifying the core
5. Run bounded prefilter and freeze the formal candidate panel
6. Freeze formal campaigns and Future OOS identities remotely
7. Run formal validation once per candidate
8. Generate immutable eligible Demo releases
9. Import releases without approval
10. Record exact-hash approval overlay
11. ARM approved OKX Demo release and close the program
```

---

# 48. 输出目录

```text
reports/automatic_research_program/<programId>/
```

至少包含：

```text
program_spec.json
program_state.json
program_ledger.jsonl
program_budget.json
program_budget_ledger.jsonl

baseline_identity.json
data_capability_matrix.*
data_profiles.json
data_gap_queue.json

hypothesis_inventory.json
hypothesis_novelty_matrix.csv
candidate_inventory.json
candidate_data_gate_matrix.csv

prefilter_metric_matrix.csv
prefilter_gate_matrix.csv
prefilter_failure_attribution.json

formal_campaign_inventory.json
formal_metric_matrix.csv
formal_gate_matrix.csv
statistical_availability_matrix.json
trial_lineage.json

eligible_candidate_ranking.csv
candidate_releases/
release_import_audit.json

demo_approval_request.*
demo_approval_overlay.json
demo_universe_audit.json
demo_arm_audit.json

final_route_decision.json
final_self_check.md
artifact_manifest.json
```

---

# 49. 证据包

程序结束时生成：

```text
AlphaPilot-Automatic-Strategy-to-Demo-Core-Evidence.zip
AlphaPilot-Automatic-Strategy-to-Demo-Candidate-Evidence.zip
AlphaPilot-Automatic-Strategy-to-Demo-Release-and-Demo-Evidence.zip
```

禁止包含：

```text
API Key
Secret
Passphrase
私有签名请求头
虚拟环境
原始大体积 OHLCV
真实账户信息
```

---

# 50. 最终自检

Codex 最终必须输出：

```text
Program ID
Final Program Route
Campaign count
Family count
Candidate count
Prefilter survivor count
Formal candidate count

Data-blocked count
Duplicate count
Implementation-invalid count
Archived count
Research-pass count
Formal-pass count

Total full backtests
Budget used
Budget remaining

Selected Release ID / Hash
Release purpose
Evidence class
Approval Hash
Risk Overlay Hash

Demo Universe intersection
Release instruments eligible
Demo ARM
Runtime health
First scheduled scan
Demo order count
Duplicate/orphan count

Locked OOS reads
Formal Evidence
Release count
Live enabled
Trade API
Withdraw API

Quant commit/tag/push
Console commit/tag/push
Docs commit/tag/push

Tests
Hash mismatch
Sensitive hits
Known limitations
Next program
```

---

# 51. 最终回复模板

```text
AlphaPilot 自动策略研究至 Demo 长工作流结束

Program:
Final Route:

Research:
- Campaigns:
- Families:
- Candidates:
- Prefilter Survivors:
- Research Pass:
- Formal Pass:
- Archived:

Budget:
- Full Backtests:
- Program Budget Status:

Selected Release:
- Release ID:
- Release Hash:
- Purpose:
- Evidence Class:
- Approval Hash:
- Demo Risk Overlay:

Demo:
- Exact USDT SWAP Universe:
- Intersection:
- ARM:
- Runtime:
- First Scan:
- Strategy Orders:
- Duplicate/Orphan:

Safety:
- Locked OOS Reads:
- Formal Evidence:
- Live:
- Withdraw:
- Credentials Persisted:

Git:
Tests:
Artifacts:

[Core Evidence ZIP](...)
[Candidate Evidence ZIP](...)
[Release and Demo Evidence ZIP](...)
[Final Self Check](...)
[Final Route](...)
```

---

# 52. 最终原则

```text
自动化的目标是快速产生可信通过或可信失败，
不是无限搜索直到出现一个漂亮回测。
```

必须坚持：

```text
先数据、后假设；
先机制、后指标；
先预注册、后结果；
先正式通过、后 Release；
先精确批准、后 Demo；
Demo 不等于 Live；
0 个通过是合法结果；
任何通过都必须可复核、不可变、可撤销。
```
