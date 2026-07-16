# AlphaPilot V13.27.1.13
# 存量数据减负、最小正式数据层与策略双级直测

## 给 Codex 的完整执行提示词（中文代号版）

> **本文件替代此前所有尚未执行的 V13.27.1.13 提示词。**
>
> V13.27.1.12 已完成全量复扫，确认当前主要问题不是“完全没有数据”，而是：
>
> ```text
> 约 24,385 个文件
> 约 47.17 GiB
> D 盘剩余约 17.29 GiB
>
> _alphapilot/canonical 约 15.63 GiB
> _alphapilot/manifests 约 5.15 GiB
>
> 同一数据同时存在：
> - 年度分片
> - ALL 文件
> - 多个滚动全量 Parquet
> - 多份大体积回测结果 JSON
>
> 原始 OHLCV 多数截止到 2026-05-03 至 2026-05-17
> Funding 主要覆盖约 2026-02 至 2026-05
>
> 缺少：
> - 完整历史 PIT 成分
> - 长历史 Open Interest
> - 长历史真实强平
> - 长历史订单簿 / 深度
> ```
>
> 本版本采用：
>
> ```text
> 最小正式数据层
> +
> 前向采集层
> +
> 两级策略筛选漏斗
> ```
>
> 不再全面补齐 Top100 / Top300 的所有周期，不再接入新的研究框架，不再等待所有因果数据历史完整后才允许 OHLCV 策略研究。
>
> 本版本必须产生：
>
> ```text
> 1. 可执行的存量数据减负计划；
> 2. 一个不复制数据的共享冻结 Snapshot；
> 3. 一个 20–40 币的核心研究池；
> 4. 至少一份、最多三份实际策略测试代码；
> 5. 快速筛选和正式验证结果；
> 6. 明确的通过、失败或数据阻塞结论。
> ```
>
> 最终允许：
>
> ```text
> Formal Pass = 0
> Release = 0
> ```
>
> 但不得再次只建设基础设施而不运行任何策略，除非连最小 OHLCV 研究池也无法建立。

---

# 1. 任务名称与版本

```text
AlphaPilot V13.27.1.13
存量数据减负、最小正式数据层与策略双级直测
```

中文简称：

```text
数据减负，直接测策略
```

建议 Quant Tag：

```text
v13.27.1.13
```

建议 Docs Tag：

```text
v13.27.1.13-docs
```

执行前必须：

```text
读取当前最新 Tag
确认版本未占用
不得覆盖现有 Tag
```

---

# 2. 版本只回答三个问题

本版本只回答：

```text
1. 哪些本地文件可以在不破坏证据链的前提下去重或归档？
2. 现有 OHLCV 是否足以建立 20–40 币的最小正式研究层？
3. 最多三个新策略假设中，是否至少有一个通过有限回测漏斗？
```

本版本不回答：

```text
是否接入 Qlib
是否扩充 Alpha191
是否购买所有历史衍生品数据
是否建设新 UI
是否进入 Demo
```

---

# 3. 权威输入

必须读取并验证 V13.27.1.12：

```text
Quant commit：cbd2b67
Quant tag：v13.27.1.12

Docs commit：9021c27
Docs tag：v13.27.1.12-docs
```

必须读取实际原件，包括但不限于：

```text
reports/v13_27_1_12/data_readiness.json
reports/v13_27_1_12/data_status_matrix.json
reports/v13_27_1_12/direction_readiness_matrix.json
reports/v13_27_1_12/campaign_start_decision.json
reports/v13_27_1_12/qlib_start_decision.json
reports/v13_27_1_12/pit_universe_audit.json
reports/v13_27_1_12/family_b_data_chain.json
reports/v13_27_1_12/snapshot_manifest.json
reports/v13_27_1_12/api_capability_audit.json
reports/v13_27_1_12/evidence_delivery/evidence_manifest.json
```

必须读取全量失败策略证据：

```text
reports/full_archived_strategy_analysis_summary.md
reports/full_archived_strategy_failure_attribution.json
reports/full_archived_strategy_cross_strategy_patterns.json
reports/full_archived_strategy_negative_rules.json
reports/full_archived_strategy_prohibited_routes.json
reports/candidate_evidence_closure_summary.md
```

必须读取：

```text
D:\Codex-Workspace\踩坑日志.txt
README.md
AGENTS.md
规矩文档
```

路径不一致：

