# AlphaPilot V13.27.1.25–V13.27.1.26
# 端到端数据合同闭合、容量语义验证与冻结候选重放工作流

## 给 Codex 的完整执行提示词

> **背景**
>
> V13.27.1.19–V13.27.1.24 已完整运行并安全收口：
>
> ```text
> Program:
> automatic_strategy_demo_f57c443abeaf06c0
>
> Final Route:
> completed_zero_qualified_candidates
>
> Families / Candidates:
> 8 / 16
>
> Prefilter Survivor:
> 1
>
> Formal Candidate:
> auto-trend_failure_reversal-4h-short-v2
>
> Full Backtests:
> 1 / 144
>
> Formal Route:
> capital_infeasible
>
> Assigned Formal Events:
> 1,258
>
> Stable Capacity Rejections:
> 1,258
>
> Accepted Trades:
> 0
>
> Locked OOS Reads:
> 0
>
> Release / Demo ARM / Orders:
> 0 / false / 0
> ```
>
> 经过证据包独立复核，当前最关键的问题不是预算耗尽，也不是候选已经完成经济失败验证，
> 而是跨阶段数据合同不一致：
>
> ```text
> Candidate Data Gate 只要求 OHLC；
> reported_volume 被标记为 optional；
> verified quote_turnover Profile 已明确 blocked；
> 但正式资本政策强制要求 verified quote_turnover。
> ```
>
> 因此该候选在进入 Formal 前已经注定无法获得资本准入。
>
> 本工作流只做：
>
> ```text
> V19–V24 基线合并与证据冻结
> → 端到端数据依赖合同修复
> → reported_volume 来源和单位追溯
> → 建立可验证 Capacity Data Profile
> → 对原冻结幸存候选进行一次新 Campaign 重放
> → 若通过则继续不可变 Release 与 OKX Demo 精确批准
> ```
>
> 不生成新市场机制，不修改候选参数，不降低资本政策或 Gate。

---

# 一、独立结论与分类侧车

## 1.1 原结果保持不可变

不得修改：

```text
V19–V24 Tags
Program Ledger
Formal Result
Formal Route
Evidence ZIP
Candidate Preregistration
Capital Policy
Release/Console Zero Route
```

## 1.2 新增解释侧车

生成：

```text
reports/automatic_research_program/
automatic_strategy_demo_f57c443abeaf06c0/
v19_24_capacity_semantics_clarification_sidecar.json
```

记录：

```text
originalRoute = capital_infeasible
originalProgramRoute = completed_zero_qualified_candidates

economicResultValid = false
strategyPerformanceFailure = false
implementationFailure = false
dataContractFailure = true

clarifiedClassification =
formal_data_blocked_capacity_semantics

clarifiedPrimaryReason =
candidate_data_profile_not_compatible_with_mandatory_capital_policy_inputs

prefilterSurvivorStatus =
frozen_prefilter_survivor_waiting_verified_capacity_profile
```

不得覆盖原报告，只新增侧车。

---

# 二、先将 V19–V24 安全合并为新基线

当前三个仓库功能分支和 Tags 已 Push，但尚未合并到 main。

执行：

```text
1. 核验 Quant / Console / Docs 的远端功能分支；
2. 核验 Tags v13.27.1.19–v13.27.1.24 和 v13.27.1.24-docs；
3. 保护 main 上已有提交和用户原有修改；
4. 使用普通 merge，不 rebase 历史结果、不 force-push；
5. 运行完整 CI；
6. 记录 merge Commit；
7. Push main；
8. 确认三个仓库工作区干净或仅保留任务前已存在内容。
```

如果合并冲突会改变权威结果 Artifact：

```text
blocked_baseline_merge
立即停止
```

建议 V25 Tag 只在本阶段完整收口时创建。

---

# 三、修复“只检查信号数据、不检查正式资本数据”的合同缺口

新增：

```text
alphapilot/research_factory/
  end_to_end_data_contract.py
  data_dependency_graph.py
  formal_data_gate.py
  demo_data_gate.py

tests/research_factory/
  test_end_to_end_data_contract.py
  test_data_dependency_graph.py
  test_formal_data_gate.py
```

## 3.1 每个候选的数据依赖必须分层

统一字段：

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

端到端硬依赖：

```text
formalRequiredFields =
signalRequiredFields
∪ rankingRequiredFields
∪ exitRequiredFields
∪ capitalRequiredFields
∪ costRequiredFields
∪ benchmarkRequiredFields

demoRequiredFields =
formalRequiredFields
∪ demo execution identity / instrument metadata fields
```

