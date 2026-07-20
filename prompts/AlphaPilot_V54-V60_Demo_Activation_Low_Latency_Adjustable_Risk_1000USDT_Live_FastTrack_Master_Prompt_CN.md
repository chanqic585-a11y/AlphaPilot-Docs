# AlphaPilot V13.27.1.54–V13.27.1.60
# TOP200 Demo 正式启用、信号可匹配性、极低延迟执行、可调运行参数与 1000 USDT 实盘功能 Fast-Track
## 给 Codex 的唯一完整 Master Prompt

> **执行定位**
>
> 当前 V13.27.1.53 的 TOP200、OKX Demo Engineering Smoke 与极简 UI 已完成。
> 本工作流从现有精确证据继续，不回滚、不重跑 V46 历史研究、不修改已经封存的
> Engineering Smoke 或 TOP200 Artifact。
>
> 本轮优先目标不是继续讨论回测，而是尽快完成：
>
> ```text
> 精确 Release 人工批准
> → ARM OKX Demo
> → 真实 TOP200 策略扫描
> → 信号、冷却、风险、订单、持仓、退出和对账
> → 下单延迟压缩
> → 可调 Demo / Live 参数
> → 独立 Live 私有只读和工程烟测
> → 人工批准的 1000 USDT Experimental Live Canary
> → 实盘策略扫描、下单、持仓、退出和极简 UI
> ```
>
> 用户明确接受以 1000 USDT 作为首个实盘功能验证的实验资金和可接受损失预算，
> 但该数字以及其他风险参数必须做成 UI 可配置 Profile，不得写成源码常量。
>
> 本轮允许放宽：
>
> ```text
> Formal Pass
> Clean Historical OOS
> Demo 最低闭合交易数
> Demo 最低运行天数
> 长期策略绩效审查等待期
> ```
>
> 本轮不得放宽：
>
> ```text
> Demo / Live 环境隔离
> 精确 Release / Risk Hash 人工批准
> Withdraw=false
> 订单幂等
> 未知订单、孤儿订单和孤儿仓位 fail-closed
> REST / WebSocket 对账
> Kill Switch
> 陈旧信号禁止下单
> 不可变版本与变更记录
> ```
>
> **用户界面继续保持当前极简方向。**
> 不重新设计整套 UI，只补充真正需要操作和观察的功能。

---

# 一、权威输入与身份

## 1.1 总证据包

```text
D:\Codex-Workspace\deliveries\
AlphaPilot_TOP200_Minimal_UI_Closeout_20260720.zip

SHA-256:
1bfb99554b57dc1a623d657e23c8f583ed104ed49a8d878e38bb924645076a56
```

必须在任何代码变更前：

```text
核验 ZIP SHA-256
核验 ZIP CRC
核验 Manifest 96/96
核验 JSON 可解析
核验凭据扫描
```

## 1.2 Git 基线

Quant：

```text
Commit:
7ad720b864d551501a7ba3a07e43019f46b638a7

Tag:
v13.27.1.53-top200-minimal-ui
```

Console：

```text
Commit:
6e8026eb0bf87f4f813c6deabd1fd5cf9fcdf4a6

Tag:
v13.27.1.53-console-top200-minimal-ui
```

工作规则：

```text
使用隔离 worktree
保护用户已有修改
不 amend 历史结果
不 rebase 已封存证据
不 force-push
不移动旧 Tag
每个不可逆阶段单独 Commit
远端 SHA 核验
```

## 1.3 Engineering Smoke

```text
Status:
passed

Order Attempts:
3

Fills:
2

Final Open Orders:
0

Final Nonzero Positions:
0

Duplicate / Orphan / Unknown:
0 / 0 / 0

Evidence Hash:
engineering_smoke_evidence_a35c363b83a0b1c6baf30b25fa3a246cbbc06cce444a90ccb08b7d13ab5ccda1

Contract Hash:
engineering_smoke_contract_a36cf4780a003388c653703294d062b2e081dfb9fa63acabac387c294bea8592
```

Engineering Smoke 已完成，不得重新要求同一环境重复烟测，除非：

```text
OKX Demo account identity 改变
Client major contract 改变
订单状态机改变
Instrument identity 改变
风险 / Kill Switch major version 改变
```

## 1.4 TOP200 Policy

```text
Policy ID:
okx_demo_top200_liquid_usdt_swap_forward_v1

Policy Hash:
top200_universe_policy_2bf7e14ddf8f3f38ead07d26b60d5935feeb2a1019292392bb530fc8d824b45a

Requested:
200

Actual:
82

Snapshot Hash:
demo_top200_universe_snapshot_5615357b51716e05159e3af6eee436d35ecd76083520a3578ea7bcbcf7adb594

Public Instruments:
426

Authenticated Demo Instruments:
116

Eligible:
82
```

