# AlphaPilot V13.27.1.11 修订版 V2
# 研究工厂方法修复、同交易所衍生品数据扩展、分类型 Gate 与三方向新假设 Campaign

## 给 Codex 的完整执行提示词（中文代号版）

> **本文件替代上一版 V13.27.1.11 提示词。**
>
> 本版本以已经完成的第三、第四阶段证据为起点，但必须先更新真实 Git 状态、建立输入文件显式映射、修复研究工厂的方法学问题，再下载和冻结同交易所数据，最后只研究三个既定方向。
>
> 本版本接受最终正式通过数量为 0。
>
> 禁止：
>
> ```text
> 为了产生策略而降低门槛
> 增加第四个策略方向
> 在 Holdout 失败后继续修改并复测同一 Holdout
> 把代理数据包装成正式证据
> 把事件回放包装成 Freqtrade 回测
> 把 A、B 当成完全独立的市场家族
> 把横截面组合机械套入单笔 R Gate
> ```

---

# 1. 任务名称

```text
AlphaPilot V13.27.1.11
研究工厂方法修复、同交易所衍生品数据扩展、
分类型 Gate 与三方向新假设 Campaign
```

中文简称：

```text
修方法、补数据、分 Gate、测三方向
```

建议 Quant Tag：

```text
v13.27.1.11
```

建议 Docs Tag：

```text
v13.27.1.11-docs
```

如 Tag 已占用：

```text
使用下一个未占用版本
不得覆盖
```

---

# 2. 当前真实 Git 基线

以下状态是本任务开始前的已知基线，但 Codex 必须实际复核：

## Console

```text
本地 main 已合并到：1f4b865
尚未推送远端 main
尚未创建新 Tag
```

要求：

```text
1. fetch 远端。
2. 确认本地 main 相对 origin/main 可安全 fast-forward 推送。
3. 不 amend 1f4b865。
4. 运行 Console 全量验收。
5. 推送 main。
6. 使用 Console 自己版本线的下一个未占用里程碑 Tag。
```

如远端已前进且不能 fast-forward：

```text
停止
输出 console_remote_diverged
不要强推
```

## Quant

```text
Phase 3 实现仍在：
feature/revised-demo-backtest-first

尚未合入 main。

Quant main 存在任务开始前已有的未提交报告改动。
```

要求：

```text
1. 不在用户当前 dirty main 上直接合并。
2. 不 reset、stash、覆盖或提交既有报告改动。
3. 使用独立干净 worktree：
   - 从 origin/main 创建
   - 合并 feature/revised-demo-backtest-first
   - 运行验收
   - 推送 main
4. 用户原 Quant main 工作区保持原样。
```

必须保护的既有改动包括但不限于：

```text
reports/archived_failed_strategy_failure_attribution_summary.md
```

## Docs

```text
当前仍停留在：
v13.27.1.9-docs
```

要求：

```text
1. 读取当前 main 和远端状态。
2. 归档第三、第四阶段最终文档与本提示词。
3. 提交、推送。
4. 使用下一个未占用 Docs Tag。
```

---

# 3. 任务 0：先完成旧阶段收口

在新研究代码开始前必须生成：

```text
reports/v13_27_1_11_baseline_closeout_manifest.json
reports/v13_27_1_11_baseline_closeout_summary.md
```

字段：

```text
consoleLocalMain
consoleOriginMainBefore
consoleOriginMainAfter
consolePushed
consoleTag

quantFeatureBranch
quantFeatureHead
quantMainBefore
quantMainAfter
quantMerged
quantOriginalDirtyWorktreePreserved
quantTag

docsHeadBefore
docsHeadAfter
docsTag

preExistingChanges
acceptanceCommands
acceptanceResults
```

如果 Console、Quant Phase 3、Docs 任一无法安全收口：

```text
status = baseline_closeout_blocked
停止后续任务
```

---

# 4. 输入原件显式映射

上一版提示词中的逻辑文件名与实际原件名称可能不同。

禁止：

```text
因为名称不一致而静默跳过
使用空对象代替
使用其他文件猜测替代
```

必须新增：

```text
alphapilot/research_factory_repair/input_artifact_mapping.py
reports/research_factory_repair/input_artifact_mapping.json
reports/research_factory_repair/input_artifact_mapping.md
tests/research_factory_repair/test_input_artifact_mapping.py
```

---

## 4.1 逻辑角色

至少映射以下角色：

```text
phase3CampaignSummary
phase3GateMatrix
phase3FailureAttribution
phase3ExperimentBudget
phase3ArtifactManifest
phase3CandidateResults
phase3Preregistration
phase3DataSnapshot
phase3FactorShortlist
phase3DataAudit
phase3DataManifest
phase3FamilyEligibility
phase3FactorBenchmark
phase3FactorTrialLedger
phase3FactorClusters
phase3FactorMechanismMatrix
phase4AcceptanceStatus
phase4ReleaseStatus
phase4DemoStatus
phase3Phase4EvidenceBundle
phase3Phase4AnalysisReport
```

---

## 4.2 每个映射记录

```text
logicalRole
actualPath
exists
contentHash
fileType
schemaFingerprint
selectedBy
selectionReason
ambiguousMatches
```

