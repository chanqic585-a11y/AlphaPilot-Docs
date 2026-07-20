# AlphaPilot V37F–V40
# Vibe-Trading 选择性吸收、当前证据独立审计与下一步决策报告

## 一、当前权威状态

V33–V40 总证据包显示，系统实际执行至 V37E：

```text
V33–V36：数据、研究服务、标准复制和 Formal 核心已形成闭环
V37：数据能力、参考策略、源忠实度、执行控制内核与 TSMOM Formal 已部分完成
V38：原计划的完整运营 UI 未正式完成
V39：没有合格 Release，策略 Demo 未开始
V40：Live Canary 未开始
```

当前计数：

```text
Formal 完成：2
Formal Pass：0
不可变策略 Release：0
策略 Demo ARM：0
策略订单：0
Live Canary：0
未解决数据阻塞：0
未解决工程阻塞：0
终局策略表现失败：2
```

两条最新 Formal：

```text
v35_tsmom_crypto_adaptation / 4h
- 36 笔
- PF 0.4893
- 平均净 R -0.3602
- 总净 R -12.9677
- 归档

v37e_tsmom_daily_capacity_successor / 1d
- 6 笔
- PF 2.5980
- 平均净 R +0.4389
- 总净 R +2.6334
- 最大回撤 2.4422%
- 仅 2 个正收益 Fold
- 相对简单基准增量 -1.4826R
- 单币和单月贡献集中
- 归档
```

因此当前瓶颈已经不是：

```text
数据不可用
Formal Core 不能运行
CandidateAdapter 依赖 S01
容量或 Funding 无法接入
```

而是：

```text
高质量、源语义明确、与历史失败家族独立的策略供给仍然不足。
```

---

## 二、独立审计发现的两个必须先修的小问题

这两个问题不会改变 V36/V37E 的“归档”结论，但必须在下一次 Formal 前修复，否则未来可能出现 Gate 报告与机械路由不一致。

### 2.1 Fold Assignment Gate 语义错误

V37E 的 Fold 审计为：

```text
inputEventCount = 19
acceptedEventCount = 8
outsideWindowEventCount = 9
boundaryRejectedEventCount = 2
rejectedEventCount = 11
```

当前 `gate_matrix.json` 将：

```text
fold_assignment_complete.actual = 11
threshold = 0
passed = false
```

但这 11 条是：

```text
显式的窗口外事件
+
预注册的跨边界排除
```

不是：

```text
未分类
多重分配
未知 disposition
泄漏
```

正确 Gate 应检查：

```text
unclassifiedEventCount = 0
multiAssignedEventCount = 0
unknownDispositionCount = 0
crossBoundaryLeakageCount = 0
```

显式排除不应导致 Fold Assignment 失败。

### 2.2 Gate Matrix 与 Route Blocker 不一致

V37E `gate_matrix.json` 还记录：

```text
single_symbol_concentration = 0.6721 > 0.35
single_month_concentration = 0.7802 > 0.35
```

均为失败；但 `route_decision.json` 只列出：

```text
positive_fold_minimum
benchmark_total_incremental_net_r
benchmark_positive_increment_fold_minimum
```

正式预注册中，单币和单月集中度属于 `riskAndEvidence` 准入 Gate，
因此应进入权威 blocker 列表。

下一版必须建立：

```text
一个权威 Gate Evaluation Object
→ Gate Matrix
→ Route Decision
→ Failure Attribution
→ UI Projection
```

全部由同一对象派生，禁止四处独立拼装。

---

## 三、Vibe-Trading 与 AlphaPilot 当前能力的重叠

AlphaPilot 已经拥有：

```text
Source Registry
Canonical Replication
后台有界 Research Service
Candidate-neutral CandidateAdapter
正式 Walk-forward
成本和 Funding
资本竞争
多重试验统计
不可变 Release 设计
Demo/Live 隔离
执行控制只读投影
```

