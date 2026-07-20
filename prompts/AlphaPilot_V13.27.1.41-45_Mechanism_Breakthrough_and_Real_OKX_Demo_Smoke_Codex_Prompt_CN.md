# AlphaPilot V13.27.1.41–V13.27.1.45
# 机制级策略突破与真实 OKX Demo 工程烟测双轨 Master Workflow
## 给 Codex 的完整执行提示词

> **总决策**
>
> 下一步必须停止“继续批量生成相似策略”，改为：
>
> ```text
> Track R：少量、来源明确、经济机制独立的机制级突破研究
> Track P：不等待盈利策略，立即用真实 OKX Demo 工程烟测跑通产品链
> ```
>
> 两条轨道并行，但 Ledger、证据和晋级资格严格隔离。
>
> **研究轨成功**
>
> ```text
> 至少一条候选通过现有 AlphaPilot Formal Gate，
> 生成 immutable_release_ready，
> 然后暂停等待用户批准精确 Release Hash。
> ```
>
> **产品轨成功**
>
> ```text
> 使用进程注入的 OKX Demo 凭据，
> 完成真实私有网络认证、精确可交易合约交集、
> 最小订单撤销路径、最小成交平仓路径、
> 私有 WebSocket、REST 对账、重启恢复、Kill Switch 和 UI 证据；
> 全程不产生策略资格。
> ```
>
> **合法研究终态**
>
> ```text
> completed_zero_qualified_candidates
> ```
>
> 0 条通过是合法结果。不得为进入 Demo 而降低 Gate、改变本轮候选或扩大试验预算。

---

# 一、权威输入与身份

## 1.1 当前阶段证据

```text
AlphaPilot-V37F-V40-End-of-Stage-Evidence.zip
SHA-256:
a851cab5b29dd6c4e654f0111d0b2faa3d56c9b0e8e942b3100c34c1fd1ecadd
```

配套入口：

```text
AlphaPilot_V37F-V40_Final_Closeout_CN.md
SHA-256:
4c7fe03e2aced691ebb6523dbdc0c061eb62f1e6a8efce9ee13f7b4700933cbb

AlphaPilot_V37F-V40_final_self_check.json
SHA-256:
127cef206ba45b8726fa6a72184258c4df904b261ad24bf3b4599d7213122557

AlphaPilot_V37I_Prefilter_Matrix.csv
SHA-256:
d9e56175f0f9ab6f0f7f7d985fb28ab723a13b801ff9641772d383bd1462069b
```

## 1.2 参考策略包

```text
AlphaPilot_Reference_Strategy_Extraction_and_Codex_Backtest_Package.zip
SHA-256:
c714273e3046b6f229f2deafc2e572ef3aef10d6664015106a7e51ce46359342
```

必须读取：

```text
docs/AlphaPilot_Reference_Strategy_Extraction_Report_CN.md
docs/AlphaPilot_Reference_Strategy_Codex_Backtest_Prompt_CN.md
candidates/candidate_specs.json
candidates/candidate_matrix.csv
inventories/analyzed_source_inventory.csv
inventories/risk_rejection_inventory.csv
```

## 1.3 Vibe-Trading 固定身份

```text
Repository:
HKUDS/Vibe-Trading

Pinned Commit:
7d42de944466e1a1f12f0df3933624fe665dee3c

License:
MIT
```

只使用 V37G/V37H 已完成的 clean-room 能力：

```text
Strategy Acquisition Manager
Artifact Lifecycle
Factor Lab
Generated Candidate Sandbox
Artifact Similarity / Dedup
Strategy Lab UI projection
```

禁止：

```text
整体合并 Vibe
建立运行时依赖
使用 Vibe 通用 Gate
使用跨交易所 Formal 数据 fallback
原地更新活跃策略参数
使用 Vibe Broker / Live Connector
启动无限 LLM 策略守护进程
```

---

# 二、当前事实与本轮禁止事项

## 2.1 V37F–V40 结论

```text
Final Route:
completed_zero_qualified_candidates

Campaigns / Families / Candidates:
2 / 4 / 5

Development Trials:
60

Full Backtests Used / Remaining:
5 / 91

Prefilter Survivors:
0

Formal Candidate / Formal Pass:
0 / 0

Release:
0

Workflow Validation:
completed_diagnostic_only

Engineering Smoke:
not_run

Private OKX Network Verified:
false

Strategy Demo / Live:
not_run / not_run
```