选择优先级：

```text
1. 用户明确给出的实际路径
2. Artifact Manifest 中登记的路径
3. Schema 指纹唯一匹配
4. 精确文件名候选
```

不允许只凭模糊文件名选择。

任一必需角色：

```text
缺失
或
存在多个无法消歧的候选
```

则：

```text
status = input_mapping_blocked
停止任务
```

---

# 5. 当前研究结论基线

必须保留以下不可变结论：

```text
上一轮正式通过：0
上一轮 Release：0
第四阶段 Runtime ARM：false
策略验证订单：0
Live：false
Withdraw：false
```

上一轮只实际测试：

```text
8 个币
8 个 Alpha191 种子因子
3 个市场机制
6 个方向变体
固定 2R / 2.2R 型退出
```

上一轮未完整测试：

```text
同交易所 Open Interest
同交易所 Basis
真实清算
正式历史 PIT 币池
真实 Freqtrade Translation Parity
真实资金竞争组合
```

两个研究线索：

```text
A：特异性下跌冲击后的做多恢复
B：极端负 Funding 后的做多反转
```

第三个方向：

```text
C：PIT 高流动性横截面中期动量
```

不得增加第四方向。

---

# 6. 三个方向的层级关系

A、B 不是完全独立的顶层家族。

必须建立：

```text
顶层家族 1：
stress_reversal
压力释放与拥挤解除反转

子方向 A：
idiosyncratic_selloff_exhaustion_long
特异性抛售耗竭做多

子方向 B：
short_crowding_unwind_long
空头拥挤解除做多

顶层家族 2：
cross_sectional_momentum
横截面中期动量

方向 C：
pit_liquid_cross_sectional_momentum
PIT 高流动性横截面中期动量
```

最终统计：

```text
topLevelFamilyCount = 2
directionCount = 3
```

---

## 6.1 层级多重检验

A、B 必须使用层级检验：

```text
先检验 stress_reversal 顶层家族
再检验 A、B 子方向
再检验各自变体
```

必须记录：

```text
topLevelHypothesisId
subfamilyHypothesisId
variantId
rawPValue
familyAdjustedPValue
subfamilyAdjustedPValue
globalFdrAdjustedPValue
```

不得把 A、B 当成两份完全独立发现。

---

## 6.2 A/B 重叠与相关性

必须输出：

```text
sameSymbolEventOverlap
timestampWindowOverlap
eventJaccard
closedTradeOverlap
monthlyReturnCorrelation
eventReturnCorrelation
drawdownOverlap
```

事件重叠定义必须预注册，例如：

```text
同一 symbol
且触发时间在一个触发周期以内
```

默认重复警戒线：

```text
eventJaccard >= 0.50
或
abs(eventReturnCorrelation) >= 0.80
```

命中后：

```text
1. 标记 correlated_subfamilies。
2. 多重检验仍按两次子方向试验计数。
3. 组合风险中视为同一个 stress_reversal 风险簇。
4. 如果两者同时满足正式 Gate，最多选择一个进入正式 Evidence；
   选择规则必须在 Development 前预注册。
```

---

# 7. 总安全边界

本任务禁止：

```text
保存真实 API Key
新增 API Key 输入
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
修改 AlphaPilot Mobile App
```

允许：

```text
公开历史数据下载
Research-only 事件研究
Freqtrade 历史回测
横截面 Portfolio 回测
Formal Evidence 生成
```

无论结果：

```text
executionEligible=false
demoApproved=false
liveTradingApproved=false
```

---

# 8. 推荐执行顺序

必须严格按照：

```text
1. 真实 Git 收口
2. 基线文件显式映射
3. Gate、时间因果和执行一致性测试
4. 数据接口能力审计
5. 断点续传、去重、缺口补齐
6. 冻结同交易所 Snapshot
7. 预注册三个方向及共同上层家族
8. 事件预筛与简单基准比较
9. 真实 Freqtrade / Portfolio 回测
10. Purged Walk-forward
11. 一次性 Clean Holdout 解封
12. 分类型 Formal Gate
13. 只生成 formal_pass_evidence
```

不得改变顺序。

---

# 9. 任务 1：严格时间因果框架

建议新增：

```text
alphapilot/research_screening/data_availability.py
alphapilot/research_screening/causal_clock.py
alphapilot/research_screening/causal_asof_join.py
alphapilot/research_screening/split_leakage_guard.py
tests/research_screening/test_data_availability.py
tests/research_screening/test_causal_clock.py
tests/research_screening/test_split_leakage_guard.py
```

---

## 9.1 每条数据必须拥有

```text
eventTimestamp
observedAt
publishedAt
availableAt
sourceTimestamp
ageSeconds
publicationLagSeconds
```

信号只能使用：

```text
availableAt <= signalDecisionTime
```

的数据。

---

## 9.2 Funding 因果规则

必须区分：

```text
fundingRateForNextPeriod
settledFundingRate
predictedFundingRate
```

规则：

```text
1. 已结算 Funding 只能在结算完成后使用。
2. 预测 Funding 必须证明当时可公开获得。
3. 不能用最终结算值回填到结算前信号。
4. Funding availability lag 必须写入 Manifest。
```

---

