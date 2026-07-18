# AlphaPilot V13.27.1.26–V13.27.1.31
# 从候选排名语义闭合到 OKX Demo 准入的自动长工作流
## 给 Codex 的完整 Master Workflow 提示词

> **使用方式**
>
> 将本文件完整交给 Codex。
> Codex 应按阶段执行、机械分支、断点恢复，并一直运行到以下任一终态：
>
> ```text
> completed_demo_admission
> completed_research_forward_demo_admission
> completed_zero_qualified_candidates
> blocked_waiting_exact_release_approval
> blocked_formal_data
> blocked_okx_data
> blocked_remote_freeze
> blocked_demo_environment
> implementation_invalid
> ```
>
> 本工作流不要求每个阶段结束后重新向用户索要下一版提示词。
>
> **唯一强制人工交易准入点**
>
> ```text
> 用户对一个已经生成的精确 Release Hash 进行明确批准。
> ```
>
> 在此之前，Codex可以自动完成研究、预筛、正式验证、OKX 数据准备、
> Release 构建和 Console 导入准备；不得自动批准、不得 ARM、不得下单。
>
> **主要成功终态**
>
> ```text
> 至少一条不可变 Release 获得用户对精确 Hash 的批准，
> 成功导入 OKX Demo，
> Demo Runtime ARM=true，
> Runtime 健康，
> 完成首个正常扫描周期，
> Live / Withdraw 始终关闭。
> ```
>
> **合法安全终态**
>
> ```text
> 在冻结预算内没有任何候选达到 Demo 准入等级，
> 所有失败均被完整归档，
> Program 自动关闭。
> ```
>
> 不得无限搜索或降低门槛制造赢家。

---

# 一、当前权威基线

## 1.1 V25 身份

```text
Quant main:
2fb5a36

Quant tag:
v13.27.1.25

Docs main:
3336676

Docs tag:
v13.27.1.25-docs

Console:
6ea1f50
```

Program：

```text
automatic_strategy_demo_f57c443abeaf06c0
```

当前冻结候选：

```text
candidateId:
auto-trend_failure-reversal-4h-short-v2

familyId:
trend_failure_reversal

direction:
short

timeframe:
4h
```

## 1.2 V25 已经证明的事实

```text
Capacity Profile:
ohlcv_verified_capacity_v2

Profile Hash:
data_profile_f4f88817927b1813a80003c878fa21dfbe5f89c800a3af880178e972879ac2f3

Universe:
capacity_verified_core_swap_v1

24 / 24 分区：
exact quote turnover

真实正式窗口信号：
1,258

Capacity 可计算：
1,258

Capacity 通过 / 拒绝：
1,257 / 1

Formal Claim / Attempt / Result / Read：
0 / 0 / 0 / 0

Formal Budget consumed：
0

Locked OOS read：
0
```

因此：

```text
capacityDataReady = true
```

当前不能再将主要阻塞描述成：

```text
quote_turnover unavailable
```

## 1.3 当前剩余阻塞

原候选没有预注册：

```text
eventExtremeResidualZ
recoverySizeZ
```

的：

```text
来源
公式
窗口
标准化方法
availableAt
Point-in-Time 规则
排序方向
缺失处理
```

当前状态应解释为：

```text
signalReady = true
formalReady = false
demoReady = false

currentBlocker =
formal_data_blocked_ranking_semantics
```

## 1.4 Release 交易所边界

当前：

```text
Research / OHLCV:
unverified_local_exchange

Funding:
binance

Demo execution:
okx

crossExchangePortabilityStatus:
not_verified

releaseEligible:
false
```

即使历史正式验证通过，也不能跳过 OKX 同交易所或跨交易所可移植性阶段。

---

# 二、不可变历史保护

不得修改、覆盖、重算或移动：

```text
V13.27.1.19–V13.27.1.25 Tags
V19–V24 Program Ledger
V19–V24 候选与正式结果
V25 容量 Profile
V25 Snapshot
V25 端到端数据合同
V25 Formal Claim 计数
V18–V18.3 S01 历史证据
所有 Locked OOS access ledger
```

允许：

```text
新增解释 Sidecar
新增 Candidate ID
新增 Campaign ID
新增 Data Profile
新增 Future Locked OOS Identity
新增不可变 Release
```

禁止：

```text
编辑旧候选
移动旧 Tag
amend 结果提交
force-push
删除失败尝试
用新证据覆盖旧报告
```

---

# 三、总体版本与阶段

