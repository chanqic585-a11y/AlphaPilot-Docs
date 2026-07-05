# AlphaPilot V13.4.23 - Benchmark Strategy Suite Implementation and Baseline Research Backtest

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.23。

本版本的目标是：**基于 V13.4.22 因子评估结果 candidateFactors=0 的结论，实现 AlphaPilot Benchmark Strategy Suite，并在 research-only 范围内跑简单基准策略的对照回测，判断后续复杂因子 / 动态策略是否真的有增益。**

本版本不是 AlphaPilot 正式交易策略版。
本版本不是 Dry-run 版。
本版本不是实盘版。
本版本不接真实 API Key。
本版本不自动交易。
本版本不创建真实订单。

Benchmark 策略只用于研究对照，不允许进入 Dry-run 或实盘。

完成后请输出完整自检报告。

---

# 1. Version Name

```text
AlphaPilot V13.4.23 - Benchmark Strategy Suite Implementation and Baseline Research Backtest
```

中文版本名：

```text
AlphaPilot V13.4.23 - 基准策略组实现与研究回测版
```

---

# 2. Background

当前已经完成：

```text
V13.4.20：Alpha Factor Research Layer and Benchmark Suite Design
V13.4.21：FactorDataPanel and Manual Factor Library Implementation
V13.4.22：Factor Evaluation Report and Forward Label Analysis
```

V13.4.22 结果：

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

V13.4.22 结论：

```text
当前 Manual Factor Library V01 没有任何因子通过 research candidate gate。
因此不能基于这些因子直接生成交易策略。
下一步应先建立 Benchmark Strategy Suite，确认简单基准策略表现。
```

本版本的目的不是找一个立即可交易的策略，而是建立：

```text
复杂策略必须打败的基准线。
```

---

# 3. Open Source Reference Boundary

本版本可以参考 CryptoAgentPro.beta 的策略分类思想：

```text
EMA Trend
RSI Mean Reversion
MACD Volume
Bollinger Rebound
TD9 Exhaustion
Regime -> Strategy mapping
```

但必须遵守：

```text
1. 不直接复制外部项目代码
2. 不直接采用其交易执行逻辑
3. 不接 testnet / live trade
4. 不采用 Martingale / 逆势加仓作为 benchmark 主线
5. 所有 benchmark 均为 research-only
```

Martingale / S4 类策略必须记录为：

```text
rejected_benchmark_idea
```

原因：

```text
风险不可控，不适合作为 AlphaPilot 实盘量化主线。
```

---

# 4. Core Goal

V13.4.23 的核心目标：

```text
1. 实现 Benchmark Strategy Suite
2. 建立简单基准策略文件
3. 建立 benchmark metadata / registry
4. 跑 research-only benchmark backtest
5. 输出 benchmark comparison report
6. 生成 raw metrics + slippage-adjusted metrics
7. 与 NoTrade / BuyHoldBTC baseline 比较
8. 明确所有 benchmark 不可进入 Dry-run
9. 为 V13.4.24 Strategy Research Factory 提供基准线
```

一句话目标：

```text
先知道简单策略能做到什么，再判断复杂策略是否值得继续。
```

---

# 5. Required Mindset

请遵守以下原则：

```text
1. Benchmark 不是正式策略
2. Benchmark 不是为了调到盈利
3. Benchmark 只提供参考基准
4. 不把某个 benchmark 暂时表现好当成实盘依据
5. 不接真实交易 API
6. 不进入 Dry-run
7. 不使用 Martingale
8. 不因为 benchmark 失败就修改历史结果
9. 缺失指标必须写 null / unavailable
10. 所有结果必须 research-only
```

---

# 6. Non Goals

V13.4.23 不做：

```text
不实现 AlphaPilot 正式新策略
不修改 AlphaPilotDynamicRegimeV01
不修改旧 VolumeRebound / TrendPullback 策略
不修改 factor evaluation 结果
不做参数搜索
不做机器学习
不接新闻数据
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
不直接复制外部项目代码
```

