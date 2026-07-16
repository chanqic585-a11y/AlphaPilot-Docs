# AlphaPilot V13.27.1.12 修订版
# 正式数据覆盖修复、历史 PIT 币池建设与 Qlib 启动门

## 给 Codex 的完整执行提示词（中文代号版）

> **本文件替代此前的 V13.27.1.12 Qlib 真实模型 Campaign 提示词。**
>
> V13.27.1.11 已经完成并得到：
>
> ```text
> status = data_not_ready
> campaignMayRun = false
> 正式就绪顶层方向 = 0 / 2
> 正式试验 = 0
> Clean Holdout 未解锁
> Release = 0
> Demo ARM = false
> Order = 0
> ```
>
> 这不是策略失败结论，而是研究数据就绪门正确失败关闭的结果。
>
> 当前不能直接运行 Qlib 真实模型 Campaign，因为 Qlib 重点服务的横截面方向 C 仍缺正式历史 PIT 币池；A/B 方向也缺同交易所 OI、Basis 和真实清算证据。
>
> 本版本只解决：
>
> ```text
> 正式数据能否被可靠取得、补齐、审计、冻结，
> 并使至少两个顶层研究方向达到 formal_ready。
> ```
>
> 本版本不生成策略、不运行正式策略 Campaign、不解锁 Holdout、不进入 Demo、不创建订单。
>
> 如果数据仍不可获得，必须如实输出 `data_not_ready`，不能通过代理、跨交易所拼接或降低覆盖门槛强行放行。

---

# 1. 任务名称

```text
AlphaPilot V13.27.1.12
正式数据覆盖修复、历史 PIT 币池建设与 Qlib 启动门
```

中文简称：

```text
补正式数据，打开研究门
```

建议 Quant Tag：

```text
v13.27.1.12
```

建议 Docs Tag：

```text
v13.27.1.12-docs
```

此前生成的：

```text
AlphaPilot V13.27.1.12
Qlib 横截面研究适配器、固定模型增量基准与新鲜留出验证
```

应改为后续版本建议：

```text
V13.27.1.13
```

并标记：

```yaml
status: deferred
blockedBy: v13.27.1.12-data-readiness
```

---

# 2. V13.27.1.11 不可变基线

必须读取并校验：

```text
Quant commit：916b3d9
Quant tag：v13.27.1.11

Docs commit：90d4f02
Docs tag：v13.27.1.11-docs
```

必须读取实际原件：

```text
reports/derivatives_data/data_readiness.json
reports/research_factory_repair/campaign_stop_decision.json
reports/derivatives_data/artifact_manifest.json
reports/reproducibility/environment_manifest.json
docs/V13.27.1.11-research-factory-data-readiness-closeout.md
```

不得依赖猜测文件名。

必须生成输入映射：

```text
reports/v13_27_1_12/input_artifact_mapping.json
reports/v13_27_1_12/input_artifact_mapping.md
```

字段：

```text
logicalRole
actualPath
exists
contentHash
schemaFingerprint
selectedBy
selectionReason
ambiguousCandidates
```

任何必需输入缺失或歧义：

```text
status = blocked_input_mapping
停止
```

---

# 3. 当前已知缺口

V13.27.1.11 已确认：

```text
数据源文件：32
SHA 不一致：0
Public API probes：6
正式就绪顶层方向：0 / 2
```

主要缺口：

```text
历史 Open Interest
真实清算数据
同交易所 Funding / OI / Spot / Perpetual / Basis 链
历史 Point-in-Time 可交易资产池
```

此外：

```text
Docker daemon / image 当前不可用
```

本版本不得声称已完成 Docker 复现，除非真实完成并记录镜像 Digest。

---

# 4. 顶层研究方向与数据优先级

研究方向维持两个顶层家族、三个方向：

```text
顶层 1：stress_reversal
  A：特异性抛售耗竭做多
  B：空头拥挤解除做多

顶层 2：cross_sectional_momentum
  C：PIT 高流动性横截面中期动量
```

本版本按以下顺序补数据：

## 优先级一：方向 B

```text
同交易所永续 OHLCV
同交易所 Funding
同交易所 Open Interest
同交易所现货 OHLCV
Spot / Perpetual Basis
合约状态
Spread / 流动性
```

理由：

```text
这些字段同时服务 B、部分 A 和未来 Qlib 衍生品特征。
```

