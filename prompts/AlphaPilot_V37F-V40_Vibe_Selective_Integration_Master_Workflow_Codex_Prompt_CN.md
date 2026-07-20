# AlphaPilot V13.27.1.37F–V13.27.1.40
# Vibe-Trading 选择性集成、策略获取冲刺、V38 产品完成与 OKX Demo/Live 条件工作流
## 给 Codex 的完整 Master Workflow 提示词

> **任务目标**
>
> 在不修改 V33–V37E 历史证据、不降低 AlphaPilot Formal Gate、
> 不复活已归档 TSMOM 身份的前提下：
>
> ```text
> 集成已验收 feature 分支
> → 修复 Formal Gate 报告一致性
> → 选择性吸收 Vibe-Trading 的策略供给能力
> → 建立 Strategy Acquisition Manager
> → 建立安全代码沙箱、Factor Lab 和重复性审计
> → 运行有界策略获取 Campaign
> → 合格候选进入 Formal
> → 同时完成 V38 Demo 功能和 UI
> → Formal Pass 后生成不可变 Release
> → 精确 Hash 批准后进入 OKX Demo
> → Demo 证据充分后才允许申请 Live Canary
> ```
>
> **不保证产生赢家。**
>
> 0 个合格策略是合法终态；不得为完成 V39/V40 而放宽 Gate。

---

# 0. 权威输入

必须读取：

```text
AlphaPilot_V33-V40_Long_Workflow_Evidence_20260720.zip
SHA-256:
b45ab5671f7cf6b3cca6e5d4aecce838cf30277a14923c22483bbabe7076db60

V33–V37E 远端 feature refs、Tags 和 243 份不可变证据

Vibe-Trading:
HKUDS/Vibe-Trading
commit:
7d42de944466e1a1f12f0df3933624fe665dee3c
license:
MIT

AlphaPilot Reference Strategy Extraction Package:
archive SHA-256:
c714273e3046b6f229f2deafc2e572ef3aef10d6664015106a7e51ce46359342

Alpha 191 因子手册
历史归档策略失败报告
Negative Research Rules
Strategy Revival Policy
Advisory-R Exit Policy
```

---

# 1. 当前冻结事实

```text
Formal completed = 2
Formal pass = 0
Immutable Release = 0
Strategy Demo ARM = 0
Strategy orders = 0
Live Canary = 0

Unresolved engineering blocker = 0
Unresolved data blocker = 0
Terminal strategy-performance failure = 2

V38 / V39 / V40 = not started
V33–V37 feature branches = pushed but not merged to origin/main
```

已归档、不得原地修改：

```text
v35_tsmom_crypto_adaptation
v37e_tsmom_daily_capacity_successor
```

任何未来 TSMOM 必须：

```text
新市场假设
新 Candidate ID
新预注册
且通过重复性审计
```

---

# 2. 版本划分

| 版本 | 阶段 | 核心交付 |
|---|---|---|
| V37F | 集成和 Gate 一致性 | feature 分支合并、预算核对、Gate 单一事实源 |
| V37G | Strategy Acquisition Manager | 来源摄取、Artifact Store、源忠实度、生命周期 |
| V37H | Sandbox / Factor Lab / Dedup | 安全策略代码、因子预筛、信号与收益去重 |
| V37I | 有界策略获取 Campaign | Funding Carry、源忠实 Turtle、Pair、Funding Event |
| V37J | Formal 与 Release Ready | 正式验证、统计、机械路由、不可变 Release Ready |
| V38 | Demo 功能与 UI | 真实 Demo 工程链、Workflow Validation、运营 UI |
| V39 | 策略 Demo | 精确 Hash 批准、ARM、首个扫描、前向证据 |
| V40 | Live Canary | 第二次精确批准；默认不自动启动 |

V37F–V38 可按依赖并行；V39 必须等待真实合格 Release。

---

# 3. 全局不可变安全边界

禁止：

```text
修改历史 Tag
改写历史 Result Artifact
force-push
结果后修改 Gate
结果后删币
读取 Locked OOS 后调参
自动批准 Release
自动 ARM 策略 Demo
自动开启 Live
启用 Withdraw
持久化 API Key / Secret / Passphrase
研究进程读取 Demo/Live 凭据
工程订单进入策略证据
```