## 9.3 Open Interest 因果规则

```text
1. 使用 API 返回时间或数据发布时点。
2. 如果真实发布延迟不可证明，至少滞后一根数据周期。
3. 禁止用 K 线收盘后的 OI 修正同一收盘信号。
```

---

## 9.4 PIT 流动性因果规则

```text
1. 使用当时已完成的 PIT 快照。
2. 当前 K 线未完成的成交额不能进入筛选。
3. 上市、下架、暂停状态按实际可得时间生效。
```

---

## 9.5 信号与成交时点

统一规则：

```text
收线后产生信号。
最早在下一根 K 线成交。
```

严禁：

```text
使用当前收盘价生成信号
并假设同一收盘价成交
```

入场价：

```text
下一根 K 线开盘
+ 预注册滑点 / Spread
```

---

## 9.6 Purge 与 Embargo

必须：

```text
1. 删除任何持有期与相邻 Fold 标签期重叠的事件。
2. Purge 长度至少覆盖最大持有期。
3. Embargo 长度在预注册中冻结。
4. 同一事件的持有区间不得跨 Development、Fold、Holdout 边界。
5. 跨边界事件直接丢弃，不截断后保留。
```

---

# 10. 任务 2：Gate 语义修复

建议新增 / 修改：

```text
alphapilot/research_screening/gate_schema.py
alphapilot/research_screening/event_strategy_gates.py
alphapilot/research_screening/portfolio_strategy_gates.py
alphapilot/research_screening/formal_data_gate.py
tests/research_screening/test_event_strategy_gates.py
tests/research_screening/test_portfolio_strategy_gates.py
tests/research_screening/test_formal_data_gate.py
```

禁止继续使用含义不清的：

```text
basePassed
formalPassed
oosMetrics
fullBacktestExecuted
```

改为：

```text
developmentPrefilterPassed
walkForwardNumericPassed
simpleBenchmarkPassed
uncertaintyPassed
capitalCompetitionPassed
implementationParityPassed
formalDataProvenancePassed
cleanHoldoutPassed
overallBasicPassed
overallFormalPassed
```

执行引擎状态：

```text
eventReplayExecuted
freqtradeBacktestExecuted
portfolioBacktestExecuted
translationParityPassed
```

---

# 11. A/B 使用事件策略 Gate

A、B 是事件型方向策略。

不能使用 C 的组合 Gate。

---

## 11.1 A/B Development 预筛

```text
基础成本 PF >= 1.08
平均净 R >= 0.03
总净 R > 0
正月份比例 >= 60%
最低事件数通过
MFE / MAE 路径完整
简单基准比较完整
```

---

## 11.2 A/B Walk-forward 基础 Gate

```text
Walk-forward PF >= 1.05
Walk-forward 平均净 R > 0
Walk-forward 总净 R > 0
最大回撤 <= 25%
至少 3/5 Fold 平均净 R > 0
基础成本已计入
```

---

## 11.3 A/B Formal Gate

必须同时满足：

```text
Walk-forward PF >= 1.15
Walk-forward 平均净 R >= 0.05
Walk-forward 总净 R > 0
至少 4/5 Fold 平均净 R > 0
最大回撤 <= 20%

Clean Holdout PF > 1.00
Clean Holdout 平均净 R > 0
Clean Holdout 总净 R > 0
Clean Holdout 最低事件数通过

1.5 倍成本 PF >= 1.05
1.5 倍成本平均净 R > 0

90% Cluster Bootstrap：
PF 下界 > 1.00
平均净 R 下界 > 0

明显超过最强简单基准
资金竞争模型通过
正式数据来源通过
Translation Parity 通过
```

最低 Holdout 事件数：

```text
1h：>=100
4h：>=50
1d：>=30
```

---

## 11.4 A/B 必报路径指标

```text
MFE
MAE
MFE / MAE
hit0_5R
hit1R
hit1_5R
hit2R
timeTo0_5R
timeTo1R
timeTo1_5R
timeTo2R
stopThenRecover0_5R
stopThenRecover1R
stopThenRecover1_5R
positiveThenStop
maximumFavorableAfterStopR
```

这些指标用于诊断，不允许在 Holdout 后修改退出。

---

# 12. C 使用组合策略 Gate

C 是横截面 Portfolio 策略。

禁止机械套用：

```text
单笔 PF
单笔平均 R
单币固定止盈目标
```

可以报告 PF，但不能作为唯一正式 Gate。

---

## 12.1 C Development 预筛

```text
基础成本后组合净收益 > 0
正月份比例 >= 60%
最大回撤 <= 25%
换手不超过预注册上限
Beta 和净暴露在变体目标内
容量模型可运行
简单 20 日动量基准已完成
```

---

## 12.2 C Walk-forward 基础 Gate

```text
Walk-forward 组合净收益 > 0
至少 3/5 Fold 净收益 > 0
最大回撤 <= 25%
1.0 倍成本后净收益 > 0
正再平衡周期比例达到预注册门槛
Beta Gate 通过
换手 Gate 通过
容量 Gate 通过
```

---

## 12.3 C Formal Gate