因此不能把 Vibe-Trading 整体合入，也不需要重新实现第二套回测系统。

Vibe-Trading 最值得吸收的是 AlphaPilot 目前仍然薄弱的“策略供给层”：

```text
1. 文档/论文/源码 → 结构化策略 Artifact 的获取流程
2. 长期持久化的策略/因子生命周期索引
3. 因子 Operator 与 Alpha Zoo 式预筛
4. 信号、事件和收益层面的重复性比较
5. Codex 生成策略代码的安全沙箱
6. 策略和因子衰减监控
7. Strategy Lab / Alpha Zoo 的研究 UI 信息架构
8. Demo/Live 闭合交易的行为归因
```

---

## 四、Vibe-Trading 选择性吸收矩阵

| Vibe-Trading 能力 | AlphaPilot 决策 | 接入位置 | 说明 |
|---|---|---|---|
| Strategy Development Manager | 采用思想并清洁重写 | V37G | 连接资料、机制、候选、回测和监控 |
| SQLite Strategy Store | 采用生命周期模型，不作为第二权威账本 | V37G | AlphaPilot Program/Campaign Ledger 仍是权威 |
| Alpha Zoo / Operator Library | 选择性采用 | V37H | 只引入 Crypto 数据可支持的独立因子 |
| Alpha Compare / Bench | 采用并加强 | V37H | 加入信号相关、事件重叠、收益相关和持仓重叠 |
| Generated SignalEngine Sandbox | 采用并加严 | V37H | AlphaPilot 禁止任何网络 import，即便代码不可达 |
| Decay Monitoring | 延后启用 | V39 以后 | 只能监控，不允许原地改 Release 参数 |
| Shadow Account 归因 | 只采用归因思想 | V39/V40 | 只分析真实 Demo/Live 闭合交易，不恢复本地 PnL 模拟 |
| Alpha Zoo / Strategy UI | 采用信息架构 | V38 | 不迁移到 React，不改现有 Console 技术栈 |
| 多市场 Data Fallback | 不采用 | — | Formal 数据继续锁定 OKX 官方来源 |
| 通用 Sharpe Gate | 不采用 | — | AlphaPilot 正式 Gate 保持权威 |
| Vibe Live/Broker 连接 | 不采用 | — | AlphaPilot 保留自己的 Release、批准和 OKX 执行链 |
| 自动参数原地更新 | 禁止 | — | 衰减后必须创建新 Candidate ID 和 Release Hash |

---

## 五、Vibe-Trading 固定审计身份

后续审计固定到：

```text
Repository:
HKUDS/Vibe-Trading

Commit:
7d42de944466e1a1f12f0df3933624fe665dee3c

License:
MIT
```

重点参考文件：

```text
agent/src/skills/strategy-dev-manager/SKILL.md
agent/src/strategy_store/models.py
agent/src/strategy_store/sqlite_store.py
agent/src/strategy_store/metrics.py
agent/src/tools/sdm_decay_scan_tool.py

agent/src/factors/base.py
agent/src/tools/alpha_zoo_tool.py
agent/src/tools/alpha_bench_tool.py
agent/src/tools/alpha_compare_tool.py

agent/backtest/runner.py
agent/tests/test_backtest_runner_security.py

agent/src/skills/shadow-account/SKILL.md
frontend/src/pages/AlphaZoo.tsx
```

规则：

```text
不跟随 main 自动更新；
不直接运行 Vibe-Trading；
不建立运行时依赖；
不复制不必要的代码、资产或整个前端；
复制实质代码时保留 MIT 声明和来源；
优先清洁重写 Adapter。
```

---

## 六、下一阶段的架构位置

推荐在原 V38–V40 之前插入：

```text
V37F：集成基线与 Formal Gate 一致性
V37G：Strategy Acquisition Manager
V37H：安全沙箱、Factor Lab 与重复性审计
V37I：有界策略获取冲刺
V37J：Formal 与 Release Ready
```

然后恢复：