## 优先级二：方向 C

```text
历史 PIT 可交易合约池
上市 / 下架 / 暂停时间
历史成交额
历史 OI
历史 Spread / 深度代理
当时流动性入选与排除原因
```

理由：

```text
C 是 Qlib 下一阶段的主要输入；
没有正式 PIT，Qlib 真实横截面 Campaign 不得启动。
```

## 优先级三：方向 A1

```text
真实历史清算数据
```

如果真实清算无法取得：

```text
A1 = unavailable
A2 = proxy_only
```

A2 只能用于：

```text
provisional research
```

永远不能因代理数据获得 Formal Pass。

---

# 5. 仓库范围

主要修改：

```text
D:\Codex-Workspace\AlphaPilot-Quant-Engine
D:\Codex-Workspace\AlphaPilot-Docs
```

只读：

```text
D:\Codex-Workspace\AlphaPilot-Control-Console
D:\Codex-Workspace\trade-discipline-journal
```

不得修改：

```text
Demo Runtime
Release Store
Approval Store
Order Engine
Mobile App
```

---

# 6. 工作区保护

Quant 当前只保留用户既有归档报告修改。

必须：

```text
记录 preExistingChanges
不 reset
不覆盖
不 stage
不 amend
```

如当前 main 不干净：

```text
使用独立干净 worktree
```

Console 的：

```text
backups/
```

保持不变，不纳入本任务。

---

# 7. 总安全边界

禁止：

```text
保存 API Key
接 Trade API
接 Withdraw API
读取账户
读取持仓
创建 Demo / Live 订单
执行 exchange Dry-run
自动 ARM
自动交易
恢复完整本地模拟
解锁策略 Holdout
运行正式策略 Campaign
```

允许：

```text
访问公开市场数据接口
下载公开历史数据
建立本地只读数据集
运行数据质量与因果审计
运行合成 / 小样本适配测试
```

如果某数据源需要：

```text
付费授权
私有 API
账户级数据
```

则：

```text
只记录 capability_gap
等待用户另行批准
不得擅自购买或使用凭证
```

---

# 8. 版本成功标准

本版本只有以下三种合法结论：

## 结果 A：至少两个顶层方向 formal_ready

```text
status = data_ready
qlibCampaignMayRun = true
threeDirectionCampaignMayRun = true
```

## 结果 B：只有一个顶层方向 formal_ready

```text
status = partial_data_ready
qlibCampaignMayRun = false
threeDirectionCampaignMayRun = false
```

## 结果 C：零个方向 formal_ready

```text
status = data_not_ready
qlibCampaignMayRun = false
threeDirectionCampaignMayRun = false
```

禁止：

```text
将 provisional_ready 计入 formal_ready
```

---

# 9. 任务 1：公开数据接口能力审计

建议新增：

```text
alphapilot/derivatives_data/api_capability_audit.py
alphapilot/derivatives_data/source_capability_schema.py
tests/derivatives_data/test_api_capability_audit.py
```

生成：

```text
reports/v13_27_1_12/api_capability_audit.json
reports/v13_27_1_12/api_capability_summary.md
```

每个源记录：

```text
provider
exchange
endpointOrArchive
dataType
marketType
requiresAuth
publicOnly
licenseOrUsageTerms
earliestAvailable
latestAvailable
symbolCoverage
granularity
pagination
rateLimit
maximumLookback
historicalCompleteness
pointInTimeSemantics
knownLimitations
probeStatus
```

必须审计：

```text
Perpetual OHLCV
Spot OHLCV
Funding
Open Interest
Liquidation
Instrument history
Listing / Delisting
Trading state
24h volume history
Spread / orderbook snapshots
```

规则：

```text
API 当前可返回数据 ≠ 历史正式可用。
```

只有历史深度、字段语义、可得时间和许可证都明确，才能进入候选数据源。

---

# 10. 任务 2：正式数据源选择政策

建议新增：

```text
alphapilot/derivatives_data/source_selection_policy.py
tests/derivatives_data/test_source_selection_policy.py
```

优先：

```text
同一交易所官方公共数据
```

候选主交易所：

```text
OKX
```

如果 OKX 某方向历史数据不足，可在预注册前选择另一单一交易所作为该方向完整核心链。

硬规则：

```text
同一方向正式核心字段不得跨交易所拼接。
```

