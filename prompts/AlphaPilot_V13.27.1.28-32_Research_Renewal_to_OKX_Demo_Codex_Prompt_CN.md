# AlphaPilot V13.27.1.28–V13.27.1.32
# 研究续期：基准增量、因果容量窗口、市场中性候选与 OKX Demo 准入长工作流
## 给 Codex 的完整执行提示词

> **执行背景**
>
> V13.27.1.26–V13.27.1.27 已合法关闭：
>
> ```text
> Program:
> automatic_strategy_to_demo_v26_2aff44adf84d039c
>
> Final Route:
> completed_zero_qualified_candidates
>
> Hypothesis families:
> 4
>
> Candidate trials:
> 8
>
> Structural pass:
> 8
>
> Ranking pass:
> 8
>
> Capacity certification pass:
> 3
>
> Economic prefilter pass:
> 0
>
> Formal / Locked OOS / Release / Demo ARM / Orders:
> 0 / 0 / 0 / false / 0
> ```
>
> 本工作流是一个新的 Research Renewal Program，不得修改或重跑 V27 候选。
>
> **核心目标**
>
> 1. 修复下一轮候选出生前的通用数据与比较合同，而不是救活 V27 候选；
> 2. 不再把“只差一个 Gate”误解为“接近可用”；
> 3. 将下一轮研究从单一 OHLCV 方向事件扩展到与机制匹配的市场中性策略类型；
> 4. 在有限预算内自动运行到：
>
> ```text
> 精确 Release Hash 等待批准
> 或
> completed_zero_qualified_candidates
> ```
>
> 5. 用户批准精确 Release Hash 后，继续到 OKX Demo ARM 和首个正常扫描周期。
>
> 不得保证一定产生 Winner，不得降低 Gate，不得根据结果删除币种或修改候选。

---

# 一、V27 独立结论

## 1.1 V27 是合法成功的零幸存者 Campaign

保持：

```text
completed_zero_qualified_candidates
```

这是研究结论，不是工程失败。

V28–V31 原阶段未到达是正确行为。

## 1.2 `range expansion 4h long` 不是“勉强可用”

权威预筛结果：

```text
candidate:
v27-range_expansion_close_followthrough-4h-long-v1

eventCount:
3155

PF:
1.0356412768

averageNetR:
0.0205737770R

totalNetR:
64.9102666R

1.5x stress PF:
1.0018759150

1.5x stress averageNetR:
0.0011036649R

1.5x stress totalNetR:
3.4820627R

maximumDrawdown:
57.39164%

simple benchmark totalNetR:
874.6873120R

benchmark incrementalNetR:
-809.7770455R
```

因此：

```text
prefilter_failed_benchmark_dominance
```

它虽然只缺一个布尔 Gate，但经济距离并不小：

```text
压力成本后的优势接近 0；
候选显著弱于简单基准；
预筛事件路径回撤很高。
```

必须永久归档当前版本。

禁止：

```text
为了让 benchmark increment 转正而调整退出、窗口、止损或 Gate；
将“只差一个 Gate”作为复活依据。
```

## 1.3 其他候选归类

```text
opening_range_failure_reversal long / short:
明确负边际、过高频、回撤失控；
当前版本永久归档。

liquidity_gap_reentry long / short:
事件过少且无事件通过冻结容量政策；
当前版本永久归档。

cross_section_dispersion leader long / short:
机制与单腿 directional_event 表达不匹配；
当前版本归档；
若未来研究，必须成为新的 cross_sectional_portfolio 候选，
不是单腿版本修订。

range expansion short:
当前版本因容量完整性合同失败；
不得原地恢复。
```

---

# 二、不可变历史与 Git 收口

## 2.1 保护身份

核验：

```text
Quant V26:
414989dbab99b6303a6649d397dceb7ca4cf0dd3
tag v13.27.1.26

Quant V27:
f2571ca6da3635f7ceea357db6dc03bb17760aaf
tag v13.27.1.27

Docs V26:
c07d6c3c809ab75948f1e800a5a89bf03faa937e

Docs V27:
5e7ee25f71c00d52124e073591bad86748632b18
tag v13.27.1.27-docs

Console:
6ea1f50e56e8ac4d6991b04d3f7717d3d33825c1
```