| 版本 | 阶段 | 核心交付 |
|---|---|---|
| V13.27.1.26 | 通用排名语义与当前候选裁决 | Candidate-neutral Ranking Contract、v3 候选或关闭当前候选、预筛 |
| V13.27.1.27 | 数据就绪的新候选自动研究 | 有界市场机制、候选生成、端到端数据 Gate、预筛幸存者 |
| V13.27.1.28 | 正式验证批次 | 五折、资本竞争、成本、基准、统计、机械路由 |
| V13.27.1.29 | OKX 同交易所与可移植性 | OKX 数据 Profile、确认性重放、Release 资格 |
| V13.27.1.30 | 不可变 Release 与批准请求 | Release Bundle、Console 导入、精确 Hash 批准暂停 |
| V13.27.1.31 | OKX Demo Cutover | 合约交集、风险 Overlay、ARM、首个正常扫描周期 |

可以跳过不需要的阶段：

```text
当前候选通过 V26 预筛
→ 不必等 V27 新候选全部生成；
→ 直接与已有正式候选队列进入 V28。
```

但任何阶段的硬 Gate 都不能跳过。

---

# 四、Master Program 编排

## 4.1 Program 身份

建议：

```text
programId =
automatic_strategy_to_demo_v26_<canonical_hash>

parentProgramId =
automatic_strategy_demo_f57c443abeaf06c0
```

新增或扩展现有：

```text
alphapilot/research_factory/orchestrator.py
alphapilot/research_factory/program_state.py
alphapilot/research_factory/program_ledger.py
alphapilot/research_factory/resume.py
```

不得建立第二套并行编排器。

## 4.2 状态机

```text
baseline_frozen

ranking_contract_ready
current_candidate_resolved

renewal_hypotheses_frozen
candidate_batch_certified
prefilter_completed

formal_panel_frozen
formal_validation_completed

okx_data_ready
release_eligibility_resolved

immutable_release_ready
demo_waiting_exact_release_approval

demo_imported
demo_armed
completed
blocked
```

## 4.3 幂等与恢复

必须：

```text
append-only Program Ledger
Hash-addressed Artifact
每阶段幂等
每次结果运行使用 one-shot token
恢复时禁止重复读取结果
失败从最近安全 Checkpoint 继续
```

恢复必须输出：

```text
previousCheckpoint
nextAllowedStage
formalClaimsConsumed
formalResultsRead
lockedOosReads
releaseCount
approvalCount
```

---

# 五、全局研究预算

读取原 Program Budget，当前：

```text
Full Backtests Used:
1

Original Maximum:
144

Remaining:
143
```

本 Master Program 冻结：

```text
maximumAdditionalCampaigns = 3
maximumFamiliesPerCampaign = 8
maximumInitialVariantsPerFamily = 2
maximumInitialCandidatesPerCampaign = 16
maximumStructuralRevisionsPerFamily = 1
maximumFormalCandidatesPerCampaign = 6
maximumFullBacktestsPerCampaign = 48
maximumAdditionalFullBacktests = 143
maximumDemoReleasesPerCampaign = 3
```

自动提前停止：

```text
发现至少一条 release-eligible 候选
→ 停止继续生成新候选；
→ 转入 Release 阶段。
```

预算耗尽：

```text
completed_zero_qualified_candidates
```

不得自动扩容预算。

---

# 六、全局安全边界

全阶段禁止：

```text
保存 raw API Key / Secret / Passphrase
输出私有签名请求头
接 Withdraw
接 Live 交易
读取 Live 账户
读取 Live 持仓
创建 Live 订单
自动批准 Release
自动晋级 Live
```

缺失数据：

```text
保持 null / unavailable
```

禁止：

```text
缺失填 0
当前 TopN 回填历史 PIT
结果后删币
结果后改 Gate
Martingale
无限加仓
扩大初始止损
黑箱生成最终买卖信号
```

OKX Demo 凭证：

```text
只由当前进程注入
不持久化
所有私有请求必须保留：
x-simulated-trading: 1
```

---

# 七、统一 Advisory-R 规则

继续使用：

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

不得恢复：

```text
targetR >= 2 的候选创建硬门
```

但仍保留：

```text
成本后正期望
PF
最大回撤
样本量
Walk-forward
资本竞争
简单基准
统计稳定性
Locked OOS
不可变 Release
```

---

# 第八章：V13.27.1.26
# 通用排名语义与当前冻结候选裁决

## 8.1 V25 解释 Sidecar

新增：

```text
reports/automatic_research_program/
automatic_strategy_demo_f57c443abeaf06c0/
v26/v25_ranking_semantics_clarification_sidecar.json
```

内容：