例如 B：

```text
OHLCV
Funding
OI
Spot
Perpetual
Basis
```

必须属于同一交易所链。

允许跨源的仅限：

```text
非核心诊断基准
```

且不得进入 Formal Gate。

---

# 11. 任务 3：断点续传与缺口修复

建议新增：

```text
alphapilot/derivatives_data/resumable_collector.py
alphapilot/derivatives_data/checkpoint_store.py
alphapilot/derivatives_data/deduplication.py
alphapilot/derivatives_data/gap_repair.py
tests/derivatives_data/test_resumable_collector.py
tests/derivatives_data/test_deduplication.py
tests/derivatives_data/test_gap_repair.py
```

数据根目录：

```text
D:\Codex-Workspace\回测数据
```

目录：

```text
raw/<provider>/<exchange>/<dataType>/
normalized/<exchange>/<dataType>/
derived/<exchange>/
manifests/
snapshots/
```

要求：

```text
checkpoint
resume
bounded retry
rate-limit backoff
existing-file scan
stable primary key
duplicate removal
gap detection
gap-only download
partial-file validation
atomic finalize
```

主键至少：

```text
exchange
instrumentId
dataType
timestampUtc
```

不得：

```text
整库覆盖用户已有数据
```

---

# 12. 任务 4：下载预算与磁盘预算

必须预注册：

```text
maximumRequestsPerMinute
maximumRetriesPerPage
maximumTotalRequests
maximumDownloadBytes
minimumFreeDiskBytes
maximumRunHours
```

生成：

```text
reports/v13_27_1_12/download_budget.json
reports/v13_27_1_12/download_resume_manifest.json
reports/v13_27_1_12/deduplication_report.json
reports/v13_27_1_12/gap_repair_report.json
```

磁盘不足：

```text
停止
不删除历史数据
```

接口限制：

```text
遵守公开速率限制
不绕过
```

---

# 13. 任务 5：严格可得时间

复用并验证 V13.27.1.11 的因果规则。

每行必须记录：

```text
eventTimestamp
observedAt
publishedAt
availableAt
sourceTimestamp
publicationLagSeconds
```

## Funding

```text
结算值只能在结算后可用
预测 Funding 必须证明当时公开可得
```

## OI

```text
使用真实发布时间
若无法证明，至少滞后一采样周期
```

## Instrument / PIT

```text
上市、下架、暂停状态按实际生效和可得时间
```

## 流动性

```text
只能使用已完成窗口
不能使用当前未完成 24h 数据
```

---

# 14. 任务 6：方向 B 数据链

内部资格 ID：

```text
short_crowding_unwind_data_chain
```

正式必需：

```text
同交易所永续 OHLCV
同交易所 Funding
同交易所 Open Interest
同交易所现货 OHLCV
Spot / Perpetual Basis
Instrument state
Spread / Slippage evidence
```

建议最低覆盖门：

```text
历史跨度 >= 24 个月
至少 20 个符合条件的永续合约
核心字段时间覆盖 >= 95%
单合约核心字段缺失率 <= 2%
未解释长缺口 = 0
未来泄漏 = 0
```

如果实际历史只能更短：

```text
保持 partial
不能降低门槛后称 formal
```

生成：

```text
reports/v13_27_1_12/family_b_data_chain.json
```

---

# 15. 任务 7：方向 C 历史 PIT 币池

内部资格 ID：

```text
pit_cross_sectional_universe
```

必须建设真正的：

```text
历史时点可投资币池
```

---

## 15.1 PIT 字段

```text
snapshotTimeUtc
instrumentId
listedAt
delistedAt
tradingState
quoteVolume24h
openInterestQuote
spreadBpsOrFormalProxy
listingAgeDays
dataQualityStatus
included
reasonZh
sourceHashes
availableAt
```

---

## 15.2 入选规则必须预注册

例如：

```text
USDT perpetual only
当时为 live
上市时长 >= 90 天
24h quote volume 达到分位门槛
OI 达到分位门槛
Spread 不超过门槛
核心 OHLCV 完整
```

具体阈值只能在数据质量层定义，不得根据策略收益调整。

---

## 15.3 正式覆盖门

建议：

```text
历史跨度 >= 24 个月
每日或预注册频率 PIT Snapshot 覆盖 >= 95%
中位可投资合约数 >= 30
大多数日期可投资合约数在 30–50 或以上
Research / Holdout Symbol Split 均非空
每个 Universe Split >= 8 个币
当前 TopN 回填历史 = false
```