## 3.2 三种就绪等级

```text
signal_ready
formal_ready
demo_ready
```

`ohlcv_core_directional_v1` 可以继续是：

```text
signal_ready
```

但在 verified quote turnover 缺失时不得标记：

```text
formal_ready
```

## 3.3 Formal Run 硬门

正式候选在提出 one-shot Claim 前必须满足：

```text
allFormalRequiredFieldsSemanticallyVerified = true
formalDataProfileStatus = ready
formalEventCapacityInputCoverage >= preregistered minimum
```

否则：

```text
formal_data_blocked_before_claim
```

不得：

```text
消耗 Formal Run
生成 0 交易正式结果
让统计方法因 0 交易统一 unavailable
```

---

# 四、冻结资本政策依赖声明

读取现有正式资本政策并机器化导出：

```text
capacity model
correlation cluster
portfolio beta
ranking
position sizing
acceptance sequence
```

生成：

```text
capital_policy_data_dependencies.json
```

至少包含：

```text
policyHash
requiredFields
fieldSemantics
minimumLookback
minimumValidObservations
availableAtRules
missingDataPolicy
```

当前 Capacity Model 的硬依赖应明确包括：

```text
verified quote_turnover
或
可依据冻结合同安全换算的 verified volume semantics
```

不得再将 `reported_volume` 同时视为：

```text
候选可选字段
```

却在 Formal 阶段变成隐式强制字段。

---

# 五、reported_volume 来源与单位追溯

新增：

```text
alphapilot/data_provenance/
  volume_provenance_audit.py
  volume_semantics_verifier.py
  turnover_derivation.py

tests/data_provenance/
  test_volume_provenance_audit.py
  test_volume_semantics_verifier.py
  test_turnover_derivation.py
```

对当前 8 个核心品种、1h/4h/1d 文件逐一审计：

```text
ADA-USDT-SWAP
BCH-USDT-SWAP
BTC-USDT-SWAP
ETC-USDT-SWAP
ETH-USDT-SWAP
LINK-USDT-SWAP
LTC-USDT-SWAP
XRP-USDT-SWAP
```

每个数据集必须记录：

```text
datasetId
sourcePath
sourceFileHash
exchange
marketType
instrumentId
timeframe
downloader / exporter
API endpoint or archive source
library and version
raw column names
volume column source index
volume unit
contract size
contract value currency
quote currency
timezone
availableAt
confidence
evidenceRefs
```

## 5.1 禁止仅靠数值分布猜单位

以下不能单独作为正式证明：

```text
volume 数值看起来像 base
volume × close 看起来合理
与网站截图大致接近
```

必须至少有一种可验证来源：

```text
原下载器配置
原 API Schema
原始多列文件
Manifest
Meta JSON
官方归档列定义
已固定库版本和市场语义
```

---

# 六、允许的 Turnover 证据路线

只允许以下路线。

## 路线 A：直接 Quote Turnover

若原始数据直接包含：

```text
quote asset volume / quote turnover
```

使用源字段并冻结：

```text
source column
unit
availableAt
hash
```

这是首选路线。

## 路线 B：Verified Base Volume 的保守下界

只有在 `volumeUnit=base_asset` 被正式证明时，允许：

```text
capacityTurnoverLowerBound =
candleLow × baseVolume
```

原因：

```text
成交价格不低于 candleLow，
因此 low × baseVolume 是该 K 线实际 Quote Turnover 的保守下界。
```

要求：

```text
不能将其命名为 exactQuoteTurnover
必须标记 lower_bound
Capacity Model 使用该下界时仍保持保守
```

禁止使用：

```text
close × baseVolume
```

冒充精确 Turnover。

## 路线 C：Verified Contract Volume

只有具备：

```text
contract count
contract size
contract value currency
linear/inverse contract type
price conversion rule
```

时才允许换算。

所有公式必须：

```text
按交易所和合约类型版本化
有合成测试
有至少一个官方样例对账
```

## 路线 D：新建独立验证的数据源

如果本地文件无法恢复来源语义：

```text
下载或导入一个新的、独立、可追溯的正式数据 Profile
```

要求：

```text
同一交易所
同一市场类型
精确 instrument ID
明确字段单位
明确时间戳和发布规则
不可变 Manifest
不覆盖旧数据
```

旧数据继续保留为：

```text
signal-only / diagnostic
```

## 路线 E：无法验证

状态：

```text
capacity_semantics_unavailable
```

不得开始正式候选重放。

---

# 七、交易所一致性审计

