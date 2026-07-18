# AlphaPilot V13.27.1.14
# Advisory-R 退出政策实现、模拟器接入与 Demo 准入兼容

## 一、目标

在运行任何新策略 Campaign 前，完整实现：

```text
advisory_r_exit_policy_v1
```

必须解决当前真实问题：

```text
CandidateSpec 拒绝 targetR < 2
正式模拟器固定按 candidate.targetR 退出
partial-exit helper 未接入正式主流程
Console Demo 将 <2R 视为阻塞
```

本版本不生成新策略、不运行结果型 Campaign、不打开 Holdout、不创建 Release。

---

## 二、Git 和基线

必须验证：

```text
Quant main / origin/main
Console main / origin/main
Docs main / origin/main
383a54f 所在分支/worktree
preExistingChanges
```

用户既有未提交文件：

```text
不 reset
不覆盖
不 stage
不 amend
```

使用独立 worktree。

---

## 三、迁移库存

新增：

```text
alphapilot/exit_policy/migration_inventory.py
reports/exit_policy/migration_inventory.json
reports/exit_policy/migration_inventory.md
```

扫描：

```text
minimumTargetR
targetR < 2
target >= 2
>=2R
riskPolicy.minimumTargetR
CandidateSpec
build_event_exit_geometry
targetReference
remaining target
Demo admission blocker
UI wording
```

每个命中分类：

```text
active_code
active_contract
active_ui
historical_preregistration
historical_report
immutable_release
documentation
test
```

规则：

```text
只修改 active 范围
历史和不可变文件不修改
```

---

## 四、退出政策模型

新增建议：

```text
alphapilot/exit_policy/
  __init__.py
  models.py
  schema.py
  validation.py
  canonical.py
  engine.py
  exit_legs.py
  legacy_adapter.py
  reporting.py
```

### 4.1 通用 Envelope

```text
policyVersion
mode
maximumHoldBars
parameters
exitPolicyHash
```

### 4.2 fixed_r

```text
targetR > 0
maximumHoldBars > 0
```

target 可以：

```text
<2
=2
>2
```

### 4.3 partial_then_trailing

```text
partialAtR > 0
0 < partialFraction < 1
trailingAtrMultiple > 0
trailingReference
maximumHoldBars
trailingEffectiveFrom=next_bar_after_partial
```

### 4.4 structure_or_time

```text
structureRuleId
structureRuleHash
structureExecution=next_bar_open
maximumHoldBars
```

结构规则必须来自：

```text
受限、确定性表达
```

不得传任意 Python Callback。

### 4.5 hybrid

```text
partialAtR
partialFraction
remainderMode=trailing|structure_or_time
remainderParameters
maximumHoldBars
```

只允许一次预注册分批腿。

### 4.6 有界参数

新增中央：

```text
ExitPolicyBounds
```

所有上下限必须：

```text
在结果前冻结
从现有工程安全常量或明确设计理由得到
不能根据策略表现选择
```

拒绝：

```text
负数
零风险参数
无限持有
partialFraction <=0 或 >=1
无限 trailing
缺失结构 Hash
```

---

## 五、CandidateSpec v2

新 schema 必须记录：

```json
{
  "riskMetric": "R",
  "targetRGateMode": "advisory",
  "minimumTargetR": null,
  "initialStopMayWiden": false,
  "exitPolicyRequired": true,
  "exitPolicyVersion": "advisory_r_exit_policy_v1"
}
```

新候选必须：

```text
有 initialStop
有 exitPolicy
有 exitPolicyHash
策略定义 Hash 包含 exitPolicy
```

`targetR`：

```text
fixed_r 时可报告
其他模式可为 null
不参与 pass/fail
```

---

## 六、Legacy 兼容

旧候选：

```text
targetR 存在
exitPolicy 不存在
```

内存解释为：

```text
legacy fixed_r
```

要求：

```text
原始 bytes 不变
原 Hash 不变
原回测事件不变
原 Release 不变
```

生成：

```text
reports/exit_policy/legacy_compatibility_manifest.json
```

---

## 七、模拟引擎语义

### 7.1 入场

```text
默认下一根 K 线开盘
```

### 7.2 同 K 线优先级