82/200 是真实合格数量，不得补造 200。

## 1.5 当前待批准 Release

```text
Release ID:
provisional_research_demo_top200_1fc1e9895d79de78e457bc9c

Release Hash:
provisional_demo_release_96aa2aa4bdb320e91745474f287dda9e4836b8a901910f252bcf447d718010d0

Risk Overlay Hash:
risk_overlay_7221d23144dcd0a357136f6e9587a505d81c86439e223457d2d7393d287b8218

Execution Intersection Hash:
demo_execution_intersection_4285a3e6dd2155945b6498c130e5a99a9943f7503007b02ccbbed66e1e76960d

Approval Request Hash:
exact_release_approval_request_d3e44900174219d0afaaf2ccf01e6bff97a7f41c72198f175a0ce8a4d2b98091
```

当前状态：

```text
formalPass=false
approved=false
demoArm=false
strategyOrderCount=0
route=blocked_waiting_exact_release_approval
live=false
withdraw=false
```

## 1.6 组合组件

```text
v13_7_40_1h_short_rejection_2149_asset_filter_top10

v13_7_20_lf_research_candidate_117

v13_7_20_lf_research_candidate_090
```

组合继续保持：

```text
三个组件不变
组件 Definition Hash 不变
同一 canonical_instrument_id 的 14 × 24 小时冷却不变
ExitPolicy 不变
历史证据分类不变
```

冷却语义：

```text
anchor:
上一笔已接受且已平仓交易的 exit timestamp

duration:
1,209,600 秒

timezone:
UTC

boundary:
entry timestamp >= cooldown end 时允许

scope:
三个组件共享同一 instrument 冷却
```

---

# 二、当前证据中必须先收口的三个小问题

这些是 V54 的短前置审计，不得演变成长期架构重建。

## 2.1 Release 绑定 Commit 审计

当前 Release 内绑定的执行 Commit 早于最终 Closeout Commit。

必须生成：

```text
release_to_final_head_execution_diff_audit.json
```

分别比较：

```text
Release-bound Quant implementation commit
→ Quant final tag commit

Release-bound Console execution commit
→ Console final tag commit
```

分类每个变更：

```text
execution_path
read_only_projection
UI_only
report_only
test_only
artifact_only
```

要求：

```text
unboundExecutionPathChangeCount = 0
```

若最终 Commit 只增加报告、只读投影、UI 或证据：

```text
当前 Release Hash 保持不变。
```

若发现未绑定的执行逻辑变化：

```text
不得批准当前 Release；
生成一个 superseding Release 和新 Hash。
```

## 2.2 Strategy Order Count 范围一致性

Engineering Smoke 隔离审计的 before/after 全局统计中存在：

```text
strategyOrderCount = 2
```

而当前 Release / Closeout 投影为：

```text
strategyOrderCount = 0
```

必须生成：

```text
strategy_order_scope_reconciliation.json
```

明确区分：

```text
historicalGlobalStrategyOrderCount
legacyReleaseOrderCount
currentReleaseOrderCount
currentOpenOrderCount
currentOpenPositionCount
forwardEligibleOrderCount
```

要求：

```text
currentReleaseOrderCount = 0
currentOpenOrderCount = 0
currentOpenPositionCount = 0
unclassifiedOrderCount = 0
```

不得删除历史记录，只修正范围和 UI 标签。

## 2.3 预 ARM 扫描漏斗投影

当前 `demo_scan_funnel_projection.json` 已列出 82 个 Instrument，
但预 ARM 的若干 Count 仍显示 0。

修复为两层：

```text
Universe Readiness Funnel
426 Public
→ 116 Authenticated
→ 82 Eligible
→ 82 Selected

Runtime Strategy Funnel
82 Selected
→ Component Evaluated
→ Signal Matched
→ Cooldown Passed
→ Risk Accepted
→ Order Eligible
→ Order Attempted
→ Exchange Accepted
→ Filled
→ Closed
```

未 ARM 时 Runtime 数值可以为 0，但 Universe Readiness 不得为 0。

---

# 三、版本与关键路径

| 版本 | 目标 |
|---|---|
| V13.27.1.54 | 基线核验、三个小问题收口、信号可匹配性、可写批准能力 |
| V13.27.1.55 | 精确批准、ARM、TOP200 正常扫描、极低延迟执行 |
| V13.27.1.56 | 可调 Runtime Risk、策略参数版本化与热切换 |
| V13.27.1.57 | 独立 Live Client / Ledger / 私有只读与极简 Live UI |
| V13.27.1.58 | Live Engineering Smoke |
| V13.27.1.59 | 1000 USDT Experimental Live Canary Release 与人工批准 |
| V13.27.1.60 | 首个实盘策略扫描 / 订单生命周期、证据包和最终 Closeout |