当前旧 Catalog 存在：

```text
OHLCV exchange provenance unverified
Funding datasetId 指向 Binance
最终目标为 OKX Demo
```

V25 必须明确：

```text
researchExchange
ohlcvExchange
fundingExchange
demoExecutionExchange
```

生成：

```text
exchange_identity_and_portability_audit.json
```

## 7.1 推荐路线

优先建立：

```text
与最终 Demo 执行交易所一致的正式历史 Data Profile
```

## 7.2 若历史研究使用不同交易所

必须在 Release 前冻结：

```text
cross_exchange_portability_status
signal timestamp parity
return correlation
event overlap
instrument identity
funding difference
known limitations
```

未通过：

```text
releaseEligible = false
```

不得把“有真实公共 Funding”自动解释为“适用于 OKX Demo”。

---

# 八、建立新的 Capacity-Ready Data Profile

建议：

```text
ohlcv_verified_capacity_v2
```

必须包含：

```text
profileId
profileHash
sourceExchange
marketType
instrumentSet
timeframes
commonCutoff

OHLC coverage
capacity turnover field
capacity turnover semantic type:
  exact_quote_turnover
  conservative_quote_turnover_lower_bound

field unit
availableAt
minimum lookback
coverage by instrument
coverage by event timestamp
known limitations
```

## 8.1 Universe 只能按数据语义冻结

允许：

```text
只保留有验证过容量数据的币种
```

但必须在任何新策略结果前完成，并且只能根据：

```text
数据语义
历史长度
字段覆盖
合约身份
```

筛选。

禁止根据：

```text
候选盈利表现
单币收益
回撤
```

删币。

## 8.2 Profile Readiness

优先复用仓库已有的正式最小 Universe 和覆盖标准。

如果没有现成权威标准：

```text
不得由 Codex自行选择最容易通过的最低品种数；
输出 human_policy_decision_required 并停止。
```

Formal Profile 至少必须证明：

```text
每个保留品种在正式验证窗口内具备必要历史；
每个正式候选事件在信号时点有足够的 prior completed days；
Capacity 输入不是未来数据；
至少存在可计算 Capacity 的真实事件。
```

---

# 九、正式运行前真实事件 Capacity 认证

新增：

```text
real_signal_capacity_certification_v1
```

使用：

```text
原冻结候选
新 Capacity-Ready Data Profile
真实信号
正式 Capital Policy
```

但禁止运行：

```text
退出
PnL
PF
回撤
基准
统计
```

只验证：

```text
raw signal count
assigned event count
capacity input available count
capacity input unavailable count
capacity calculation count
capacity pass count
capacity reject count
```

必须满足：

```text
assignedEventCount > 0
capacityInputAvailableCount > 0
capacityCalculationCount > 0
```

如果：

```text
capacityInputAvailableCount = 0
```

则：

```text
formal_data_blocked_before_claim
```

不得再次用正式 one-shot 运行得出 0 接受交易。

认证不计入经济 Trial。

---

# 十、V26：原冻结候选新 Campaign 重放

只有 V25 Profile Ready 且 Capacity 认证通过后才启动。

新 Campaign：

```text
automatic_strategy_demo_capacity_replay_<hash>
```

候选：

```text
auto-trend_failure_reversal-4h-short-v2
```

保持不变：

```text
strategy definition
direction
timeframe
entry
stop
ExitPolicy
maximumHold
Capital Policy 数值
Gate
Benchmark
Statistical Policy
```

允许变化：

```text
Data Profile
Data Snapshot
Campaign ID
Preregistration Hash
Future OOS Identity
```

## 10.1 是否重跑 Prefilter

如果新 Profile 的 OHLC 来源、交易所或 K 线字节与旧 Snapshot 不同：

```text
必须从 Prefilter 重新运行。
```

如果 OHLC 字节完全相同，只新增已验证的 Volume/Turnover 语义：

```text
仍建议生成新的 Prefilter Replay Artifact，
确认信号事件身份与旧结果一致；
不得直接复制旧结果为新证据。
```

## 10.2 Formal

完成：

```text
5 Fold
资本竞争
Base / 1.5x / 2x
Funding
Benchmark
Statistics when available
```

one-shot：

```text
Claim / Attempt / Result / Read = 1 / 1 / 1 / 1
```

Locked OOS：

```text
0
```

---

# 十一、V26 机械路由

允许：

```text
formal_data_blocked_capacity_semantics
implementation_invalid
capital_infeasible
formal_economic_failed
statistical_failed
research_pass_no_clean_holdout
research_pass_funding_unavailable
formal_pass
```