```text
Walk-forward 净收益 > 0
至少 4/5 Fold 净收益 > 0
最大回撤 <= 20%

Clean Holdout 净收益 > 0
Clean Holdout 相对可比基准超额收益 > 0
Clean Holdout 正再平衡周期比例达到门槛

1.5 倍成本后净收益 > 0

90% Cluster Bootstrap：
相对简单 20 日动量的超额收益下界 > 0

Beta Gate 通过
换手 Gate 通过
容量 Gate 通过
单一币种贡献不超过预注册上限
单一月份贡献不超过 35%
PIT 正式数据 Gate 通过
```

---

## 12.4 C 变体 Beta 目标

C1：

```text
多强势组 + 空弱势组
目标：
abs(BTC Beta) <= 0.20
净市场暴露 <= 0.15
```

C2：

```text
多强势组 + BTC/ETH 对冲
目标：
abs(BTC Beta) <= 0.25
```

Beta 计算必须：

```text
只使用历史滚动窗口
不能使用全样本 Beta
```

---

# 13. 任务 3：严格执行一致性

建议新增：

```text
alphapilot/research_screening/translation_parity.py
alphapilot/research_screening/trade_identity.py
tests/research_screening/test_translation_parity.py
```

---

## 13.1 必须 100% 一致的字段

```text
signalId
symbol
direction
signalTimestamp
entryBarTimestamp
exitTimestamp
exitReason
```

要求：

```text
完全一致率 = 100%
```

任何无法解释的：

```text
missingInFreqtrade
extraInFreqtrade
directionMismatch
timestampMismatch
exitReasonMismatch
```

均：

```text
failed_closed
```

---

## 13.2 数值容差字段

只有以下可以使用容差：

```text
entryPrice
exitPrice
grossR
feeR
fundingR
slippageR
netR
```

要求：

```text
>=99% 交易在预注册数值容差内
且
剩余差异全部有可解释、已分类原因
```

注意：

```text
99% 不适用于交易身份。
```

任意无法解释的价格或净 R 差异：

```text
translationParityPassed=false
```

---

# 14. 任务 4：代理数据资格限制

数据资格状态：

```text
diagnostic_ready
provisional_ready
formal_ready
unavailable
```

---

## 14.1 A2 清算代理

A2 可以使用：

```text
OI 下降
成交量异常
长影线
ATR 扩张
```

作为明确的清算代理。

但 A2 最高只能获得：

```text
provisional_research_pass
```

没有：

```text
真实清算数据
或
已独立验证的清算数据
```

不得：

```text
overallFormalPassed=true
```

---

## 14.2 B 数据要求

B 的 Formal Pass 必须拥有同交易所：

```text
Funding
OI
永续价格
现货价格
Basis
```

缺任一项：

```text
formalDataProvenancePassed=false
```

---

## 14.3 C 数据要求

C 的 Formal Pass 必须拥有：

```text
历史 PIT 可交易状态
历史流动性
上市 / 下架时间
当时可交易合约池
```

使用当前 TopN 回填历史：

```text
只能 provisional
```

---

# 15. 任务 5：真实资金竞争与组合约束

A、B 扫描多个币时，禁止把所有事件简单独立相加。

建议新增：

```text
alphapilot/research_screening/capital_competition.py
alphapilot/research_screening/signal_ranking.py
alphapilot/research_screening/correlation_clusters.py
alphapilot/research_screening/capacity_model.py
tests/research_screening/test_capital_competition.py
tests/research_screening/test_signal_ranking.py
```

---

## 15.1 必须预注册

```text
initialCapital
riskPerTrade
maximumConcurrentPositions
maximumOpenRisk
maximumSameDirectionRisk
maximumCorrelationClusterRisk
maximumSingleSymbolRisk
maximumPortfolioBeta
liquidityCapacityPolicy
```

建议同时报告：

```text
10,000 USDT
50,000 USDT
100,000 USDT
```

主 Gate 使用哪个资本规模必须预注册。

---

## 15.2 多信号竞争

同一时点信号数超过可用仓位时，使用确定性排序。

排序字段只能使用信号当时可知信息，例如：

```text
dataFreshness
liquidityScore
mechanismStrength
residualExtremeness
fundingExtremeness
OIChangeStrength
```

排序公式必须：

```text
在 Development 前冻结
```

禁止：

```text
按未来收益排序
按 Holdout 表现改排序
```

---

## 15.3 相关簇

使用历史滚动收益生成：

```text
BTC 高 Beta 簇
主流币簇
DeFi 簇
高相关山寨簇
```

只使用当时数据。

A、B 共同归入：

```text
stress_reversal 风险簇
```

即使信号条件不同，也必须共享：

```text
同方向风险上限
相关簇上限
```

---

## 15.4 容量

至少报告：

```text
orderNotional / 24h quote volume
orderNotional / bar quote volume
orderNotional / depth proxy
estimatedSlippage
capacityRejectedSignals
```

容量规则必须预注册。

无法证明容量：

```text
capacityPassed=false
```

---

# 16. 任务 6：简单基准

复杂机制只有明显超过简单基准，才有研究价值。

---

## 16.1 A/B 基准

必须至少实现：

```text
benchmark_unconditional_rebound
无条件反弹基准

benchmark_btc_beta_matched
BTC Beta 与市场下跌幅度匹配基准

benchmark_simple_residual_mean_reversion
简单残差均值回归基准
```