## 2.2 已关闭的五条身份

不得原地修改、改阈值或重跑：

```text
v37i_funding_carry_source_replication
v37i_funding_carry_okx_adaptation
v37i_turtle_source_faithful_candidate
v37i_distance_selected_pair_rv_source_faithful
v37i_funding_surprise_event_candidate
```

## 2.3 本轮明确不研究

```text
普通 Turtle / TSMOM
普通 EMA 趋势
普通 Bollinger / RSI 均值回归
普通突破回踩
UTC 时段突破
Funding Surprise Event
距离法 Pair 的阈值改版
Martingale / Grid / Recovery / Basket Rescue
```

原因：

```text
已归档身份重复
历史信号边际为负
或本轮结果已经明确反证当前实现
```

---

# 三、版本与双轨阶段

| 版本 | Track | 目标 |
|---|---|---|
| V13.27.1.41 | Common + Product | 合并冻结当前 feature 基线；真实 OKX Demo 工程烟测预检 |
| V13.27.1.42 | Research | 参考资料摄取、机制准入、相似度审计、候选冻结 |
| V13.27.1.43 | Research | 少量候选预筛；动态 Pair；条件 Funding Carry 语义审计 |
| V13.27.1.44 | Research | 最多 2 条幸存者进入 Formal；生成 Release Ready 或可信失败 |
| V13.27.1.45 | Product / Convergence | 完成真实 Demo 烟测和 UI；有 Release 时等待精确 Hash 批准并进入策略 Demo |

V41 产品轨和 V42/V43 研究轨允许并行。

---

# 四、V41：合并并冻结当前基线

当前证据记录：

```text
Quant feature HEAD:
129feb3990fe955bd9104b6c0aa1d121ec2415d0

Console feature HEAD:
8c1de27043322d36d072bc91ed1e5bf80667d301

Docs feature HEAD:
bbabfd75c5264d9e6fe90bb0ff29db81cf88b5f1

mergedToMain:
false / false / false
```

## 4.1 执行规则

```text
建立三个隔离 integration worktree
保护用户主目录中已有未提交内容
普通 merge
不 rebase 已出结果历史
不 force-push
不移动历史 Tag
完整测试
Push origin/main
远端 SHA 核验
```

若冲突会修改历史 Result Artifact：

```text
blocked_integration_evidence_conflict
立即停止
```

## 4.2 新 Successor Program

不得原地恢复已关闭 Program。

新建：

```text
programId:
mechanism_breakthrough_and_demo_smoke_v41_<canonical_hash>

continuationOf:
V37F–V40 final program

inheritedFullBacktestsRemaining:
91
```

本轮只允许消耗：

```text
maximumDevelopmentTrials = 12
maximumFullBacktests = 6
maximumFormalCandidates = 2
maximumFormalRuns = 2
maximumCampaigns = 2
maximumMechanismFamilies = 3
maximumCandidateIds = 6
```

未使用的预算继续保留，不因版本切换重置。

---

# 五、Track P：真实 OKX Demo 工程烟测

## 5.1 Track P 与策略证据完全隔离

全部记录：

```text
engineering_only
strategyQualification=false
formalPass=false
promotionEvidenceEligible=false
livePromotionEligible=false
```

不得更新：

```text
策略 PF
胜率
净 R
Formal Evidence
Forward Review Count
Release Qualification
Live State
```

## 5.2 凭据边界

凭据只允许：

```text
当前进程注入
Read + Trade
Demo API Key
不写文件
不写数据库
不写日志
不返回响应中的认证头
```

必须：

```text
x-simulated-trading: 1
```

禁止：

```text
Withdraw
Live API Key
Live account
Live order
```

若凭据缺失：

```text
Track P route = blocked_demo_credentials_not_injected
Track R 继续运行
```

若只有 Read、没有 Trade：

```text
blocked_demo_trade_permission
```

## 5.3 域名与账户模式

先读取项目已冻结的 OKX 区域域名配置，不硬编码跨地区域名。

预检：

```text
server time
account configuration / position mode
account instruments
balance
positions
private WebSocket login
```