缺失数据：

```text
保持 null / unavailable
```

---

# 4. V37F：集成基线

## 4.1 建立 Integration Worktree

Quant、Console、Docs 分别建立隔离 Worktree。

不得在用户现有脏主目录执行 merge。

## 4.2 依赖顺序

Quant 推荐顺序：

```text
feature/v13.27.1.33-34a-official-data-pilot
→ feature/v13.27.1.34b-funding-pit-forward
→ feature/v13.27.1.34c-public-data-scheduler
→ feature/v13.27.1.35-standard-replication-playbook
→ feature/v13.27.1.36-strategy-research
→ feature/v13.27.1.36-tsmom-formal-handoff
→ feature/v13.27.1.36.5-mechanism-renewal
→ feature/v13.27.1.37a-funding-data-capability
→ feature/v13.27.1.37b-reference-strategy-campaign
→ feature/v13.27.1.37c-reference-strategy-parity-audit
→ feature/v13.27.1.37d-source-faithful-reproduction
→ feature/v13.27.1.37e-candidate-replay-tsmom-formal-closure
```

Console：

```text
feature/v13.27.1.37a-execution-function-core
```

Docs：

```text
V33–V35 文档分支
```

使用普通 merge 或有证据的依赖合并，不 rebase 已出结果历史。

任何冲突会修改不可变 Result Artifact：

```text
blocked_integration_evidence_conflict
```

立即停止。

## 4.3 预算核对

不能直接假设仍有 96 次。

生成：

```text
budget_reconciliation.json
```

读取：

```text
V33 inherited_budget
V36 Formal ledger
V36.5/V36.6 trial ledger
V37B full-backtest count
V37E Formal ledgers
```

分别统计：

```text
developmentTrialsUsed
fullBacktestsUsed
formalRunsUsed
remainingByAuthoritativePolicy
```

新 Program 只能继承，不得重置。

## 4.4 Gate 单一事实源

新增：

```text
FormalGateEvaluation
```

结构：

```text
gateId
gateClass:
  admission
  diagnostic
status:
  passed
  failed
  unavailable
actual
threshold
reasonCode
evidenceRefs
```

以下均由它派生：

```text
gate_matrix
route_decision
failure_attribution
campaign_summary
Console projection
```

## 4.5 修正 Fold Assignment

Gate 只检查：

```text
unclassifiedEventCount
multiAssignedEventCount
unknownDispositionCount
crossBoundaryLeakageCount
```

以下属于合法 disposition：

```text
outside formal window
before eligible history
after common cutoff
cross-boundary exclusion
purge/embargo exclusion
```

只要守恒完整，不得导致 `fold_assignment_complete` 失败。

## 4.6 修正 blocker 同步

所有失败的 `admission` Gate 必须出现在：

```text
route_decision.blockers
failure_attribution.blockers
```

V37E 历史不重写，只新增：

```text
v37e_gate_semantics_clarification_sidecar.json
```

记录：

```text
historicalRouteChanged = false
historicalAdmissionChanged = false
reportingConsistencyPatched = true
```

## 4.7 V37F 退出门

```text
三个 main 合并完成
完整测试通过
历史 Artifact 修改数 0
预算核对完成
Gate Matrix / Route / Attribution 一致
无 Formal 结果重跑
```

Tag：

```text
v13.27.1.37f-integration
```

---

# 5. V37G：Vibe-Trading 选择性能力审计

## 5.1 固定来源

创建：

```text
research/external_capabilities/vibe_trading/
  source_manifest.json
  license_notice.md
  component_adoption_map.json
  security_review.md
```

记录：

```text
repository
commit
paths
sha
license
adoptionDecision
cleanRoomRewrite
copiedCode
```

不得：

```text
git subtree
git submodule 运行时依赖
pip install vibe-trading-ai 进入生产环境
整仓代码合并
```

## 5.2 采用组件

采用：