```text
显式建立 logicalRole → actualPath 映射
不得静默跳过
```

---

# 4. 当前不可变研究结论

必须保留：

```text
旧归档策略大多数主要失败在信号边际，而不是单纯缺数据。
结构性 PF < 1 或平均净 R <= 0 的策略不得靠小幅调参复活。
过度交易、成本放大和时间状态不稳定是主要次级失败。
旧策略只作为负样本，不恢复为候选。
```

因此本版本：

```text
不重新实现旧 EMA / RSI / MACD / Bollinger / TD9 家族
不恢复 Volume Rebound
不恢复 Short Rejection
不恢复旧 Trend Pullback
不恢复旧对称 Breakout / Breakdown
```

如新假设与旧家族重合度过高：

```text
必须拒绝生成
```

---

# 5. 仓库与目录

主要修改：

```text
D:\Codex-Workspace\AlphaPilot-Quant-Engine
D:\Codex-Workspace\AlphaPilot-Docs
```

数据根：

```text
D:\Codex-Workspace\回测数据
```

只读：

```text
D:\Codex-Workspace\AlphaPilot-Control-Console
D:\Codex-Workspace\trade-discipline-journal
```

不得修改：

```text
Console Runtime
Demo Release
Approval
Order Engine
Mobile App
```

---

# 6. 工作区保护

Quant 当前存在用户原有未提交报告修改。

必须：

```text
记录 preExistingChanges
不 reset
不覆盖
不 stage
不 amend
```

建议使用：

```text
独立干净 worktree
```

---

# 7. 总安全边界

禁止：

```text
保存 API Key
接 Trade API
接 Withdraw API
读取真实账户
读取真实持仓
创建 Demo 订单
创建实盘订单
执行 exchange Dry-run
自动 ARM
自动交易
恢复完整本地模拟
```

允许：

```text
只读扫描本地数据
生成引用与去重清单
在显式批准后清理无引用重复文件
运行历史事件研究
运行 Freqtrade 回测
运行本地 Portfolio 回测
```

最终：

```text
demoApproved=false
liveTradingApproved=false
```

---

# 8. 复杂度预算

本版本硬限制：

```text
新增子系统：最多 2 个
策略顶层假设：最多 3 个
每个假设初始实现：1 个
结构修订：每个假设最多 1 次
完整 Freqtrade 回测：最多 8 次
Portfolio 回测：最多 3 次
不新增 UI
不修改 Console
```

新增子系统只允许：

```text
1. storage_governance
2. minimal_research_campaign
```

---

# 9. 第一部分：存量数据减负

## 9.1 目标

先建立引用图，再给出去重和回收计划。

默认：

```text
Dry Run
```

除非用户显式执行：

```text
-ApplyCleanup
```

否则不得删除、移动或替换任何文件。

---

## 9.2 建议新增

```text
alphapilot/storage_governance/
  reference_graph.py
  duplicate_classifier.py
  retention_policy.py
  cleanup_planner.py
  cleanup_executor.py

tests/storage_governance/
```

脚本：

```text
scripts/audit_and_plan_data_cleanup.ps1
scripts/apply_verified_data_cleanup.ps1
```

---

## 9.3 引用来源

必须扫描以下引用：

```text
Git tracked files
Git tags
Release Bundle
Formal Evidence
Artifact Manifest
Data Snapshot
Preregistration
回测报告
README / docs 中明确路径
旁车 Hash
测试 Fixture
```

输出：

```text
reports/storage_governance/reference_graph.json
reports/storage_governance/reference_graph_summary.md
```

每个文件：

```text
path
sha256
sizeBytes
referencedBy
referenceCount
immutableEvidence
safeToRemove
```

---

## 9.4 重复分类

必须区分：

```text
byte_identical
content_equivalent
annual_vs_all_representation
rolling_snapshot_superseded
overlapping_but_not_identical
conflicting
unique
```

禁止：

```text
只按文件名判断重复
只按大小判断重复
```

---

## 9.5 保留策略

必须保留：

```text
所有被 Release 引用的文件
所有被 Formal Evidence 引用的文件
所有被 Preregistration / Snapshot 引用的文件
每个数据身份的一份权威 Raw 表示
每个数据身份的一份权威 Canonical 表示
所有冲突文件
所有来源链不明确文件
```

可以进入清理候选：

```text
未被任何证据引用
内容完全重复
已被新冻结 Snapshot 替代
旧滚动全量快照
ALL 与完整年度分片之间的表示层重复
无引用的大体积派生结果
```