Demo 启动是第一优先级。

Live 功能可在 Demo 正常扫描后并行建设，不要求等待 30 / 100 笔交易。

---

# 四、V54：Signal Matchability Readiness

目标是避免旧 10 条策略出现的：

```text
有信号
→ Demo 产品不匹配
→ orderAttemptCount=0
```

该审计只检查信号和执行资格，不计算虚拟 PnL，不授予 Formal Pass。

## 4.1 组件真实 Universe 能力

逐组件读取真实代码和不可变定义，输出：

```text
component_signal_matchability.csv
component_signal_matchability.json
```

每个组件至少包含：

```text
componentId
definitionHash
timeframe
direction
internalAssetFilter
hardcodedSymbolList
dynamicUniverseSupported
requiredLookbackBars
requiredFields

top200SelectedCount
identityCompatibleCount
lookbackReadyCount
internalFilterPassedCount
actuallyEvaluatedCount
```

特别核验：

```text
v13_7_40_1h_short_rejection_2149_asset_filter_top10
```

名称中的 `asset_filter_top10` 是否属于真实硬编码 Universe。

不得仅凭名称推断，必须从代码和 Definition Hash 证明。

若它确实只支持 Top10：

```text
按当前不可变定义运行；
不得静默改成 TOP200；
UI 显示该组件实际评估数量。
```

## 4.2 近期只读信号演练

使用：

```text
当前三个组件
当前 82 币 Snapshot
完全相同的信号代码
只使用已完成 K 线
```

运行两个诊断窗口：

```text
最近 30 个完整 UTC 日
最近 90 个完整 UTC 日
```

只输出：

```text
barsEvaluated
componentEvaluations
rawSignalCount
uniqueSignalInstrumentCount
canonicalIdentityPassed
demoInstrumentMatched
cooldownRejected
duplicateRejected
riskRejected
orderEligibleCount
```

禁止输出：

```text
虚拟订单
虚拟持仓
虚拟成交
虚拟 PnL
策略通过结论
```

## 4.3 路由

以下为硬阻塞：

```text
组件代码不能加载
组件 Definition 不一致
所有组件 actuallyEvaluatedCount=0
Canonical identity mismatch
Demo instrument match path broken
数据时间戳不因果
```

以下不是硬阻塞：

```text
30 天 0 信号
90 天 0 信号
自然低频
14 天冷却导致可下单数低
```

零信号时允许人工 Demo 放行，但 UI 必须显示：

```text
策略自然低频，当前没有匹配信号
```

不得误写为 API 或币种故障。

## 4.4 页面只显示四个数字

Demo 页默认显示：

```text
Universe 82
可评估 X
信号 Y
可下单 Z
```

点击后再展开组件和拒绝原因。

---

# 五、V54：人工批准与 ARM 写操作

当前 API 全部为只读。本版本只增加最小受控写操作，不新建第二事实源。

## 5.1 Exact Approval API

新增或复用现有 Approval Store：

```http
POST /api/strategy/releases/<releaseId>/approve
```

请求必须包含：

```text
releaseId
releaseHash
riskOverlayHash
executionIntersectionHash
engineeringSmokeEvidenceHash
engineeringSmokeContractHash
approvalRequestHash
operatorIdentity
approvedAt
```

后端机械验证所有 Hash。

前端无需用户手工复制 64 位 Hash；
批准弹窗显示：

```text
Release 名称
Release Hash 短码
Risk Hash 短码
Universe 82/200
风险摘要
证据等级：Provisional Research Demo
Formal Pass：否
```

点击：

```text
[确认放行 Demo]
```

前端请求中仍提交完整精确 Hash。

批准结果写入：

```text
append-only Approval Overlay
```

不得修改 Release JSON。

## 5.2 ARM API

```http
POST /api/demo/releases/<releaseId>/arm
POST /api/demo/releases/<releaseId>/disarm
```

ARM 前要求：

```text
approved=true
engineeringSmokeReady=true
currentSnapshotPolicyHash matches
current authenticated Demo universe non-empty
no unknown/open orphan state
Kill Switch inactive
credentials ready
```

Strategy 页操作：

```text
[放行到 Demo]
```

Demo 页操作：

```text
[运行所有已批准策略]
```

用户批准与 ARM 分开保留在后端；
UI 可以连续引导，但不得自动批准。

## 5.3 动态 Snapshot 绑定

当前 Release 同时保存了 Policy Hash 与初始 Snapshot Hash。

