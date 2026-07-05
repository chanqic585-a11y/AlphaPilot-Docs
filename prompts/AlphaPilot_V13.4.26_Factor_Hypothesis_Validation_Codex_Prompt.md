# AlphaPilot V13.4.26 - Factor Hypothesis Validation Dataset

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.26。

本版本的目标是：**基于 V13.4.25 Strategy Research Factory 生成的 research hypotheses，把高优先级假设转换为可统计验证的条件组合，生成 Hypothesis Validation Dataset 和每个 hypothesis 的历史统计验证报告。**

本版本仍然是 research-only。
本版本不是交易策略实现版。
本版本不是 Freqtrade 策略回测版。
本版本不是 Dry-run 版。
本版本不是实盘版。
本版本不接真实 API Key。
本版本不自动交易。
本版本不创建真实订单。

完成后请输出完整自检报告。

---

# 1. Version Name

```text
AlphaPilot V13.4.26 - Factor Hypothesis Validation Dataset
```

中文版本名：

```text
AlphaPilot V13.4.26 - 因子假设验证数据集版
```

---

# 2. Background

当前已经完成：

```text
V13.4.20：Alpha Factor Research Layer and Benchmark Suite Design
V13.4.21：FactorDataPanel and Manual Factor Library Implementation
V13.4.22：Factor Evaluation Report and Forward Label Analysis
V13.4.23：Benchmark Strategy Suite Implementation and Baseline Research Backtest
V13.4.24：Benchmark Result Review and Strategy Research Reset
V13.4.25：Strategy Research Factory - Factor Hypothesis Mining
```

V13.4.25 已生成：

```text
reports/v13_4_25_strategy_research_factory_report.json
reports/v13_4_25_strategy_research_factory_summary.md
reports/v13_4_25_research_hypotheses.json
```

V13.4.25 结果：

```text
hypotheses: 14
research_only: 9
deferred: 1
rejected: 4
high priority: HYP-001, HYP-002, HYP-004, HYP-006, HYP-007, HYP-008
rejected: HYP-R01, HYP-R02, HYP-R03, HYP-R04
```

V13.4.26 要做的是：

```text
把 high priority hypotheses 变成可验证条件组合。
用历史因子面板和 forward labels 统计每个 hypothesis 是否有统计优势。
```

---

# 3. Core Goal

V13.4.26 的核心目标：

```text
1. 读取 V13.4.25 research hypotheses
2. 读取 V13.4.22 factor evaluation / factor panel / forward labels
3. 将 high priority hypotheses 转换为可评估 condition rules
4. 构建 hypothesis validation dataset
5. 对每个 hypothesis 统计 sampleCount、forward return、TP/SL 命中、PF、expectancy、MFE/MAE
6. 按 pair / month / regime / universe membership 做稳定性分析
7. 输出 hypothesis validation report
8. 输出下一步策略候选建议
9. 不写交易策略
10. 不运行 Freqtrade backtest
11. 不进入 Dry-run
```

一句话目标：

```text
验证“研究假设”有没有统计支持，而不是直接把假设写成交易策略。
```

---

# 4. Required Mindset

请遵守以下原则：

```text
1. hypothesis 不是 strategy
2. 条件组合通过统计验证，也不代表可以交易
3. 本版本只做研究验证，不做交易执行
4. 不把单一高收益样本包装成稳定优势
5. 不用未来数据构造特征
6. forward labels 只能用于评价，不得回流到特征
7. 样本不足必须写 insufficient_sample
8. 所有结论必须引用数据
9. 不进入 Dry-run
10. 不接真实交易能力
```

---

# 5. Non Goals

V13.4.26 不做：

```text
不实现 Freqtrade 交易策略
不运行 Freqtrade backtest
不生成真实交易信号
不修改 AlphaPilotDynamicRegimeV01
不修改 benchmark 策略
不修改 factor evaluation 历史结果
不进入 Dry-run
不实盘
不接真实 API Key
不接 Trade API
不接 Withdraw API
不读取真实账户
不读取真实持仓
不创建真实订单
不自动交易
不修改 AlphaPilot Mobile App
不接新闻数据
不接机器学习模型
不接多模型决策
```

允许做：

```text
读取本地 factor panel
读取 forward labels
读取 research hypotheses
生成 hypothesis validation dataset
生成统计验证报告
生成下一步策略假设建议
更新 README / docs
```

---

# 6. Precondition

执行前必须确认：