```text
Strategy Development Manager 工作流思想
Strategy Artifact 生命周期
Alpha Zoo Operator 语义
Alpha Compare 思路
Generated Code AST Sandbox
Decay Monitoring 思路
Shadow Attribution 思路
研究 UI 信息架构
```

拒绝：

```text
Vibe 通用 Gate
数据 fallback
Broker / Live connector
自动参数原地更新
整套前端迁移
```

---

# 6. V37G：Strategy Acquisition Manager

新增：

```text
alphapilot/strategy_acquisition/
  models.py
  store.py
  source_ingest.py
  mechanism_extract.py
  source_equivalence.py
  lifecycle.py
  lineage.py
  bench_history.py
  projections.py
```

优先复用 AlphaPilot 现有 SQLite/Registry/Program Ledger。

Artifact Store 是索引和投影，不是第二权威账本。

## 6.1 Artifact Schema

```text
artifactId
artifactType
name
familyId
sourceIds
sourceHashes
licenseClass
sourceEquivalenceClass
marketMechanism
formula
requiredFields
universe
timeframe
entryRules
exitRules
positionSizing
riskManagement
dataProfile
candidateId
candidateHash
status
createdAt
updatedAt
```

## 6.2 来源摄取

支持：

```text
PDF / Markdown / HTML
MQ4 / MQ5 / Python
SET 参数文件
EX4 / EX5 黑箱元数据
ZIP 参考包
官方 API 文档
内部研究报告
```

Compiled black box：

```text
只能登记黑箱身份和行为审计计划；
不能宣称恢复源码。
```

## 6.3 提取证据

每个公式或规则必须带：

```text
sourceId
sourcePath
page/line/function
sourceHash
extractionConfidence
```

没有来源证据：

```text
formula_hallucination_blocked
```

## 6.4 来源类型与忠实度

```text
exact_executable_source
source_faithful_reproduction
clean_room_normalized_variant
mechanism_only
insufficient_source_evidence
```

候选报告必须明确：

```text
当前结果证明的是哪一个实现；
不得扩大到原作者策略。
```

## 6.5 生命周期

```text
source_ingested
mechanism_extracted
data_blocked
candidate_draft
candidate_frozen
prefilter_running
prefilter_failed
formal_running
formal_failed
research_pass
formal_pass
release_ready
demo_monitoring
decayed
disabled
archived
```

状态变化写入 append-only history。

---

# 7. V37H：生成代码安全沙箱

新增：

```text
alphapilot/generated_candidate_sandbox/
  ast_policy.py
  import_policy.py
  file_policy.py
  runtime.py
  resource_limits.py
  audit.py
```

## 7.1 全文件禁止

候选文件任意位置出现以下 import 或调用即失败：

```text
socket
subprocess
requests
httpx
urllib
aiohttp
ftplib
smtplib
multiprocessing
ctypes
os.system
os.popen
os.spawn*
os.exec*
os.environ
os.getenv
eval
exec
compile
__import__
```

与 Vibe 不同：

```text
不可达网络 helper 也禁止。
```

## 7.2 文件边界

只允许：

```text
读取只读 Snapshot mount
写临时 run directory
```

禁止：

```text
绝对路径
..
项目源码目录写入
用户 Home
凭据目录
```

## 7.3 运行边界

```text
独立进程
timeout
memory limit
CPU limit
process count limit
no inherited secret env
deterministic seed
offline
```

Windows 优先使用：

```text
Job Object 或现有可审计进程隔离方式
```

## 7.4 晋级要求

```text
AST passed
sandbox passed
candidate adapter passed
synthetic fixture passed
real-signal structural certification passed
```

---

# 8. V37H：Factor Lab

新增或扩展：

```text
alphapilot/factor_lab/
  operators.py
  registry.py
  bench.py
  compare.py
  similarity.py
  reports.py
```

## 8.1 Operator 语义

至少：

```text
rank
zscore
scale
ts_mean
ts_std
ts_rank
ts_corr
ts_cov
ts_max
ts_min
delta
decay_linear
signed_power
safe_div
vwap
```

规则：

