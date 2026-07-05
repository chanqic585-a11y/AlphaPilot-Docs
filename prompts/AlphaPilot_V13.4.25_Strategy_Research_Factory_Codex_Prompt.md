# AlphaPilot V13.4.25 - Strategy Research Factory: Factor Hypothesis Mining

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.25。

本版本的目标是：**基于 V13.4.22 因子评估、V13.4.23 Benchmark Suite、V13.4.24 Benchmark Result Review 的真实研究结果，建立 Strategy Research Factory 的第一版“策略假设挖掘”流程，提炼下一批 research-only 策略假设，而不是继续盲目写新策略或调参。**

本版本不是策略实现版。
本版本不是回测执行版。
本版本不是 Dry-run 版。
本版本不是实盘版。
本版本不接真实 API Key。
本版本不自动交易。
本版本不创建真实订单。

完成后请输出完整自检报告。

---

# 1. Version Name

```text
AlphaPilot V13.4.25 - Strategy Research Factory: Factor Hypothesis Mining
```

中文版本名：

```text
AlphaPilot V13.4.25 - 策略研究工厂：因子假设挖掘版
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
```

V13.4.22 结论：

```text
sampleCount: 124111
validLabelCount: 123439
evaluatedFactorCount: 16
candidateFactors: 0
top RankIC: volatility_3d, atr_pct, volatility_24h
top Spread: trend_strength, distance_to_ema50, volume_expansion_3d
top Profit Factor: trend_strength, distance_to_ema50, atr_pct
trend_strength top profit factor 约 1.0154，低于候选门槛
```

V13.4.23 结论：

```text
Benchmark Suite 已跑通。
BenchmarkBollingerRebound 是相对最好 benchmark。
但主动 benchmark 整体表现很差，尤其大幅跑输 NoTrade 和 BuyHoldBTC。
```

V13.4.24 结论：

```text
0/5 active benchmarks 跑赢 NoTrade。
0/5 active benchmarks 跑赢 BuyHoldBTC。
BenchmarkBollingerRebound 是相对最好，但仍为负收益：
raw -87.6274%
slippage-adjusted -181.9728%
PF 0.4513

结论：relative best != tradable
推荐下一步：V13.4.25 - Strategy Research Factory: Factor Hypothesis Mining
```

因此，下一步不能继续直接写策略，而要建立：

```text
Strategy Research Factory
```

从研究结果中挖掘“可验证的策略假设”。

---

# 3. Core Goal

V13.4.25 的核心目标：

```text
1. 读取 V13.4.22 factor evaluation report
2. 读取 V13.4.23 benchmark suite report
3. 读取 V13.4.24 benchmark result review
4. 提炼 useful hypothesis seeds
5. 生成 research-only strategy hypotheses
6. 对每个 hypothesis 记录证据、风险、验证计划、拒绝条件
7. 将 hypothesis 分为 factor-based / benchmark-informed / regime-based / execution-reality-based / rejected
8. 输出 Strategy Research Factory 第一版假设库
9. 生成下一步 V13.4.26 实验计划
10. 不写策略代码
11. 不运行 backtest
12. 不进入 Dry-run
```

一句话目标：

```text
把失败回测、因子分析、benchmark 复盘转化为下一轮可验证的研究假设。
```

---

# 4. Required Mindset

请遵守以下原则：

```text
1. 策略假设不是交易策略
2. 假设必须基于证据，不允许拍脑袋
3. 不把相对最好 benchmark 当成可交易策略
4. 不把单个因子表现弱的结果硬包装成优势
5. 不运行回测
6. 不写 Freqtrade 策略代码
7. 不进入 Dry-run
8. 不接真实交易能力
9. 所有 hypothesis 必须 research-only
10. 所有 hypothesis 必须有 invalidation rule
```

---

# 5. Non Goals

V13.4.25 不做：

```text
不实现交易策略
不实现 Benchmark 策略变体
不运行 Freqtrade backtest
不生成买卖信号
不修改任何已有策略
不修改 config.backtest.json
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
不做遗传算法因子搜索
```

允许做：

```text
读取本地报告
生成 hypothesis schema
生成 hypothesis mining report
生成 strategy research factory plan
生成 next experiment plan
更新 README / docs
```

---

# 6. Precondition

执行前必须确认：

```text
1. v13.4.24 tag 已存在
2. reports/v13_4_22_factor_evaluation_report.json 存在
3. reports/v13_4_22_factor_candidates.json 存在
4. reports/v13_4_23_benchmark_suite_report.json 存在
5. reports/v13_4_24_benchmark_result_review.json 存在
6. reports/v13_4_24_benchmark_status_archive.json 存在
7. 当前工作区干净
```

如果不满足：