```text
capacitySemanticsResolved = true
capacityCertificationPassed = true

remainingFormalMissingFields = [
  eventExtremeResidualZ,
  recoverySizeZ
]

clarifiedCurrentStatus =
formal_data_blocked_ranking_semantics

oldCandidateMutable = false
```

---

## 8.2 Candidate-neutral Ranking Contract

新增：

```text
alphapilot/formal_validation/
  candidate_ranking_contract.py
  candidate_ranking_registry.py
  candidate_ranking_evidence.py

tests/formal_validation/
  test_candidate_ranking_contract.py
  test_candidate_ranking_registry.py
  test_candidate_ranking_evidence.py
```

Formal Core 统一使用语义槽位：

```text
primaryEventSeverity
confirmationStrength
liquidity30d
instrumentId
```

Core 只负责：

```text
字段值
排序方向
availableAt
Point-in-Time 规则
缺失政策
sourceHash
contractHash
```

Core 不得写死：

```text
S01
Residual
EMA
Trend Failure
Breakout
Pullback
```

旧 S01 合同和 Hash 保持不变，通过 Adapter 映射到通用槽位。

---

## 8.3 当前候选语义推导审计

只读检查原冻结候选：

```text
HypothesisSpec
CandidateSpec
CandidateAdapter
Freqtrade Adapter
Entry Predicate
Event Payload
Simple Benchmark
Source Commit
```

禁止读取：

```text
新 PnL
新退出结果
新统计
Locked OOS
```

两个排名语义必须来自原候选已经使用的：

```text
趋势定义
趋势失效条件
均衡区定义
反向确认条件
ATR / 波动标准化
原有窗口
```

输出：

```text
ranking_semantics_derivation_audit.json
ranking_semantics_derivation_audit.md
```

状态：

```text
uniquely_derivable_from_frozen_entry_logic

not_derivable_without_new_hypothesis

multiple_plausible_semantics
```

禁止：

```text
测试多个公式后按回测挑最好
套用 S01 默认窗口
引入原候选未使用的新指标
```

---

## 8.4 当前候选自动分支

### 分支 A：唯一可推导

创建新候选：

```text
candidateId:
auto-trend_failure-reversal-4h-short-v3-ranking-contract

parentCandidateId:
auto-trend_failure-reversal-4h-short-v2

revisionType:
single_preregistered_structural_revision
```

允许变化：

```text
Candidate ID
Ranking Contract
Formal Data Profile
Future OOS Identity
```

保持不变：

```text
信号
入场
方向
周期
止损
ExitPolicy
maximumHold
Capital Policy 数值
Gate
Universe
Split
Cost
Benchmark
Statistics
```

该家族结构修订预算：

```text
消耗 1 / 1
```

### 分支 B：无法唯一推导

原 v2 状态：

```text
closed_current_candidate_ranking_semantics_not_derivable
```

不得人工猜测，也不得阻塞整个 Program。

自动进入：

```text
V27 新候选研究
```

---

## 8.5 Ranking Contract 对象

必须包含：

```text
candidateRankingContractId
candidateId
familyId
schemaVersion

primaryEventSeverity:
  semanticDefinition
  sourceVariables
  formula
  normalization
  lookback
  order
  availableAt
  pointInTimeRule
  missingPolicy

confirmationStrength:
  semanticDefinition
  sourceVariables
  formula
  normalization
  lookback
  order
  availableAt
  pointInTimeRule
  missingPolicy

liquidity30d:
  dataProfileHash
  formula
  lookback
  order=descending
  availableAt
  missingPolicy

instrumentId:
  order=ascending
  exactCanonicalIdentity=true
```

全部 Canonical JSON、稳定 Hash。

---

## 8.6 当前候选真实信号认证

新增：

```text
real_signal_candidate_ranking_certification_v1
```

使用：

```text
原候选信号路径
新 CandidateAdapter
新 Ranking Contract
V25 Capacity Profile
全部 1,258 条正式窗口信号
```

禁止：

```text
退出
PnL
PF
回撤
基准收益
统计
```

硬门：

```text
canonicalSignalParityVsV2 = 100%
rankingRecordCoverage = 100%
requiredRankingAvailability >= 95%
capacityInputCoverage = 100%
postEntryReadCount = 0
economicReadCount = 0
```

不通过：

```text
当前候选关闭
自动进入 V27
Formal Claim = 0
```

---

## 8.7 当前候选预筛重放

新 Candidate ID 必须生成新预筛证据。

要求：

```text
v2 / v3 signal parity = 100%
entry parity = 100%
exitPolicy identity parity = 100%
```