必须确认运行语义：

```text
Release 绑定 Universe Policy；
初始 Snapshot 是激活证据；
以后每个正常扫描绑定当天 Snapshot Hash；
同一 Policy 下每日名单变化不需要新 Release。
```

若当前 Schema 实际把初始 82 币固定成永久名单：

```text
在批准前创建最后一个 superseding Release，
增加 snapshotBindingMode=policy_bound_daily_snapshot。
```

Policy 变化才要求新 Release。

---

# 六、V55：TOP200 正常运行

## 6.1 点击“运行所有已批准策略”

只运行：

```text
approved=true
demoArmEligible=true
executionEnabled=true
notKilled=true
```

不得运行：

```text
Legacy experimental_override
未批准 Release
归档候选
Shadow
Engineering Smoke
```

## 6.2 调度

当前组件周期：

```text
1h
1d
```

要求：

```text
只在完整 K 线关闭后计算信号
强制因果 availableAt
最早下一根 K 线执行
```

每日先刷新冻结 Universe Snapshot，
每个扫描周期的三个组件共享同一 Snapshot。

## 6.3 全局漏斗

```text
Universe Selected
→ Data Ready
→ Component Evaluated
→ Signal Matched
→ Canonical Identity
→ Demo Tradable
→ Cooldown
→ Duplicate Check
→ Capital Competition
→ Risk
→ Order Eligible
→ Sent
→ Accepted
→ Filled / Canceled / Rejected
→ Position
→ Closed
```

要求守恒：

```text
上一层
=
下一层
+
本层拒绝
```

## 6.4 无自然信号时

不得伪造策略订单。

允许一个独立的：

```text
Integrated Demo Pipeline Validation Fixture
```

验证：

```text
Release 加载
Scheduler
Candidate Adapter
Risk
Sizing
Order
Position
Exit
```

该 Fixture：

```text
engineeringOnly=true
strategyEvidenceEligible=false
forwardEvidenceEligible=false
```

不得计入策略订单和前向样本。

自然策略继续正常等待信号。

---

# 七、V55：Ultra-Low-Latency Execution Path

用户明确要求：

```text
扫描到信号后不能再等待约 20 秒下单。
```

20 秒必须定义为严重故障。

## 7.1 延迟分段

每个信号记录：

```text
barCloseExchangeTs
marketEventReceivedTs
signalCompletedTs
riskCompletedTs
orderIntentDurableTs
orderSendTs
gatewayInTime
gatewayOutTime
exchangeOrderCreatedTs
firstFillTs
finalFillTs
```

计算：

```text
marketDataLagMs
signalComputeMs
riskDecisionMs
signalToOrderSendMs
gatewayProcessingMs
exchangeAckMs
fillWaitMs
endToEndMs
```

使用：

```text
UTC wall-clock timestamp
+
monotonic clock
```

避免系统时间跳变。

## 7.2 可调 ExecutionLatencyProfile

所有目标值放入版本化配置，不写死在代码。

第一版建议默认值：

```text
signalToOrderSendTargetMs = 750
signalToOrderSendSoftWarnMs = 1500
maximumSignalAgeMs = 3000
exchangeAckTimeoutMs = 2000
orderRequestExpiryMs = 3000
orderTransportMode = auto
```

UI 允许人工调整并生成：

```text
executionLatencyProfileVersion
executionLatencyProfileHash
```

系统不可关闭的故障规则：

```text
signalToOrderSendMs >= 20000
→ critical_latency_failure
→ 该陈旧信号禁止下单
```

`maximumSignalAgeMs` 不得设置为 20 秒以上。

## 7.3 热路径

正常运行前持续完成：

```text
TOP200 行情预热
1h / 1d 滚动窗口驻留内存
指标增量计算
tickSz / lotSz / minSz 缓存
账户、持仓和订单状态常驻
风险预算预计算
Cooldown 索引预计算
私有 WebSocket 登录和订阅
```

收线后的关键路径只允许：

```text
追加最后确认 K 线
→ 最终信号判断
→ 内存冷却检查
→ 内存风险检查
→ 原子 clOrdId / intent reservation
→ 发送订单
```

以下不得阻塞发单：

```text
生成报告
写大 JSON
截图
UI 刷新
统计 PF
更新月度报表
下载完整历史
重建 Universe
```

这些放入异步队列。

## 7.4 REST / WebSocket 下单

实现可调：

```text
orderTransportMode:
auto
websocket
rest
```

工程基准同时测试 REST 与 Private WebSocket：

```text
P50 / P95 / P99
错误率
重连恢复
重复订单
```

`auto` 选择当前环境中更快且更稳定的通道。

要求：