如只有从现在开始采集的 PIT：

```text
future_collection_ready=true
historical_formal_ready=false
```

不能运行历史正式 Qlib Campaign。

生成：

```text
reports/v13_27_1_12/pit_universe_audit.json
reports/v13_27_1_12/pit_universe_manifest.json
reports/v13_27_1_12/pit_universe_coverage.csv
reports/v13_27_1_12/pit_universe_coverage.json
```

---

# 16. 任务 8：真实清算与代理分离

数据状态：

```text
real_liquidation
independently_validated_liquidation
proxy_liquidation
unavailable
```

真实清算至少记录：

```text
timestampUtc
instrumentId
side
notional
quantity
price
source
availableAt
```

代理只能包含：

```text
OI 下降
成交量异常
ATR 扩张
影线
价格收回
```

代理字段必须使用独立命名：

```text
liquidationProxyScore
```

禁止命名为：

```text
liquidationNotional
```

除非是真实清算。

A1 Formal Ready 要求：

```text
真实或独立验证清算覆盖达标
```

A2：

```text
最高 provisional_ready
```

---

# 17. 任务 9：Basis 构建

Basis 必须来自同交易所：

```text
Spot price
Perpetual price
```

字段：

```text
timestampUtc
baseAsset
spotInstrumentId
perpetualInstrumentId
spotPrice
perpetualPrice
basisPct
annualizedBasisProxy
sourceTimeDifferenceSeconds
availableAt
```

要求：

```text
只允许 backward as-of join
最大时间差预注册
超过时标记 stale
```

禁止：

```text
nearest join 命中未来
```

---

# 18. 任务 10：正式 / 临时数据资格

建议新增：

```text
alphapilot/derivatives_data/data_readiness_gate.py
tests/derivatives_data/test_data_readiness_gate.py
```

状态：

```text
unavailable
diagnostic_ready
provisional_ready
formal_ready
```

每个方向分别报告：

```text
A1
A2
B
C
```

顶层家族：

```text
stress_reversal
cross_sectional_momentum
```

计数规则：

```text
只有至少一个子方向 formal_ready，
顶层家族才计 formal_ready。
```

A2 不能使 stress_reversal 正式就绪，除非 B formal_ready。

---

# 19. 任务 11：数据质量 Gate

每种数据检查：

```text
Schema
时间单调性
重复
缺口
缺失
零值异常
极值
单位
时区
陈旧
未来数据
符号映射
上市状态
来源
许可证
内容 Hash
```

生成：

```text
reports/v13_27_1_12/data_quality_by_source.json
reports/v13_27_1_12/data_quality_by_instrument.csv
reports/v13_27_1_12/data_quality_by_instrument.json
```

缺失不得填 0。

---

# 20. 任务 12：冻结 Snapshot

只有完成全部审计后才能冻结。

生成：

```text
research/data_snapshots/<snapshotId>.json
reports/v13_27_1_12/snapshot_manifest.json
```

记录：

```text
snapshotId
sourceManifestHashes
normalizedHashes
derivedHashes
PIT Hash
familyReadiness
createdAt
GitCommit
environmentManifestHash
```

Snapshot 不包含：

```text
任何策略结果
任何 Holdout 结果
```

---

# 21. 任务 13：Qlib 最小启动门

本版本不运行真实 Qlib 模型 Campaign。

只允许：

```text
固定 Qlib Commit 审计
MIT License 记录
隔离环境计划或可选构建
合成数据 Dataset Adapter 测试
小型非正式 Snapshot Parity 测试
```

Qlib 固定参考：

```text
d5379c520f66a39953bad76234a7019a72796fd0
```

生成：

```text
reports/v13_27_1_12/qlib_preflight.json
reports/v13_27_1_12/qlib_readiness_gate.json
```

真实 Qlib Campaign 启动要求：

```text
C formal_ready = true
PIT 合约数 >= 30
新鲜 Holdout 可建立
Snapshot Hash 可验证
```

否则：

```text
qlibCampaignMayRun=false
```

---

# 22. 任务 14：未来数据连续采集

即使历史正式数据无法补齐，也要建立未来只读连续采集器。

采集：