---

## 9.6 Raw 表示选择

对于：

```text
年度分片
ALL 文件
```

不得一刀切。

选择规则：

```text
如果年度分片完整、连续且适合增量更新：
  年度分片为权威 Raw

如果年度分片不完整、ALL 更完整且有来源链：
  ALL 为权威 Raw

如果两者内容冲突：
  均保留，标记 conflicting
```

---

## 9.7 Canonical 保留

相同：

```text
交易所
市场类型
币种
周期
时间范围
来源
内容
```

只保留一份权威 Canonical。

滚动快照不得每次全量复制。

后续 Snapshot 必须采用：

```text
Manifest + 文件引用 + Hash
```

不得再次复制十几 GiB 数据。

---

## 9.8 大结果文件政策

历史已被引用的大 JSON：

```text
保持原样
不得原地转换
```

未来新结果：

```text
摘要 JSON
+
明细 Parquet（固定 PyArrow 时）
或
压缩 CSV
+
SHA-256
```

禁止未来继续生成多 GiB 的单一 JSON。

---

## 9.9 清理产物

必须生成：

```text
reports/storage_governance/storage_inventory.json
reports/storage_governance/duplicate_groups.json
reports/storage_governance/cleanup_dry_run.json
reports/storage_governance/cleanup_dry_run.md
reports/storage_governance/reclaim_estimate.json
reports/storage_governance/retention_manifest.json
```

如执行 `-ApplyCleanup`，额外生成：

```text
reports/storage_governance/cleanup_apply_manifest.json
reports/storage_governance/cleanup_integrity_check.json
```

---

## 9.10 实际清理硬规则

只有同时满足才可删除：

```text
referenceCount = 0
immutableEvidence = false
duplicateClass 为可安全清理类别
权威副本存在
权威副本 Hash 验证通过
用户显式使用 -ApplyCleanup
```

删除后必须：

```text
重新验证所有 Snapshot
重新验证所有 Manifest
运行全部相关测试
```

---

# 10. 第二部分：最小正式数据层

## 10.1 三级数据层

### Tier 1：核心正式研究层

目标：

```text
20–40 个成熟、高流动性 USDT 永续合约
```

主周期：

```text
1h
4h
1d
```

15m：

```text
只在候选明确需要更细触发时启用
```

5m：

```text
只为微观执行候选启用
币种最多 10–20 个
```

### Tier 2：诊断辅助层

```text
短历史 Funding
现货 / 合约价格差
代理 Spread
固定币池宽度
```

只能：

```text
解释
确认
否决
```

不能单独使 Formal Pass 成立。

### Tier 3：前向采集层

从现在持续采集：

```text
Open Interest
真实强平
订单簿 / Spread
合约状态
PIT 流动性
Funding
Basis
```

只做：

```text
append-only
```

不得伪造历史。

---

# 11. 核心池构建

## 11.1 核心池选择不得看策略收益

选择只能使用：

```text
数据来源可追溯
有效历史长度
上市年龄
OHLCV 完整度
成交活跃度
价差代理
合约状态
符号映射稳定性
```

禁止使用：

```text
回测收益
Profit Factor
胜率
策略排名
```

---

## 11.2 成熟度

建议最低：

```text
有效历史 >= 24 个月
```

优先：

```text
>= 36 个月
```

从：

```text
effectiveBacktestStart
```

计算，不使用第一行占位 K 线。

---

## 11.3 未确认与占位数据

必须排除：

```text
confirmed=false
前导零成交量
前导完全平坦占位 K 线
已知暂停 / 不可交易区间
```

---

## 11.4 截止日期

每个周期冻结统一截止日期：

```text
commonCutoff =
所选核心池内最后确认 K 线时间的最小值
```

不得为了补齐最后几周或两个月继续大规模下载。

报告：

```text
latestAvailableByInstrument
commonCutoffByTimeframe
excludedTailRows
```

---

## 11.5 核心池输出

```text
reports/minimal_data_layer/core_universe.json
reports/minimal_data_layer/core_universe.csv
reports/minimal_data_layer/core_universe_selection.md
```

字段：

```text
instrumentId
exchange
marketType
effectiveBacktestStart
commonCutoff
historyMonths
coveragePct
missingRatePct
liquidityScore
included
reasonZh
```

---

# 12. 固定池与 PIT 的边界