```text
私有 orders / positions / account WebSocket 常连接
orders channel 在发单前已订阅
REST 作为恢复和备用路径
```

## 7.5 防止迟到订单

订单请求携带可调：

```text
expTime
```

要求：

```text
orderRequestExpiryMs <= maximumSignalAgeMs
```

信号过期时不发送或由交易所拒绝，
不得在 20 秒后意外成交旧信号。

## 7.6 UI

正常只显示：

```text
信号→发单
交易所确认
成交
```

例如：

```text
0.42s / 0.31s / 0.86s
```

黄色：

```text
超过 Soft Warn
```

红色：

```text
陈旧信号未发送
或 signalToOrderSend >= 20s
```

---

# 八、V56：所有主要参数可人工调整

绝大多数参数必须从源码常量迁移为版本化 Profile。

## 8.1 Runtime Risk Profile

可配置：

```text
allocatedCapital
riskPerTradePercent
riskPerTradeUSDT
maximumPortfolioOpenRiskPercent
maximumPortfolioOpenRiskUSDT
maximumConcurrentPositions
maximumInstrumentRisk
maximumSameDirectionRisk
maximumCorrelationClusterRisk
maximumPortfolioBeta
maximumLeverage
marginMode
dailyLossLimit
programLossLimit
hardKillLossLimit
scanTopN
```

每个字段包含：

```text
currentValue
suggestedValue
minimumAllowed
maximumAllowed
effectiveAt
changeMode
```

## 8.2 风险变更

降低风险：

```text
可立即生效
或下一笔新订单生效
```

提高风险：

```text
生成新 Runtime Risk Overlay Hash
→ 显示差异
→ 人工确认
→ 生效
```

不需要 Codex 改源码。

前向结果按：

```text
Risk Overlay Hash
```

分段统计。

## 8.3 策略参数

进入 Demo 或 Live 后仍允许修改：

```text
入场参数
时间周期
组件权重
信号排名
冷却时长
Universe 规则
初始止损公式
止盈
分批退出
移动止损
最大持仓时间
结构退出
```

修改流程：

```text
复制为新版本
→ 显示 Diff
→ 新 Candidate ID
→ 新 Definition Hash
→ 新 Release Hash
→ 人工确认
→ 热切换
```

不得静默覆盖正在运行的 Release。

## 8.4 热切换模式

```text
new_entries_only
现有持仓由旧版本管理，新订单使用新版本

flatten_then_switch
停止旧版本开仓，平旧仓后切换

manual_position_migration
逐笔决定继续旧版、使用新止损、减仓或平仓
```

默认：

```text
new_entries_only
```

## 8.5 单笔人工干预

允许：

```text
收紧止损
修改更保守的止盈
部分减仓
全部平仓
暂停该币
暂停该策略
```

记录：

```text
manualIntervention=true
operator
timestamp
before
after
reason
```

账户 PnL 仍统计，
但纯自动策略表现单独报告。

## 8.6 不允许普通关闭的底线

```text
Demo / Live 隔离
Withdraw=false
订单幂等
订单 / 持仓对账
未知状态停止新开仓
Kill Switch
凭据保护
变更历史不可删除
```

---

# 九、V56：一键 Strategy Factory

当前页面已有入口和只读进度。
本版本将其接入真实后台，不新建第二研究引擎。

## 9.1 操作

```text
[生成策略]
[组合策略]
```

点击后默认只显示：

```text
周期
快速 / 标准
最大候选
最大试验预算
开始
```

高级设置折叠。

## 9.2 后台

复用：

```text
Strategy Acquisition Manager
Vibe 生命周期
Artifact Dedup
Sandbox
Research Service
Prefilter
Formal Core
Release Builder
```

用户只看到：

```text
准备资料
生成方案
数据检查
调参与回测
稳健性验证
组合评估
完成
```

## 9.3 约束

```text
有限预算
全部 Trial 记录
不读 Locked OOS 调参
不无限优化到盈利
零幸存者是合法结果
不自动批准 Release
```

该任务在 Demo 运行时可以后台并行，
不得阻塞订单热路径。

---

# 十、V57：独立 Live 功能核心

本阶段只在 Demo 已完成：

```text
批准
ARM
至少一次正常扫描
无执行完整性异常
```

后开启。

不要求 Demo 盈利或最低交易数量。

## 10.1 环境隔离

建立独立：

```text
Live Config
Live Credential Reference
Live Client Profile
Live Release Store
Live Approval Store
Live Order Ledger
Live Fill Ledger
Live Position Ledger
Live Reconciliation
Live Kill Switch
```

可复用通用签名、HTTP、WebSocket 和订单状态机代码，
但 Demo 与 Live：