允许做：

```text
新增 benchmark strategy classes
新增 benchmark registry
新增 benchmark backtest scripts
运行 research-only Freqtrade backtest
生成 benchmark report
生成 benchmark docs
更新 README
```

---

# 7. Precondition

执行前必须确认：

```text
1. v13.4.22 tag 已存在
2. reports/v13_4_22_factor_evaluation_report.json 存在
3. reports/v13_4_22_factor_evaluation_summary.md 存在
4. reports/v13_4_22_factor_candidates.json 存在
5. Docker / Freqtrade 环境可用
6. 当前工作区干净
```

如果 Docker 不可用：

```text
可以实现 benchmark code 和 blocked report
不要声称 V13.4.23 完成
不要打 v13.4.23 tag
```

如果本地数据不足：

```text
生成 insufficient data report
不要伪造 benchmark results
不要打 tag
```

---

# 8. Repository Scope

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

# 9. Benchmark Strategy Suite

本版本至少实现以下 research-only benchmark。

---

## 9.1 BenchmarkNoTrade

类型：

```text
report-only baseline
```

含义：

```text
不交易，收益为 0，用于比较复杂策略是否值得承担风险和成本。
```

可以不作为 Freqtrade strategy 实现。

---

## 9.2 BenchmarkBuyHoldBTC

类型：

```text
report-only baseline
```

含义：

```text
买入并持有 BTC，同一 timerange 下比较收益和回撤。
```

可以由报告生成器基于 BTC OHLCV 计算，不强制作为 Freqtrade strategy。

---

## 9.3 BenchmarkEMATrend

策略：

```text
EMA trend following
```

建议第一版：

```text
timeframe = 1h
long only
enter:
  EMA20 > EMA50
  close > EMA20
  MACD histogram > 0
exit:
  close < EMA20
  or ROI/stoploss
stoploss = -2.5%
minimal_roi = +5%
```

---

## 9.4 BenchmarkRSIMeanReversion

策略：

```text
RSI mean reversion
```

建议第一版：

```text
timeframe = 1h
long only
enter:
  RSI14 < 30
  close <= Bollinger lower band * 1.01
  BTC not crash if available
exit:
  RSI14 > 50
  or close >= Bollinger middle
  or ROI/stoploss
stoploss = -2%
minimal_roi = +2.5%
```

---

## 9.5 BenchmarkMACDVolume

策略：

```text
MACD + volume confirmation
```

建议第一版：

```text
timeframe = 1h
long only
enter:
  MACD histogram > previous MACD histogram
  MACD line > signal line
  volumeRatio >= 1.2
  close > EMA50
exit:
  MACD histogram weakens 2 bars while profitable
  or ROI/stoploss
stoploss = -2.5%
minimal_roi = +4%
```

---

## 9.6 BenchmarkBollingerRebound

策略：

```text
Bollinger rebound
```

建议第一版：

```text
timeframe = 1h
long only
enter:
  close reclaims lower Bollinger band
  RSI14 < 45
  volume not collapsing
exit:
  close >= Bollinger middle
  or ROI/stoploss
stoploss = -2%
minimal_roi = +2.5%
```

---

## 9.7 BenchmarkTD9Exhaustion

策略：

```text
TD9 exhaustion
```

建议第一版：

```text
timeframe = 1h
long only
enter:
  TD buy setup count >= 9
  RSI14 < 45
  BTC not crash if available
exit:
  RSI recovers
  or ROI/stoploss
stoploss = -2%
minimal_roi = +3%
```

---

## 9.8 RejectedBenchmarkMartingale

必须记录但不实现：

```text
Martingale / 逆势加仓
```

状态：

```text
rejected_benchmark_idea
```

原因：

```text
风险不可控，不符合 AlphaPilot 实盘风控路线。
```