## 2.2 先合并 main

当前功能分支尚未合并 main。

执行：

```text
保护 main 现有提交与用户原有修改
→ 普通 merge
→ 不 rebase 已出结果历史
→ 不移动 Tag
→ 完整 CI
→ Push main
```

冲突会改变权威 Artifact：

```text
blocked_baseline_merge
立即停止
```

## 2.3 大 Artifact 治理

V27 的 Ranking Evidence JSON 约 52.7 MB，已在历史 Tag 中存在。

禁止：

```text
重写或删除已 Tag 文件
```

从本版本开始：

```text
摘要 JSON
+
明细 Parquet
+
抽样 CSV
+
Manifest
```

Git 只保存：

```text
摘要
Schema
Manifest
有限样本
```

完整大明细：

```text
本地证据包
或经项目明确批准的 Git LFS
```

不得再次直接提交新的 >50 MB 普通 Git Blob。

---

# 三、新 Program 身份与预算

建议：

```text
programId:
automatic_strategy_renewal_v28_<canonical_hash>

parentProgramId:
automatic_strategy_to_demo_v26_2aff44adf84d039c
```

预算：

```text
maximumCampaigns = 2
maximumFamiliesPerCampaign = 6
maximumInitialVariantsPerFamily = 2
maximumInitialCandidatesPerCampaign = 12
maximumStructuralRevisionsPerFamily = 1
maximumFormalCandidatesPerCampaign = 4
maximumFullBacktestsPerCampaign = 48
maximumAdditionalFullBacktests = 96
maximumDemoReleasesPerCampaign = 3
```

提前停止：

```text
出现至少一条 release-eligible 候选
→ 停止生成更多候选
→ 转入 OKX / Release 阶段
```

预算耗尽或没有实质新机制：

```text
completed_zero_qualified_candidates
```

---

# 第四章：V13.27.1.28
# 通用数据合同、容量资格窗口与基准可比性

## 4.1 新增 Causal Eligibility Window

V27 的容量认证要求所有原始事件均具备完整 30 日容量历史，
导致少量前置历史不足事件使整个候选失败。

不得简单把容量覆盖门从 100% 降到 95%。

正确修复是：

```text
先在任何候选结果前冻结可交易资格窗口；
资格窗口内继续要求 100% 容量输入完整；
资格窗口前事件明确排除，不进入候选经济样本。
```

新增：

```text
causal_eligibility_window_v1
```

每个：

```text
instrument
timeframe
dataProfile
requiredField
```

计算：

```text
fieldFirstAvailableAt
requiredLookback
firstEligibleSignalTimestamp
lastEligibleSignalTimestamp
```

事件 disposition：

```text
eligible_candidate_event

excluded_before_capacity_history_ready
excluded_after_common_cutoff
excluded_missing_verified_semantics
excluded_instrument_not_in_frozen_universe
```

守恒：

```text
rawSignalCount
=
eligibleEventCount
+
explicitExclusionCount
```

并要求：

```text
eligibleEventCapacityCoverage = 100%
unclassifiedCount = 0
postEntryReadCount = 0
```

禁止：

```text
看到经济结果后改变开始时间；
按候选盈利选择时间窗口；
将真实容量拒绝改为数据排除。
```

## 4.2 Data Gate 层级

每个候选必须在创建 Candidate ID 前通过：

```text
signal_ready
ranking_ready
capacity_ready
prefilter_ready
formal_ready
release_ready
demo_ready
```

各层显式记录：

```text
requiredFields
fieldSemantics
minimumLookback
eligibleStart
coverage
availableAt
missingPolicy
```

Formal Claim 之前：

```text
formalReady = true
```

## 4.3 基准可比性审计

新增：

```text
benchmark_comparability_contract_v1
```

每个简单基准必须声明：

```text
candidateId
benchmarkId
sameSignalUniverse
sameEligibleWindow
sameDirection
sameEntryTimestamp
sameCosts
samePositionCompetitionPolicy
sameCapitalBudget
sameConcurrencyLimit
sameCapacityPolicy
exitDifferenceOnly | fullMechanismDifference
```

至少输出两类增量：

```text
eventLevelIncrementNetR
accountPathIncrementNetR
```

禁止比较：

```text
候选有限资本账户路径
vs
基准无限资本事件简单相加
```

