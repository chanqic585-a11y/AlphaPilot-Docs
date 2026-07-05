# AlphaPilot V13.4.24 - Benchmark Result Review and Strategy Research Reset

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.24。

本版本的目标是：**基于 V13.4.23 Benchmark Strategy Suite 的真实研究回测结果，系统复盘为什么主动 benchmark 整体表现很差，拆解相对最好的 Bollinger Rebound 仍不可用的原因，并决定下一步是否进入 Strategy Research Factory / Factor Hypothesis Mining，而不是继续盲目写新策略。**

本版本不是策略调参版。
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
AlphaPilot V13.4.24 - Benchmark Result Review and Strategy Research Reset
```

中文版本名：

```text
AlphaPilot V13.4.24 - 基准策略结果复盘与策略研究重置版
```

---

# 2. Background

当前已经完成：

```text
V13.4.20：Alpha Factor Research Layer and Benchmark Suite Design
V13.4.21：FactorDataPanel and Manual Factor Library Implementation
V13.4.22：Factor Evaluation Report and Forward Label Analysis
V13.4.23：Benchmark Strategy Suite Implementation and Baseline Research Backtest
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

V13.4.23 已完成：

```text
BenchmarkNoTrade
BenchmarkBuyHoldBTC
BenchmarkEMATrend
BenchmarkRSIMeanReversion
BenchmarkMACDVolume
BenchmarkBollingerRebound
BenchmarkTD9Exhaustion
RejectedBenchmarkMartingale
```

V13.4.23 结果摘要：

```text
Top10 / 1h / 20260101-
5 个 Freqtrade benchmark 全部成功归档
最佳 raw benchmark: BenchmarkBollingerRebound
最佳 slippage-adjusted benchmark: BenchmarkBollingerRebound
所有 benchmark 仍为 research-only
主动 benchmark 整体很差，尤其大幅跑输 NoTrade 和 BuyHoldBTC
```

本版本要做的是：

```text
复盘 benchmark 为什么差，而不是继续调 benchmark 参数。
```

---

# 3. Core Goal

V13.4.24 的核心目标：

```text
1. 读取 V13.4.23 benchmark suite report
2. 读取 V13.4.23 benchmark summary / manifest
3. 分析主动 benchmark 与 NoTrade / BuyHoldBTC 的差距
4. 深入拆解 BenchmarkBollingerRebound 为什么相对最好但仍不可用
5. 分析每个 benchmark 的亏损来源、交易频率、手续费/滑点、止损密度、pair/month 稳定性
6. 归档失败 benchmark 状态
7. 生成 strategy research reset report
8. 给出下一步 Strategy Research Factory / Factor Hypothesis Mining 路线
9. 不修改任何 benchmark 策略
10. 不运行新 backtest
```

一句话目标：

```text
用 benchmark 结果告诉我们：简单策略为什么不行，下一代策略研究应该从哪里重新开始。
```

---

# 4. Required Mindset

请遵守以下原则：

```text
1. Benchmark 结果差是有效研究结论，不是执行失败
2. 不因为 Bollinger Rebound 相对最好就批准它
3. 不用单次 benchmark 结果直接生成实盘策略
4. 不调 benchmark 参数
5. 不重新跑 benchmark
6. 不选择性忽略 NoTrade / BuyHoldBTC
7. 不进入 Dry-run
8. 不接真实交易能力
9. 结论必须来自 V13.4.23 report
10. 缺失数据必须写 unavailable
```

---

# 5. Non Goals

V13.4.24 不做：

```text
不修改 Benchmark 策略代码
不调 benchmark 参数
不运行新的 Freqtrade backtest
不实现新策略
不实现因子策略
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
读取 benchmark report
生成 benchmark result review
生成 failure attribution
生成 benchmark status archive
生成 Strategy Research Factory 下一步计划
更新 README / docs
```

---

# 6. Precondition

执行前必须确认：

```text
1. v13.4.23 tag 已存在
2. reports/v13_4_23_benchmark_suite_report.json 存在
3. reports/v13_4_23_benchmark_suite_summary.md 存在
4. reports/v13_4_23_benchmark_manifest.json 存在
5. 当前工作区干净
```

如果不满足：

```text
停止 V13.4.24
提示先完成 V13.4.23
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
reports/v13_4_23_benchmark_suite_report.json
reports/v13_4_23_benchmark_suite_summary.md
reports/v13_4_23_benchmark_manifest.json
```

可参考：

```text
reports/v13_4_22_factor_evaluation_report.json
reports/v13_4_22_factor_evaluation_summary.md
reports/v13_4_22_factor_candidates.json
```

如果字段缺失：