```text
1. adverse gap / stop
2. current stop
3. positive fixed / partial target
4. bar-close structure decision
5. time decision
```

结构和时间：

```text
下一根 K 线开盘执行
```

### 7.3 Trailing

```text
只能收紧
不能低于当前/初始保护水平
只由已完成 K 线更新
更新后下一根 K 线生效
```

禁止：

```text
用当前高点收紧后在同一 K 线低点触发
```

### 7.4 Partial

每个 leg：

```text
exitLegId
fraction
triggerType
triggerTimestamp
executionTimestamp
executionPrice
grossR
feesR
slippageR
spreadR
fundingR
netR
```

总结果：

```text
weighted gross R
weighted costs
weighted net R
```

### 7.5 Gap

必须复用并明确现有保守 Gap Policy。

至少覆盖：

```text
stop_gap
target_gap
partial_gap
trailing_gap
```

---

## 八、报告升级

每笔事件必须包含：

```text
entry
initialStop
initialRisk
exitPolicyVersion
exitPolicyMode
exitPolicyHash
advisoryTargetR
exitLegs
realizedGrossR
feesR
slippageR
spreadR
fundingR
realizedNetR
MFE
MAE
maximumHoldBars
```

新增：

```text
reports/exit_policy/exit_policy_schema.json
reports/exit_policy/simulation_parity_report.json
```

---

## 九、Freqtrade Parity

事件引擎与 Freqtrade 对 event 和 exit leg 做一致性检查。

必须 100% 一致：

```text
signalId
symbol
direction
entryTimestamp
legCount
legFraction
exitReason
triggerTimestamp
executionTimestamp
```

数值容差只用于：

```text
price
grossR
costR
netR
```

任意无法解释的 missing/extra leg：

```text
失败关闭
```

---

## 十、Preregistration 和报告

新 Preregistration：

```text
schema v2
policyVersion
exitPolicy
exitPolicyHash
targetRGateMode=advisory
minimumTargetR=null
```

旧文件不修改。

报告 UI 字段：

```text
退出方式
目标 R（参考）
实际 Gross R
实际 Net R
```

---

## 十一、Console Demo 边界

Console 必须支持：

```text
legacy v1 fixed-2R Release
advisory-R v2 Release
```

V2 Demo admission 要求：

```text
完整 exitPolicy
Hash 验证
initialStopMayWiden=false
Formal Evidence 完整
```

不得要求：

```text
targetR >=2
```

UI：

```text
退出方式：固定 R / 分批后追踪 / 结构或时间 / 混合
目标 R：参考指标，不作为通过硬门槛
实际净 R：成本后结果
```

Live：

```text
保持旧准入行为
本版本不允许 non-2R 自动进入 Live
```

---

## 十二、测试先行

必须覆盖：

```text
fixed_r 1.0R / 1.5R 被接受
fixed_r >2R 被接受
非固定模式 targetR=null 被接受
缺失 exitPolicy 被拒绝
无界参数被拒绝
初始止损不能放宽
Trailing 不能放宽
Trailing 下一根 K 线生效
同 K 线 Stop-first
Partial weighted R / costs
Structure next-bar execution
Time next-bar execution
Legacy 事件和 Hash 不变
Prereg v2 写 advisory policy
Formal Gate 仍拒绝负期望、弱 PF、过大回撤
Console 接受完整 v2
Console 拒绝可变/不完整 v2
Live 行为不变
```

---

## 十三、提交顺序

建议：

```text
Quant Commit 1：
Add versioned Advisory-R exit policy contracts

Quant Commit 2：
Connect exit policies to formal simulator and reports

Console Commit 1：
Accept Advisory-R Demo evidence without changing Live

Docs Commit：
Document Advisory-R migration and supersession
```

---

## 十四、验收

必须输出：

```text
active 2R hard-gate hit 数
修复数
保留 historical hit 数

fixed_r 测试
partial_then_trailing 测试
structure_or_time 测试
hybrid 测试

legacy byte/hash parity
new schema hash stability
Console v1/v2 import
Live unchanged

新 Campaign 数=0
Holdout access=0
Release=0
ARM=false
Order=0
```

V14 全部通过后才允许执行 V15。