不得自动修改账户模式或持仓模式。

## 5.4 精确 Demo Universe

执行：

```text
GET public SWAP instruments
∩
GET /api/v5/account/instruments?instType=SWAP
∩
exact USDT SWAP
∩
state=live
```

确定性选择：

```text
优先 BTC-USDT-SWAP；
不可用时选择交集中按 instId 排序的第一个正式可交易合约。
```

必须使用账户返回的：

```text
instId
tickSz
lotSz
minSz
ctVal
ctType
state
```

禁止任何模糊 Base Symbol 替代。

## 5.5 烟测前批准合同

生成：

```text
engineering_smoke_contract.json
engineering_smoke_approval_request.md
```

绑定：

```text
environment=demo
instId
accountMode
positionMode
maximumSize=minSz
maximumConcurrentPositions=1
maximumOpenPositions=1
noAdding=true
noAveraging=true
noMartingale=true
releaseQualification=false
contractHash
```

只有当前进程明确设置：

```text
ALPHAPILOT_ENGINEERING_SMOKE_APPROVED=<exact contractHash>
```

才允许下单。

## 5.6 Smoke A：真实挂单—查询—撤单路径

使用：

```text
deterministic clOrdId
post_only 或安全非市价 limit
sz=minSz，按 lotSz 舍入
px 按 tickSz 舍入
expTime
```

执行：

```text
submit
→ REST query by clOrdId/ordId
→ private WS orders update
→ cancel request
→ 通过 orders WS 或 GET order details 确认最终 canceled
```

注意：

```text
撤单接口成功响应只代表请求被接受；
必须继续确认订单最终状态。
```

若订单意外部分或全部成交：

```text
转入 Smoke B 关闭已成交数量；
不得再提交重复开仓单。
```

## 5.7 Smoke B：真实最小成交—持仓—平仓路径

在同一个批准合同下：

```text
最多一个 minSz 仓位
```

根据账户模式使用：

```text
market / IOC / optimal_limit_ioc
或项目已有安全最小成交实现
```

执行：

```text
submit
→ fill / partial fill
→ positions WS + REST
→ account update
→ 按实际 filled size 使用 reduce-only 或 close-position
→ 确认 position=0
```

不得假设：

```text
请求成功 = 成交成功
撤单成功 = 最终 canceled
平仓请求成功 = 仓位已归零
```

## 5.8 重启恢复与对账

在已关闭和开放状态各运行一次受控重启测试：

```text
加载 engineering ledger
查询 open orders
查询 fills
查询 positions
恢复 state machine
```

硬要求：

```text
duplicateOrderCount = 0
orphanOrderCount = 0
orphanPositionCount = 0
unknownStateCount = 0
```

私有 WebSocket：

```text
orders
positions
account
```

REST：

```text
order details
open orders
fills
positions
balance
```

必须输出 WS/REST 一致性审计。

## 5.9 Kill Switch

真实验证：

```text
manual kill
stale market data
stale account
authentication failure
unknown order
orphan position
risk limit
release hash mismatch
approval hash mismatch
```

Kill 后：

```text
禁止新开仓
允许安全查询和对账
按预注册政策关闭已有工程仓位
```

## 5.10 Track P 成功门

```text
authenticatedDemoUniverseNonEmpty = true
privateNetworkVerified = true
cancelPathCompleted = true
fillClosePathCompleted = true
restartRecoveryPassed = true
wsRestReconciliationPassed = true
duplicate/orphan = 0
credentialsPersisted = false
strategyEvidenceChanged = false
```

机械路线：

```text
completed_real_demo_engineering_smoke
```

---

# 六、Track R：机制级准入，而不是候选批量生成

## 6.1 使用 Strategy Acquisition Manager

将参考策略包作为新来源摄取：

```text
SOURCE INGEST
→ MECHANISM EXTRACT
→ SOURCE EQUIVALENCE
→ DATA CONTRACT
→ SIMILARITY AUDIT
→ MECHANISM ADMISSION
→ CANDIDATE FREEZE
```

每个机制生成：

```text
MechanismDossier
```

至少包含：

```text
mechanismId
sourceIds / sourceHashes
sourceEquivalenceClass
economicTransferStory
whoPaysTheEdge
whyItMayPersist
requiredData
causalAvailableAt
simpleBenchmark
falsificationConditions
knownFailureModes
historicalOverlap
parameterDegreesOfFreedom
```