```text
URL
Header
API Key
Store
Ledger
Approval
Release
UI environment label
```

必须完全隔离。

## 10.2 Live API

用户在 OKX 页面手动：

```text
创建独立子账户（优先）
转入实验资金
创建 Read + Trade API Key
Withdraw=false
绑定固定 IP
```

AlphaPilot 不得调用：

```text
Withdraw
Transfer
创建 / 修改 API Key
```

## 10.3 Live 私有只读

先验证：

```text
server time
account config
API permissions
balance
positions
pending orders
recent fills
account instruments
private WebSocket login
```

输出脱敏账户身份 Hash。

## 10.4 极简 Live UI

沿用当前 Demo UI，不重构视觉。

显示：

```text
LIVE 红色环境标签
连接
权益
可用余额
今日盈亏
浮动盈亏
实验损失预算剩余
活动策略
开放持仓
异常订单
```

操作：

```text
[运行所有已批准实盘策略]
[风险设置]
[暂停新开仓]
[取消全部挂单]
[紧急停止并平仓]
```

K 线图可以在持仓详情中懒加载，
不作为本轮硬门。

---

# 十一、V58：Live Engineering Smoke

需要独立、精确的工程烟测批准。

它只验证 Live 功能，不批准策略 Live Release。

## 11.1 合约

用户在 UI 选择：

```text
精确 Instrument
订单规模
最大订单数
```

默认建议最小规模，但不写死。

必须：

```text
账户无未知订单
账户无未知持仓
Read + Trade
Withdraw=false
```

## 11.2 路径

```text
Path A:
post-only submit
→ query
→ cancel
→ final canceled

Path B:
minimum fill / partial fill
→ position
→ reduce-only close
→ position zero
```

验证：

```text
restart recovery
REST / WS reconciliation
Kill Switch
duplicate / orphan / unknown = 0
```

成功：

```text
liveEngineeringSmokeReady=true
```

然后生成 Experimental Live Release。

---

# 十二、V59：1000 USDT Experimental Live Canary

## 12.1 定位

```text
releasePurpose:
operator_approved_live_canary

formalPass:
false

productionQualified:
false

automaticPromotion:
false
```

这是一条用户接受损失后用于功能和真实执行验证的实验 Release。

## 12.2 可调 Live Experiment Profile

所有值由 UI 填写并生成 Hash。

字段：

```text
allocatedCapitalUSDT
maximumAcceptedLossUSDT
riskPerTradePercent
riskPerTradeUSDT
maximumPortfolioOpenRiskPercent
maximumPortfolioOpenRiskUSDT
maximumConcurrentPositions
maximumInstrumentRisk
maximumLeverage
marginMode
dailyLossLimit
programLossLimit
hardKillLossLimit
scanTopN
```

初始 UI 可以预填：

```text
allocatedCapitalUSDT = 1000
maximumAcceptedLossUSDT = 1000
```

但用户可修改，不得作为源码常量。

强制关系：

```text
hardKillLossLimit 必填
hardKillLossLimit <= maximumAcceptedLossUSDT
maximumAcceptedLossUSDT <= allocatedCapitalUSDT
```

UI 必须提示：

```text
损失控制为目标，不保证快速行情中的最终成交损失绝对不超出。
```

## 12.3 精确批准

生成：

```text
Live Release Hash
Live Risk Overlay Hash
Live Environment Hash
Live Universe Policy Hash
```

批准弹窗必须显示：

```text
LIVE
实验资金
最大接受损失
每笔风险
总开放风险
持仓数
杠杆
Withdraw=false
```

用户确认：

```text
exact Live Release Hash
exact Live Risk Overlay Hash
最大接受损失金额
```

后才允许 ARM。

## 12.4 无绩效前置硬门

不要求：

```text
Formal Pass
Demo 盈利
30 / 100 笔 Demo
30 / 90 天 Demo
```

只要求：

```text
Demo execution integrity passed
Live private read passed
Live engineering smoke passed
Live risk profile complete
exact manual approval
```

---

# 十三、V60：实盘策略功能验证

## 13.1 扫描

Live 使用独立的动态 Universe Snapshot。

可配置：

```text
scanTopN
```

不写死 200。

第一版可使用与 Demo 相同 Policy，
但 Live Snapshot 和 Hash 独立生成。

## 13.2 下单

```text
自然策略信号
→ Identity
→ Live Tradable
→ Cooldown
→ Capital Competition
→ Live Risk
→ Latency / Signal Age
→ 自动发单
```

人工批准 Release 后，
不需要逐笔人工批准。

## 13.3 首个成功闭环

目标：

```text
至少一次正常 Live Scheduler Scan
至少一次完整信号漏斗记录
出现自然信号时完成订单生命周期
```