Ranking 不能用于：

```text
删除预筛亏损事件
改变信号
改变 Exit
```

预筛失败：

```text
archive_v3_prefilter_failed
→ 进入 V27
```

预筛通过：

```text
加入 V28 Formal Candidate Queue
```

---

# 第九章：V13.27.1.27
# 数据正式就绪的新候选自动研究

本阶段仅在：

```text
当前候选未进入 V28
或
需要补充正式候选数量
```

时执行。

## 9.1 第一批策略类型

正式支持：

```text
directional_event
```

优先：

```text
4h
1h
```

15m 仅在：

```text
成本、Quote Turnover、Ranking 和执行数据完整
```

时允许。

5m 默认禁用。

暂不扩展：

```text
pair_relative_value
cross_sectional_portfolio
carry_basis
```

除非已有独立经过认证的 Capital Policy Profile 和 Adapter Contract。

---

## 9.2 候选创建前端到端数据合同

每个候选必须在 Candidate ID 生成前声明：

```text
signalRequiredFields
rankingRequiredFields
exitRequiredFields
capitalRequiredFields
costRequiredFields
benchmarkRequiredFields
statisticalRequiredFields
demoRequiredFields
optionalDiagnosticFields
```

状态：

```text
signal_ready
prefilter_ready
formal_ready
release_ready
demo_ready
```

正式候选创建硬门：

```text
required field semantic coverage >= 95%
Capacity coverage = 100%
availableAt causality = 100%
Point-in-Time rule complete
no post-entry dependency
```

不满足：

```text
data_blocked_before_candidate_creation
```

不消耗 Candidate Trial 和 Formal Budget。

---

## 9.3 研究资料

必须读取：

```text
186 个策略身份
72 个历史策略家族
失败归因
Negative Research Rules
Strategy Revival Policy
Advisory-R
Alpha 因子手册
既有市场机制与架构文档
```

外部资料只用于：

```text
提出可证伪市场机制
研究方法
数据定义
```

禁止：

```text
复制许可证不兼容代码
直接搬用黑箱策略
把指标列表当市场假设
```

---

## 9.4 市场机制家族

每 Campaign 最多：

```text
8 个独立家族
每家族最多 2 个初始版本
```

允许研究方向示例：

```text
趋势失效后的反向再定价
结构回踩后的趋势恢复
波动压缩后的方向释放
BTC 冲击后的因果滞后
波动冲击的不对称恢复
市场宽度变化下的方向过滤
跨时段流动性转换
价格—成交额背离后的状态切换
```

每家族必须有：

```text
机制
加密市场适用理由
反证条件
简单基准
正式 Data Profile
候选专用 Ranking Contract
历史家族重叠审计
```

---

## 9.5 HypothesisSpec

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

dataProfileId
requiredFields
rankingContractDraft
simpleBenchmark
expectedFrequency
knownFailureModes
historicalOverlap
sourceReferences
```

历史重叠：

```text
只是阈值微调
同一失败机制换名字
堆叠指标
```

必须拒绝：

```text
hypothesis_duplicate
```

---

## 9.6 CandidateSpec

每个候选：

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

candidateRankingContract
dataProfileHash
simpleBenchmarkHash
capitalPolicyProfileHash
GateHash
```

每条候选只允许：

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

## 9.7 CandidateAdapter 与结构认证

每个候选自动生成：

```text
CandidateAdapter
Freqtrade Adapter
Simple Benchmark
Synthetic Fixture
Real-Signal Structural Certification
Ranking Evidence Certification
PIT Context Certification
```

要求：

```text
Formal Core 不修改
coreEngineChangedForCandidate = false
```

---

## 9.8 事件预筛

使用：

```text
冻结候选
冻结 Data Profile
代表币
基础成本
1.5x 成本
简单基准
```

输出：

```text
事件数
PF
平均净 R
总净 R
MFE / MAE
退出原因
最大回撤
基准增量
集中度
```

预筛 Gate：

```text
读取现有批准的 PrefilterGatePolicy 和 Hash；
不得在本提示词中另造较宽松门槛。
```

每家族最多：

```text
1 条幸存者
```

每 Campaign 最多：

```text
6 条 Formal Candidate
```

0 条幸存是合法结果。

---

# 第十章：V13.27.1.28
# 正式验证批次

## 10.1 比较面板先冻结

正式结果前冻结：

```text
候选集合
统一日期
统一成本
统一 Capital Policy
各自 Benchmark
Trial 计数规则
日度收益面板 Schema
```

用于：

```text
NW
BH / BY
DSR
PBO
White Reality Check
SPA
```