```text
NaN 传播
禁止 fillna(0)
常数窗口相关为 NaN
delta lag >= 1
无负向 shift
输出禁止 inf
```

## 8.2 第一版因子范围

不超过 36 个。

主题：

```text
TS momentum
cross-sectional momentum
short-term reversal
residual momentum
volatility
range/ATR
turnover change
liquidity
beta
correlation regime
funding
basis
```

## 8.3 用途

只用于：

```text
单因子 IC/IR
候选 Ranking
候选 Veto
Regime
Benchmark
Dedup
```

因子通过不等于策略 Formal Pass。

---

# 9. V37H：重复性与血缘审计

新增：

```text
ArtifactSimilarityPolicy
```

必须在任何新候选结果前冻结。

比较：

```text
sourceLineageHash
canonicalFormulaHash
semanticMechanism
signalCorrelation
eventOverlap
dailyReturnCorrelation
holdingOverlap
parameterLineage
```

分类：

```text
exact_duplicate
near_duplicate
same_family_variant
mechanism_related
independent
```

因子参考：

```text
correlation > 0.99:
duplicate

0.90–0.99:
variant
```

策略必须联合多个维度，不能单看一个相关系数。

输出：

```text
artifact_similarity_matrix.parquet
artifact_similarity_summary.csv
candidate_dedup_decision.json
```

---

# 10. V37I：有界策略获取 Campaign

## 10.1 预算

从 V37F `budget_reconciliation.json` 读取。

本轮额外上限：

```text
maximumCampaigns = 2
maximumFamilies = 6
maximumInitialCandidates = 12
maximumVariantsPerFamily = 2
maximumStructuralRevisionPerFamily = 1
```

任何值不得超过继承余额。

## 10.2 禁止复活

不得生成与以下身份近似的版本：

```text
v35_tsmom_crypto_adaptation
v37e_tsmom_daily_capacity_successor
V36.5 intraday session
V36.6 BTC downside spillover
V37B clean-room normalized reference candidates
```

除非：

```text
新来源
新机制
新策略类型
且通过 similarity policy 判定独立。
```

## 10.3 Campaign A：OKX Funding Carry

优先。

候选最多 2 条：

```text
source replication:
positive funding delta-neutral carry

OKX adaptation:
cost/basis/capacity gated positive funding carry
```

Universe：

```text
BTC
ETH
SOL
```

合同：

```text
Spot long
Perpetual short
Delta hedge
Funding availableAt
Basis
双腿 fee/slippage
双腿 capacity
capital usage
rebalance
one-leg failure
exit
```

历史 Formal 可以运行。

Release / Demo 前：

```text
必须具备独立前向 Spot/Perp Order Book 证据。
```

## 10.4 Campaign B：源忠实策略获取

候选来源优先级：

```text
1. 可读、可执行源码
2. 论文精确公式
3. 官方规则文档
4. 上下文 prose
```

候选：

```text
Turtle / Donchian source-faithful candidate
Pair relative value source-faithful candidate
Funding event candidate
```

Turtle 创建前必须与归档 TSMOM 去重。

Pair 创建前必须证明与 V35 Pair 版本实质不同。

## 10.5 Chan

只允许作为：

```text
context
ranking
veto
structure exit
```

不得首轮形成独立信号家族。

## 10.6 参数预算

每 Candidate：

```text
3–8 个预注册 Development 组合
```

选择：

```text
稳定平台
```

拒绝：

```text
孤立最高点
```

## 10.7 预筛

复用现有策略类型 Gate。

必须加入：

```text
source equivalence status
similarity decision
capital-comparable benchmark
parameter neighborhood stability
```

0 个幸存是合法结果。

---

# 11. V37J：正式验证

只对预筛幸存者。

顺序：

```text
代码 Commit/Push
→ Candidate Freeze
→ Panel Freeze
→ Preregistration Commit/Push
→ Future Locked OOS Identity
→ one-shot Authorization
→ Formal
```

运行：

```text
Purged 5-Fold Walk-forward
Embargo
Base / 1.5x / 2x
Funding
Capital Competition
Comparable Benchmark
NW
BH/BY
DSR
PBO
White Reality Check
SPA
Bootstrap
Concentration
```