```text
停止 V13.4.25
提示先完成 V13.4.24 或缺失输入报告
不要继续
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

# 8. Required Input Reports

必须读取：

```text
reports/v13_4_22_factor_evaluation_report.json
reports/v13_4_22_factor_evaluation_summary.md
reports/v13_4_22_factor_candidates.json
reports/v13_4_23_benchmark_suite_report.json
reports/v13_4_24_benchmark_result_review.json
reports/v13_4_24_benchmark_result_summary.md
reports/v13_4_24_benchmark_status_archive.json
```

如果字段缺失：

```text
写入 warnings
不要伪造
```

---

# 9. Hypothesis Categories

V13.4.25 必须生成以下类别的研究假设。

---

## 9.1 Factor-Based Hypotheses

基于 V13.4.22 的因子表现。

候选依据：

```text
top RankIC: volatility_3d, atr_pct, volatility_24h
top Spread: trend_strength, distance_to_ema50, volume_expansion_3d
top Profit Factor: trend_strength, distance_to_ema50, atr_pct
```

但注意：

```text
candidateFactors = 0
所以这些只能是弱假设或观察假设。
```

可能假设：

```text
1. Volatility/ATR 可能更适合作为 risk filter，而不是 alpha signal。
2. trend_strength 可能不足以单独交易，但可作为 regime score 的组成部分。
3. distance_to_ema50 可能对回调/均值回归结构有解释力，但需要和 regime 结合。
4. volume_expansion_3d 可能适合作为 activity filter，而不是 entry signal。
```

---

## 9.2 Benchmark-Informed Hypotheses

基于 V13.4.23 / V13.4.24 benchmark 复盘。

已知：

```text
BenchmarkBollingerRebound 相对最好，但仍为负收益。
0/5 主动 benchmark 跑赢 NoTrade / BuyHoldBTC。
```

可能假设：

```text
1. Bollinger / mean reversion 结构比简单 trend / MACD / TD9 更值得研究，但需要更严格 regime 和 liquidity filter。
2. 单纯 RSI 或 Bollinger 不足，需要加入 market regime 和 probability score。
3. 频繁主动交易在当前样本下整体负贡献，下一轮策略必须显著降低交易频率。
4. BuyHoldBTC 是重要基准，主动策略必须证明超过它。
```

---

## 9.3 Regime-Based Hypotheses

基于 Dynamic Regime 失败和 probability gate 结果。

可能假设：

```text
1. 当前 Regime Router 的分类可能过细或概率表样本不足。
2. trend / mean_reversion 不应共享同一套 probability gate。
3. 动态 universe 需要更少但更高质量的币种池。
4. avoid regime 不应直接产生交易，但可作为市场风险标签。
```

---

## 9.4 Execution-Reality Hypotheses

基于 V13.4.11 方向和多次滑点后恶化结果。

可能假设：

```text
1. 大多数 1h / 15m 主动策略的边际收益被滑点和手续费吃掉。
2. 只有低频、高盈亏比、高流动性的信号才值得继续研究。
3. Liquidity Gate 应成为所有策略假设的前置条件。
4. 交易频率必须作为 strategy quality 的核心约束。
```

---

## 9.5 Rejected Hypotheses

必须明确拒绝或暂停：

```text
1. Martingale / 逆势加仓
2. 仅凭 RSI 极端值交易
3. 简单双 EMA 交叉直接交易
4. 单纯 MACD + volume 放量直接交易
5. 没有流动性过滤的 Top30 全部交易
6. 没有滑点压力测试的策略推进
```

---

# 10. Hypothesis Schema

新增：

```text
alphapilot/research_factory/hypothesis_schema.py
```

每个 hypothesis 字段：

```json
{
  "hypothesisId": "HYP-001",
  "name": "",
  "category": "factor_based | benchmark_informed | regime_based | execution_reality | rejected",
  "status": "research_only | rejected | deferred",
  "evidence": [],
  "sourceReports": [],
  "proposedMechanism": "",
  "expectedBehavior": "",
  "riskNotes": [],
  "requiredData": [],
  "validationPlan": [],
  "invalidationRules": [],
  "priority": "high | medium | low",
  "dryRunApproved": false,
  "liveTradingApproved": false
}
```

---

# 11. Hypothesis Mining Engine

新增：

```text
alphapilot/research_factory/hypothesis_mining.py
```

功能：

```text
1. 读取 factor evaluation report
2. 读取 benchmark result review
3. 读取 benchmark status archive
4. 生成 hypothesis list
5. 分类排序
6. 输出 JSON / Markdown
```

---

# 12. Strategy Research Factory Report

新增报告生成器：

```text
alphapilot/reports/generate_strategy_research_factory_report.py
alphapilot/reports/strategy_research_factory_schema.py
```

输出：

```text
reports/v13_4_25_strategy_research_factory_report.json
reports/v13_4_25_strategy_research_factory_summary.md
reports/v13_4_25_research_hypotheses.json
```

---

# 13. Required Hypotheses

V13.4.25 至少生成 8 个 hypotheses。

建议必须包含：

```text
HYP-001 Volatility as Risk Filter
HYP-002 Trend Strength as Regime Filter
HYP-003 EMA50 Distance as Pullback Context
HYP-004 Bollinger Rebound Requires Regime Filter
HYP-005 Activity / Volume Expansion as Universe Filter
HYP-006 Low Frequency Requirement
HYP-007 Liquidity Gate First
HYP-008 BuyHoldBTC Benchmark Requirement
```

Rejected：

```text
HYP-R01 Martingale Rejected
HYP-R02 RSI Only Rejected
HYP-R03 Simple EMA Cross Rejected
```

---

# 14. Priority Rules

优先级评估：

```text
high:
  有多个报告证据支持
  有清晰验证路径
  风险可控
  不依赖实盘数据