不得结果后追补可比候选。

---

## 10.2 正式预注册

每候选绑定：

```text
Candidate Hash
Ranking Contract Hash
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
→ Preregistration Commit / Push
→ Future Locked OOS Identity
→ one-shot Authorization
```

---

## 10.3 正式运行前认证

每候选必须：

```text
Runtime loaded
Canonical Identity = 100%
Event Disposition = 100%
Ranking Record = 100%
Required Ranking Availability >=95%
PIT Context = 100%
Capacity Input = 100%
Capital Decision parity = 100%
Position Size parity = 100%
Exit-leg Fixture parity = 100%
```

未通过：

```text
formal_data_blocked
或
implementation_invalid
```

不得消耗 Formal Claim。

---

## 10.4 五折正式验证

复用 V18.3/V25 正式核心：

```text
5 Fold
Purge
Embargo
资本竞争
Base
1.5x
2.0x
Funding actual / stress / unavailable
Simple Benchmark
```

必须报告：

```text
每 Fold tradeCount
PF
averageNetR
totalNetR
maximumDrawdown
benchmarkIncrement
costStress
```

---

## 10.5 Gate

必须复用仓库现有正式 Gate Hash。

禁止：

```text
为了产生 Release 调低 PF
放宽 Drawdown
减少 Fold 要求
删除不利币种或月份
```

输出至少包括：

```text
经济 Gate
成本 Gate
Benchmark Gate
统计 Gate
集中度 Gate
Parity Gate
Funding/OOS Evidence Grade
```

---

## 10.6 结果等级

```text
formal_pass

research_pass_no_clean_holdout

research_pass_funding_unavailable

formal_economic_failed

statistical_failed

capital_infeasible

implementation_invalid

formal_data_blocked
```

失败候选：

```text
当前版本永久归档
不得原地调参
```

---

## 10.7 自动 Campaign 续期

若 0 条通过：

只有以下条件才能自动启动下一 Campaign：

```text
Program Budget 未耗尽
存在 materially different hypothesis
Formal Data Profile ready
不是同一家族阈值微调
没有降低 Gate
```

达到预算或没有新信息：

```text
completed_zero_qualified_candidates
```

---

# 第十一章：V13.27.1.29
# OKX 同交易所、可移植性与 Release 资格

只处理：

```text
V28 的 formal_pass / research_pass 候选
```

## 11.1 首选：OKX 同交易所正式数据 Profile

优先从官方或经过认证的 OKX 数据源建立：

```text
okx_same_exchange_release_profile_v1
```

要求：

```text
精确 USDT SWAP
OHLCV
Quote Turnover
Funding
Instrument state
时间戳与 availableAt
不可变 Manifest
```

数据下载/导入必须独立于结果运行：

```text
先采集
→ 审计
→ 冻结
→ 才运行确认性结果
```

不得在正式结果运行中静默补数据。

---

## 11.2 OKX 确认性 Campaign

如果 OKX 历史数据充分：

```text
同一 Candidate
同一 Strategy Hash
同一 ExitPolicy
同一 Capital Policy
同一 Gate
新 Data Profile
新 Campaign
新 Preregistration
```

重新执行：

```text
信号 Parity
预筛
五折
成本
基准
统计
```

OKX 确认性失败：

```text
blocked_okx_portability
或
okx_confirmatory_failed
```

不得生成标准 Release。

---

## 11.3 跨交易所可移植性路线

只有 OKX 同交易所历史无法达到正式覆盖时才允许。

必须在任何候选可移植性结果前冻结 Portability Policy。

至少检查：

```text
exact instrument identity
signal timestamp parity
event overlap
return correlation
direction parity
entry-price behavior
Funding difference
cost difference
capacity difference
```

不存在既有批准阈值时：

```text
先冻结一个独立 Portability Policy；
不得看完候选表现后设置门槛。
```

未通过：

```text
releaseEligible = false
```

---

## 11.4 两种 Demo Evidence Class

### A. 标准策略验证 Demo

要求：

```text
formal_pass
+
OKX same-exchange confirmatory pass
或
frozen portability policy pass
```

Release：

```text
releasePurpose = strategy_forward_validation
evidenceClass = demo_strategy_validation
strategyQualification = true
livePromotionEligible = false
```

### B. Research-Forward Demo

仅允许：

```text
research_pass_no_clean_holdout
或
research_pass_funding_unavailable
```

且必须：

```text
实现/Parity 全部通过
历史经济 Gate 通过
不存在数据语义或实现 blocker
用户明确批准该 evidenceClass
```

Release：