权威 Gate Evaluation Object 必须驱动所有报告。

结果：

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

失败后：

```text
当前 Candidate ID 归档
不原地修复
```

通过后：

```text
immutable_release_ready
approved=false
Demo ARM=false
orders=0
```

---

# 12. 衰减监控

V37G 建立 Schema，但 V39 后才激活。

最小数据：

```text
至少 3 个独立 periodic bench
```

更推荐：

```text
多个时间窗口
或
至少 30 笔闭合策略 Demo 交易
```

状态：

```text
healthy
warning
decayed
critical
```

禁止：

```text
自动修改当前 Release 参数
```

衰减：

```text
暂停或禁用当前 Release
→ 新 Candidate ID
→ 新预注册
→ 新 Release
```

---

# 13. V38：Demo 功能核心

V37A 只读执行控制继续作为基线。

## 13.1 真实 Demo 接口

必须实现并验证：

```text
public instruments
authenticated Demo instruments
exact USDT SWAP intersection
server time offset
x-simulated-trading: 1
order submit
order query
partial fill
cancel
close
positions
balance
private WS
REST reconciliation
```

## 13.2 幂等

```text
deterministic clOrdId
single-flight
signal dedup
restart recovery
bounded retry
unknown state reconciliation
```

## 13.3 Kill Switch

```text
manual
stale market data
stale account
auth failure
unknown order
orphan position
risk limit
Release mismatch
Approval mismatch
```

## 13.4 Workflow Validation Demo

使用：

```text
diagnostic_only Release
strategyQualification=false
formalPass=false
livePromotionEligible=false
```

验证：

```text
Release import
exact approval
ARM
signal
order
position
exit
reconciliation
UI
```

该 Ledger 永远不能进入策略证据。

---

# 14. V38：UI

不迁移到 React。

现有 Console 增加：

## 14.1 Strategy Lab

```text
Source Registry
Artifact cards
source equivalence
candidate lineage
similarity matrix
factor bench
campaign progress
budget
failure attribution
decay state
```

## 14.2 Operator UI

```text
Data freshness
Runtime
Release/Approval Hash
ARM
Kill Switch
Orders
Positions
Risk
Reconciliation
Blocker code
Chinese next action
```

## 14.3 只读研究边界

UI 禁止：

```text
编辑冻结 Candidate
修改 Gate
看 OOS 后调参数
直接批准未来未知 Release
```

---

# 15. V39：策略 Demo

只有真实 `immutable_release_ready` 才允许。

流程：

```text
Console import unapproved
→ demo_approval_request
→ 用户批准 exact Release Hash + Risk Overlay Hash
→ exact OKX Demo universe
→ ARM
→ first scheduled scan
```

策略 Demo Ledger 与：

```text
engineering smoke
workflow validation
shadow
legacy
```

隔离。

---

# 16. Demo 交易归因

借鉴 Vibe Shadow Account 的归因思想，但只分析真实闭合策略 Demo：

```text
expected vs actual entry
slippage
fee
funding
early exit
late exit
rejection
missed signal
latency
partial fill
```

不恢复：

```text
本地虚拟资本
本地虚拟持仓
本地虚拟 PnL
```

---

# 17. V40：Live Canary

默认：

```text
not_started
Live=false
Withdraw=false
```

只有：

```text
Demo forward evidence passed
execution deviation acceptable
no duplicate/orphan
separate exact Live Release approval
separate exact Live Risk approval
```

才允许。

运行中 Release 不可修改。

---

# 18. 测试

## 18.1 Gate Consistency

```text
explicit exclusions do not fail disposition completeness
leakage does fail
all admission gate failures appear in route blockers
diagnostic gates do not block
matrix/route/attribution parity
```

## 18.2 Acquisition Manager

```text
source hashing
license
source evidence
formula hallucination rejection
lifecycle history
source equivalence
artifact lineage
```

## 18.3 Sandbox

```text
top-level execution
method-body execution
transitive helper
network import
env read
subprocess
eval/exec
file escape
timeout
memory
```

## 18.4 Factor Lab