medium:
  有弱证据或需要更多数据
  可通过研究回测验证

low:
  证据不足
  需要额外数据
  或容易过拟合

rejected:
  风险不可接受
  已明显失败
  不符合 AlphaPilot 风控原则
```

---

# 15. Next Experiment Plan

V13.4.25 必须输出 V13.4.26 计划。

推荐下一步：

```text
V13.4.26 - Factor Hypothesis Validation Dataset
```

目标：

```text
1. 将 high priority hypotheses 转为可评估条件组合
2. 生成 hypothesis validation dataset
3. 不写策略
4. 不运行 Freqtrade backtest
5. 输出每个 hypothesis 的历史统计结果
```

或者：

```text
V13.4.26 - Liquidity-First Universe Filter Study
```

如果执行真实性被判定为最高优先级。

推荐默认：

```text
V13.4.26 - Factor Hypothesis Validation Dataset
```

---

# 16. Files To Add

建议新增：

```text
alphapilot/research_factory/hypothesis_schema.py
alphapilot/research_factory/hypothesis_mining.py
alphapilot/research_factory/hypothesis_registry.py
alphapilot/reports/generate_strategy_research_factory_report.py
alphapilot/reports/strategy_research_factory_schema.py
reports/v13_4_25_strategy_research_factory_report.json
reports/v13_4_25_strategy_research_factory_summary.md
reports/v13_4_25_research_hypotheses.json
docs/V13.4.25-strategy-research-factory.md
docs/factor-hypothesis-mining.md
docs/rejected-strategy-hypotheses.md
docs/next-experiment-plan.md
```

---

# 17. Files To Modify

可能修改：

```text
README.md
docs/strategy-research-factory.md
docs/strategy-research-reset-plan.md
```

不要修改：

```text
user_data/strategies/*.py
user_data/config/config.backtest.json
AlphaPilot Mobile App
```

---

# 18. Commands To Run

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m compileall alphapilot
python -m alphapilot.reports.generate_strategy_research_factory_report
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

不运行 backtest。

---

# 19. Safety Requirements

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
不运行 backtest
不修改 AlphaPilot Mobile App
不修改策略代码
```

允许：

```text
读取本地报告
生成 research hypotheses
生成 docs
更新 README
```

---

# 20. Acceptance Criteria

完成后必须满足：

```text
1. hypothesis schema 已创建
2. hypothesis mining engine 已创建
3. strategy research factory report generator 已创建
4. research hypotheses JSON 已生成
5. strategy research factory summary 已生成
6. 至少 8 个 research hypotheses
7. 至少 3 个 rejected hypotheses
8. 每个 hypothesis 有 evidence / validationPlan / invalidationRules
9. 输出 priority
10. 输出 V13.4.26 next experiment plan
11. dryRunApproved=false
12. liveTradingApproved=false
13. 不写交易策略
14. 不运行 backtest
15. 不进入 Dry-run
16. 不接真实 API Key
17. 不接 Trade API
18. 不接 Withdraw API
19. 不读取真实账户
20. 不读取真实持仓
21. 不创建真实订单
22. 不自动交易
23. compileall 通过
24. validate_config 通过
25. safety scan 通过
26. git diff --check 通过
```

---

# 21. Commit And Tag

如果检查通过：

```bash
git add .
git commit -m "Add V13.4.25 strategy research factory hypotheses"
git push
git tag v13.4.25
git push origin v13.4.25
```

---

# 22. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. 输入 factor evaluation report 路径
5. 输入 benchmark review report 路径
6. strategy research factory report 路径
7. research hypotheses 路径
8. hypothesis 数量
9. rejected hypothesis 数量
10. high priority hypotheses
11. medium priority hypotheses
12. low priority hypotheses
13. rejected hypotheses
14. next experiment plan
15. 是否写交易策略
16. 是否运行 backtest
17. 是否进入 Dry-run
18. 是否保存真实 API Key
19. 是否接 Trade API
20. 是否接 Withdraw API
21. 是否读取真实账户
22. 是否读取真实持仓
23. 是否创建真实订单
24. 是否自动交易
25. compileall 结果
26. validate_config 结果
27. safety scan 结果
28. git diff --check 结果
29. commit hash
30. push 结果
31. tag 结果
32. 已知问题
33. 下一步建议
```

---

# 23. Next Version Preview

V13.4.25 完成后，下一步建议：

```text
V13.4.26 - Factor Hypothesis Validation Dataset
```

目标：

```text
把 high priority hypotheses 转换为可统计验证的条件组合，
生成每个 hypothesis 的历史统计结果，
仍然不写交易策略、不运行 Freqtrade backtest、不进入 Dry-run。
```

---

# 24. Final Instruction

请严格执行 V13.4.25。

本版本只做策略研究工厂和因子假设挖掘。

不要写交易策略。

不要运行 backtest。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。