---

# 10. Files To Add

建议新增：

```text
user_data/strategies/AlphaPilotBenchmarkStrategies.py
alphapilot/benchmarks/benchmark_registry.py
alphapilot/benchmarks/benchmark_suite_runner.py
alphapilot/benchmarks/buy_hold_baseline.py
alphapilot/reports/generate_benchmark_suite_report.py
alphapilot/reports/benchmark_suite_report_schema.py
scripts/run_benchmark_suite.ps1
reports/v13_4_23_benchmark_suite_report.json
reports/v13_4_23_benchmark_suite_summary.md
reports/v13_4_23_benchmark_manifest.json
docs/V13.4.23-benchmark-strategy-suite.md
docs/benchmark-strategy-definitions.md
docs/benchmark-results-interpretation.md
```

---

# 11. Files To Modify

可能修改：

```text
README.md
alphapilot/benchmarks/benchmark_strategy_schema.py
alphapilot/benchmarks/benchmark_suite_spec.py
```

不要修改：

```text
AlphaPilotDynamicRegimeV01.py
AlphaPilotTrendPullback1HV01.py
AlphaPilotVolumeReboundV01.py
AlphaPilotVolumeReboundV02Candidates.py
config.backtest.json
AlphaPilot Mobile App
```

---

# 12. Benchmark Registry

新增或完善：

```text
alphapilot/benchmarks/benchmark_registry.py
```

每个 benchmark 记录：

```text
benchmarkId
name
type
timeframe
direction
status
description
riskNotes
isFreqtradeStrategy
className
isReportOnly
```

所有 benchmark 必须：

```text
status = research_only
dryRunApproved = false
liveTradingApproved = false
```

---

# 13. Backtest Scope

第一版范围建议：

```text
Timerange: 20260101-
Timeframe: 1h
Pairs: supported Top10 或 supported Top30
```

考虑到运行成本，优先：

```text
supported Top10
```

如果脚本稳定，可选：

```text
supported Top30
```

必须记录：

```text
requestedPairs
supportedPairs
excludedPairs
```

---

# 14. Scripts

新增：

```text
scripts/run_benchmark_suite.ps1
```

支持参数：

```powershell
-Timerange
-UseTop10
-UseTop30
-Pairs
-Strategies
-Run
```

默认：

```text
只打印命令
只有 -Run 才执行
```

示例：

```powershell
.\scripts\run_benchmark_suite.ps1 -UseTop10 -Timerange "20260101-"
```

执行：

```powershell
.\scripts\run_benchmark_suite.ps1 -UseTop10 -Timerange "20260101-" -Run
```

---

# 15. Report Generator

新增：

```text
alphapilot/reports/generate_benchmark_suite_report.py
```

功能：

```text
1. 读取 Freqtrade benchmark backtest results
2. 计算 report-only baseline：NoTrade / BuyHoldBTC
3. 汇总各 benchmark metrics
4. 计算 slippage-adjusted metrics
5. 输出 benchmark report / summary
```

---

# 16. Metrics

必须比较：

```text
totalReturnPct
slippageAdjustedTotalReturnPct
maxDrawdownPct
profitFactor
slippageAdjustedProfitFactor
winRate
tradeCount
maxConsecutiveLosses
averageHoldingMinutes
feesPaid
slippageCost
monthlyStability
pairStability
```

如果指标缺失：

```text
null
```

并写 warnings。

---

# 17. Slippage

默认后处理滑点：

```text
0.05% one-way
0.10% one-way
0.20% one-way
```

必须明确：

```text
slippageAppliedByFreqtrade = false
slippageAppliedByPostProcessing = true
```

如适用。

---

# 18. Benchmark Report Schema

输出：

```text
reports/v13_4_23_benchmark_suite_report.json
reports/v13_4_23_benchmark_suite_summary.md
```

结构：