```text
OI
Funding
Spot
Perpetual
Basis
Instrument state
PIT liquidity
Spread
可用时的 Liquidation
```

要求：

```text
append-only
可恢复
可审计
不连接账户
不创建订单
```

生成：

```text
reports/v13_27_1_12/forward_collection_plan.json
reports/v13_27_1_12/forward_collection_status.json
```

但：

```text
未来采集状态不能冒充历史 formal_ready
```

---

# 23. 任务 15：最终 Campaign 启动决策

生成：

```text
reports/v13_27_1_12/data_readiness.json
reports/v13_27_1_12/campaign_start_decision.json
reports/v13_27_1_12/qlib_start_decision.json
reports/v13_27_1_12/data_readiness_summary.md
```

字段：

```text
A1Status
A2Status
BStatus
CStatus
stressReversalFormalReady
crossSectionalMomentumFormalReady
formalTopLevelDirectionCount
threeDirectionCampaignMayRun
qlibCampaignMayRun
blockers
nextAction
```

规则：

```text
formalTopLevelDirectionCount >= 2
→ threeDirectionCampaignMayRun=true

CStatus=formal_ready
→ qlibCampaignMayRun=true
```

本版本仍然：

```text
不运行 Campaign
```

---

# 24. 环境可复现

必须更新：

```text
reports/reproducibility/environment_manifest.json
```

记录：

```text
OS
Python executable
Python version
pandas
NumPy
PyArrow
Freqtrade
Docker daemon status
Docker image
dependency lock hash
random seeds
timezone
locale
```

Docker 不可用时：

```text
dockerReproducibilityPassed=false
reason=daemon_or_image_unavailable
```

不得声称通过。

---

# 25. CSV / JSON / Parquet

Windows CSV 必须：

```text
UTF-8
固定 LF
稳定列顺序
稳定排序
```

重要表必须有：

```text
CSV
canonical JSON
SHA-256 sidecar
```

Parquet 只有在：

```text
固定 PyArrow 版本
依赖锁记录
```

时输出。

没有 PyArrow：

```text
不声称生成 Parquet
```

---

# 26. 建议代码结构

```text
alphapilot/derivatives_data/
alphapilot/qlib_adapter/preflight.py
```

建议脚本：

```text
scripts/audit_derivatives_data_capabilities.ps1
scripts/collect_formal_derivatives_data.ps1
scripts/build_historical_pit_universe.ps1
scripts/freeze_derivatives_snapshot.ps1
scripts/run_qlib_preflight.ps1
```

默认：

```text
只打印计划
```

显式：

```text
-Run
```

才联网或写数据。

---

# 27. 提交顺序

至少五个提交。

## Commit 1

```text
Map V13.27.1.11 evidence and audit public data capabilities
```

## Commit 2

```text
Build resumable same-exchange derivatives collectors
```

## Commit 3

```text
Build historical point-in-time universe and data-quality gates
```

## Commit 4

```text
Freeze formal derivatives data snapshot and Qlib preflight
```

## Commit 5

```text
Record V13.27.1.12 data-readiness decisions
```

每步：

```text
测试
Commit
Push
确认工作区
```

---

# 28. 测试要求

至少覆盖：

```text
输入映射缺失失败
现有数据不被覆盖
断点续传
重复去除
缺口补齐
限速和重试
磁盘预算

同交易所核心链
跨交易所正式拼接拒绝
Funding 可得时间
OI 可得时间
Basis backward as-of
PIT 不使用未来

当前 TopN 回填历史拒绝
上市 / 下架状态
流动性 Snapshot
Universe Split 非空

真实清算与代理字段分离
代理清算不能 Formal Ready

Formal / Provisional / Diagnostic 状态
顶层方向计数
少于两个方向关闭 Campaign
C 未正式就绪关闭 Qlib Campaign

CSV 固定 LF
SHA sidecar 稳定
Docker 未运行不声称通过
Holdout 不被访问
无策略试验
无订单
```

---

# 29. 验证命令

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine

python -m pytest tests/derivatives_data -q
python -m pytest tests/qlib_adapter -q
python -m pytest tests -q
python -m compileall alphapilot
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

数据能力审计：

```powershell
powershell -ExecutionPolicy Bypass -File scripts\audit_derivatives_data_capabilities.ps1 -Run
```

公开数据采集：