```text
写入 warnings
不要伪造
```

---

# 9. Required Analysis Dimensions

V13.4.24 必须分析以下维度。

---

## 9.1 Benchmark vs NoTrade

回答：

```text
主动 benchmark 是否跑赢 NoTrade？
如果没有，说明主动交易是否带来了负价值？
每个 benchmark 相对 0 收益的超额收益是多少？
滑点后是否进一步变差？
```

---

## 9.2 Benchmark vs BuyHoldBTC

回答：

```text
主动 benchmark 是否跑赢 BuyHoldBTC？
如果没有，是否说明简单持有主流币优于频繁交易？
BuyHoldBTC 的回撤和收益是否比主动策略更稳？
```

---

## 9.3 Best Benchmark Review

V13.4.23 显示：

```text
最佳 raw benchmark: BenchmarkBollingerRebound
最佳 slippage-adjusted benchmark: BenchmarkBollingerRebound
```

必须分析：

```text
为什么 Bollinger Rebound 相对最好？
它是否仍然为负？
它是否只是少亏而不是有优势？
它的交易次数是多少？
它的最大回撤是多少？
它的 PF 是否 > 1？
它是否有单一 pair 或月份主导？
```

结论必须明确：

```text
relative best != tradable
```

---

## 9.4 Strategy Family Review

逐个 benchmark 分析：

```text
BenchmarkEMATrend
BenchmarkRSIMeanReversion
BenchmarkMACDVolume
BenchmarkBollingerRebound
BenchmarkTD9Exhaustion
```

每个输出：

```text
status
mainWeakness
costSensitivity
pairConcentration
monthlyConcentration
whetherUsefulForFutureHypothesis
```

状态建议：

```text
failed_benchmark
research_reference
possible_hypothesis_seed
rejected
```

---

## 9.5 Trading Frequency and Cost

分析：

```text
tradeCount
feesPaid
slippageCost
rawReturn
slippageAdjustedReturn
```

回答：

```text
哪些 benchmark 被交易成本吞噬？
哪些 benchmark 交易频率过高？
哪些 benchmark 交易少但仍失败？
```

---

## 9.6 Stop Loss / Exit Attribution

如果 report 有 exit reason：

```text
stop loss 次数
take profit 次数
signal exit 次数
EOF 次数
```

如果没有：

```text
exitAttributionAvailable=false
```

并建议后续 benchmark report 增强。

---

## 9.7 Pair / Month Stability

分析：

```text
哪些 pair 造成主要亏损？
哪些 pair 盈利？
是否某些 pair 稳定更好？
是否某些月份集中亏损？
是否盈利集中在少数月份？
```

如果不可用：

```text
pairMonthlyStabilityAvailable=false
```

并写 warning。

---

# 10. Output Reports

新增：

```text
alphapilot/reports/generate_benchmark_result_review.py
alphapilot/reports/benchmark_result_review_schema.py
reports/v13_4_24_benchmark_result_review.json
reports/v13_4_24_benchmark_result_summary.md
reports/v13_4_24_benchmark_status_archive.json
```

---

# 11. Report Schema

主报告结构建议：

```json
{
  "reportId": "v13_4_24_benchmark_result_review",
  "sourceBenchmarkReport": "reports/v13_4_23_benchmark_suite_report.json",
  "currentStatus": "benchmark_review_completed",
  "dryRunApproved": false,
  "liveTradingApproved": false,
  "noTradeComparison": {},
  "buyHoldBtcComparison": {},
  "benchmarkReviews": [],
  "bestBenchmarkReview": {},
  "failureFindings": [],
  "usefulHypothesisSeeds": [],
  "rejectedIdeas": [],
  "strategyResearchReset": {},
  "recommendedNextStep": "",
  "warnings": [],
  "generatedAt": "UTC ISO"
}
```

---

# 12. Benchmark Status Archive

新增：

```text
reports/v13_4_24_benchmark_status_archive.json
```

每个 benchmark 记录：

```json
{
  "benchmarkId": "BenchmarkBollingerRebound",
  "status": "research_reference",
  "dryRunApproved": false,
  "liveTradingApproved": false,
  "reason": "",
  "canBeUsedAsBaseline": true,
  "canBeUsedAsStrategy": false
}
```

Martingale：

```json
{
  "benchmarkId": "RejectedBenchmarkMartingale",
  "status": "rejected",
  "reason": "Unbounded risk / not compatible with AlphaPilot controlled live principles"
}
```

---

# 13. Required Findings

报告必须明确输出：