```text
NaN propagation
no inf
no lookahead
operator parity
factor similarity
PIT readiness
```

## 18.5 Demo

```text
simulated header
exact instrument
idempotency
partial fill
restart
WS/REST reconciliation
kill switch
ledger isolation
```

---

# 19. 验证命令

Quant：

```powershell
python -m pytest tests/strategy_acquisition -q --import-mode=importlib
python -m pytest tests/generated_candidate_sandbox -q --import-mode=importlib
python -m pytest tests/factor_lab -q --import-mode=importlib
python -m pytest tests/research_service -q --import-mode=importlib
python -m pytest tests/formal_validation -q --import-mode=importlib
python -m pytest tests/statistical_validation -q --import-mode=importlib
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

# 20. 提交顺序

```text
1. Merge and freeze the V33–V37 accepted feature baseline

2. Reconcile inherited research budgets and fix Formal gate/report consistency

3. Pin and audit the Vibe-Trading source without creating a runtime dependency

4. Add the Strategy Acquisition Manager and source-equivalence lifecycle

5. Add the generated-candidate sandbox

6. Add the selected Factor Lab and artifact similarity policy

7. Run the bounded Funding Carry and source-faithful acquisition campaigns

8. Freeze and run Formal only for survivors

9. Complete the real OKX Demo function core and isolated workflow validation

10. Complete the Strategy Lab and operator UI

11. Generate an immutable qualifying Release and stop for exact-hash approval

12. ARM the approved OKX strategy Demo

13. Keep Live Canary disabled until a second exact approval
```

---

# 21. 输出

至少：

```text
integration_merge_receipt.json
budget_reconciliation.json
v37e_gate_semantics_clarification_sidecar.json
formal_gate_parity_audit.json

vibe_trading_source_manifest.json
vibe_component_adoption_map.json
vibe_license_notice.md

strategy_artifact_store_schema.json
source_inventory.json
mechanism_inventory.json
source_equivalence_matrix.csv
artifact_lifecycle_history.jsonl

generated_candidate_sandbox_audit.json
factor_registry.json
factor_bench_matrix.csv
artifact_similarity_matrix.parquet
candidate_dedup_decision.json

campaign_inventory.json
candidate_inventory.json
prefilter_matrix.csv
formal_matrix.csv
statistical_matrix.json
failure_attribution.json

workflow_validation_demo_audit.json
demo_execution_audit.json
reconciliation_audit.json
strategy_lab_ui_audit.json

release_inventory.json
demo_approval_request.json
demo_arm_audit.json

final_route.json
final_self_check.md
artifact_manifest.json
```

---

# 22. 机械终态

```text
completed_release_ready
completed_demo_admission
completed_zero_qualified_candidates

blocked_integration_evidence_conflict
blocked_strategy_source_evidence
blocked_formal_data
blocked_formal_engine
blocked_demo_environment
blocked_waiting_exact_release_approval
implementation_invalid
```

---

# 23. 最终自检

```text
Historical Artifact mutation count
Merged feature refs
Budget used/remaining

Gate matrix/route/attribution parity
Fold disposition conservation
Admission blocker completeness

Vibe source commit/license
Adopted/rejected/deferred components
Copied-code inventory

Source count
Artifact count
Factor count
Duplicate/variant/independent count

Campaigns
Families
Candidates
Prefilter survivors
Formal candidates
Research/Formal passes

Funding Carry result
Turtle result
Pair result
Event result

Release ID/Hash
Approval
Demo ARM
Orders
Live
Withdraw

Tests
Hashes
Sensitive scan
Known limitations
```

---

# 24. 最终原则

```text
Vibe-Trading 只补策略供给层；
AlphaPilot 继续裁决数据、Formal、Release、Demo 和 Live。
```

必须坚持：

```text
不整体合并 Vibe；
不降低 AlphaPilot Gate；
不把标准化版本失败扩大成原策略失败；
不让 LLM 后台无限改代码和参数；
不让因子 Zoo 变成多重试验机器；
不让工程 Demo 冒充策略证据；
不让衰减监控原地修改 Release；
不让 Live 自动开启。
```