没有完整历史 PIT 时，必须区分：

```text
fixed_core_cohort
historical_pit_universe
```

规则：

```text
单币时间序列事件策略：
可以在固定核心池上进入正式研究，
但报告必须说明固定池选择限制。

横截面排名策略：
没有历史 PIT 时只能 diagnostic，
不能生成 Release。
```

不得把：

```text
当前 20–40 个币回填历史
```

称为 PIT。

---

# 13. 共享 Snapshot

必须创建：

```text
Manifest-only Snapshot
```

不能复制数据。

生成：

```text
research/data_snapshots/<snapshotId>.json
```

记录：

```text
snapshotId
coreUniverseHash
datasetReferences
fileHashes
commonCutoffByTimeframe
effectiveStarts
createdAt
GitCommit
```

所有策略共享同一 Snapshot。

禁止：

```text
每条策略复制一套数据
每次回测重新下载
每次回测生成全量 Canonical 副本
```

---

# 14. 前向采集层

## 14.1 不阻塞当前 OHLCV 研究

前向采集与策略测试并行但证据隔离。

当前 OHLCV 策略：

```text
不因缺 OI / 强平 / 订单簿而停止
```

只有依赖这些字段的策略才受阻。

---

## 14.2 最小采集器

复用现有 Collector；如缺失，只新增一个统一入口。

采集：

```text
OI
Funding
Basis
Liquidation
Spread
Instrument State
PIT Liquidity
```

输出：

```text
append-only 原始文件
每日 Manifest
最新 Checkpoint
```

不新增 UI。

---

## 14.3 采集状态

```text
reports/forward_collection/collection_plan.json
reports/forward_collection/collection_status.json
```

明确：

```text
forwardDataCannotBackfillHistory=true
```

---

# 15. 策略双级漏斗

```text
一级：快速结构筛选
→
二级：正式验证
```

---

# 16. 一级快速结构筛选

## 16.1 代表币种

从核心池预先选择：

```text
8–12 个
```

必须使用确定性规则：

```text
BTC / ETH
+
上市年龄分层
+
流动性分层
+
波动率分层
+
稳定 Hash
```

禁止根据策略结果选币。

输出：

```text
reports/minimal_strategy_campaign/representative_universe.json
```

---

## 16.2 时间状态覆盖

快速筛选必须覆盖：

```text
牛市
熊市
震荡
高波动
低波动
```

状态只用于分层报告，不事后删除失败状态。

---

## 16.3 一级门槛

事件策略：

```text
基础成本 PF >= 1.03
平均净 R > 0
总净 R > 0
正月份 >= 50%
最低事件数通过
```

组合策略：

```text
成本后净收益 > 0
正月份 >= 50%
最大回撤 <= 35%
```

失败：

```text
立即归档
不进入正式验证
```

---

# 17. 二级正式验证

只允许一级幸存者进入。

使用：

```text
20–40 币核心池
55% Development
25% Purged Walk-forward
20% Campaign Locked OOS
5 Fold
Embargo
基础 / 1.5x / 2x 成本
```

Campaign Locked OOS：

```text
本 Campaign 中只能解封一次
```

如果历史区间已被旧策略看过：

```text
必须标记 globalCleanHoldout=false
campaignLockedOos=true
```

不得夸大为全局未见样本。

---

# 18. 策略假设选择

最多生成三个策略。

必须先做：

```text
archived-family novelty audit
```

若与旧失败家族高度重叠：

```text
拒绝生成
```

---

## 18.1 策略一：特异性抛售恢复做多

内部 ID：

```text
core_idiosyncratic_selloff_recovery_long_4h
```

建议类名：

```text
AlphaPilotCoreIdiosyncraticSelloffRecoveryLong4H
```

核心机制：

```text
某币相对 BTC / 核心池市场组合出现极端负残差
+
价格重新收回事件区间
+
市场整体没有同步崩溃
```

方向：

```text
只做多
```

周期：

```text
4h 环境与触发
```

不能要求：

```text
历史 OI
真实清算
```

这些只能作为未来辅助。

---

## 18.2 策略二：宽度状态切换后的领导币延续

内部 ID：

```text
core_breadth_transition_leader_continuation_4h
```

建议类名：

```text
AlphaPilotCoreBreadthTransitionLeaderContinuation4H
```

核心机制：

```text
固定核心池市场宽度从收缩转为扩张
+
领导币相对强势保持
+
4h 浅回调后重新收回
```