没有完整 Dossier：

```text
blocked_mechanism_source_evidence
```

## 6.2 Mechanism Admission Gate

必须在候选代码生成前通过：

```text
sourceEvidenceComplete
economicMechanismExplicit
dataReady
benchmarkCapitalComparable
falsificationExplicit
parameterDegreesOfFreedom <= 4
notExactDuplicate
notNearDuplicate
notProhibitedRiskDesign
```

禁止：

```text
“多加几个指标看看”
“不同阈值都跑一次”
“先跑结果再解释机制”
```

## 6.3 Vibe 功能的使用边界

### Strategy Acquisition

用于：

```text
来源、机制、候选和生命周期血缘
```

### Similarity

必须比较：

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

### Sandbox

所有 Codex 生成的 Candidate 代码先通过：

```text
AST
import allowlist
无网络
无 subprocess
无 env read
无任意文件访问
独立进程
CPU/内存/时间限制
```

### Factor Lab

本轮只能用于：

```text
结果前定义的 Regime 报告
Ranking 证据
Veto 诊断
相似度
```

不能：

```text
自由组合因子
结果后添加过滤器
直接授予策略资格
```

本轮允许的诊断因子仅：

```text
range-atr
turnover-change
volatility
beta
correlation-regime
```

基础候选信号不依赖这些因子，除非来源原本明确要求。

---

# 七、机制 Campaign A：突破失败后二次入场反转

## 7.1 来源

使用参考包中的：

```text
ref_pa_breakout_failure_second_entry_4h_v1
```

来源：

```text
PA_Agent/文件15-二次入场机会.txt
PA_Agent/文件18-突破失败与突破测试.txt
PA_Agent/文件16-K线信号识别.txt
```

来源等价：

```text
documentation_normalization
```

不得宣称复制某个黑箱 EA。

## 7.2 机制

```text
第一次突破吸引迟到参与者
→ 价格重新回到边界内
→ 第二次测试再次失败
→ 被套方向被迫退出
→ 产生反向再定价
```

## 7.3 候选身份

最多两条，预先分方向：

```text
v42_breakout_trap_second_entry_long_4h_v1
v42_breakout_trap_second_entry_short_4h_v1
```

它们属于同一 Family，并计入多重试验。

## 7.4 冻结规则

直接使用参考规格，不搜索几何参数：

```text
referenceBoundaryBars = 20
firstBreakout = close 超出旧边界 0–0.5 ATR20
failure = 2 根内收回边界
secondTestWindowBars = 6
second failure = 未创新极值且收盘突破前一根反转极值
entry = 下一根开盘
```

初始止损：

```text
第二测试和首次突破结构极值外 0.10 ATR20
maximumDistanceAtr = 2.5
```

Exit：

```text
0.5 仓位在 1R
剩余使用区间中点 / 对侧边界 / 1.5 ATR swing trail
maximumHoldBars = 20
```

## 7.5 基准

主基准：

```text
first_breakout_failure_immediate_fade
```

要求：

```text
相同事件源
相同方向
相同成本
相同资本模型
相同容量
```

必须证明：

```text
第二次入场
>
第一次失败立即反向
```

否则机制被反证。

---

# 八、机制 Campaign B：尖峰后浅回撤延续

## 8.1 来源

```text
ref_pa_spike_pullback_continuation_1h_v1
```

来源：

```text
PA_Agent/极速上涨交易策略.txt
PA_Agent/极速下跌交易策略.txt
PA_Agent/文件16-K线信号识别.txt
```

## 8.2 机制

```text
连续多根强方向 K 线代表单边订单流
→ 2–4 根浅回撤未破坏冲击结构
→ 再次确认后，迟到趋势参与者推动延续
```

## 8.3 候选身份

```text
v42_spike_pullback_continuation_long_1h_v1
v42_spike_pullback_continuation_short_1h_v1
```

## 8.4 冻结规则

```text
spikeBarsMin = 3
每根 body >= 1.5 × 过去 20 根 body 中位数
每根收盘位于自身区间外侧 25%
pullbackBars = 2–4
maximumRetracement = 0.5
confirmation = 收盘突破上一根回撤 K 线的趋势方向极值
entry = 下一根开盘
```