没有自然信号时：

```text
继续运行；
不得伪造策略信号。
```

Live Engineering Smoke 已独立证明实盘订单功能。

## 13.4 绩效分类

分开显示：

```text
Demo Core / Expansion
Live Automatic
Live Manual Intervention
Account Combined
```

不自动晋级生产。

---

# 十四、UI 最小增量修改

保持当前视觉和导航。

只做必要改动：

## 14.1 Strategy 页

当前：

```text
Release 已冻结 6/7
```

批准后：

```text
Release 已批准 7/7
```

增加一个主按钮：

```text
[放行到 Demo]
```

批准后按钮变为：

```text
[前往 Demo 运行]
```

`Generate / Combine` 在研究服务空闲时可用，
不应因为一个 Release 等待批准而永久禁用。

隐藏：

```text
完整 Release ID
完整 Hash
长篇安全说明
```

默认仅显示短码，完整值放折叠详情。

## 14.2 Demo 页

增加：

```text
[运行所有已批准策略]
[风险设置]
[全部暂停]
[紧急停止]
```

顶部只显示：

```text
连接
权益
今日盈亏
浮动盈亏
运行策略
开放持仓
```

漏斗显示：

```text
82 → 可评估 → 信号 → 可下单 → 已下单
```

延迟显示：

```text
信号→发单
确认
成交
```

## 14.3 移动端

修复：

```text
浮动返回按钮遮挡内容
长 Release ID 换行占据过多空间
零值分类卡纵向过长
```

零值分类默认隐藏或合并为：

```text
其他 0
```

## 14.4 提示

正常：

```text
不显示长篇解释
```

仅问题弹窗：

```text
认证失败
Universe 为空
无可评估组件
订单未知
孤儿订单 / 仓位
对账失败
信号陈旧
延迟严重
Kill Switch
```

---

# 十五、安全与操作原则

## 15.1 人工可调

允许：

```text
资金
风险
持仓数
杠杆
TOP N
停止线
策略参数
止损止盈
冷却
组件权重
```

但：

```text
提高风险需要新 Overlay Hash 和人工确认
策略逻辑变化需要新 Release Hash
```

## 15.2 运行中变更

不得静默修改当前 Release。

所有变更：

```text
草稿
→ Diff
→ 新版本
→ Hash
→ 生效方式
→ 人工确认
```

## 15.3 不可关闭

```text
环境隔离
Withdraw=false
幂等
对账
Kill Switch
陈旧信号拒绝
变更审计
凭据保护
```

---

# 十六、测试要求

## 16.1 基线和一致性

```text
Evidence ZIP SHA / CRC
Manifest 96/96
Release-to-final-head execution diff
Strategy order count scope reconciliation
Pre-ARM funnel counts
Historical Artifact mutation count=0
```

## 16.2 Matchability

```text
每个组件实际支持币数
Top10 filter detection
30 / 90 day signal diagnostic
Canonical identity
Demo match
No virtual PnL
Zero signal not misclassified as API failure
```

## 16.3 Approval / ARM

```text
Exact hashes required
Wrong hash rejected
Unapproved cannot ARM
Approved can ARM
Run-all excludes legacy/unapproved
Approval append-only
```

## 16.4 Latency

```text
warm cache
incremental indicators
no heavy I/O hot path
monotonic timestamps
REST / WS benchmark
expTime
stale signal reject
>=20s critical failure
P50/P95/P99 report
```

## 16.5 Parameters

```text
Risk changes versioned
Lower risk immediate
Higher risk reapproval
Strategy change creates new Release
Hot switch modes
Manual intervention separated
```

## 16.6 Demo / Live

```text
Demo and Live keys rejected cross-environment
Separate ledgers
Read + Trade only
Withdraw endpoints unavailable
Live smoke lifecycle
Hard Kill
Restart recovery
Reconciliation
```

## 16.7 UI

```text
Current real state
No stale cache
No long normal-state explanation
Actionable problem popup
Mobile no overlap
No credential exposure
```

---

# 十七、机械路线

## Demo

```text
blocked_matchability_implementation
blocked_exact_release_approval
approved_not_armed
armed_waiting_scan
running_no_signal
running_with_rejections
running_with_orders
forward_collecting
paused
killed
```

## Latency

```text
latency_ready
latency_warning
stale_signal_rejected
critical_latency_failure
transport_degraded_rest_fallback
```

## Live

```text
live_not_configured
live_private_read_ready
waiting_live_smoke_approval
live_smoke_ready
waiting_exact_live_release_approval
live_canary_armed
live_canary_running
live_killed
```

---

# 十八、执行顺序