```text
V38：产品功能和 UI 完成
V39：正式策略 OKX Demo
V40：独立批准的小资金 Live Canary
```

这样不会改变 V38–V40 原来的产品含义。

---

## 七、Strategy Acquisition Manager 的定位

它不是另一个自动回测器，也不是自由生成策略的 LLM 循环。

工作流：

```text
SOURCE INGEST
→ MECHANISM EXTRACT
→ DATA CONTRACT
→ SOURCE EQUIVALENCE
→ DUPLICATION AUDIT
→ CANDIDATE FREEZE
→ PREFILTER
→ FORMAL
→ RELEASE READY
→ MONITOR
```

Artifact 类型：

```text
factor
strategy
mixed
context_only
negative_research_asset
```

来源类型：

```text
paper
research_report
official_documentation
readable_source_code
parameter_file
compiled_black_box
user_trade_evidence
internal_spec
```

源忠实度：

```text
exact_executable_source
source_faithful_reproduction
clean_room_normalized_variant
mechanism_only
insufficient_source_evidence
```

V37B/C/D 的经验必须固化：

```text
“AlphaPilot 标准化版本失败”
不能自动写成
“原作者策略失败”。
```

---

## 八、策略 Artifact 生命周期

建议投影状态：

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

权威关系：

```text
Program/Campaign Ledger：
决定研究和执行事实

Strategy Artifact Store：
提供跨 Campaign 的检索、血缘、来源、Bench History 和状态投影
```

Artifact Store 不能自己授予：

```text
Formal Pass
Release
Demo ARM
Live
```

---

## 九、安全沙箱

Vibe-Trading 的 AST 安全思想值得采用，但 AlphaPilot 应更严格。

生成候选代码全部在独立进程执行，要求：

```text
只读 Data Snapshot
只写临时 Artifact 目录
禁止网络
禁止 subprocess
禁止 shell
禁止 eval/exec/compile
禁止动态 import
禁止读取环境变量
禁止绝对路径
禁止父目录跳转
禁止写项目目录
CPU/内存/时间预算
进程超时强制结束
```

与 Vibe 不同：

```text
AlphaPilot 生成候选文件中，
即便网络 helper 不可达，也禁止 requests/httpx/socket 等 import。
```

只有经过：

```text
AST Audit
Dependency Audit
Sandbox Fixture
CandidateAdapter Contract
Real-Signal Structural Certification
```

后，代码才可进入候选分支。

---

## 十、Factor Lab 与 Alpha Zoo 的正确用途

不把 452 个 Alpha 一次性投进回测。

第一版只建立少量不同经济含义的因子组：

```text
时间序列动量
横截面动量
短期反转
残差动量
实现波动率
振幅/ATR 状态
成交额变化
流动性
市场 Beta
相关性状态
Funding
Basis
```

用途：

```text
单因子 IC/IR 研究
候选 Ranking
候选 Veto
Regime 标签
简单基准
因子去重
```

禁止：

```text
数百个因子自由组合
因子 Zoo 直接授予 Formal Pass
A 股因子不经数据语义审计直接用于 Crypto
```

---

## 十一、重复性审计

当前已有文字语义去重，但不足以控制自动策略家族膨胀。

新增五层指纹：

```text
sourceLineageHash
canonicalFormulaHash
signalCorrelation
eventOverlap
dailyReturnCorrelation
holdingOverlap
```

分类：

```text
exact_duplicate
near_duplicate
same_family_variant
mechanism_related
independent
```

因子可以参考：

```text
IC/信号相关 > 0.99：重复
0.90–0.99：近似变体
```

策略不能只用一个相关阈值，必须联合：

```text
事件重叠
方向
持仓路径
日收益
来源和机制
```

所有阈值在候选结果前冻结。

---

## 十二、下一轮策略研究优先级

不再重跑已经归档的两个 TSMOM 身份。

### 优先级 1：OKX 正 Funding Carry

当前 V37A 已证明：