```text
releasePurpose = research_forward_validation
evidenceClass = demo_research_forward
strategyQualification = false
formalPass = false
livePromotionEligible = false
```

Research-Forward Demo 不能自动成为 Live Candidate。

---

## 11.5 OKX Demo 元数据

从认证 Demo Instrument 接口获取：

```text
instrument_id
lot_size
tick_size
contract value
minimum size
state
```

要求：

```text
精确 USDT SWAP
```

禁止：

```text
Spot 替代 Swap
USDC 替代 USDT
Coin-margined 替代
Base symbol 模糊匹配
认证失败后回退公共列表
```

---

# 第十二章：V13.27.1.30
# 不可变 Release 与精确批准

## 12.1 Release Bundle

每 Campaign 最多：

```text
3 条
```

每条包含：

```text
releaseId
releaseHash
campaignId
candidateId
familyId

strategyDefinitionHash
candidateRankingContractHash
exitPolicyHash
dataProfileHash
dataManifestHash
preregistrationHash
costModelHash
capitalPolicyHash
benchmarkHash
formalGateHash
backtestReportHash

okxDataProfileHash
portabilityPolicyHash
portabilityAuditHash

releasePurpose
evidenceClass
environment=demo
approvalRequired=true
approved=false
createdAt
```

源 Artifact 任一 Hash 变化：

```text
拒绝生成
```

---

## 12.2 Release 排名

只在相同 Evidence Class 内确定性排序。

复用现有 Release Ranking Policy。

至少考虑：

```text
1.5x Cost PF
averageNetR
positive folds
drawdown
benchmark increment
concentration
sample size
statistical evidence
candidateId tiebreak
```

禁止人工按“看起来最赚钱”挑选。

---

## 12.3 Demo Risk Overlay

Overlay 只能更严格，不能更宽松。

标准 Demo：

```text
不得超过 Release 冻结风险
同时受 Console 全局 Demo 上限
```

Research-Forward Demo 建议：

```text
maximumConcurrentPositions = 1
maximumOpenRisk <= 0.10% equity
no adding
no averaging
no martingale
exact frozen ExitPolicy
```

Overlay 必须独立 Hash。

---

## 12.4 Console 导入

Release 导入状态：

```text
demo_waiting_approval
```

不得：

```text
自动 approved
自动 ARM
自动下单
```

输出：

```text
release_import_audit.json
release_store_record.json
```

---

## 12.5 精确 Hash 批准请求

生成：

```text
demo_approval_request.md
demo_approval_request.json
```

必须展示：

```text
Release ID / Hash
Candidate
Evidence Class
Market Mechanism
5 Fold
Base / 1.5x / 2x
Drawdown
Benchmark Increment
Statistics
Funding/OOS 限制
OKX Portability
Risk Overlay
Live 禁止状态
```

然后暂停：

```text
blocked_waiting_exact_release_approval
```

用户必须回复：

```text
精确 Release Hash
```

不能预批准未来未知 Release。

---

# 第十三章：V13.27.1.31
# OKX Demo Cutover

只有精确批准后执行。

## 13.1 批准 Overlay

批准记录：

```text
append-only
releaseHash
riskOverlayHash
approver
timestamp
evidenceClass
```

Approval Hash 必须与 Release Hash 精确绑定。

---

## 13.2 Demo Universe

运行：

```text
公共 Point-in-Time Universe
∩
Authenticated OKX Demo Instruments
```

要求：

```text
private response fresh
exact USDT SWAP
Release instruments eligible
```

输出：

```text
publicUniverseCount
demoInstrumentCount
intersectionCount
releaseEligibleInstrumentCount
excludedReasons
stale
```

---

## 13.3 工程烟测隔离

若 Demo 下单链路从未完成或当前环境失效：

```text
先运行 engineering_smoke
```

工程烟测只证明：

```text
discover
submit
inspect
cancel / close
reconcile
```

它不能：

```text
更新策略 PF
计入 Forward Evidence
晋级策略
```

独立 Ledger。

---

## 13.4 Strategy Demo ARM

验证：

```text
Release Hash
Approval Hash
Risk Overlay Hash
CandidateAdapter
Runtime Hash
Demo Universe
x-simulated-trading: 1
```

然后：

```text
ARM Strategy Validation Demo Runtime
```

禁止：

```text
ARM Live
Withdraw
Live Account
Live Position
Live Order
```

---

## 13.5 工作流成功终止

标准 Demo：

```text
releasePurpose = strategy_forward_validation
approvalHashMatched = true
demoArm = true
runtimeHealthy = true
firstScheduledScanCompleted = true
liveTradingEnabled = false
```