B 额外建议：

```text
benchmark_funding_only_reversal
Funding-only 反转
```

比较必须：

```text
同币种
同月份
同事件数量或匹配抽样
同最大持有期
同退出几何
同成本
```

A/B Formal Pass 要求：

```text
平均净 R 高于最强可比基准
总净 R 高于最强可比基准
90% Cluster Bootstrap 的增量平均净 R 下界 > 0
```

---

## 16.2 C 基准

必须实现：

```text
NoTrade
PIT 等权组合
BTC BuyHold
ETH BuyHold
普通 20 日横截面动量
```

正式比较主基准：

```text
普通 20 日横截面动量
```

要求保持：

```text
相同 PIT 币池
相同再平衡频率
相同 Gross / Net Exposure
相同成本
相同 Beta 目标
```

BTC、ETH、等权仅作为上下文基准，不用不同 Beta 暴露强行比较绝对收益。

---

# 17. 任务 7：不确定性报告

建议新增：

```text
alphapilot/research_screening/cluster_bootstrap.py
alphapilot/research_screening/uncertainty_report.py
tests/research_screening/test_cluster_bootstrap.py
```

Bootstrap 次数：

```text
至少 5,000
```

随机种子：

```text
预注册并写入环境 Manifest
```

---

## 17.1 A/B Bootstrap 单位

分别执行：

```text
按月份 Cluster
按币种 Cluster
按事件簇 Cluster
```

输出：

```text
PF 80% / 90% / 95% CI
平均净 R 80% / 90% / 95% CI
总净 R CI
最大回撤 CI
相对基准增量 CI
```

---

## 17.2 C Bootstrap 单位

```text
按月份 Cluster
按再平衡周期 Cluster
按币种 / 相关簇 Cluster
```

输出：

```text
组合净收益 CI
相对 20 日动量超额收益 CI
最大回撤 CI
Beta CI
换手 CI
```

点估计达到门槛但置信区间过宽：

```text
uncertaintyPassed=false
```

---

# 18. 任务 8：Clean Holdout 一次性解封

建议新增：

```text
alphapilot/research_screening/holdout_unlock.py
alphapilot/research_screening/holdout_unlock_store.py
tests/research_screening/test_holdout_unlock.py
```

生成：

```text
research/holdout_unlocks/<campaignId>.json
reports/backtest_screening/<campaignId>/holdout_unlock_audit.json
```

---

## 18.1 解封前必须冻结

```text
Git Commit
Data Snapshot Hash
Preregistration Hash
Strategy Definition Hash
Exit Model Hash
Benchmark Hash
Risk / Capital Competition Hash
Environment Manifest Hash
```

---

## 18.2 解封记录

```text
campaignId
holdoutHash
unlockedAtUtc
operator=human_local_operator
reason
accessCount
codeCommit
dataSnapshotHash
preregistrationHash
recordHash
```

要求：

```text
accessCount 初始为 0
正式解封后必须恰好为 1
```

---

## 18.3 Holdout 失败

任一正式 Holdout Gate 失败：

```text
campaignStatus = closed_holdout_failed
```

之后禁止：

```text
修改参数
修改退出
修改币池
修改基准
重新读取同一 Holdout 进行再次选型
```

必须创建：

```text
新 Campaign
新数据或新未来 Holdout
```

---

## 18.4 技术故障重放

只有以下情况允许技术重放：

```text
代码 Hash 完全相同
数据 Hash 完全相同
预注册 Hash 完全相同
未修改策略和门槛
故障在指标生成前已记录
```

技术重放不计为第二次统计解封，但必须记录：

```text
technicalReplay=true
incidentHash
```

---

# 19. 任务 9：数据下载能力与可复现环境

建议新增：

```text
alphapilot/derivatives_data/api_capability_audit.py
alphapilot/derivatives_data/resumable_collector.py
alphapilot/derivatives_data/gap_repair.py
alphapilot/derivatives_data/environment_manifest.py
tests/derivatives_data/test_resumable_collector.py
tests/derivatives_data/test_gap_repair.py
```

---

## 19.1 API 能力审计

在下载前输出：

```text
reports/derivatives_data/api_capability_audit.json
```

每种数据记录：

```text
exchange
endpoint
dataType
earliestAvailable
latestAvailable
pagination
rateLimit
requiresAuth
publicOnly
historicalDepth
symbolCoverage
knownLimitations
```

如果官方接口不提供足够历史：

```text
明确 unavailable
不能伪造
```

---

## 19.2 断点续传

必须支持：

```text
checkpoint
resume
boundedRetry
rateLimitBackoff
partialFileValidation
```

下载中断后：

```text
从最后已验证游标继续
```

---

## 19.3 去重与缺口

主键至少：

```text
exchange
instrumentId
dataType
timestampUtc
```

要求：

```text
现有数据先扫描
重复行去重
缺口显式列出
只补缺口
不整库覆盖
```

生成：

```text
download_resume_manifest.json
deduplication_report.json
gap_repair_report.json
```

---

## 19.4 速率与磁盘预算

预注册：

```text
maximumRequestsPerMinute
maximumRetries
maximumDownloadBytes
minimumFreeDiskBytes
```