这不是：

```text
普通 EMA 趋势
普通突破
普通指标堆叠
```

方向：

```text
多头与空头可以分别定义
但属于同一个策略家族
并共享试验预算
```

如与历史 Trend Pullback / Breakout 重合度过高：

```text
不生成该策略
```

---

## 18.3 策略三：固定核心池横截面中期动量

内部 ID：

```text
diagnostic_fixed_core_cross_sectional_momentum_1d
```

模块：

```text
alphapilot/portfolio_strategies/
diagnostic_fixed_core_cross_sectional_momentum.py
```

核心机制：

```text
在固定核心池中进行中期相对强弱排名
```

组合：

```text
多 Top 组
空 Bottom 组
或 BTC / ETH Beta 对冲
固定周期再平衡
```

没有历史 PIT 时：

```text
formalPassEligible=false
releaseEligible=false
diagnosticOnly=true
```

---

# 19. 策略数量路由

必须生成：

```text
至少 1 个
最多 3 个
```

只要：

```text
核心池 >= 8
且
4h 或 1d 数据覆盖满足最低门槛
```

就不得以 `data_not_ready` 为由跳过所有策略。

如果策略二因新颖性失败：

```text
只运行策略一和策略三
```

不得新增第四个替代策略。

---

# 20. 预注册

一级筛选前生成：

```text
research/preregistrations/<campaignId>_prefilter.json
```

正式验证前生成：

```text
research/preregistrations/<campaignId>_formal.json
```

正式预注册只能包含：

```text
一级幸存者
```

必须在结果前：

```text
Commit
Push
```

---

# 21. 结构修订预算

一级失败：

```text
不修订
直接归档
```

一级通过但暴露明确实现缺陷：

```text
每个家族最多允许 1 次结构修订
```

结构修订必须：

```text
创建新版本 ID
说明修改原因
重新预注册
```

禁止：

```text
连续微调阈值
多次改止损
结果导向删币
```

---

# 22. 时间因果

所有策略：

```text
收线后生成信号
最早下一根 K 线成交
```

基础指标：

```text
只使用已完成 K 线
```

固定核心池：

```text
在 Campaign 前冻结
```

不得使用未来成交量、未来币池或未来市场状态。

---

# 23. 退出与风险

方向事件策略：

```text
初始止损在入场前冻结
入场后绝不放宽

+1R 可部分止盈
剩余仓位初始目标 >= 2R

允许：
机制失效
残差回归完成
时间到期
提前退出
```

止损扩大：

```text
仓位同比缩小
风险 R 不变
```

横截面组合：

```text
不使用单币固定 R 止盈
```

使用：

```text
排名跌出
定期再平衡
Beta
组合风险
流动性
```

---

# 24. 成本

必须报告：

```text
base
1.5x
2.0x
```

至少计入：

```text
手续费
Funding（有真实覆盖时）
Spread
滑点
```

缺真实 Funding：

```text
保持 unavailable
不得填 0
```

可额外报告：

```text
excludingFunding
conservativeFundingProxy
```

但必须分开。

---

# 25. 资本竞争

正式验证必须模拟：

```text
最大并发持仓
总开放风险
单币风险
同方向风险
相关簇风险
BTC Beta
多信号确定性排序
流动性容量
```

禁止把所有信号独立相加。

---

# 26. 基准

策略一：

```text
无条件超跌反弹
不剥离 Beta 的下跌反弹
BTC 同期反弹
```

策略二：

```text
简单 1d/4h 趋势
单独市场宽度
单独相对强弱
```

策略三：

```text
NoTrade
固定核心池等权
BTC
ETH
普通 20 日动量
```

复杂策略必须超过最强可比简单基准。

---

# 27. 一级输出

```text
reports/minimal_strategy_campaign/<campaignId>/prefilter/
  strategy_inventory.json
  novelty_audit.json
  representative_universe.json
  event_prefilter.json
  portfolio_prefilter.json
  simple_benchmarks.json
  failure_attribution.json
  prefilter_summary.md
```

---

# 28. 正式输出

```text
reports/minimal_strategy_campaign/<campaignId>/formal/
  shared_snapshot_reference.json
  strategy_definitions.json
  freqtrade_results.json
  portfolio_results.json
  translation_parity.json
  walk_forward.json
  campaign_locked_oos.json
  cost_stress.json
  capital_competition.json
  gate_matrix.json
  failure_attribution.json
  campaign_summary.json
  campaign_summary.md
```