```text
1. 核验总证据包和 Git 基线

2. 完成三个小问题审计：
   - Release Commit Binding
   - Strategy Order Count Scope
   - Pre-ARM Funnel Counts

3. 完成 Signal Matchability Readiness

4. 增加 Exact Approval 和 ARM 写操作

5. 用户通过 UI 放行当前 TOP200 Demo Release

6. ARM 并运行所有已批准策略

7. 完成首个正常扫描，启用完整漏斗

8. 实现并验收 Ultra-Low-Latency Hot Path

9. 开放可调 Runtime Risk 和策略版本化

10. 启用一键 Strategy Factory 后台任务

11. 建立独立 Live 私有只读和极简 UI

12. 生成并等待独立 Live Engineering Smoke 批准

13. 完成 Live Smoke

14. 生成可调 1000 USDT Experimental Live Canary Release

15. 用户精确批准 Live Release / Risk Hash

16. ARM Live Canary

17. 完成首个实盘正常扫描和自然策略订单生命周期

18. 输出一个总证据 ZIP、SHA-256 和一份中文 Closeout
```

---

# 十九、必须交付的核心证据

允许内部生成多个 JSON / Parquet，但用户最终只接收一个总证据 ZIP。

ZIP 至少包含：

```text
final_closeout_cn.md
final_self_check.json

baseline_evidence_verification.json
release_to_final_head_execution_diff_audit.json
strategy_order_scope_reconciliation.json

component_signal_matchability.csv
signal_matchability_30d.json
signal_matchability_90d.json

demo_approval_overlay.json
demo_arm_audit.json
demo_first_scan_audit.json
demo_scan_funnel.jsonl

execution_latency_profile.json
execution_latency_benchmark.json
execution_latency_ledger.parquet
stale_signal_audit.json

runtime_risk_profile.json
runtime_risk_overlay_history.jsonl
strategy_version_switch_audit.json

demo_order_ledger.jsonl
demo_fill_ledger.jsonl
demo_position_ledger.jsonl
demo_closed_trades.parquet
demo_reconciliation_audit.json

live_environment_contract.json
live_private_read_audit.json
live_engineering_smoke_closeout.json

experimental_live_release.json
experimental_live_risk_overlay.json
live_approval_request.json
live_approval_overlay.json

live_order_ledger.jsonl
live_fill_ledger.jsonl
live_position_ledger.jsonl
live_reconciliation_audit.json
live_kill_switch_audit.json

simple_ui_screenshot_manifest.json
artifact_manifest.json
git_tag_push_receipt.json
test_results.json
```

未执行项必须：

```text
status=not_run
```

不得伪造空结果。

---

# 二十、Codex 最终回复模板

```text
AlphaPilot V54–V60 Fast-Track 完成

Baseline:
- Evidence ZIP:
- Quant:
- Console:
- historical mutation:

Pre-ARM:
- release binding:
- order scope:
- universe funnel:

Signal Matchability:
- TOP200 actual:
- component A evaluated / signals:
- component B evaluated / signals:
- component C evaluated / signals:
- identity matches:
- order eligible:
- conclusion:

Demo:
- Release ID / Hash:
- Risk Hash:
- approved:
- ARM:
- first scan:
- strategy signals:
- orders:
- fills:
- positions:
- closed trades:
- reconciliation:

Latency:
- transport:
- signal→send P50/P95/P99:
- ack P50/P95/P99:
- maximum:
- stale rejects:
- critical >=20s:
- profile hash:

Parameters:
- risk UI:
- strategy versioning:
- hot switch:
- manual intervention:

Strategy Factory:
- generate:
- combine:
- background progress:

Live:
- environment:
- private read:
- engineering smoke:
- experiment capital:
- maximum accepted loss:
- Release ID / Hash:
- Risk Hash:
- approved:
- ARM:
- first scan:
- first order lifecycle:
- Kill Switch:

UI:
- Strategy:
- Demo:
- Live:
- mobile:
- K-line:

Safety:
- Withdraw:
- environment isolation:
- duplicate/orphan/unknown:
- credential exposure:

Git:
Tests:
Evidence ZIP:
SHA-256:
Current Route:
```

---

# 二十一、最终原则

```text
先把系统真实跑起来，
再用 Demo 和小资金实盘证据继续进化策略。
```

```text
放宽的是策略证据等待期，
不是订单、账户和风险控制完整性。
```

```text
参数可以由用户调整，
但每次变化必须可追溯、可确认、可回滚。
```

```text
正常信号产生后目标在 1 秒内发出订单请求；
20 秒必须视为 critical_latency_failure。
```

```text
UI 保持简单：
用户只看到状态、盈亏、持仓、延迟和下一步；
复杂证据默认折叠。
```