磁盘预算不足：

```text
停止
不删除用户历史数据
```

---

## 19.5 环境 Manifest

必须生成：

```text
reports/reproducibility/environment_manifest.json
```

记录：

```text
OS
Python executable
Python version
Freqtrade version
Docker version
Docker image tag
Docker image digest
dependency lock hash
pip freeze hash
Git commits
random seeds
timezone
locale
pandas version
numpy version
pyarrow version
```

时区统一：

```text
UTC 存储
Asia/Shanghai 仅显示
```

---

## 19.6 PyArrow / Parquet

当前 bundled Python 没有 PyArrow 的情况不能静默跳过。

规则：

```text
1. CSV 与 canonical JSON 校验副本为强制输出。
2. Parquet 只有在：
   - 隔离环境安装固定版本 PyArrow
   - 依赖锁已更新
   - 版本已写入环境 Manifest
   时才是允许输出。
3. 没有 PyArrow 时：
   - 不声称生成 Parquet
   - 使用 CSV + JSON + SHA-256
```

每个重要表必须至少有：

```text
CSV
JSON schema / summary
SHA-256
```

---

# 20. 同交易所数据 Snapshot

优先交易所：

```text
OKX Public Market Data
```

核心数据应来自同一交易所：

```text
永续 OHLCV
Funding
Open Interest
永续价格
合约状态
```

Basis：

```text
同交易所现货价格
与
同交易所永续价格
```

如果 OKX 历史能力不足：

```text
可以在预注册前选择 Binance 作为整个 A/B 正式数据源，
但不能跨交易所拼接核心字段。
```

正式报告必须记录：

```text
exchangeChoiceReason
sameExchangeCoreDataPassed
```

---

# 21. 三方向预注册

方向固定：

```text
A：特异性抛售耗竭做多
B：空头拥挤解除做多
C：PIT 高流动性横截面中期动量
```

上层家族固定：

```text
stress_reversal
cross_sectional_momentum
```

禁止：

```text
增加第四方向
创建 A/B 对称做空
```

---

# 22. A：特异性抛售耗竭做多

内部 ID：

```text
idiosyncratic_selloff_exhaustion_long
```

方向：

```text
只做多
```

周期：

```text
4h 环境
1h 或 4h 触发
```

核心机制：

```text
相对 BTC / 市场组合的极端负残差
+ OI 下降
+ 多头清算上升
+ Funding / Basis 显示恐慌
+ 价格重新收回
```

变体：

```text
A1：真实清算版
A2：清算代理版
```

A2 最高：

```text
provisional_research_pass
```

---

# 23. B：空头拥挤解除做多

内部 ID：

```text
short_crowding_unwind_long
```

方向：

```text
只做多
```

周期：

```text
4h 环境
1h / 4h 触发
```

机制：

```text
极端负 Funding
+ OI 先扩张后收缩
+ 永续贴水
+ 价格停止创新低并回收
```

变体：

```text
B1：4h 触发
B2：4h 环境 + 1h 回收触发
```

---

# 24. C：PIT 高流动性横截面中期动量

内部 ID：

```text
pit_liquid_cross_sectional_momentum
```

周期：

```text
1d 排名
4h / 1d 执行
```

PIT 池：

```text
30–50 个历史可交易、高流动性 USDT 永续
```

变体：

```text
C1：多强势组 + 空弱势组
C2：多强势组 + BTC / ETH Beta 对冲
```

禁止使用：

```text
单币固定 R 止盈
```

退出：

```text
固定再平衡
排名跌出
Beta 偏离
组合风险
流动性下降
```

---

# 25. 2R 规则统一口径

为避免与旧原则冲突，采用以下最终规则。

## A/B 方向事件策略

```text
1. 初始止损在入场前冻结。
2. 入场后永不放宽初始止损。
3. 允许在 +1R 部分止盈。
4. 剩余仓位的初始盈利目标不得低于 +2R。
5. 允许在以下情况下提前退出：
   - 市场机制失效
   - Funding / Basis 正常化
   - 残差回归完成
   - 最大持有期到期
6. 止损距离扩大时，仓位必须同比缩小，保持 1R 不变。
```

这意味着：

```text
可以提前结构退出
但不能把剩余仓位固定目标降到 1.5R 来制造通过
```

## C 横截面组合

```text
不使用单币 R 目标。
```

使用：

```text
再平衡
排名
Beta
组合风险
流动性
```

---

# 26. 预注册退出模型

A、B 每个方向最多两个退出模型。

Development 前冻结：

```text
Exit Model 1：
1R 部分止盈
剩余 >=2R
结构失效 / 时间提前退出

Exit Model 2：
不部分止盈
剩余 >=2R
结构失效 / 时间提前退出
```

只允许在 Development 选择一个。

进入 Walk-forward 后：

```text
exitModelHash 不可变
```

---

# 27. Holdout 策略

上一轮 Holdout 已被查看，不能重复称为干净 Holdout。

本轮必须至少使用：

```text
新币种 Holdout
```

建议：

```text
70% Research Symbols
30% Symbol Holdout
```

如有预注册后新增未来数据：

```text
再增加 Future Time Holdout
```