```text
1. v13.4.25 tag 已存在
2. reports/v13_4_25_research_hypotheses.json 存在
3. reports/v13_4_25_strategy_research_factory_report.json 存在
4. reports/v13_4_22_factor_evaluation_report.json 存在
5. reports/v13_4_22_factor_candidates.json 存在
6. V13.4.21 factor panel 可读取或可重建
7. 当前工作区干净
```

如果不满足：

```text
停止 V13.4.26
提示先完成 V13.4.25 或缺失输入报告
不要继续
```

如果完整 factor panel 不存在：

```text
允许重新运行 V13.4.21 factor panel builder。
必须说明是否重新生成。
不要只用 200 行 sample 做正式验证。
```

如果无法读取或重建 factor panel：

```text
生成 blocked / insufficient data report
不要伪造 validation result
不要打 v13.4.26 tag
```

---

# 7. Repository Scope

主要修改：

```text
D:\Codex-Workspace\AlphaPilot-Quant-Engine
```

可修改 Docs 仓库归档 prompt：

```text
D:\Codex-Workspace\AlphaPilot-Docs
```

不要修改 App 仓库主逻辑：

```text
D:\Codex-Workspace\trade-discipline-journal
```

---

# 8. Input Files

必须读取：

```text
reports/v13_4_25_research_hypotheses.json
reports/v13_4_25_strategy_research_factory_report.json
reports/v13_4_22_factor_evaluation_report.json
reports/v13_4_22_factor_candidates.json
```

优先读取或重建：

```text
reports/generated/v13_4_21_factor_panel_full.*
reports/v13_4_21_factor_panel_report.json
reports/v13_4_21_factor_panel_sample.json
```

可参考：

```text
reports/v13_4_23_benchmark_suite_report.json
reports/v13_4_24_benchmark_result_review.json
```

如果字段缺失：

```text
写入 warnings
不要伪造
```

---

# 9. Hypotheses To Validate

优先验证 V13.4.25 高优先级：

```text
HYP-001
HYP-002
HYP-004
HYP-006
HYP-007
HYP-008
```

具体含义应从 `reports/v13_4_25_research_hypotheses.json` 中读取，不要硬编码错误含义。

如果读取失败，可使用以下预期方向作为 fallback，但必须写 warning：

```text
HYP-001：Volatility / ATR as Risk Filter
HYP-002：Trend Strength as Regime Filter
HYP-004：Bollinger / Mean Reversion Requires Regime Filter
HYP-006：Low Frequency Requirement
HYP-007：Liquidity Gate First
HYP-008：BuyHoldBTC Benchmark Requirement
```

Rejected hypotheses 不参与验证，只在报告中保留状态。

---

# 10. Hypothesis Condition Rules

新增：

```text
alphapilot/research_factory/hypothesis_validation_rules.py
```

每个 high priority hypothesis 需要转换成可统计验证的条件组合。

示例：

---

## 10.1 HYP-001 Volatility / ATR as Risk Filter

可能验证：

```text
atr_pct bucket = low / medium / high / extreme
volatility_3d bucket = low / medium / high / extreme
比较不同 volatility/ATR bucket 下未来 return / PF / TP-SL
```

要回答：

```text
高波动是否显著更差？
低/中波动是否更适合交易？
ATR 更像 risk filter 还是 alpha factor？
```

---

## 10.2 HYP-002 Trend Strength as Regime Filter

可能验证：

```text
trend_strength top / middle / bottom quantile
或 trend_strength > cross-sectional median
```

要回答：

```text
趋势强度高的币是否未来表现更好？
趋势强度是否只适合作为 regime filter？
```

---

## 10.3 HYP-004 Bollinger / Mean Reversion Requires Regime Filter

可能验证：

```text
bollinger_position = lower
AND regime != strong_downtrend / avoid
AND btcState != crash
```

要回答：

```text
Bollinger lower rebound 是否只有在非崩盘 regime 下有效？
```

---

## 10.4 HYP-006 Low Frequency Requirement

可能验证：

```text
仅保留更严格条件组合
统计 sampleCount 和 expected trade frequency
与高频候选对比
```

要回答：

```text
降低信号频率是否改善 PF / drawdown proxy？
```

---

## 10.5 HYP-007 Liquidity Gate First

可能验证：

```text
liquidityBucket = high / medium / low
quoteVolume bucket
liquidity_rank quantile
```

要回答：

```text
高流动性样本是否表现更稳定？
低流动性样本是否拖累？
```

---

## 10.6 HYP-008 BuyHoldBTC Benchmark Requirement