Stop：

```text
回撤结构极值外 0.10 ATR20
maximumDistanceAtr = 2.5
```

Exit：

```text
0.5 仓位在 1R
剩余使用最后确认 swing 或 1.5 ATR
maximumHoldBars = 24
```

## 8.5 基准

```text
unconditional_three_bar_momentum_continuation
```

必须证明：

```text
浅回撤 + 再确认
```

相对普通三根动量延续有增量。

---

# 九、机制 Campaign C：动态残差 Pair

只有 A/B 没有形成 2 条以上预筛幸存者且预算允许时运行。

## 9.1 与已失败 Pair 的区别

已失败：

```text
formation-window normalized-price distance
→ 选 Pair
→ 固定距离 Z-score 收敛
```

新机制必须是：

```text
动态 Hedge Ratio
beta-adjusted residual
关系稳定性
结构断裂
双腿同步执行
```

只是修改 entryZ：

```text
不算新机制
```

## 9.2 来源

使用参考包：

```text
ref_pair_residual_mean_reversion_4h_v1
```

但创建新身份：

```text
v43_dynamic_residual_pair_portfolio_4h_v1
```

## 9.3 Pair Universe

结果前按以下条件冻结：

```text
OKX 精确产品身份
共同历史
双腿 quote turnover
同步时间边界
非结果型经济联系
```

候选 Pair 建议池：

```text
BTC–ETH
ETH–LINK
BTC–LTC
```

若数据审计不支持某一 Pair，只能按数据原因排除，不能按 PnL 排除。

整个 Pair 池作为一个候选组合，不在结果后挑最佳 Pair。

## 9.4 固定规则

主版本：

```text
rolling OLS hedge ratio = 90 根已完成 4H
residual z window = 120
entry abs(z) >= 2.0
exit abs(z) <= 0.5
stop abs(z) >= 3.5
maximumHoldBars = 60
next-bar-open both legs
```

只允许一个敏感性版本：

```text
hedgeWindow = 180
zWindow = 180
```

敏感性不能救主版本失败。

## 9.5 基准

```text
static_hedge_ratio_residual_reversion
```

必须报告：

```text
两腿费用
两腿滑点
Funding
Gross Exposure
Net Beta
一腿失败
关系断裂
```

---

# 十、Funding Carry：只做语义审计，默认不再生成候选

当前候选整体失败，但在一个固定 Trial 中 BTC/ETH 为正、SOL 为负。
不得据此事后删 SOL 或重跑。

先做无经济结果语义审计：

```text
funding_carry_episode_semantics_audit_v1
```

检查：

```text
是否以完整持仓 episode 记账
是否每个 Funding observation 被错误当成一次开平仓
Funding 是否作为现金流
Basis 是否逐期盯市
两腿价格 PnL 是否 Delta 抵消
费用是否只在真实开仓、再平衡和退出发生
连续正 Funding 是否保持仓位
一腿失败和再平衡如何处理
资本占用是否重复计算
```

使用：

```text
人工可手算 golden fixtures
不读取新策略结果
```

### 路由 A：语义正确

```text
funding_carry_current_mechanism_closed
```

本轮不创建新候选。

### 路由 B：语义实质错误

只能创建：

```text
新 Hypothesis ID
新 Candidate ID
新预注册
```

最多一条：

```text
v43_funding_carry_episode_accounting_v2
```

不得：

```text
修改旧候选
按 BTC/ETH 正收益删 SOL
降低 Gate
```

---

# 十一、候选总预算

最大候选：

```text
A 长 / 短：2
B 长 / 短：2
C Pair：1
Conditional Carry：1

总计 <= 6
```

Development 组合：

```text
A：每方向 1
B：每方向 1
C：主版本 + 1 个诊断敏感性
Carry：最多 2，且仅在语义审计触发
```

总 Development Trial：

```text
<= 12
```

Full Backtest：

```text
<= 6
```

Formal：

```text
<= 2
```

---

# 十二、候选实现与预结果认证

每条候选必须经过：

```text
Strategy Acquisition source evidence
Source Equivalence
Historical Similarity
Generated Candidate Sandbox
CandidateAdapter Contract
Synthetic Fixture
Real-Signal Structural Certification
Signal Identity
Event Disposition
Ranking / PIT Evidence
Capacity
Exit-leg Parity
```