```powershell
powershell -ExecutionPolicy Bypass -File scripts\collect_formal_derivatives_data.ps1 -Run
```

PIT 建设：

```powershell
powershell -ExecutionPolicy Bypass -File scripts\build_historical_pit_universe.ps1 -Run
```

冻结：

```powershell
powershell -ExecutionPolicy Bypass -File scripts\freeze_derivatives_snapshot.ps1 -Run
```

---

# 30. Codex 最终自检

必须输出：

```text
1. 实际版本号
2. Quant commit / tag
3. Docs commit / tag
4. preExistingChanges
5. V13.27.1.11 输入映射

6. Public API / Archive probes
7. 数据源候选数
8. 选定主交易所
9. 各数据类型最早 / 最晚时间
10. License / Usage 状态

11. 下载请求数
12. 断点恢复次数
13. 重试次数
14. 下载字节
15. 磁盘预算
16. 重复删除数
17. 缺口数
18. 未修复缺口

19. OHLCV 状态
20. Funding 状态
21. OI 状态
22. Spot 状态
23. Perpetual 状态
24. Basis 状态
25. Liquidation 状态
26. Spread 状态
27. Instrument History 状态

28. PIT Snapshot 数
29. PIT 历史跨度
30. PIT 覆盖率
31. 中位可投资币数
32. Research Symbols
33. Holdout Symbols
34. Universe Split

35. A1 状态
36. A2 状态
37. B 状态
38. C 状态
39. stress_reversal 正式就绪
40. cross_sectional_momentum 正式就绪
41. 正式顶层方向数

42. Snapshot ID
43. Snapshot Hash
44. Snapshot Commit

45. Qlib Commit
46. Qlib Preflight
47. qlibCampaignMayRun
48. threeDirectionCampaignMayRun

49. Holdout 访问次数
50. 正式策略 Trial 数
51. Release 数
52. Demo ARM
53. Order 数

54. 是否跨交易所拼接正式核心数据
55. 是否使用代理数据 Formal Ready
56. 是否使用今天 TopN 回填历史
57. 是否填充缺失为 0
58. 是否访问 API Key
59. 是否接 Trade API
60. 是否接 Withdraw API
61. 是否读取账户
62. 是否创建订单
63. 是否自动交易

64. pytest
65. compileall
66. validate_config
67. safety scan
68. git diff --check
69. Push
70. Tag
71. 已知问题
72. 下一步建议
```

---

# 31. 停止规则

## 输入映射失败

```text
立即停止
```

## 公开源能力不足

```text
记录 unavailable
不伪造
```

## 下载无法完成

```text
保留 checkpoint
输出 partial
不冻结 formal Snapshot
```

## PIT 历史无法重建

```text
C != formal_ready
Qlib Campaign 不启动
```

## OI / Basis 链不完整

```text
B != formal_ready
```

## 真实清算不可用

```text
A1 != formal_ready
A2 最高 provisional
```

## 正式顶层方向少于 2

```text
Campaign 继续关闭
```

---

# 32. 下一版本路由

## 如果 B 与 C formal_ready

执行：

```text
V13.27.1.13
Qlib 横截面研究适配器、固定模型增量基准
与新鲜留出验证
```

同时可独立安排：

```text
stress_reversal + cross_sectional_momentum 正式 Campaign
```

但仍需新预注册。

## 如果只有 B formal_ready

```text
继续收集 PIT
不运行三方向 Campaign
不运行真实 Qlib Campaign
```

## 如果只有 C formal_ready

```text
可以准备 Qlib Adapter
但是否运行单家族 Qlib Campaign必须由用户单独批准；
不能冒充三方向 Campaign。
```

## 如果仍为 0

```text
暂停历史正式策略 Campaign
继续未来数据积累
评估是否需要用户批准外部历史数据授权
```

---

# 33. 最终原则

请严格执行：

```text
V13.27.1.11 已经证明 Gate 正常关闭，
V13.27.1.12 只负责补齐研究入口数据。
```

必须坚持：

```text
data_not_ready 不是策略失败
没有运行 Campaign，就不能评价策略 Alpha

正式数据必须同交易所
代理数据不能正式晋级
历史 PIT 不能由今天 TopN 回填

Qlib 必须等待 C formal_ready
Holdout 保持未解锁
本版本不生成策略、不回测策略、不创建 Release

最终仍为 data_not_ready，
也是有效且可信的结果
```