如无法做到同资本政策比较：

```text
benchmarkComparabilityStatus = diagnostic_only
```

不得作为正式准入 Gate。

## 4.4 V27 Benchmark Audit

只读复核 `range expansion 4h long`：

```text
candidate totalNetR = 64.9103R
benchmark totalNetR = 874.6873R
increment = -809.7770R
```

验证：

```text
信号集合
成本
持有期
资本预算
并发
容量
```

是否可比。

结果只用于确认 V27 归档解释，不得重跑或复活候选。

---

# 第五章：V13.27.1.29
# 新策略类型能力与新市场机制

本轮不再只生成单腿 OHLCV 方向事件。

## 5.1 支持类型

### A. `directional_event_v2`

最多：

```text
2 个家族
4 个候选
```

要求：

```text
4h / 1d 为主
低频
明确为什么优于简单同事件基准
候选创建前完整 Data / Ranking / Capacity 合同
```

不得重做：

```text
普通无条件区间扩张
普通开盘区间反转
旧 EMA 趋势
旧 Bollinger 反转
```

### B. `pair_relative_value_v1`

最多：

```text
2 个家族
4 个候选
```

必须复用或扩展既有 Pair Replay，不得将双腿策略伪装成两个单腿策略。

正式合同：

```text
pair identity
leg A / leg B
hedge ratio
gross exposure
net beta
two-leg capacity
two-leg costs
synchronous execution policy
one-leg fill failure policy
pair exit
```

允许机制示例：

```text
beta-hedged residual mean reversion
correlation-break convergence
```

不得在全历史上挑表现最好的 Pair。

Pair Universe 必须在结果前依据：

```text
精确合约身份
容量
历史长度
因果相关/回归可计算性
```

冻结。

### C. `cross_sectional_portfolio_v1`

最多：

```text
2 个家族
4 个候选
```

V27 的横截面离散机制若继续研究，必须在此类型中建立全新身份。

正式合同：

```text
PIT universe
rebalance schedule
long / short quantiles
gross / net exposure
turnover
capacity
cluster neutrality
BTC beta
deterministic ranking
portfolio benchmark
```

组合候选不得机械套用单笔 R Gate。

---

## 5.2 暂不进入本 Campaign

```text
carry_basis
funding_arbitrage
liquidation_event
orderbook_microstructure
```

除非同交易所、因果、正式数据 Profile 已 ready。

---

## 5.3 市场机制 Gate

每个 Hypothesis 必须回答：

```text
收益来源是什么？
为什么不是简单 Beta？
为什么复杂候选可能超过简单基准？
什么结果会反证？
必需数据当前是否 ready？
与 72 个历史失败家族有什么实质差异？
```

不能只写：

```text
“加入更多确认指标可以减少假信号”
```

## 5.4 历史去重

读取：

```text
186 个策略身份
72 个家族
V19–V27 全部自动候选
Negative Research Rules
```

拒绝：

```text
阈值微调
同机制换名称
旧失败指标堆叠
结果驱动币种过滤
```

---

# 第六章：V13.27.1.30
# 候选生成、预筛与正式验证

## 6.1 CandidateSpec

所有候选必须声明：

```text
candidateId
familyId
hypothesisId
strategyType
direction / portfolio side
timeframe / rebalance frequency

signal definition
ranking contract
capacity contract
risk contract
exit contract
benchmark contract
data profile
falsification condition
```

## 6.2 预筛 Gate 类型分离

### Directional Event Gate

复用当前批准 Gate，但增加：

```text
benchmark comparability passed
accountPathIncrementNetR > 0
```

若 Benchmark 只能 diagnostic：

```text
不得晋级 Formal
```

### Pair Gate

至少：

```text
pair event/trade count
net spread return
PF 或等价收益质量
maximum drawdown
two-leg costs
hedge residual stability
capacity
benchmark increment
```

### Portfolio Gate

至少：

```text
net return
maximum drawdown
turnover
cost-adjusted return
BTC beta
gross / net exposure
capacity
positive period ratio
benchmark increment
```

不得机械套用单笔 R 门槛。

## 6.3 预筛失败处理

失败候选：

```text
当前 Candidate ID 永久归档
```

结构修订只有在：

```text
预注册前已定义允许条件
且存在新机制证据
```

