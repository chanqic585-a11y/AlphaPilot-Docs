# AlphaPilot 风险模型候选复核与锁定样本批量验证 —— Codex 完整提示词（中文代号版）

> **任务定位**：基于 AlphaPilot 全量归档失败策略证据库，从 186 个历史策略身份、72 个策略家族中，批量复核那些“信号层可能仍有正向优势、但账户风险路径或证据完整度没有通过”的少数候选家族。
> **核心原则**：冻结信号、预注册验证方案、锁定样本、统一成本、统一风险模型、批量验证、禁止边测边调、禁止直接恢复归档版本。
> **本任务不是**：继续批量生成新策略、重新优化旧策略、把历史最好结果直接送入模拟盘、接交易所或自动交易。
> **用户可见语言**：所有状态、结论、失败原因、建议动作和报告标题统一使用中文。内部 Python 变量和机器字段可保留英文，但必须提供中文显示字段。

---

# 1. 任务名称

```text
AlphaPilot 风险模型候选复核与锁定样本批量验证
```

中文简称：

```text
候选证据闭环验证
```

版本号：

```text
请先读取 AlphaPilot-Quant-Engine 当前最新 tag，
使用下一个未占用版本号。
不得覆盖已有 tag。
```

---

# 2. 背景基线

全量归档分析已经完成，当前已知：

```text
策略身份：186
策略家族：72
注册表归档版本：162
旧 Freqtrade 身份：23
旧状态身份：1
证据产物：779
去重逐笔交易：123,624
有逐笔证据策略：23
没有逐笔证据策略：163
未可靠映射旧证据：218
仅允许创建新版本后重新研究：89
继续归档：97
```

主要失败分布：

```text
信号边际失败：99
数据或证据不足：77
风险模型主失败：8
零交易或流程阻塞：1
风险设计直接拒绝：1
```

次要问题包括：

```text
成本放大
过度交易
账户风险路径失败
市场状态或时间不稳定
方向与市场状态不匹配
退出设计问题
币种集中
```

当前最重要的结论：

```text
1. 186 个策略身份不是 186 个独立策略思想。
2. 自动优化父子版本高度相关，必须按策略家族和定义哈希去重。
3. 99 个信号边际为负的策略不应继续小调。
4. 只有少数“风险模型主失败”候选值得进入证据闭环。
5. 归档版本不能直接恢复，只能在证据通过后提出新版本建议。
6. 本任务最多为 1～2 个策略家族生成“建议创建新版本”的结论。
7. 本任务不授予本地自动执行、交易所模拟盘或实盘资格。
```

---

# 3. 主要仓库

主要执行仓库：

```text
D:\Codex-Workspace\AlphaPilot-Quant-Engine
```

文档仓库：

```text
D:\Codex-Workspace\AlphaPilot-Docs
```

可只读参考：

```text
D:\Codex-Workspace\AlphaPilot-Control-Console
```

不要修改：

```text
D:\Codex-Workspace\trade-discipline-journal
```

---

# 4. 必须读取的输入

Quant 仓库必须读取：

```text
README.md
AGENTS.md 或仓库规则
规矩文档
踩坑日志
```

核心报告：

```text
reports/full_archived_strategy_analysis_summary.md
reports/full_archived_strategy_coverage_audit.json
reports/full_archived_strategy_inventory.json
reports/full_archived_strategy_evidence_index.json
reports/full_archived_strategy_evidence_gaps.json
reports/full_archived_strategy_metrics_matrix.json
reports/full_archived_strategy_failure_attribution.json
reports/full_archived_strategy_failure_matrix.csv
reports/full_archived_strategy_cross_strategy_patterns.json
reports/full_archived_strategy_negative_rules.json
reports/full_archived_strategy_revival_candidates.json
reports/full_archived_strategy_continue_archive.json
reports/full_archived_strategy_prohibited_routes.json
```

本地大文件，如存在：

```text
reports/generated/full_archived_strategy_trade_level_metrics.jsonl
```

Docs 必须读取：

```text
D:\Codex-Workspace\AlphaPilot-Docs\architecture\AlphaPilot_Strategy_Generation_Core_Principles.md
```