终态：

```text
completed_demo_admission
```

Research-Forward：

```text
releasePurpose = research_forward_validation
strategyQualification = false
livePromotionEligible = false
approvalHashMatched = true
demoArm = true
firstScheduledScanCompleted = true
```

终态：

```text
completed_research_forward_demo_admission
```

不要求强行制造订单：

```text
没有信号时 orderCount 可以为 0
```

---

# 十四、进入 Demo 后的停止边界

本 Master Program 到以下位置结束：

```text
Release 已批准
Demo 已 ARM
首个正常扫描周期完成
```

不在本 Program 内等待：

```text
30 笔闭合交易
100 笔闭合交易
Live Canary
自动策略进化
```

这些属于后续：

```text
Demo Forward Evidence and Strategy Evolution Program
```

可以建立 Ledger，但不等待长期结果。

---

# 十五、机械路由全集

候选状态只允许：

```text
data_blocked_before_candidate_creation
hypothesis_duplicate
ranking_semantics_not_derivable
implementation_invalid
prefilter_failed
formal_data_blocked
capital_infeasible
formal_economic_failed
statistical_failed
archived

research_pass_no_clean_holdout
research_pass_funding_unavailable
formal_pass

okx_confirmatory_failed
blocked_okx_portability

immutable_release_ready
demo_waiting_approval
demo_validation_running
```

Program 状态只允许：

```text
completed_demo_admission
completed_research_forward_demo_admission
completed_zero_qualified_candidates

blocked_waiting_exact_release_approval
blocked_formal_data
blocked_okx_data
blocked_remote_freeze
blocked_demo_environment
implementation_invalid
```

---

# 十六、测试要求

## 16.1 Ranking Contract

```text
Candidate-neutral Core
Stable Canonical Hash
S01 旧合同不变
Candidate-specific mapping
Unique-derivation audit
Multiple semantics fail closed
No result reads
```

## 16.2 End-to-End Data Gate

```text
Signal Ready
Prefilter Ready
Formal Ready
Release Ready
Demo Ready

Capacity coverage
Ranking availability
availableAt
PIT
No post-entry dependency
```

## 16.3 Research Factory

```text
Program Budget
Resume
One-shot
Artifact immutability
Historical family dedup
CandidateAdapter generic path
No Core changes
```

## 16.4 Formal Validation

```text
Runtime
Identity
Disposition
Ranking
PIT
Capacity
Capital
Position size
Exit legs
5 Fold
Costs
Benchmark
Statistics
```

## 16.5 OKX / Release / Demo

```text
Same-exchange data profile
Portability policy preregistered
Exact USDT SWAP
Authenticated instrument intersection
Release Hash immutability
No auto approval
Risk Overlay cannot widen
Demo / Live isolation
Engineering smoke isolation
No duplicate/orphan
```

---

# 十七、验证命令

Quant：

```powershell
python -m pytest tests/formal_validation -q --import-mode=importlib
python -m pytest tests/research_factory -q --import-mode=importlib
python -m pytest tests/research_screening -q --import-mode=importlib
python -m pytest tests/statistical_validation -q --import-mode=importlib
python -m pytest tests/exit_policy -q --import-mode=importlib
python -m pytest tests -q --import-mode=importlib

python -m compileall alphapilot
python -m alphapilot.scripts.validate_config

powershell -ExecutionPolicy Bypass `
  -File scripts\check_safety.ps1

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

# 十八、Git 与远端冻结纪律

每阶段：

```text
独立 Worktree
保护用户原有修改
不 reset
不 amend 历史 Tag
不 force-push
```

结果前：

```text
Implementation Commit / Push
Remote Commit verification
Preregistration Commit / Push
Remote Preregistration verification
Future Locked OOS Identity
one-shot Authorization
```

正式结果后：

```text
Result Commit
Closeout Commit
Tag
Push
工作区核验
```

---

# 十九、提交顺序

建议最少：

```text
1. Preserve V25 and freeze the V26 Master Program identity

2. Add candidate-neutral ranking contracts without mutating historical S01 contracts

3. Resolve the frozen trend-failure candidate or close it without guessing

4. Generate only end-to-end formal-ready new candidates under bounded budgets

5. Freeze the formal comparison panel and preregistrations remotely

6. Run formal validation exactly once per authorized candidate

7. Build and freeze OKX same-exchange or portability evidence

8. Generate immutable eligible Demo releases

9. Import releases as unapproved and stop for exact-hash approval

10. Record the exact approval overlay

11. ARM the approved OKX Demo release and close after the first normal scan
```