要求：

```text
Economic Read = 0
Formal Claim = 0
Locked OOS Read = 0
```

任何生成代码 Sandbox 失败：

```text
implementation_invalid
```

不允许手工绕过。

---

# 十三、预筛

复用已经批准的 AlphaPilot Gate：

```text
不新增 Gate
不降低 Gate
不修改阈值
```

每条候选至少输出：

```text
event/trade count
PF
averageNetR
totalNetR
maximumDrawdown
Base / 1.5x costs
capital-comparable benchmark increment
MFE / MAE
symbol/month/regime concentration
direction breakdown
parameter neighborhood status
source equivalence
similarity classification
```

机制增量是硬要求：

```text
A > immediate fade
B > unconditional three-bar momentum
C > static hedge residual
```

失败：

```text
archive current Candidate ID
```

不得结果后添加 Factor Filter。

---

# 十四、Formal

最多 2 条预筛幸存者进入。

使用当前权威：

```text
FormalGateEvaluation
Candidate-neutral Core
Purged 5-Fold Walk-forward
Embargo
Capital Competition
Base / 1.5x / 2x
Funding
Comparable Benchmark
NW
BH / BY
DSR
PBO
White Reality Check
SPA
Bootstrap
Concentration
```

所有报告必须由同一个 Gate Evaluation Object 派生：

```text
gate_matrix
route_decision
failure_attribution
campaign_summary
Console projection
```

Locked OOS：

```text
0，除非独立、一次性解封政策明确允许；
本轮默认不解封。
```

机械结果：

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

# 十五、Release 与策略 Demo

Formal / 合格 Research Pass 才允许：

```text
immutable_release_ready
```

Release：

```text
approved=false
Demo ARM=false
strategyOrderCount=0
```

生成：

```text
demo_approval_request.json
demo_approval_request.md
```

然后停止：

```text
blocked_waiting_exact_release_approval
```

只有用户明确批准：

```text
exact Release Hash
exact Risk Overlay Hash
```

才允许策略 Demo。

工程烟测批准不能替代策略 Release 批准。

---

# 十六、产品 UI 收口

当前缺失的 `formal_gate_matrix` 页面必须补齐。

在真实工程烟测后更新：

```text
Private Network Verified
Authenticated Demo Universe
Smoke Contract Hash
Order State Timeline
Fill / Partial Fill
Position
Close
WS / REST Reconciliation
Duplicate / Orphan
Kill Switch
Restart Recovery
```

截图：

```text
real_demo_universe.png
real_engineering_smoke_order.png
real_engineering_smoke_position.png
real_engineering_smoke_reconciliation.png
real_kill_switch.png
formal_gate_matrix.png
```

截图不得显示凭据、签名头或敏感账户信息。

---

# 十七、双轨机械终态

Track R：

```text
completed_release_ready
completed_zero_qualified_candidates
blocked_mechanism_source_evidence
blocked_formal_data
implementation_invalid
```

Track P：

```text
completed_real_demo_engineering_smoke
blocked_demo_credentials_not_injected
blocked_demo_trade_permission
blocked_demo_environment
blocked_execution_integrity
```

Master：

```text
engineering_smoke_complete_release_ready
engineering_smoke_complete_zero_qualified
research_complete_demo_smoke_blocked
blocked_waiting_exact_release_approval
completed_strategy_demo_admission
```

Track P 阻塞不得自动停止 Track R；Track R 零候选不得取消 Track P。

---

# 十八、测试

## 18.1 Strategy Acquisition / Vibe

```text
source package hash
source locator
source equivalence
artifact lifecycle
similarity
duplicate rejection
sandbox
no network
no env
no file escape
```

## 18.2 Mechanisms

```text
A：first failure vs second entry identity
B：spike / pullback / confirmation causality
C：rolling OLS only prior data
Carry：episode accounting golden fixture
```

## 18.3 Formal

```text
gate single source
route blocker parity
explicit exclusion conservation
benchmark capital comparability
one-shot
Locked OOS 0
```

## 18.4 Demo