正式通过者才生成：

```text
formal_pass_evidence/*.json
```

本版本不生成：

```text
Demo Release
Approval
Order
```

---

# 29. Gate

## 29.1 事件策略 Basic Pass

```text
Walk-forward PF >= 1.05
平均净 R > 0
总净 R > 0
至少 3/5 Fold 为正
最大回撤 <= 25%
基础成本通过
```

## 29.2 事件策略 Formal Pass

```text
Walk-forward PF >= 1.15
平均净 R >= 0.05
至少 4/5 Fold 为正
最大回撤 <= 20%

Campaign Locked OOS：
PF > 1
平均净 R > 0
总净 R > 0

1.5x 成本 PF >= 1.05
1.5x 平均净 R > 0

单币贡献 <= 35%
单月份贡献 <= 35%
明显超过简单基准
```

## 29.3 组合 Diagnostic Gate

```text
Walk-forward 净收益 > 0
至少 3/5 Fold 为正
1.5x 成本后不为负
相对普通 20 日动量有正增量
最大回撤 <= 25%
Beta / Turnover / Capacity 可计算
```

无 PIT：

```text
不得 Formal Pass
```

---

# 30. 5m、15m 使用政策

本版本主线：

```text
1h
4h
1d
```

15m：

```text
只有一级幸存策略明确需要更精细触发时，
才能在正式预注册中启用。
```

5m：

```text
本版本不启用。
```

后续只有：

```text
已通过 1h/4h 结构验证
且
明确需要微观执行优化
```

时才允许使用，币种最多 10–20 个。

---

# 31. 测试要求

至少覆盖：

```text
Reference Graph
被 Release 引用文件绝不清理
被 Snapshot 引用文件绝不清理
Dry Run 默认不删文件
ApplyCleanup 需要显式参数
清理后 Hash 与 Manifest 验证

共享 Snapshot 不复制数据
策略不能自行下载
策略不能自行复制 Canonical

核心池选择不使用收益
有效历史起点
共同截止日期
unconfirmed 排除
占位 K 线排除

Fixed Core != PIT
横截面无 PIT 不得 Formal

一级代表币种确定性选择
最多三个策略
每个策略一个初始实现
新颖性审计
不增加第四策略

下一根 K 线成交
Stop 不放宽
成本不能为 0
资金竞争
Translation Parity

一级失败不进入正式
Campaign Locked OOS 一次解封
Formal Pass 不自动生成 Release
Demo ARM=false
Order=0
```

---

# 32. 验证命令

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine

python -m pytest tests/storage_governance -q
python -m pytest tests/minimal_research_campaign -q
python -m pytest tests -q

python -m compileall alphapilot
python -m alphapilot.scripts.validate_config

powershell -ExecutionPolicy Bypass `
  -File scripts\check_safety.ps1

git diff --check
```

存量治理 Dry Run：

```powershell
powershell -ExecutionPolicy Bypass `
  -File scripts\audit_and_plan_data_cleanup.ps1 `
  -DataRoot "D:\Codex-Workspace\回测数据" `
  -Run
```

核心池和 Snapshot：

```powershell
powershell -ExecutionPolicy Bypass `
  -File scripts\build_minimal_formal_data_layer.ps1 `
  -DataRoot "D:\Codex-Workspace\回测数据" `
  -Run
```

策略一级筛选：

```powershell
powershell -ExecutionPolicy Bypass `
  -File scripts\run_minimal_strategy_campaign.ps1 `
  -Stage Prefilter `
  -Run
```

正式验证：

```powershell
powershell -ExecutionPolicy Bypass `
  -File scripts\run_minimal_strategy_campaign.ps1 `
  -Stage Formal `
  -Run
```

实际清理仅在用户明确批准后：

```powershell
powershell -ExecutionPolicy Bypass `
  -File scripts\apply_verified_data_cleanup.ps1 `
  -ApplyCleanup
```

---

# 33. 提交顺序

至少六个提交：

```text
1. Add reference-aware storage governance and cleanup dry-run

2. Build shared minimal core data layer without physical copies

3. Freeze bounded strategy prefilter hypotheses

4. Record representative-universe prefilter results

5. Freeze formal survivor campaign

6. Record formal walk-forward and locked-OOS decisions
```

如用户批准实际清理：