```text
BTC/ETH/SOL
OKX Spot + OKX USDT Swap + 实际 Funding
历史与 Formal 数据 ready
```

新机制：

```text
长期 Spot
+
等 Delta Short Perpetual
+
只在预期 Funding 覆盖双腿成本、Basis 风险和资本占用时持有
```

第一批只研究：

```text
正 Funding Carry
```

不要求短 Spot，不引入借币不可得问题。

Formal 前必须加入：

```text
双腿成交
双腿容量
Basis 风险
Funding availableAt
资金占用
再平衡成本
一腿失败政策
```

Demo 前仍需前向 Order Book 证据。

### 优先级 2：源忠实 Turtle / Donchian

来自可读 MQ4 源码的规则优先于上下文 prose。

候选创建前必须与已归档 TSMOM 做指纹审计：

```text
若信号相关和事件重叠达到近似重复：
拒绝新候选。

若退出、入场和风险机制实质不同：
允许作为新家族。
```

### 优先级 3：源忠实 Pair Relative Value

以前的 Pair 版本在 Development 略负。

只有在新候选具备实质变化时继续，例如：

```text
预注册 Pair Selection
动态 Hedge Ratio
协整/结构断裂
同步执行
一腿失败处理
双腿 Funding 和成本
```

不得只改 Z-score 阈值。

### 优先级 4：Funding 事件研究

例如：

```text
Funding 极端
Funding 结算时点
Basis 与 Funding 联合状态
```

必须与纯 Carry 分开。

### 暂缓

```text
无 PIT Universe 的横截面多因子 Formal
普通 RSI/Bollinger 均值回归
已失败的日内时段
已失败的 BTC 下跌外溢
PA prose 的“源忠实”宣称
缠论独立买卖信号
```

缠论先作为：

```text
结构标签
Ranking
Veto
Exit invalidation
```

---

## 十三、V38 产品轨道

V37A Console 已有：

```text
execution-control.v1
只读状态投影
幂等动作外观
确定性 blocker code
中文 next action
对账摘要
行情摘要
Workflow Validation Demo fixture
```

但没有：

```text
真实 OKX Demo 下单
真实私有 WebSocket
真实订单/持仓对账
```

V38 应先完成功能，再扩充 UI。

### 功能

```text
精确 USDT SWAP 身份
认证 Demo Instruments 交集
x-simulated-trading: 1
时间同步
clOrdId 幂等
部分成交
重启恢复
WS + REST 对账
Kill Switch
Demo/Live 隔离
```

### UI

在现有 Console 技术栈中加入：

```text
Strategy Lab
Source Registry
Artifact Lifecycle
相似度和血缘
后台 Research 状态
Campaign 预算
Formal Gate Matrix
Release/Approval Hash
Data Freshness
订单、持仓、风险、对账
```

不迁移到 React，不复制 Vibe-Trading 前端。

---

## 十四、V39/V40

### V39

只有：

```text
Formal Pass / 合格 Research Pass
+
OKX Release Eligibility
```

才能：

```text
生成 Immutable Release
等待精确 Hash 批准
Demo ARM
真实策略 Demo
```

### V40

保持默认关闭。

只有：

```text
Demo 前向证据
成本和执行差异合格
无重复/孤儿
独立 Live Hash 批准
```

才允许小资金 Canary。

---

## 十五、最终决策

```text
现有 Formal Core：保留
现有 Research Service：保留
现有 V37A Console Core：保留
Vibe-Trading：选择性吸收
整体合并 Vibe：禁止
旧 TSMOM 原地修复：禁止
下一轮主要机制：Funding Carry + 源忠实策略获取
V38：可并行推进
V39/V40：继续条件阻塞
```

最重要的判断：

> Vibe-Trading 不能直接替 AlphaPilot 找出盈利策略，但它可以补齐“资料变成高质量、可追踪、可去重、安全可运行的候选”这一层；这正是当前系统最需要完善的位置。