可能验证：

```text
候选条件组合的 forwardReturn 与 BTC forwardReturn 对比
excessReturnVsBTC = forwardReturn - btcForwardReturn
```

要回答：

```text
候选条件是否能产生相对 BTC 的超额收益？
```

---

# 11. Validation Dataset Schema

新增：

```text
alphapilot/research_factory/hypothesis_validation_schema.py
```

每行字段：

```text
validationId
hypothesisId
timestamp
pair
timeframe
conditionPassed
conditionReason
factorValues
forwardReturn_4
forwardReturn_8
forwardReturn_12
forwardReturn_24
mfePct_4/8/12/24
maePct_4/8/12/24
hitTpBeforeSl_4/8/12/24
hitSlBeforeTp_4/8/12/24
btcForwardReturn_4/8/12/24
excessReturnVsBTC_4/8/12/24
regimeLabel
liquidityBucket
volatilityBucket
universeMember
```

---

# 12. Validation Metrics

每个 hypothesis 输出：

```text
sampleCount
conditionPassCount
conditionPassRate
validLabelCount
averageForwardReturn
medianForwardReturn
hitTpBeforeSlProbability
hitSlBeforeTpProbability
profitFactor
expectancy
averageMfe
averageMae
excessReturnVsBTC
monthlyStability
pairStability
regimeStability
liquidityStability
```

若不可用：

```text
null
```

并写 warning。

---

# 13. Validation Gates

本版本只用于研究，不产生交易 gate。

但需要评价研究强度。

建议等级：

```text
strong_research_support
moderate_research_support
weak_research_support
no_support
insufficient_sample
```

建议规则：

```text
strong:
  sampleCount >= 500
  profitFactor >= 1.15
  expectancy > 0
  excessReturnVsBTC > 0
  stableAcrossMonths = true

moderate:
  sampleCount >= 200
  profitFactor >= 1.05
  expectancy >= 0

weak:
  sampleCount >= 100
  profitFactor > 1.0

insufficient:
  sampleCount < 100
```

必须强调：

```text
strong_research_support != dry_run_ready
```

---

# 14. No Lookahead Requirements

必须遵守：

```text
condition features 只能使用当前及历史数据
forward labels 可以看未来，但只能用于评价
不能用 future return 作为 condition
不能用未来表现选择 pair
不能用未来结果修改 universe
```

报告必须写：

```text
features are point-in-time
labels are forward-looking for validation only
```

---

# 15. Files To Add

建议新增：

```text
alphapilot/research_factory/hypothesis_validation_schema.py
alphapilot/research_factory/hypothesis_validation_rules.py
alphapilot/research_factory/hypothesis_validation_dataset.py
alphapilot/research_factory/validate_hypotheses.py
alphapilot/reports/generate_hypothesis_validation_report.py
alphapilot/reports/hypothesis_validation_report_schema.py
scripts/validate_hypotheses.ps1
reports/v13_4_26_hypothesis_validation_report.json
reports/v13_4_26_hypothesis_validation_summary.md
reports/v13_4_26_hypothesis_validation_dataset_sample.json
reports/v13_4_26_hypothesis_recommendations.json
docs/V13.4.26-hypothesis-validation-dataset.md
docs/hypothesis-validation-methodology.md
docs/no-lookahead-hypothesis-validation.md
docs/hypothesis-support-vs-trading-approval.md
```

---

# 16. Files To Modify

可能修改：

```text
README.md
docs/next-experiment-plan.md
docs/factor-hypothesis-mining.md
```

不要修改：

```text
user_data/strategies/*.py
user_data/config/config.backtest.json
AlphaPilot Mobile App
```

---

# 17. CLI

新增：

```text
python -m alphapilot.research_factory.validate_hypotheses
```

参数：

```text
--hypotheses
--factor-panel
--timerange
--timeframe
--horizons
--tp-pct
--sl-pct
--output-report
--output-summary
--output-sample
--output-recommendations
```

默认：

```text
hypotheses = reports/v13_4_25_research_hypotheses.json
timerange = 20260101-
timeframe = 1h
horizons = 4,8,12,24
tp = 0.05
sl = 0.025
```

---

# 18. PowerShell Script

新增：

```text
scripts/validate_hypotheses.ps1
```

默认调用：

```powershell
python -m alphapilot.research_factory.validate_hypotheses
```

---

# 19. Output Reports

生成：