```text
1. 主动 benchmark 整体没有证明可交易优势
2. BenchmarkBollingerRebound 只是相对最好，不代表可用
3. NoTrade / BuyHoldBTC 是后续所有策略必须对比的基准
4. 当前不应进入 Dry-run
5. 下一步不应调 benchmark，而应进入 Strategy Research Factory / Factor Hypothesis Mining
```

---

# 14. Useful Hypothesis Seeds

虽然 benchmark 差，但可以提取后续研究假设。

例如：

```text
1. Bollinger Rebound 相对最好 → 均值回归 / 偏离回收值得继续研究
2. EMA Trend 差 → 简单均线趋势不足以捕捉加密轮动
3. MACD Volume 差 → 简单动能放量不足
4. TD9 差 → 极端计数不能单独作为交易逻辑
5. BuyHoldBTC 对比重要 → 主动策略必须明显优于持有或不交易
```

这些只能标记：

```text
hypothesis_seed
```

不能标记：

```text
trade_ready
```

---

# 15. Strategy Research Reset

报告必须提出下一步研究路线。

推荐下一步：

```text
V13.4.25 - Strategy Research Factory: Factor Hypothesis Mining
```

目标：

```text
1. 从 V13.4.22 factor evaluation 和 V13.4.23 benchmark result 中提取假设
2. 优先研究 Bollinger / mean reversion 类因子
3. 研究低交易频率、高质量条件组合
4. 与 NoTrade / BuyHoldBTC / BenchmarkBollingerRebound 对比
5. 不直接写交易策略
```

---

# 16. Files To Add

建议新增：

```text
alphapilot/reports/generate_benchmark_result_review.py
alphapilot/reports/benchmark_result_review_schema.py
reports/v13_4_24_benchmark_result_review.json
reports/v13_4_24_benchmark_result_summary.md
reports/v13_4_24_benchmark_status_archive.json
docs/V13.4.24-benchmark-result-review.md
docs/benchmark-failure-analysis.md
docs/no-trade-buyhold-baseline-importance.md
docs/strategy-research-reset-plan.md
```

---

# 17. Files To Modify

可能修改：

```text
README.md
docs/benchmark-results-interpretation.md
docs/benchmark-strategy-definitions.md
```

不要修改：

```text
user_data/strategies/AlphaPilotBenchmarkStrategies.py
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
python -m alphapilot.reports.generate_benchmark_result_review
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
不运行新 backtest
不修改 AlphaPilot Mobile App
不修改 benchmark 策略代码
```

允许：

```text
读取本地 benchmark report
生成 benchmark review
生成 docs
更新 README
```

---

# 20. Acceptance Criteria

完成后必须满足：

```text
1. 生成 benchmark result review JSON
2. 生成 benchmark result summary MD
3. 生成 benchmark status archive JSON
4. 分析 NoTrade comparison
5. 分析 BuyHoldBTC comparison
6. 分析 BenchmarkBollingerRebound 为什么相对最好但仍不可用
7. 分析每个 benchmark family
8. 输出 useful hypothesis seeds
9. 输出 strategy research reset plan
10. dryRunApproved=false
11. liveTradingApproved=false
12. 不修改 benchmark 策略代码
13. 不运行 backtest
14. 不进入 Dry-run
15. 不接真实 API Key
16. 不接 Trade API
17. 不接 Withdraw API
18. 不读取真实账户
19. 不读取真实持仓
20. 不创建真实订单
21. 不自动交易
22. compileall 通过
23. validate_config 通过
24. safety scan 通过
25. git diff --check 通过
```

---

# 21. Commit And Tag

如果检查通过：

```bash
git add .
git commit -m "Add V13.4.24 benchmark result review"
git push
git tag v13.4.24
git push origin v13.4.24
```

---

# 22. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. 输入 benchmark report 路径
5. benchmark result review 路径
6. benchmark result summary 路径
7. benchmark status archive 路径
8. NoTrade comparison 摘要
9. BuyHoldBTC comparison 摘要
10. best benchmark review
11. 各 benchmark 状态
12. useful hypothesis seeds
13. rejected ideas
14. recommended next step
15. 是否修改 benchmark 策略代码
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

V13.4.24 完成后，下一步建议：

```text
V13.4.25 - Strategy Research Factory: Factor Hypothesis Mining
```

该版本目标：

```text
1. 基于 V13.4.22 factor evaluation
2. 基于 V13.4.23 benchmark result
3. 基于 V13.4.24 benchmark review
4. 提炼 factor hypothesis
5. 形成 research-only candidate hypotheses
6. 不写交易策略
7. 不运行回测
```

---

# 24. Final Instruction

请严格执行 V13.4.24。

本版本只做 benchmark 结果复盘和策略研究重置。

不要修改 benchmark 策略。

不要运行 backtest。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。