所有 Holdout Symbol：

```text
不得参与方向选择
不得参与阈值选择
不得参与退出选择
不得参与基准选择
```

---

# 28. Campaign 预算

固定：

```text
顶层家族：2
方向：3
每方向初始变体：最多 2
初始候选：最多 6
每方向退出模型：最多 2
完整事件策略回测：最多 20
组合回测：最多 12
总正式 Trial：最多 48
```

禁止：

```text
遗传搜索
大规模参数网格
追加方向
Holdout 后调参
```

---

# 29. 输出文件

## Git 与映射

```text
reports/v13_27_1_11_baseline_closeout_manifest.json
reports/v13_27_1_11_baseline_closeout_summary.md
reports/research_factory_repair/input_artifact_mapping.json
reports/research_factory_repair/input_artifact_mapping.md
```

## 方法修复

```text
reports/research_factory_repair/gate_migration_report.json
reports/research_factory_repair/causality_policy.json
reports/research_factory_repair/translation_parity_report.json
reports/research_factory_repair/benchmark_registry.json
reports/research_factory_repair/bootstrap_policy.json
reports/research_factory_repair/holdout_unlock_policy.json
reports/research_factory_repair/exit_geometry_schema.json
```

## 数据与环境

```text
reports/derivatives_data/api_capability_audit.json
reports/derivatives_data/download_resume_manifest.json
reports/derivatives_data/deduplication_report.json
reports/derivatives_data/gap_repair_report.json
reports/derivatives_data/data_source_registry.json
reports/derivatives_data/data_audit.json
reports/derivatives_data/exchange_alignment.json
reports/derivatives_data/pit_universe_manifest.json
reports/derivatives_data/data_manifest.json
reports/reproducibility/environment_manifest.json
research/data_snapshots/<snapshotId>.json
```

## 预注册与结果

```text
research/preregistrations/<campaignId>.json
research/holdout_unlocks/<campaignId>.json

reports/backtest_screening/<campaignId>/family_hierarchy.json
reports/backtest_screening/<campaignId>/event_overlap_report.json
reports/backtest_screening/<campaignId>/event_prefilter.json
reports/backtest_screening/<campaignId>/simple_benchmark_report.json
reports/backtest_screening/<campaignId>/exit_geometry.json
reports/backtest_screening/<campaignId>/capital_competition.json
reports/backtest_screening/<campaignId>/freqtrade_translation_parity.json
reports/backtest_screening/<campaignId>/portfolio_backtest_report.json
reports/backtest_screening/<campaignId>/walk_forward_report.json
reports/backtest_screening/<campaignId>/holdout_unlock_audit.json
reports/backtest_screening/<campaignId>/holdout_report.json
reports/backtest_screening/<campaignId>/uncertainty_report.json
reports/backtest_screening/<campaignId>/cost_stress_report.json
reports/backtest_screening/<campaignId>/gate_matrix.json
reports/backtest_screening/<campaignId>/failure_attribution.json
reports/backtest_screening/<campaignId>/experiment_budget.json
reports/backtest_screening/<campaignId>/campaign_summary.json
reports/backtest_screening/<campaignId>/campaign_summary.md
reports/backtest_screening/<campaignId>/formal_pass_evidence/*.json
```

重要表：

```text
必须同时输出 CSV + canonical JSON 校验副本。
Parquet 仅在固定 PyArrow 依赖可用时输出。
```

---

# 30. 测试要求

至少新增或更新测试：

```text
Console main 收口检测
Quant dirty main 保护
Feature Branch 干净 worktree 合并
输入文件显式映射
缺失映射失败关闭

A/B 顶层家族归类
A/B 事件重叠
层级 FDR
相关风险簇

事件 Gate 与组合 Gate 完全分离
C 不依赖单笔 R Gate

Funding publication lag
OI availability lag
PIT snapshot lag
下一根 K 线成交
跨 Split 事件删除
Purge / Embargo

代理清算不能 Formal Pass
PIT Proxy 不能 Formal Pass

交易身份 100% Parity
缺失 / 额外交易失败
数值容差 99% 只用于价格和净 R

资金竞争
并发持仓
总风险
相关簇
BTC Beta
确定性信号排名
容量拒绝

简单基准
Cluster Bootstrap
置信区间 Gate

一次性 Holdout 解封
Holdout 失败关闭 Campaign
技术重放审计

断点续传
数据去重
缺口补齐
速率限制
磁盘预算
环境 Manifest
PyArrow 缺失处理
CSV / JSON 校验副本

初始止损冻结
禁止放宽
1R 部分止盈
剩余目标 >=2R
C 不使用单币 R
```

---

# 31. 验证命令

Quant：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine

python -m pytest tests/research_factory_repair -q
python -m pytest tests/derivatives_data -q
python -m pytest tests/research_screening -q
python -m pytest tests/factor_lab -q
python -m pytest tests -q

python -m compileall alphapilot
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

Console 收口验收：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Control-Console

python -m unittest discover -s tests -v
python -m compileall alphapilot_control_console
node --check web/app.js
python -m alphapilot_control_console.http_app --smoke
git diff --check
```

---

# 32. 推荐提交顺序

## 收口提交 / 推送

```text
Console：
验证 1f4b865 后推送 main
创建 Console 里程碑 Tag