```text
reports/v13_4_26_hypothesis_validation_report.json
reports/v13_4_26_hypothesis_validation_summary.md
reports/v13_4_26_hypothesis_validation_dataset_sample.json
reports/v13_4_26_hypothesis_recommendations.json
```

如果完整 dataset 太大：

```text
不要提交完整大文件
提交 sample + report + summary + recommendations
完整 dataset 放 reports/generated/ 并加入 .gitignore
```

---

# 20. Summary Requirements

Markdown summary 必须包含：

```text
hypothesisCount
validatedHypothesisCount
sampleCount
topSupportedHypotheses
unsupportedHypotheses
insufficientSampleHypotheses
hypothesesWithPositiveExcessVsBTC
stabilityWarnings
recommendations
nextStep
```

---

# 21. Commands To Run

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m compileall alphapilot
python -m alphapilot.research_factory.validate_hypotheses
powershell -ExecutionPolicy Bypass -File scripts\validate_hypotheses.ps1
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

不运行 backtest。

---

# 22. Safety Requirements

必须保持：

```text
不接真实 Trade API
不接 Withdraw API
不保存真实 API Key
不读取真实账户
不读取真实持仓
不创建真实订单
不自动交易
不执行 Dry-run
不运行策略 backtest
不修改 AlphaPilot Mobile App
不修改策略代码
```

允许：

```text
读取本地 factor panel
读取 hypotheses
生成 hypothesis validation dataset
生成 research reports
```

---

# 23. Acceptance Criteria

完成后必须满足：

```text
1. hypothesis validation schema 已创建
2. validation rules 已创建
3. validation dataset builder 已创建
4. validate_hypotheses CLI 已创建
5. PowerShell script 已创建
6. hypothesis validation report 已生成
7. hypothesis validation summary 已生成
8. dataset sample 已生成
9. recommendations 已生成
10. 至少尝试验证 high priority hypotheses
11. 每个 hypothesis 有 supportLevel
12. 每个 hypothesis 有 validation metrics
13. 输出 no-lookahead 说明
14. 不写交易策略
15. 不运行 backtest
16. 不进入 Dry-run
17. 不接真实 API Key
18. 不接 Trade API
19. 不接 Withdraw API
20. 不读取真实账户
21. 不读取真实持仓
22. 不创建真实订单
23. 不自动交易
24. compileall 通过
25. validate_config 通过
26. safety scan 通过
27. git diff --check 通过
```

如果无法读取/重建 factor panel：

```text
生成 blocked report
不要打 v13.4.26 tag
```

如果成功生成 validation report：

```text
可以提交并打 v13.4.26 tag
```

---

# 24. Commit And Tag

如果检查通过且成功生成 hypothesis validation report：

```bash
git add .
git commit -m "Add V13.4.26 factor hypothesis validation dataset"
git push
git tag v13.4.26
git push origin v13.4.26
```

---

# 25. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. 输入 hypotheses 路径
5. 输入 factor panel 路径或重建说明
6. validatedHypothesisCount
7. sampleCount
8. topSupportedHypotheses
9. unsupportedHypotheses
10. insufficientSampleHypotheses
11. hypothesesWithPositiveExcessVsBTC
12. validation report 路径
13. validation summary 路径
14. dataset sample 路径
15. recommendations 路径
16. 是否写交易策略
17. 是否运行 backtest
18. 是否进入 Dry-run
19. 是否保存真实 API Key
20. 是否接 Trade API
21. 是否接 Withdraw API
22. 是否读取真实账户
23. 是否读取真实持仓
24. 是否创建真实订单
25. 是否自动交易
26. compileall 结果
27. validate_config 结果
28. safety scan 结果
29. git diff --check 结果
30. commit hash
31. push 结果
32. tag 结果
33. 已知问题
34. 下一步建议
```

---

# 26. Next Version Preview

V13.4.26 完成后：

如果有 strong/moderate supported hypotheses：

```text
V13.4.27 - Hypothesis-Based Strategy Candidate Specification
```

目标：

```text
把支持度最高的 hypotheses 组合成 research-only strategy specification。
仍然不直接实盘。
```

如果没有支持的 hypotheses：

```text
V13.4.27 - Research Direction Reset / Data Expansion
```

目标：

```text
扩展数据源，例如 funding rate、OI、orderbook、news/macro filters，
或降低到更简单的 BTC/ETH/SOL 低频策略研究。
```

---

# 27. Final Instruction

请严格执行 V13.4.26。

本版本只做 factor hypothesis validation dataset。

不要写交易策略。

不要运行 backtest。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。