如果任何文件缺失：

```text
记录缺失
不要伪造
不要用 0 代替
```

---

# 5. 执行前 Git 与工作区预检

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status --short
git log -1 --oneline
git tag --list
```

必须确认：

```text
全量归档分析 commit bd6ae0d 或其后继提交可见
```

Docs 必须确认：

```text
策略生成核心准则 commit ad67d99 或其后继提交可见
```

如果 Quant 工作区存在任务开始前的旧报告改动：

```text
保持原样
不要混入本任务提交
不要 reset
不要覆盖
```

本任务新增文件必须与旧未提交文件清楚分离。

---

# 6. 总安全边界

本任务允许：

```text
运行 research-only 历史回放
运行锁定样本验证
运行 Walk-forward
运行成本压力测试
运行风险模型账户路径模拟
读取公开历史数据和现有本地数据
生成新报告、测试和文档
```

本任务禁止：

```text
不保存真实 API Key
不接 Trade API
不接 Withdraw API
不读取真实账户
不读取真实持仓
不创建真实订单
不创建交易所测试网订单
不执行 exchange Dry-run
不执行实盘
不自动交易
不修改 AlphaPilot Mobile App
不修改控制台执行权限
不启用任何 executionEligible
不将 dryRunApproved 设为 true
不将 liveTradingApproved 设为 true
```

---

# 7. 研究边界

本任务的目标是：

```text
验证历史候选信号是否能在锁定样本、真实成本和统一风险模型下保留正优势。
```

本任务不是：

```text
找一组最赚钱参数
通过不断试错让候选过关
把风险模型当作信号优化器
恢复旧归档策略
```

禁止：

```text
修改候选入场逻辑
修改候选信号阈值
修改候选方向
修改候选周期
修改候选币种筛选逻辑
扩大初始止损来减少止损次数
通过提高杠杆提高收益
通过删除亏损币改写结论
边看锁定样本结果边修改参数
```

---

# 8. 候选队列

必须从全量归档报告自动确认下列候选的最新状态、策略定义和身份。

## A 级：完整证据闭环优先队列

### A1：1H 深弱势扫低收回，因子后继 ATR1.2

规范家族：

```text
short_cycle_event_1h_sweep_reclaim_factor_v2
```

已知不可变版本：

```text
strategy_version_05c158de451321444bd1ef4485305b239a0d1bdb67b369f08f95a53295bd4d8e
strategy_version_54dc908bb921b1f0dd2a92a53f505dea54d895e7e27d998bb6b828f719489430
```

已知历史摘要：

```text
Profit Factor：1.32132738
平均净 R：0.1869224
```

要求：

```text
识别两个版本是否定义哈希完全一致。
如完全一致，只保留一个规范代表，另一个标记为重复证据。
不得把两个版本当成两次独立成功。
```

---

### A2：1D 趋势突破回踩 ATR2.0

规范家族：

```text
event_1d_breakout_retest_atr20_v1
```

规范版本：

```text
strategy_version_c0afa97d531616a24d020e2aad5acd5df1168b41e7acbccab330549821aaa7d8
```

已知历史摘要：

```text
Profit Factor：1.25225502
平均净 R：0.13930653
```

---

### A3：1D 趋势压缩释放 ATR2.0

规范家族：

```text
event_1d_squeeze_breakout_atr20_v1
```

规范版本：

```text
strategy_version_a80c785eebf379a311de35b1697e39956849177a157f2d789aaeed433f3a80e3
```

已知历史摘要：

```text
Profit Factor：1.21849701
平均净 R：0.12529251
```

---

### A4：1D 广谱压缩释放 ATR2.0

规范家族：

```text
event_1d_broad_squeeze_breakout_atr20_v1
```

规范版本：

```text
strategy_version_0dc7701b4b48a6d2d3eaf727bfec274b5ca93016a5ea9aa862ffcc7f3ebe9b1b
```

已知历史摘要：

```text
Profit Factor：1.17280778
平均净 R：0.10179918
```

---

## B 级：完整验证的次优先队列

### B1：1H 突破回踩 BTC 顺势确认，因子后继 ATR1.2

规范家族：

```text
short_cycle_event_1h_breakout_retest_btc_factor_v2
```

规范版本：

```text
strategy_version_a2c0736f1f1de60ef2e571761970cbf64c35688ebc4631c55c2dc6570a557778
```

已知历史摘要：

```text
Profit Factor：1.07037278
平均净 R：0.04679832
```

---

## C 级：薄优势观察队列

### C1：1D 超卖扫低收回 ATR1.2 影子

规范家族：

```text
event_1d_oversold_sweep_reclaim_atr12_v1
```

规范版本：

```text
strategy_version_c308c66aeb070009e17ff1eaeb63177d4c17647c6679ca13d8dd49a011d84085
```

历史摘要：

```text
Profit Factor：1.04154194
平均净 R：0.02377073
```

---

### C2：15m 假突破弱趋势反转，因子后继 ATR1.2

规范家族：

```text
short_cycle_event_15m_failed_breakout_factor_v2
```

规范版本：

```text
strategy_version_5f27f24019c5fff46eaf083dc5a57c78029afb8f3c8fe3bab4f3dbe3093a8785
```

历史摘要：

```text
Profit Factor：1.00940092
平均净 R：0.00640423
```

C 级规则：

```text
先运行信号层和基础成本预筛。
只有预筛通过，才进入完整风险模型和锁定样本流程。
不得因为历史 PF 略高于 1 就直接完整推进。
```

---

# 9. 候选自动发现与去重

不要只硬编码候选列表。

必须执行：

```text
1. 从 full_archived_strategy_failure_attribution.json 找出 primaryFailureType=risk_model_failure 的全部版本。
2. 按 strategyFamily、父子链、信号定义哈希、参数哈希去重。
3. 生成“版本 -> 策略家族 -> 规范代表”映射。
4. 自动优化子版本不能作为独立投票。
5. 同定义、同指标、同数据范围的重复版本只保留一条规范证据。
```

输出：

```text
候选版本数
候选家族数
重复版本数
规范代表数
```

---

# 10. 先建立不可变验证身份

历史归档策略不得直接恢复。

本任务不创建可执行策略版本。

必须创建：

```text
validationRunId
validationManifestHash
strategyDefinitionHash
signalDefinitionHash
riskModelHash
costModelHash
dataSnapshotHash
splitManifestHash
```

验证唯一键：

```text
归档策略版本 ID
+ 信号定义哈希
+ 周期
+ 币种池
+ 数据快照哈希
+ 分割清单哈希
+ 成本模型哈希
+ 风险模型哈希
```

不要只用策略类名作为实验身份。

---

# 11. 信号冻结规则

每个候选必须生成：

```text
signal_frozen = true
```

冻结内容：

```text
入场条件
方向
周期
信号窗口
指标计算方式
阈值
币种池规则
退出策略中的信号部分
```

如果当前仓库缺少可执行的信号引擎：

```text
1. 尝试调用注册表已有 signalEngine。
2. 只能实现“等价研究适配器”，不得改变逻辑。
3. 适配器必须输出定义哈希。
4. 如果无法证明等价，标记“不可复现”，停止该候选。
5. 不得根据策略名称猜测实现。
```

允许新增：

```text
alphapilot/validation/
alphapilot/research_validation/
```

禁止修改：

```text
原归档策略定义
原策略版本记录
原失败报告
原参数文件
```

---

# 12. 预注册锁定验证协议

必须在看锁定样本结果前生成：

```text
reports/candidate_locked_validation_preregistration.json
reports/candidate_locked_validation_preregistration.md
```

预注册内容必须包括：

```text
候选名单
去重规则
信号哈希
数据快照
开发区间
已使用验证区间
锁定区间
币种留出
Walk-forward 窗口
成本模型
风险模型
样本门槛
通过门槛
失败门槛
多重试验校正方法
输出文件
```

建议使用两个 Git 提交：

```text
提交一：
Register locked candidate validation protocol