Quant：
在干净 worktree 合并 feature/revised-demo-backtest-first
推送 main
创建 Phase 3 里程碑 Tag

Docs：
归档最终文档和本提示词
推送并打 Tag
```

## V13.27.1.11 研究提交

```text
Commit 1：
Repair research gates, causality, parity, and holdout controls

Commit 2：
Build resumable same-exchange derivatives data snapshot

Commit 3：
Preregister hierarchical three-direction campaign

Commit 4：
Record event prefilter, benchmarks, and capital competition

Commit 5：
Record Freqtrade and portfolio walk-forward results

Commit 6：
Unlock clean holdout once and record final decisions
```

---

# 33. Formal Evidence 生成规则

A/B 只有：

```text
事件 Formal Gate 全部通过
真实数据 Gate 通过
非代理清算要求通过
Translation Parity 通过
资金竞争通过
置信区间通过
简单基准通过
Clean Holdout 通过
```

才能生成。

C 只有：

```text
组合 Formal Gate 全部通过
PIT 正式数据通过
相对简单 20 日动量超额通过
Beta / Turnover / Capacity 通过
置信区间通过
Clean Holdout 通过
```

才能生成。

A2：

```text
永远不能生成 Formal Evidence
```

除非其清算数据状态在预注册前升级为正式真实数据。

---

# 34. Codex 最终自检

必须输出：

```text
1. 实际版本
2. Console main / origin / tag
3. Quant feature / main / tag
4. Docs commit / tag
5. 用户原 Quant dirty main 是否保持原样
6. preExistingChanges
7. 输入映射路径
8. 缺失或歧义映射

9. 顶层家族数
10. 方向数
11. A/B 事件重叠率
12. A/B 收益相关性
13. 层级 FDR

14. 时间因果规则
15. Funding lag
16. OI lag
17. PIT lag
18. 下一根 K 线成交
19. Purge / Embargo
20. 跨边界事件数量

21. Event Gate Schema
22. Portfolio Gate Schema
23. Proxy 数据资格
24. Translation Identity Parity
25. Translation Numerical Parity

26. API 能力
27. 下载断点
28. 去重
29. 缺口
30. 速率限制
31. 磁盘预算
32. Python / Freqtrade / Docker
33. PyArrow 状态
34. CSV / JSON 副本

35. Snapshot ID / Hash
36. 同交易所对齐
37. PIT 币种数
38. Research / Holdout 币种数

39. Campaign ID
40. Preregistration Hash
41. Holdout Hash
42. Holdout 解封时间
43. 操作者
44. 访问次数

45. A 预筛
46. B 预筛
47. C 预筛
48. 简单基准
49. 退出模型
50. 资金竞争
51. Bootstrap CI
52. Walk-forward
53. Holdout

54. A Basic / Formal
55. B Basic / Formal
56. C Basic / Formal
57. Provisional Pass 数
58. Formal Pass 数
59. Formal Evidence 数
60. 最佳候选
61. 失败归因

62. 是否增加第四方向
63. 是否把 A/B 当完全独立
64. 是否代理数据 Formal Pass
65. 是否 Holdout 后调参
66. 是否重复解封 Holdout
67. 是否放宽止损
68. 是否将剩余目标降到 2R 以下
69. 是否结果运行联网
70. 是否恢复旧候选
71. 是否访问 API Key
72. 是否接 Trade API
73. 是否接 Withdraw API
74. 是否读取账户
75. 是否创建订单
76. 是否自动交易

77. pytest
78. compileall
79. validate_config
80. safety scan
81. git diff --check
82. Commit
83. Push
84. Tag
85. 已知问题
86. 下一步建议
```

---

# 35. 停止规则

## Git 或输入映射失败

```text
立即停止
```

## 方法学测试失败

```text
不下载新数据
不运行 Campaign
```

## 数据能力不足

如果少于两个方向正式数据就绪：

```text
停止
先补数据
```

## 三方向全部失败

```text
接受 0 个正式通过
不增加第四方向
不降低门槛
不复测同一 Holdout
```

## 只有 Provisional Pass

```text
保留研究资产
不生成 Release
```

## Formal Pass

```text
只生成 formal_pass_evidence
不导入 Console
不批准
不 ARM
不创建订单
```

---

# 36. 最终原则

请严格执行：

```text
先收口
再映射
再修方法
再补数据
再预注册
再运行
最后一次性解封 Holdout
```

必须坚持：

```text
A、B 属于共同 stress_reversal 上层家族
C 使用组合 Gate，不使用单笔 R Gate

数据实际可得时间先于信号
信号在收线后产生
最早下一根 K 线成交

交易身份必须 100% 一致
99% 只用于数值容差

代理清算不能 Formal Pass
PIT Proxy 不能 Formal Pass

复杂机制必须明显超过简单基准
点估计必须有 Cluster Bootstrap 置信区间

Holdout 只能解封一次
Holdout 失败即关闭 Campaign

初始止损永不放宽
可在 1R 部分止盈
方向策略剩余目标不低于 2R
横截面组合不使用单币 R

最终正式通过数为 0，是允许且可信的结果
```