---

# 二十、输出目录

```text
reports/automatic_strategy_to_demo/<programId>/
```

至少包含：

```text
program_spec.json
program_state.json
program_ledger.jsonl
program_budget.json
program_budget_ledger.jsonl

baseline_identity.json
v25_ranking_semantics_clarification_sidecar.json

candidate_ranking_registry.json
ranking_semantics_derivation_audit.*
current_candidate_resolution.json

data_profile_inventory.json
hypothesis_inventory.json
hypothesis_novelty_matrix.csv
candidate_inventory.json
candidate_data_gate_matrix.csv

prefilter_metric_matrix.csv
prefilter_gate_matrix.csv
prefilter_failure_attribution.json

formal_candidate_inventory.json
formal_metric_matrix.csv
formal_gate_matrix.csv
statistical_availability_matrix.json
trial_lineage.json

okx_data_profile_manifest.json
okx_confirmatory_results.json
portability_policy.json
portability_audit.json

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

# 二十一、程序证据包

程序结束时生成：

```text
AlphaPilot-V26-31-Automatic-Strategy-to-Demo-Core-Evidence.zip

AlphaPilot-V26-31-Candidate-and-Formal-Evidence.zip

AlphaPilot-V26-31-OKX-Release-and-Demo-Evidence.zip
```

禁止包含：

```text
API Key
Secret
Passphrase
私有签名请求头
虚拟环境
真实账户信息
原始大体积 OHLCV
```

---

# 二十二、最终自检

Codex 最终必须输出：

```text
Program ID
Parent Program
Final Route

V25 baseline commit / tag
Historical artifact mutation count

Current candidate resolution
Old v2 / new v3 identity
Ranking derivation status
Ranking Contract Hash
V2/V3 signal parity

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

Full backtests used
Budget remaining

Formal results:
- accepted trades
- Base / 1.5x / 2x
- Drawdown
- Benchmark
- Statistics
- Funding / OOS status

OKX:
- Same-exchange Profile
- Portability status
- Release eligibility
- Instrument metadata
- Exact USDT SWAP intersection

Selected Release:
- Release ID
- Release Hash
- Purpose
- Evidence Class
- Risk Overlay Hash
- Approval Hash

Demo:
- Import
- ARM
- Runtime health
- First scheduled scan
- Strategy order count
- Duplicate/orphan count

Safety:
- Locked OOS reads
- Formal Evidence
- Live enabled
- Trade API
- Withdraw
- Credentials persisted

Git:
- Quant commit/tag/push
- Console commit/tag/push
- Docs commit/tag/push

Tests
Hash mismatches
Sensitive hits
Known limitations
Next Program
```

---

# 二十三、Codex 最终回复模板

```text
AlphaPilot V13.27.1.26–V13.27.1.31 长工作流结束

Program:
Final Route:

Current Candidate:
- v2 resolution:
- v3 Candidate:
- Ranking Contract:

Research:
- Campaigns:
- Families:
- Candidates:
- Prefilter Survivors:
- Research Pass:
- Formal Pass:
- Archived:

Formal:
- Trades:
- Base:
- 1.5x:
- 2x:
- Drawdown:
- Benchmark:
- Statistics:
- Funding/OOS:

OKX:
- Same-Exchange Profile:
- Portability:
- Release Eligible:
- Demo Instrument Intersection:

Release:
- Release ID:
- Release Hash:
- Purpose:
- Evidence Class:
- Approval Hash:
- Risk Overlay Hash:

Demo:
- Imported:
- ARM:
- Runtime:
- First Scan:
- Orders:
- Duplicate/Orphan:

Safety:
- Locked OOS:
- Live:
- Trade API:
- Withdraw:
- Credentials Persisted:

Budget:
- Full Backtests:
- Remaining:

Git:
Tests:
Artifacts:

[Core Evidence ZIP](...)
[Candidate and Formal Evidence ZIP](...)
[OKX Release and Demo Evidence ZIP](...)
[Final Self Check](...)
[Final Route](...)
```

---

# 二十四、最终原则

```text
自动化的目标是产生可信的通过或可信的失败，
不是一直搜索到出现一个漂亮回测。
```

必须坚持：

```text
排名语义从候选机制出发；
正式必需字段在候选创建前定义；
先 Data Ready，再消耗正式预算；
先预注册，再读取结果；
先 OKX 可移植性，再生成执行 Release；
先精确 Hash 批准，再 ARM Demo；
Demo 不等于 Live；
0 个通过是合法结果；
任何通过都必须不可变、可复核、可撤销。
```