```json
{
  "reportId": "v13_4_23_benchmark_suite",
  "timerange": "20260101-",
  "timeframe": "1h",
  "requestedPairs": [],
  "supportedPairs": [],
  "excludedPairs": [],
  "benchmarks": [],
  "bestBenchmarkRaw": null,
  "bestBenchmarkSlippageAdjusted": null,
  "noTradeBaseline": {},
  "buyHoldBtcBaseline": {},
  "interpretation": [],
  "dryRunApproved": false,
  "liveTradingApproved": false,
  "generatedAt": "UTC ISO"
}
```

---

# 19. Interpretation Rules

报告必须说明：

```text
1. Benchmark 盈利不代表可交易
2. Benchmark 只是对照基准
3. 后续复杂策略必须优于 NoTrade / BuyHoldBTC / simple benchmarks
4. 任何 benchmark 都不批准 Dry-run
5. Martingale 已拒绝
```

---

# 20. Commands To Run

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m py_compile user_data\strategies\AlphaPilotBenchmarkStrategies.py
python -m compileall alphapilot
powershell -ExecutionPolicy Bypass -File scripts\run_benchmark_suite.ps1 -UseTop10 -Timerange "20260101-" -Run
python -m alphapilot.reports.generate_benchmark_suite_report
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

如果 Docker / Freqtrade 不可用：

```text
实现 code + blocked report
不要打 tag
```

---

# 21. Safety Requirements

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
不修改 AlphaPilot Mobile App
```

允许：

```text
运行 Freqtrade research backtest
读取 public historical data
生成 benchmark reports
```

---

# 22. Acceptance Criteria

完成后必须满足：

```text
1. Benchmark strategies 文件已创建
2. 至少 5 个 research-only benchmark 已实现或 report-only 实现
3. Martingale 被记录为 rejected benchmark idea
4. benchmark registry 已创建
5. benchmark suite runner 已创建
6. benchmark report generator 已创建
7. run_benchmark_suite.ps1 已创建
8. 真实 benchmark suite backtest 成功，或失败原因明确
9. benchmark report 已生成
10. benchmark summary 已生成
11. NoTrade baseline 已生成
12. BuyHoldBTC baseline 已生成或 unavailable warning
13. slippage-adjusted metrics 已生成
14. dryRunApproved=false
15. liveTradingApproved=false
16. 不接 API Key
17. 不自动交易
18. compileall 通过
19. validate_config 通过
20. safety scan 通过
21. git diff --check 通过
```

如果真实 benchmark backtest 未成功：

```text
不要打 v13.4.23 tag
```

如果成功生成 benchmark report：

```text
可以提交并打 v13.4.23 tag
```

---

# 23. Commit And Tag

如果检查通过且 benchmark report 成功生成：

```bash
git add .
git commit -m "Add V13.4.23 benchmark strategy suite"
git push
git tag v13.4.23
git push origin v13.4.23
```

---

# 24. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. benchmark 列表
5. rejected benchmark ideas
6. timerange
7. requestedPairs
8. supportedPairs
9. excludedPairs
10. benchmark report 路径
11. benchmark summary 路径
12. bestBenchmarkRaw
13. bestBenchmarkSlippageAdjusted
14. NoTrade baseline
15. BuyHoldBTC baseline
16. 是否运行 benchmark backtest
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

# 25. Next Version Preview

V13.4.23 完成后：

如果 benchmark 都很差：

```text
V13.4.24 - Strategy Research Factory: Factor Hypothesis Mining
```

如果某些 benchmark 明显有效：

```text
V13.4.24 - Benchmark-Informed Strategy Candidate Design
```

推荐默认：

```text
V13.4.24 - Factor-Based Strategy Hypothesis Design
```

---

# 26. Final Instruction

请严格执行 V13.4.23。

本版本只实现 benchmark strategy suite 和 research-only benchmark report。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。

不要把 benchmark 当成正式策略。
