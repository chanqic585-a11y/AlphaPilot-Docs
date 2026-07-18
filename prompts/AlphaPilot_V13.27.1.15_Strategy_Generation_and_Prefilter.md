# AlphaPilot V13.27.1.15
# Advisory-R 多假设策略生成、退出政策冻结与一级快速预筛

## 启动门

V13.27.1.14 必须：

```text
全部测试通过
Quant / Console / Docs 已提交推送
exit-policy artifacts 已冻结
legacy parity 通过
```

---

## 一、目标

生成：

```text
6–8 个独立市场机制家族
8–12 条初始策略代码
```

每个候选必须拥有：

```text
一个机制匹配的 primary exitPolicy
```

禁止：

```text
同一信号跑完四种退出后只保留最好结果
```

---

## 二、候选定义

每条候选：

```text
familyId
variantId
humanHypothesisZh
falsificationCriteriaZh
featureDefinition
entryDefinition
initialStopDefinition
exitPolicy
exitPolicyRationaleZh
maximumHold
simpleBenchmark
complexityScore
semanticFingerprint
strategyDefinitionHash
exitPolicyHash
originLineage
```

---

## 三、候选家族和主退出模式

### S01 熊市特异性抛售恢复 4H

```text
mode=hybrid
```

机制：

```text
熊市
相对 BTC / 核心池极端负残差
价格收回
```

退出：

```text
一次预注册部分退出
剩余仓位在残差/结构恢复或时间到期退出
```

这是 V13.27.1.13 熊市事后线索形成的新 Trial。

### S02 BTC Lead-Lag 同向延续 1H

```text
mode=partial_then_trailing
```

### S03 BTC Lead-Lag 过度反应回归 1H

```text
mode=structure_or_time
```

S02/S03 同一家族。

### S04 高相关币对残差均值回归

```text
mode=structure_or_time
```

结构目标：

```text
残差回到预注册中性区
```

### S05 相关性破裂后的关系恢复 4H

```text
mode=hybrid
```

### S06 相关性破裂后的脱钩延续 4H

```text
mode=partial_then_trailing
```

S05/S06 同一家族。

### S07 波动冲击方向非对称 4H

```text
mode=partial_then_trailing 或 hybrid
```

只能在预注册前二选一，不得结果后改变。

### S08 UTC 时段转换效应 1H

```text
mode=fixed_r
```

允许预注册：

```text
targetR < 2
```

同时有最大持有期和时段结束退出。

### S09 Beta 防御轮动 4H

```text
mode=structure_or_time
```

属于组合策略，不要求单币固定 R。

### S10 正交弱信号矩阵 4H

```text
mode=fixed_r 或 structure_or_time
diagnosticOnly=true
```

结果前冻结。

---

## 四、退出参数预算

每个候选：

```text
只允许一个 primary exitPolicy
```

如同一家族确需两个退出政策：

```text
必须作为两个 candidate variant
总候选数仍 <=12
同一家族变体 <=2
全部计入 Trial Ledger
```

禁止：

```text
结果后切换模式
结果后调 partial fraction
结果后调 targetR
结果后调 ATR trail
结果后调 maxHold
```

---

## 五、Trial Ledger

字段新增：

```text
exitPolicyVersion
exitPolicyMode
exitPolicyHash
exitPolicyParentTrialId
```

所有已读取结果的退出策略都计 Trial。

---

## 六、预注册

生成：

```text
research/preregistrations/<campaignId>_prefilter_v2.json
```

必须记录：

```text
targetRGateMode=advisory
minimumTargetR=null
exitPolicyRequired=true
所有 candidate exitPolicy
ExitPolicyBounds Hash
```

先 Commit、Push，再运行结果。

---

## 七、一级经济预筛

代表币：

```text
8–12 个
```

事件策略：

```text
PF >= 1.03
平均 realizedNetR > 0
总 realizedNetR > 0
正月份 >= 50%
最大回撤 <= 35%
最低样本和历史覆盖通过
```

组合策略：

```text
成本后净收益 > 0
正月份 >= 50%
最大回撤 <= 35%
```

不得检查：

```text
targetR >=2
剩余目标 >=2R
```

---

## 八、退出诊断

每个事件策略额外输出：

```text
exitPolicyMode
平均 realizedGrossR
平均 realizedNetR
平均 win R
平均 loss R
payoff ratio
break-even win rate
partial-exit ratio
trailing-exit ratio
structure-exit ratio
time-exit ratio
stop ratio
MFE / MAE
profit giveback
stop-then-recover
```

这些用于解释，不用于结果后改退出。

---

## 九、一级路由

```text
经济失败
→ 直接归档
→ 不改 exitPolicy
→ 不运行高级统计

经济通过
→ 最多 6 条进入 V16
```

每家族最多 1 条。

同家族选择顺序：

```text
更低复杂度
更低换手
更低回撤
更高简单基准增量
稳定 ID
```

---

## 十、输出

```text
strategy_inventory.json
novelty_audit.json
trial_ledger.json/csv
representative_universe.json
prefilter_results.json
exit_policy_attribution.json
simple_benchmarks.json
prefilter_gate_matrix.json
archived_prefilter_failures.json
prefilter_summary.md
```

---

## 十一、自检

```text
策略代码数
家族数
exit policy mode 分布
targetR <2 候选数
targetR=null 候选数
exitPolicyHash 唯一数
一级幸存数
是否结果后改变退出
Holdout=0
Release=0
ARM=false
Order=0
```