```text
x-simulated-trading
exact account instrument
clOrdId idempotency
expTime
cancel final-state confirmation
partial fill
fill-close
position mode
restart recovery
private WS
REST reconciliation
kill switch
ledger isolation
```

---

# 十九、验证命令

Quant：

```powershell
python -m pytest tests/strategy_acquisition -q --import-mode=importlib
python -m pytest tests/generated_candidate_sandbox -q --import-mode=importlib
python -m pytest tests/factor_lab -q --import-mode=importlib
python -m pytest tests/reference_strategy_research -q --import-mode=importlib
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

# 二十、提交顺序

```text
1. Merge and freeze the V37F–V40 feature baseline without mutating evidence

2. Create the budget-inheriting V41 dual-track successor

3. Run real OKX Demo private-network preflight

4. Execute the real cancel and fill-close engineering-smoke paths

5. Ingest the reference package into Strategy Acquisition Manager

6. Freeze mechanism dossiers and reject duplicates before candidate creation

7. Implement A/B candidates through the hardened sandbox

8. Run bounded A/B prefilter

9. If allowed, implement and run dynamic Pair and conditional Carry audit

10. Freeze and run Formal for at most two survivors

11. Generate an immutable Release only for a qualified candidate

12. Complete real-smoke UI and evidence

13. Stop for exact Release Hash approval

14. Keep Live and Withdraw disabled
```

---

# 二十一、必须输出的证据

## 21.1 Common

```text
integration_merge_receipt.json
program_spec.json
program_state.json
program_ledger.jsonl
program_budget.json
program_budget_ledger.jsonl
```

## 21.2 Product

```text
demo_private_preflight.json
demo_universe_audit.json
engineering_smoke_contract.json
engineering_smoke_approval_overlay.json
engineering_smoke_order_ledger.jsonl
engineering_smoke_fill_ledger.jsonl
engineering_smoke_position_ledger.jsonl
engineering_smoke_cancel_audit.json
engineering_smoke_fill_close_audit.json
private_websocket_audit.json
rest_reconciliation_audit.json
restart_recovery_audit.json
kill_switch_audit.json
strategy_evidence_isolation_audit.json
ui_screenshot_manifest.json
```

## 21.3 Research

```text
source_inventory.json
mechanism_dossiers/
source_equivalence_matrix.csv
artifact_similarity_matrix.parquet
candidate_dedup_decisions.json

funding_carry_episode_semantics_audit.json

candidate_inventory.json
candidate_specs/
candidate_adapters/
preregistrations/

prefilter_matrix.csv
prefilter_gate_matrix.csv
prefilter_failure_attribution.json
benchmark_comparability_matrix.csv

formal_matrix.csv
formal_gate_matrix.csv
formal_route_matrix.csv
failure_attribution.json
statistical_matrix.json
```

## 21.4 Release

```text
release_inventory.json
candidate_releases/
release_hash_audit.json
demo_approval_request.json
demo_approval_request.md
```

未到达时：

```text
status=not_run
```

不得伪造空交易结果。

---

# 二十二、最终自检

必须报告：

```text
Baseline merge commits
Historical Artifact mutation count
Budget inherited / used / remaining

Track P:
- credentials injected
- private network verified
- Demo universe intersection
- smoke contract hash
- cancel path
- fill-close path
- restart recovery
- WS/REST reconciliation
- duplicate/orphan
- strategy evidence delta

Track R:
- sources
- mechanism dossiers
- duplicates rejected
- candidates
- trials
- prefilter survivors
- Formal candidates
- Research/Formal Pass
- archived

By mechanism:
- breakout trap second entry
- spike pullback continuation
- dynamic pair
- funding carry semantics

Release:
- count
- releaseId/hash
- approval

Safety:
- Locked OOS
- credentials persisted
- Live
- Trade API
- Withdraw

Git
Tests
Hashes
Known limitations
Next step
```

---

# 二十三、最终原则

```text
机制级突破：
少量独立收益机制
+ 来源证据
+ 经济转移故事
+ 简单可比基准
+ 明确反证
```

不是：

```text
多生成一些指标组合
+ 看结果再加过滤器
+ 调到盈利为止
```

同时：

```text
工程烟测先把产品链真实跑通；
工程订单永远不冒充策略证据；
策略通过后才进入策略 Demo；
Live 与 Withdraw 始终关闭。
```