提交二：
Add locked candidate validation results
```

第一提交完成后，禁止修改：

```text
候选名单
信号规则
锁定区间
风险模型
成本模型
通过门槛
```

如果只能单次提交：

```text
必须先将预注册文件写入磁盘并计算 SHA256，
报告中记录 preRegistrationHash，
然后才能执行锁定验证。
```

---

# 13. 数据污染检查

对每个候选读取：

```text
trainWindow
temporalValidationWindow
holdoutWindow
lockedWindow
symbolHoldback
lockedOrHoldoutUsedForSelection
selectionMethod
parentStrategyVersionId
自动优化代数
```

必须生成：

```text
usedForSelectionRanges
usedForSelectionSymbols
potentialLeakageFlags
```

禁止：

```text
把已经用于筛选、自动优化、阈值选择的数据重新叫作锁定样本。
```

---

# 14. 锁定样本选择规则

优先顺序：

```text
1. 策略选定后新增的未来数据
2. 从未用于选型的连续时间区间
3. 从未用于选型的币种留出
4. 嵌套 Walk-forward 的外层测试窗口
```

如果没有真正未污染的锁定样本：

```text
lockedSampleStatus = 无污染锁定样本不可用
```

可以运行：

```text
伪锁定回放
```

但必须标记：

```text
仅供诊断
不能支持候选通过
```

---

# 15. 最低锁定样本要求

建议最低门槛：

## 1D 候选

```text
锁定时间 >= 12 个月
锁定交易数 >= 30
```

## 1H 候选

```text
锁定时间 >= 6 个月
锁定交易数 >= 80
```

## 15m 候选

```text
锁定时间 >= 6 个月
锁定交易数 >= 150
```

如果交易数不足：

```text
状态 = 样本不足
不得通过
```

---

# 16. Walk-forward 设计

每个 A / B 候选至少运行：

```text
3 个外层 Walk-forward 窗口
```

建议：

```text
滚动训练区间
固定验证区间
外层测试区间
```

但本任务禁止重新训练信号参数。

因此 Walk-forward 用于：

```text
观察冻结信号跨时间稳定性
```

不是用于：

```text
每个窗口重新找最佳参数
```

输出：

```text
每窗口交易数
每窗口 PF
每窗口平均净 R
每窗口最大回撤
正收益窗口比例
正平均 R 窗口比例
```

---

# 17. 币种留出验证

如果历史候选使用多个币种：

```text
必须保留从未用于选择的 symbol holdback。
```

输出：

```text
开发币种
留出币种
留出币种交易数
留出币种 PF
留出币种平均净 R
```

禁止：

```text
因为某个币种亏损而在看到结果后删除。
```

---

# 18. 三层验证框架

每个候选必须分开验证：

```text
第一层：信号层
第二层：成本层
第三层：账户风险层
```

不得混合后只给一个总收益结论。

---

# 19. 信号层验证

信号层使用：

```text
固定 1R 单位
不复利
不使用杠杆放大
不受账户本金影响
```

输出：

```text
交易数
胜率
Profit Factor
平均净 R
中位净 R
平均盈利 R
平均亏损 R
期望 R
MFE
MAE
MFE / MAE
先到 +1R 比例
先到 +2R 比例
先到 -1R 比例
最大连续亏损
按币种
按月份
按市场状态
按方向
按入场标签
按退出标签
```

---

# 20. 信号层统计检验

建议使用：

```text
区块 Bootstrap
交易序列 Monte Carlo
```

至少输出：

```text
平均净 R 的 80% / 90% / 95% 置信区间
Profit Factor Bootstrap 区间
P(平均净 R > 0)
P(PF > 1)
```

Bootstrap 建议：

```text
至少 5,000 次
保留时间簇或交易簇，避免完全打散相关性
```

---

# 21. 多重试验与选择偏差

由于历史存在：

```text
自动优化
父子版本
多个候选
多轮筛选
```

必须记录：

```text
familyTrialCount
versionTrialCount
totalCandidateCount
```

建议计算：

```text
Deflated Sharpe Ratio，如数据足够
PBO 或近似选择偏差指标，如实现已有
Benjamini-Hochberg FDR 校正，如进行多个统计检验
```

如果无法计算：

```text
标记不可用
不要伪造
```

不能因为：

```text
7 个候选中有 1 个偶然为正
```

就视为通过。

---

# 22. 成本模型

基础成本优先读取策略注册表和历史定义。

若定义中已有：

```text
feeRate = 0.0005
slippageRate = 0.0005
```

则作为基础模型。

必须建立以下场景：

## 场景一：基础成本

```text
手续费：按策略定义或当前研究默认
滑点：按策略定义或当前研究默认
资金费：有真实数据则使用；无数据则 null
```

## 场景二：1.5 倍成本压力

```text
滑点与执行摩擦按基础模型的 1.5 倍
```

## 场景三：2 倍成本压力

```text
滑点与执行摩擦按基础模型的 2 倍
```

如资金费不可用：

```text
分别输出“不含资金费”和“保守代理”结果
不得将代理伪装成真实资金费
```

---

# 23. 成本层输出

每个场景输出：

```text
净 Profit Factor
净平均 R
净总 R
手续费
滑点
资金费
交易成本占毛利润比例
由正转负的成本阈值
```

必须回答：

```text
信号原始有优势但成本后失效？
还是原始信号本身就很弱？
```

---

# 24. 预定义风险模型

禁止搜索最佳风险比例。

必须预先固定以下研究模型。

## 模型零：信号等权模型

```text
每笔 = 1R
不复利
不计算真实本金
用于纯信号比较
```

## 模型一：低风险固定比例

```text
每笔账户风险 = 0.25%
最大总开放风险 = 1.00%
最大同时持仓 = 4
单一币种开放风险上限 = 0.50%
同方向相关簇风险上限 = 0.50%
日内新增风险暂停阈值 = -1.50%
账户回撤研究停止线 = 10%
```

## 模型二：标准固定比例

```text
每笔账户风险 = 0.50%
最大总开放风险 = 1.50%
最大同时持仓 = 3
单一币种开放风险上限 = 0.75%
同方向相关簇风险上限 = 0.75%
日内新增风险暂停阈值 = -2.00%
账户回撤研究停止线 = 15%
```

## 模型三：相关性约束模型

```text
基础单笔风险 = 0.25%
最大总开放风险 = 1.00%
BTC / ETH / SOL 等高相关资产按风险簇计算
同方向簇风险上限 = 0.50%
高 Beta 山寨币风险折扣
禁止通过杠杆提高允许亏损
```

这些参数是：

```text
预定义研究模型
```

不是：

```text
最终实盘配置
```

---

# 25. 风险模型硬规则

所有模型必须：

```text
止损存在
风险用 R 表达
杠杆不改变最大允许亏损
不加仓摊平
不马丁
不网格
不亏损加仓
不放宽初始止损
不允许无限并发
```

---

# 26. 账户路径输出

每个风险模型输出：

```text
初始权益
最终权益
总收益
最大回撤百分比
最大回撤 R
回撤持续时间
最长恢复时间
最大连续亏损
最大同时持仓
最大开放风险
最大相关簇风险
日最大亏损
周最大亏损
月最大亏损
风险停止触发次数
```

---

# 27. Monte Carlo 风险评估

对交易序列执行：

```text
至少 5,000 次 Monte Carlo
```

输出：

```text
终值分布
最大回撤中位数
最大回撤 90% / 95% 分位数
最大连续亏损 95% 分位数
达到 10% / 15% / 20% 回撤的概率
账户归零概率，如模型允许
```

若交易存在强时间依赖：

```text
使用区块重采样
```

---

# 28. 组合风险分析

必须分析：

```text
币种相关性
BTC Beta
同方向暴露
策略间信号重叠
同时持仓重叠
收益贡献集中度
亏损贡献集中度
```

候选家族单独通过不代表组合可用。

输出：

```text
pairContribution
monthContribution
regimeContribution
directionContribution
correlatedExposure
```

---

# 29. 市场状态分析

统一中文市场状态：

```text
牛市
熊市
震荡
急跌
修复
高波动
未知
```

每个候选输出：

```text
各市场状态交易数
各市场状态 PF
各市场状态平均净 R
各市场状态最大回撤
各市场状态暴露比例
```

如果市场状态字段不可用：

```text
标记“市场状态证据不足”
不得作为通过证据
```

---

# 30. 退出设计分析

必须拆分：

```text
止损
2R 目标
趋势尾仓
时间退出
动能退出
其他退出
```

输出：

```text
退出次数
平均 R
总 R
MFE
MAE
止损前曾达到正浮盈比例
达到 1R 后最终止损比例
```

不得在本任务中修改退出逻辑。

本任务只诊断：

```text
风险路径是否失败
```

如果退出结构是主要问题：

```text
输出“需要创建新的退出研究版本”
```

但本任务不实现该新版本。

---

# 31. A / B / C 队列执行规则

## A 级

```text
完整执行：
信号层
成本层
锁定样本
Walk-forward
风险模型
Monte Carlo
组合风险
```

## B 级

```text
与 A 级相同，但优先级较低。
```

## C 级

先执行：

```text
冻结信号复现
基础成本信号层
样本量检查
```

只有满足：

```text
基础成本 PF >= 1.05
基础成本平均净 R >= 0.02
最低样本量通过
```

才继续完整流程。

否则：

```text
继续归档
```

---

# 32. 预注册通过门槛

以下门槛必须写入预注册文件，运行后不得修改。

## 证据层

```text
信号定义可复现
数据快照可追溯
无未解释未来数据
锁定样本未用于选型
最低交易数通过
```

## 信号层

建议研究初筛门槛：

```text
基础成本后 PF >= 1.10
基础成本后平均净 R >= 0.05
P(平均净 R > 0) >= 0.90
Walk-forward 正平均 R 窗口比例 >= 60%
```

## 锁定样本层

```text
锁定样本 PF > 1.00
锁定样本平均净 R > 0
锁定样本总 R > 0
```

## 成本压力层

```text
1.5 倍成本后 PF >= 1.00
1.5 倍成本后平均净 R >= 0
```

2 倍成本用于压力观察，不要求盈利，但不得出现异常灾难性失真。

## 稳定性层

```text
单一币种贡献不得超过总正收益的 35%
单一月份贡献不得超过总正收益的 35%
不能只有一个 Walk-forward 窗口盈利
```

如样本太少无法应用 35% 规则：

```text
标记样本不足
```

## 风险层

模型一：

```text
历史最大回撤 <= 15%
Monte Carlo 95% 最大回撤 <= 25%
```

模型二：

```text
历史最大回撤 <= 20%
Monte Carlo 95% 最大回撤 <= 30%
```

风险层门槛只用于研究分级，不授予执行资格。

---

# 33. 决策状态

所有用户可见状态必须中文。

允许状态：

```text
待验证
重复版本
定义不可复现
证据不足
无污染锁定样本不可用
样本不足
信号层失败
成本层失败
锁定样本失败
稳定性失败
风险层失败
局部优势但不稳定
证据闭环通过
建议创建新研究版本
继续归档
永久禁止
```

---

# 34. 最终决策逻辑

## 继续归档

任一情况：

```text
PF < 1
平均净 R <= 0
锁定样本为负
成本压力后失效严重
定义不可复现
```

## 局部优势但不稳定

```text
部分窗口或市场状态为正
但整体稳定性、币种集中或月份集中不通过
```

## 信号通过但风险失败

```text
基础成本和锁定样本通过
但账户路径或 Monte Carlo 风险不通过
```

## 证据闭环通过

必须同时满足：

```text
证据层通过
信号层通过
锁定样本通过
成本层通过
稳定性层通过
风险层至少一个低风险模型通过
```

即便证据闭环通过：

```text
dryRunApproved = false
liveTradingApproved = false
executionEligible = false
```

只能输出：

```text
建议创建新研究版本
```

---

# 35. 新版本建议限制

本任务最多输出：

```text
1～2 个建议创建新研究版本的策略家族
```

选择不是按总收益最高，而是综合：

```text
锁定样本
成本后 PF
平均净 R
Walk-forward
风险
集中度
证据完整性
选择偏差惩罚
```

不得直接创建可执行 Release。

---

# 36. 建议新增代码

建议新增：

```text
alphapilot/validation/__init__.py
alphapilot/validation/candidate_selection.py
alphapilot/validation/candidate_deduplication.py
alphapilot/validation/signal_freezer.py
alphapilot/validation/data_split_registry.py
alphapilot/validation/locked_sample_protocol.py
alphapilot/validation/cost_stress.py
alphapilot/validation/risk_models.py
alphapilot/validation/monte_carlo.py
alphapilot/validation/candidate_validator.py
alphapilot/reports/generate_candidate_evidence_closure_report.py
alphapilot/reports/candidate_evidence_closure_schema.py
```

如已有同类模块：

```text
优先复用
不要重复造轮子
```

---

# 37. 建议新增脚本

```text
scripts/run_candidate_evidence_closure.ps1
```

支持：

```powershell
-PreRegister
-RunSignalValidation
-RunLockedValidation
-RunRiskModels
-RunAll
-CandidateTier
```

默认：

```text
只打印计划
不执行
```

只有显式：

```powershell
-RunAll
```

才运行研究验证。

---

# 38. 输出文件

必须生成：

```text
reports/candidate_evidence_closure_candidate_queue.json
reports/candidate_evidence_closure_deduplication.json
reports/candidate_locked_validation_preregistration.json
reports/candidate_locked_validation_preregistration.md
reports/candidate_validation_data_manifest.json
reports/candidate_validation_cost_models.json
reports/candidate_validation_risk_models.json
reports/candidate_signal_layer_report.json
reports/candidate_locked_sample_report.json
reports/candidate_walk_forward_report.json
reports/candidate_cost_stress_report.json
reports/candidate_risk_model_report.json
reports/candidate_monte_carlo_report.json
reports/candidate_portfolio_risk_report.json
reports/candidate_evidence_closure_report.json
reports/candidate_evidence_closure_summary.md
reports/candidate_evidence_closure_leaderboard.csv
reports/candidate_continue_archive.json
reports/candidate_new_version_recommendations.json
```

大体积逐笔文件：

```text
reports/generated/candidate_validation_trade_rows.jsonl
reports/generated/candidate_validation_monte_carlo_samples.jsonl
```

大文件默认不提交 Git。

提交：

```text
manifest
summary
aggregate report
sample
hash
```

---

# 39. 文档输出

新增：

```text
docs/candidate-evidence-closure-methodology.md
docs/locked-sample-validation-policy.md
docs/signal-vs-risk-model-validation.md
docs/candidate-validation-results.md
```

Docs 仓库归档完整 Prompt。

---

# 40. 报告必须体现的字段

每个候选至少输出：

```text
策略版本 ID
策略家族
规范代表
重复版本
优先级
历史 PF
历史平均净 R
定义哈希
数据哈希
分割哈希
成本模型哈希
风险模型哈希
锁定样本是否无污染
锁定样本交易数
基础成本 PF
基础成本平均净 R
1.5 倍成本 PF
2 倍成本 PF
Walk-forward 通过比例
锁定样本 PF
锁定样本平均净 R
最大回撤
Monte Carlo 95% 最大回撤
币种集中度
月份集中度
市场状态稳定性
证据完整度
最终中文状态
失败原因
建议动作
```

---

# 41. 不允许的结果处理

禁止：

```text
看到锁定结果后调整阈值再重跑
删除表现差的币种后改写主结果
只展示最好的风险模型
隐藏失败的成本场景
把同一家族重复版本当成多次成功
把历史 PF>1 当作当前验证通过
把证据不足写成通过
把本任务结果直接送入本地自动执行
```

所有运行结果必须保留。

---

# 42. 测试要求

必须新增专项测试，至少覆盖：

```text
候选去重
信号定义哈希稳定
数据分割不可变
锁定样本污染检测
成本模型不可变
风险模型不可变
状态决策
重复版本不重复计票
缺失值保持 null
C 级预筛停止逻辑
```

---

# 43. 验证命令

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine

python -m compileall alphapilot
python -m pytest
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

预注册：

```powershell
powershell -ExecutionPolicy Bypass -File scripts\run_candidate_evidence_closure.ps1 -PreRegister
```

验证：

```powershell
powershell -ExecutionPolicy Bypass -File scripts\run_candidate_evidence_closure.ps1 -RunAll
```

生成报告：

```powershell
python -m alphapilot.reports.generate_candidate_evidence_closure_report
```

---

# 44. 安全检查

必须确认：

```text
liveTradingEnabled=false
tradeApiEnabled=false
withdrawApiEnabled=false
dryRunApproved=false
liveTradingApproved=false
executionEligible=false
```

安全扫描命中只允许：

```text
历史字段
否定语境
安全声明
研究配置
```

---

# 45. Git 提交建议

## 提交一：预注册

```bash
git add <预注册文件和验证框架>
git commit -m "Register locked candidate evidence validation protocol"
git push
```

记录：

```text
preRegistrationCommit
preRegistrationHash
```

## 提交二：结果

```bash
git add <聚合结果、文档和测试>
git commit -m "Add candidate evidence closure validation results"
git push
```

如果全部验证成功：

```text
使用当前 Quant 下一个未占用 tag
```

Docs 使用对应：

```text
<版本号>-docs
```

不得覆盖已有 tag。

---

# 46. 是否允许打 Tag

只有以下条件同时满足才允许：

```text
预注册完成
研究验证实际运行
报告生成成功
所有结果可追溯
测试通过
安全扫描通过
工作区状态清楚
```

无候选通过也可以打：

```text
研究里程碑 Tag
```

但必须明确：

```text
这不是执行批准 Tag
```

---

# 47. Codex 最终自检输出

完成后必须输出：

```text
1. 使用的版本号
2. 新增文件列表
3. 修改文件列表
4. 删除文件列表
5. 输入报告清单
6. 候选版本数
7. 去重后候选家族数
8. 重复版本数
9. A / B / C 队列
10. 规范代表映射
11. 预注册文件路径
12. 预注册哈希
13. 预注册 commit
14. 数据快照哈希
15. 锁定样本分割
16. 锁定样本是否无污染
17. 信号层结果摘要
18. 成本压力结果摘要
19. Walk-forward 结果摘要
20. 风险模型结果摘要
21. Monte Carlo 结果摘要
22. 币种集中度摘要
23. 月份集中度摘要
24. 市场状态摘要
25. A 级每个候选最终状态
26. B 级候选最终状态
27. C 级候选预筛状态
28. 继续归档候选
29. 信号通过但风险失败候选
30. 证据闭环通过候选
31. 建议创建新版本的候选
32. 是否超过 2 个新版本建议
33. 是否修改归档策略
34. 是否修改信号参数
35. 是否边看锁定结果边调参
36. 是否删除亏损币后重跑
37. 是否保存 API Key
38. 是否接 Trade API
39. 是否接 Withdraw API
40. 是否读取真实账户
41. 是否读取真实持仓
42. 是否创建订单
43. 是否执行 exchange Dry-run
44. 是否自动交易
45. compileall 结果
46. pytest 结果
47. validate_config 结果
48. safety scan 结果
49. git diff --check 结果
50. 结果 commit
51. push 结果
52. tag 结果
53. 已知问题
54. 下一步建议
```

---

# 48. 最终下一步规则

## 没有候选通过

输出：

```text
当前风险模型候选全部继续归档。
停止从旧归档库复活策略。
下一步转向新的数据假设或仅继续本地前向样本积累。
```

## 只有信号层通过、风险层失败

输出：

```text
保留为风险研究候选。
不得创建执行版本。
下一步只研究组合风险、仓位和退出路径。
```

## 证据闭环通过 1～2 个家族

输出：

```text
建议为通过家族创建新的不可变 research-only 策略版本。
新版本仍不具备执行资格。
下一步进入本地前向观察 / 本地模拟闭合样本积累。
```

---

# 49. 最终指令

请严格执行本提示词。

不要继续批量生成新策略。

不要修改归档候选的信号逻辑。

不要通过调参强行让候选过关。

先预注册，再看锁定样本。

统一成本，统一风险模型。

按策略家族去重，不把自动优化父子版本当作独立成功。

最终最多建议 1～2 个新研究版本。

即使证据闭环通过，也必须保持：

```text
executionEligible=false
dryRunApproved=false
liveTradingApproved=false
```

本任务的目标不是“找一个一定通过的策略”，而是：

```text
用未污染数据和统一风险框架，
判断少数历史正边际候选是否真的存在可重复优势。
```