时使用一次。

## 6.4 正式比较面板

正式结果前冻结：

```text
候选集合
日期
Data Profile
Capital Profile
Benchmark
成本
Trial Ledger
```

运行：

```text
5 Fold
Purge / Embargo
Base / 1.5x / 2x
Capital Competition
Benchmark
NW / BH / BY / DSR / PBO / White RC / SPA
Bootstrap CI
```

统计不可用：

```text
保持 unavailable
```

## 6.5 结果等级

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

---

# 第七章：V13.27.1.31
# OKX 同交易所确认与 Release 资格

只处理：

```text
formal_pass
research_pass
```

候选。

## 7.1 OKX Same-Exchange Profile

优先建立：

```text
okx_same_exchange_release_profile_v1
```

至少：

```text
OHLCV
quote turnover
funding
instrument state
exact USDT SWAP identity
availableAt
immutable manifest
```

如果不能达到正式历史覆盖：

```text
执行预注册的跨交易所可移植性审计
```

## 7.2 可移植性

结果前冻结阈值。

检查：

```text
signal timestamp parity
event overlap
return correlation
direction parity
capacity difference
Funding difference
cost difference
```

失败：

```text
blocked_okx_portability
```

## 7.3 Release

只有：

```text
历史正式/研究 Gate 通过
+
OKX 同交易所确认或可移植性通过
```

才允许生成不可变 Release。

---

# 第八章：V13.27.1.32
# 精确 Release 批准与 OKX Demo

## 8.1 Release 生成

每 Campaign 0–3 条。

必须：

```text
approved=false
Demo ARM=false
Orders=0
```

## 8.2 唯一人工暂停点

生成：

```text
demo_approval_request.md
demo_approval_request.json
```

显示：

```text
Release Hash
Evidence Class
5 Fold
成本压力
回撤
基准增量
统计
OKX 数据/可移植性
风险 Overlay
```

状态：

```text
blocked_waiting_exact_release_approval
```

用户必须批准精确 Hash。

## 8.3 Demo Cutover

批准后：

```text
公共 PIT Universe
∩
Authenticated OKX Demo USDT SWAP
```

校验：

```text
instrumentId
lotSize
tickSize
contractValue
minimumSize
state
```

然后：

```text
导入 exact Release
验证 Approval Hash
加载不放宽风险的 Overlay
ARM Demo
完成首个正常扫描周期
```

不要求强制订单。

成功：

```text
completed_demo_admission
或
completed_research_forward_demo_admission
```

Live 始终关闭。

---

# 九、程序机械终态

```text
completed_demo_admission
completed_research_forward_demo_admission
completed_zero_qualified_candidates

blocked_baseline_merge
blocked_formal_data
blocked_okx_data
blocked_okx_portability
blocked_waiting_exact_release_approval
blocked_demo_environment
implementation_invalid
```

---

# 十、测试与验证

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

# 十一、最终输出

```text
Program ID
Parent Program
Final Route

Baseline merge commits
Historical mutation count

Eligibility Window Policy Hash
Benchmark Comparability Policy Hash

Campaign count
Family count by strategy type
Candidate count
Prefilter survivors
Formal candidates
Research Pass
Formal Pass
Archived

Capacity / Ranking / Data-blocked counts
Full backtests used
Budget remaining

Best candidate metrics
Benchmark comparison
Formal statistics

OKX Profile
Portability status
Release eligibility

Release ID / Hash
Approval Hash
Risk Overlay Hash

Demo import
ARM
Runtime health
First scan
Orders

Locked OOS
Live / Trade / Withdraw
Tests
Hash mismatches
Sensitive hits
Known limitations
```

---

# 十二、最终原则

```text
下一轮不是“再多生成几条方向策略”，
而是修正候选出生合同，并研究与机制匹配的策略类型。
```

必须坚持：

```text
不复活 V27 当前候选；
不因少量前置数据缺口降低 100% 正式覆盖要求；
用因果资格窗口明确排除不可用前缀；
基准必须资本与事件可比；
横截面机制必须用组合模型；
Pair 必须用双腿模型；
先 Data Ready，再消耗结果预算；
先 OKX 可移植性，再生成 Release；
先精确 Hash 批准，再 ARM Demo。
```