## 11.1 Capacity 数据存在但仍全部拒绝

只有在：

```text
Capacity input availability > 0
Capacity calculation完整
Capital parity = 100%
```

的前提下，0 接受交易才能解释为：

```text
capital_infeasible
```

此时归档当前候选，不放宽政策。

## 11.2 有交易但经济失败

```text
formal_economic_failed
```

归档当前候选。

## 11.3 通过

按原工作流继续：

```text
不可变 Release
→ 精确 Release Hash 人工批准
→ OKX Demo Universe
→ Demo ARM
→ 首个正常扫描周期
```

---

# 十二、Program Budget

原 Program：

```text
Full Backtests Used = 1
Full Backtests Remaining = 143
```

不得将其描述为预算耗尽。

V25–V26 只允许：

```text
1 个数据语义修复 Program
1 个冻结候选 Replay Campaign
```

本工作流不生成新候选。

若重放结束仍无合格策略：

```text
关闭该候选；
再通过 Research Renewal Gate 决定是否启动新假设 Program。
```

---

# 十三、测试要求

至少覆盖：

```text
End-to-end dependency union
Signal-ready vs Formal-ready vs Demo-ready
Formal claim blocked when capital fields unavailable
No formal budget consumed by data-blocked candidate

Volume provenance:
direct quote
base volume
contract volume
unknown

Conservative low × baseVolume lower bound
No close × volume exact mislabel
Contract conversion fail closed
Exchange identity

Data-only Universe filtering
No result-driven symbol deletion

Real signal Capacity certification
No PnL / exit / statistics read
At least one capacity-computable event

Frozen candidate replay
No strategy/policy/Gate changes
Locked OOS 0
No Release before pass
No automatic approval
```

---

# 十四、验证命令

Quant：

```powershell
python -m pytest tests/data_provenance -q --import-mode=importlib
python -m pytest tests/research_factory -q --import-mode=importlib
python -m pytest tests/formal_validation -q --import-mode=importlib
python -m pytest tests/statistical_validation -q --import-mode=importlib
python -m pytest tests -q --import-mode=importlib

python -m compileall alphapilot
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

Console 如产生 Release：

```powershell
python -m pytest tests -q
python -m compileall alphapilot_control_console
node --check web/app.js
python -m alphapilot_control_console.http_app --smoke
git diff --check
```

---

# 十五、提交顺序

```text
1. Merge and freeze the V19–V24 completed-zero baseline

2. Clarify the capacity-data contract failure without mutating history

3. Add end-to-end candidate, formal and Demo data dependency contracts

4. Recover or independently establish verified turnover semantics

5. Freeze the capacity-ready Data Profile and data-only Universe

6. Certify real-signal capacity inputs before any formal claim

7. Freeze and push the exact-candidate replay Campaign

8. Run the frozen candidate once under the new Data Profile

9. Record the mechanical route

10. If eligible, generate immutable Release and wait for exact-hash approval

11. If approved, ARM OKX Demo and close after the first normal scan
```

---

# 十六、最终自检

Codex 必须输出：

```text
V19–V24 merge commits
Tags preserved
Original evidence modified count

Clarified classification

End-to-end data dependency contract Hash
Signal/Formal/Demo readiness

Volume provenance by instrument
Verified exact turnover count
Verified conservative lower-bound count
Unavailable count

Research/OHLCV/Funding/Demo exchange identities
Cross-exchange portability

Capacity Data Profile ID / Hash
Universe ID / Hash
Coverage

Real-signal Capacity certification
Capacity-computable event count
Capacity-pass/reject counts
Economic reads during certification

Replay Campaign ID
Candidate definition diff
Policy/Gate diff
Data Profile diff

Prefilter replay
Formal Claim/Attempt/Result/Read
Accepted trades
Base / 1.5x / 2x
Benchmark
Statistics
Final route

Release / Approval / Demo ARM / Orders
Locked OOS reads
Live / Trade / Withdraw

Tests
Git / Push / Tags
Known limitations
Next step
```

---

# 十七、最终原则

```text
先让正式数据 Profile 与正式资本政策兼容，
再消耗正式回测。
```

必须坚持：

```text
不把数据缺失叫作策略经济失败；
不把 reported_volume 猜成 quote_turnover；
不通过放宽 Capacity Policy 救候选；
不修改原冻结候选；
不在新数据 Profile 完成前生成更多方向策略；
不读取 Locked OOS；
不自动批准 Release；
不启用 Live。
```