```text
单独提交 cleanup manifest
```

不得将清理动作与策略代码混在同一个提交。

---

# 34. Codex 最终自检

必须输出：

```text
1. 实际版本
2. Quant commit / tag
3. Docs commit / tag
4. preExistingChanges

5. 数据文件总数
6. 数据总大小
7. 执行前剩余磁盘
8. 执行后剩余磁盘

9. Reference Graph 文件数
10. 被引用文件数
11. Immutable Evidence 文件数

12. Byte Duplicate 组数
13. Content Equivalent 组数
14. Rolling Snapshot 组数
15. Annual vs ALL 组数
16. Conflict 组数

17. 预计可回收空间
18. 实际回收空间
19. 是否执行 ApplyCleanup
20. 删除文件数
21. 清理后 Hash 失配

22. 核心池币种数
23. 1h 覆盖
24. 4h 覆盖
25. 1d 覆盖
26. Common Cutoff
27. Effective Start 分布

28. Shared Snapshot ID
29. Shared Snapshot Hash
30. 是否物理复制数据

31. 前向采集 OI 状态
32. Funding 状态
33. Basis 状态
34. Liquidation 状态
35. Spread 状态
36. PIT 状态

37. 一级代表币数
38. 策略假设数
39. 策略代码数
40. 新颖性拒绝数

41. 策略一一级结果
42. 策略二一级结果
43. 策略三一级结果
44. 一级幸存数

45. Formal Freqtrade 回测数
46. Formal Portfolio 回测数
47. Walk-forward
48. Campaign Locked OOS 访问次数
49. 基础成本
50. 1.5x 成本
51. 2x 成本
52. Capital Competition
53. Translation Parity

54. Basic Pass 数
55. Formal Pass 数
56. Diagnostic Promising 数
57. Formal Evidence 数
58. Release 数
59. Demo ARM
60. Order 数

61. 是否全面补齐 Top100 / Top300
62. 是否运行 5m
63. 是否每条策略复制数据
64. 是否每次回测下载数据
65. 是否使用当前 TopN 冒充 PIT
66. 是否事后删币
67. 是否 Hyperopt
68. 是否增加第四策略
69. 是否放宽止损
70. 是否访问 API Key
71. 是否接 Trade API
72. 是否接 Withdraw API
73. 是否读取账户
74. 是否创建订单

75. pytest
76. compileall
77. validate_config
78. safety scan
79. git diff --check
80. Push
81. Tag
82. 已知问题
83. 下一步建议
```

---

# 35. 停止规则

## 磁盘不足

如果扫描或结果运行前：

```text
freeDisk < 12 GiB
```

则：

```text
停止生成新的大结果文件
先输出 Cleanup Dry Run
等待用户批准清理
```

---

## 核心池不足

如果：

```text
4h 有效覆盖币种 < 8
```

则：

```text
data_blocked
停止策略代码生成
```

如果：

```text
8 <= 币种 < 20
```

则：

```text
只运行一级 Diagnostic
不运行 20–40 币正式验证
```

---

## 一级全部失败

```text
停止
不进入正式验证
不增加第四策略
不接 Qlib
```

---

## 正式全部失败

```text
归档
不降低门槛
不继续小调
```

---

## 有 Formal Pass

```text
只生成 Formal Evidence
等待独立 Release 任务
```

不进入 Demo。

---

# 36. 下一版本路由

## 有 Formal Pass

下一版：

```text
不可变 Release 生成与 OKX Demo 人工准入
```

## 只有 Basic Pass

下一版：

```text
一次有限结构修订或新前向数据验证
```

## 只有 Diagnostic Promising

下一版：

```text
只补该机制真正需要的正式因果数据
```

## 全部失败

```text
暂停策略扩展
继续 Tier 3 前向采集
不增加平台复杂度
```

---

# 37. 最终原则

请严格执行：

```text
先减负
再冻结一份共享数据
再直接测试少量新假设
```

必须坚持：

```text
不再全面补齐所有币种和周期
不再每条策略复制数据
不再让 Funding / OI / 强平历史缺口阻塞所有 OHLCV 研究
不再用缺数据解释结构性负策略
不再无限调参救失败策略

数据不够时缩小研究范围，
不是扩大平台。

一级筛选快速淘汰，
正式验证只给幸存者。

最终 0 个通过仍然是有效结论，
但本版本必须真正运行策略代码并得到结果。
```
