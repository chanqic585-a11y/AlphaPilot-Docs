# AlphaPilot V13.4.35 - Multi-Strategy Batch Research Backtest

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.35。

本版本的目标是：**停止单个策略反复来回试错，直接把前面所有研究结论、参考项目思路、Benchmark 结果、低频主流币路线和理论假设合并成一批 research-only 策略候选，一次性批量实现、批量回测、批量排行、批量淘汰。**

本版本会产生真实回测结果。
本版本不是纯规格文档。
本版本不是 Dry-run。
本版本不是实盘。
本版本不接真实 API Key。
本版本不自动交易。
本版本不创建真实订单。

本版本的核心原则：

```text
一次生成多策略
一次批量回测
统一指标排行
不边测边调参
结果好坏都记录
不合格直接降级为研究参考
```

---

# 1. Version Name

```text
AlphaPilot V13.4.35 - Multi-Strategy Batch Research Backtest
```

中文版本名：

```text
AlphaPilot V13.4.35 - 多策略批量研究回测版
```

---

# 2. Route Change

之前准备过：

```text
V13.4.35 - Regime-Gated Low-Frequency Long/Short V2 Research Backtest
```

该版本只测试两个策略方向。

现在根据用户要求，改为：

```text
V13.4.35 - Multi-Strategy Batch Research Backtest
```

原因：

```text
单个策略反复测试会无穷无尽。
现在需要批量生成多个候选策略，同时跑研究回测，用结果快速筛选。
```

因此不要执行旧的单策略 V13.4.35 文档。

---

# 3. Background

AlphaPilot 已经得到以下研究结论：

```text
1. 15m 放量反弹策略失败。
2. 1h Trend Pullback 小样本为正，但 Top30 扩大验证失败。
3. Dynamic Regime + Probability Gate 没有产生可交易信号。
4. Manual Factor Library 没有因子通过 research candidate gate。
5. Benchmark 主动策略整体跑输 NoTrade 和 BuyHold。
6. 1h short rejection 策略失败，交易过多，止损密集。
7. 4h low-frequency directional V1 也失败，long/short 混合后交易仍过多。
8. V13.4.32 已完成 BTC/ETH/SOL 的 4h / 1d 低频数据与 NoTrade / BuyHold / EqualWeight baseline。
```

开源项目参考：

```text
alpha101：
  参考其因子研究思路：先验证因子、再生成策略。
  本版本不做遗传搜索，不复制代码。

CryptoAgentPro.beta：
  参考其多策略组和市场状态映射思路。
  本版本不复制代码，不采用交易执行逻辑，不采用 Martingale。
```

---

# 4. Core Goal

V13.4.35 的核心目标：

```text
1. 一次性实现一批 low-frequency research strategies。
2. 每个策略保持简单、可解释、条件少。
3. 使用同一数据范围、同一指标、同一报告口径。
4. 批量运行 Freqtrade research backtest。
5. 输出统一 leaderboard。
6. 输出 long / short / pair / month / regime / baseline 对比。
7. 判断哪些策略值得继续研究。
8. 结果差的策略直接降级为 research reference。
9. 不进入 Dry-run。
10. 不接真实交易权限。
```

一句话目标：

```text
一次性测试多个候选方向，用真实结果快速筛掉无效路线。
```

---

# 5. Required Mindset

请遵守以下原则：

```text
1. 不边跑边改参数。
2. 不因为某个结果差就临时修策略。
3. 不因为某个小样本好就批准 Dry-run。
4. 不再堆十几个 gate。
5. 每个策略最多 4-6 个核心条件。
6. 所有策略必须 research-only。
7. 所有策略必须和 NoTrade / BuyHold / EqualWeight 比较。
8. 所有策略必须有 slippage-adjusted metrics。
9. 所有策略必须可单独归档。
10. 用结果说话。
```

---

# 6. Non Goals

V13.4.35 不做：

```text
不进入 Dry-run
不进入实盘
不接真实 API Key
不接 Trade API
不接 Withdraw API
不读取真实账户
不读取真实持仓
不创建真实订单
不自动交易
不修改 AlphaPilot Mobile App
不接机器学习
不接新闻系统
不接多模型
不接 funding / OI
不接 orderbook
不做参数搜索
不做遗传算法搜索
不做 Martingale
不自动加仓
不自动网格
```

允许做：

```text
新增多个 research-only Freqtrade strategy classes
运行 public historical data backtest
生成 batch leaderboard
生成 strategy comparison report
生成 baseline comparison
更新 README / docs
```

---

# 7. Precondition

执行前必须确认：

```text
1. v13.4.34 tag 已存在
2. reports/v13_4_32_low_frequency_baseline_report.json 存在
3. reports/v13_4_34_low_frequency_directional_4h_report.json 存在
4. BTC/ETH/SOL 4h 数据可用
5. Docker / Freqtrade 可用
6. 当前工作区干净
```

如果 Docker 不可用：

```text
可以提交策略代码和 blocked report
不要声称 V13.4.35 完成
不要打 v13.4.35 tag
```

---

# 8. Backtest Scope

## 8.1 Required Scope

必须运行：

```text
Pairs:
BTC/USDT:USDT
ETH/USDT:USDT
SOL/USDT:USDT

Timeframe:
4h

Timerange:
20240101-
```

如果 20240101- 因数据或 runtime 失败，fallback：

```text
20250701-
```

再 fallback：

```text
20260101-
```

必须记录实际使用 timerange。

---

## 8.2 Optional Expanded Scope

如果 required scope 全部策略运行成功，并且运行时间可接受，可选运行：

```text
supported Top10
4h
20240101-
```

如果不执行 optional expanded，需要在报告中说明：

```text
expandedTop10Executed=false
reason=...
```

---

# 9. Strategy Batch File

新增一个策略批量文件：

```text
user_data/strategies/AlphaPilotLowFrequencyStrategyBatchV01.py
```

该文件包含多个策略类。

所有策略必须在文件头注明：

```text
本文件中的策略仅用于研究和回测。
不得用于 Dry-run。
不得用于实盘。
不得接真实 API Key。
不得自动交易。
```

---

# 10. Shared Rules

所有策略共享：

```text
timeframe = "4h"
research_backtest_only
dryRunApproved = false
liveTradingApproved = false
```

Long 策略：

```text
can_short = False
只生成 enter_long
```

Short 策略：

```text
can_short = True
只生成 enter_short
不生成 enter_long
```

双向策略如果存在：

```text
必须单独标记 mixed_direction
报告中 long / short 分开统计
```

---

# 11. Shared Indicators

所有策略可复用以下指标：

```text
EMA20
EMA50
EMA200
RSI14
MACD
MACD signal
MACD histogram
ATR14
volume rolling mean 20
volumeRatio
Bollinger Bands 20 / 2
recentReturn3Bars
recentHigh20
recentLow20
rangeHigh20
rangeLow20
```

可选 1d informative：

```text
1d close
1d EMA20
1d EMA50
1d EMA200
```

如果 1d informative 不稳定：

```text
不要阻塞所有策略。
使用 4h fallback。
报告 dailyRegimeAvailable=false。
```

---

# 12. Strategy Candidates

本版本至少实现以下 8 个候选策略。

---

## 12.1 Strategy A - EMA Trend Long 4H

类名：

```text
AlphaPilotBatchA_EMATrendLong4H
```

方向：

```text
long only
```

核心思想：

```text
在大周期向上时，顺势做多。
```

条件：

```text
1. close > EMA200
2. EMA20 > EMA50
3. close > EMA20
4. MACD histogram > 0
5. RSI < 70
```

出场：

```text
stoploss = -0.04
minimal_roi = {"0": 0.08}
time stop = 10 根 4h 未盈利
```

---

## 12.2 Strategy B - EMA Trend Short 4H

类名：

```text
AlphaPilotBatchB_EMATrendShort4H
```

方向：

```text
short only
```

核心思想：

```text
在大周期向下时，顺势做空。
```

条件：

```text
1. close < EMA200
2. EMA20 < EMA50
3. close < EMA20
4. MACD histogram < 0
5. RSI > 30
```

出场：

```text
stoploss = -0.04
minimal_roi = {"0": 0.08}
time stop = 10 根 4h 未盈利
```

---

## 12.3 Strategy C - Breakout Retest Long 4H

类名：

```text
AlphaPilotBatchC_BreakoutRetestLong4H
```

方向：

```text
long only
```

核心思想：

```text
突破前高后回踩不破，再做多。
```

条件：

```text
1. previous close broke above prior 20-bar high
2. current low retests prior breakout level within 2%
3. current close > breakout level
4. volumeRatio >= 0.8
5. recentReturn3Bars < 0.12
```

说明：

```text
若实现 exact retest 复杂，可用 recentHigh20.shift(1) 作为 breakout level。
```

出场：

```text
stoploss = -0.04
minimal_roi = {"0": 0.08}
```

---

## 12.4 Strategy D - Breakdown Retest Short 4H

类名：

```text
AlphaPilotBatchD_BreakdownRetestShort4H
```

方向：

```text
short only
```

核心思想：

```text
跌破前低后反抽不回，再做空。
```

条件：

```text
1. previous close broke below prior 20-bar low
2. current high retests prior breakdown level within 2%
3. current close < breakdown level
4. volumeRatio >= 0.8
5. recentReturn3Bars > -0.12
```

出场：

```text
stoploss = -0.04
minimal_roi = {"0": 0.08}
```

---

## 12.5 Strategy E - Bollinger Mean Reversion Long 4H

类名：

```text
AlphaPilotBatchE_BollingerReversionLong4H
```

方向：

```text
long only
```

核心思想：

```text
非崩盘环境下，价格跌到布林下轨后回收。
```

条件：

```text
1. previous close < Bollinger lower
2. current close > Bollinger lower
3. RSI < 45
4. volumeRatio >= 0.7
5. recentReturn3Bars > -0.12
```

出场：

```text
stoploss = -0.03
minimal_roi = {"0": 0.05}
optional exit at Bollinger middle if easy
```

---

## 12.6 Strategy F - Bollinger Mean Reversion Short 4H

类名：

```text
AlphaPilotBatchF_BollingerReversionShort4H
```

方向：

```text
short only
```

核心思想：

```text
价格冲到布林上轨后回落。
```

条件：

```text
1. previous close > Bollinger upper
2. current close < Bollinger upper
3. RSI > 55
4. volumeRatio >= 0.7
5. recentReturn3Bars < 0.12
```

出场：

```text
stoploss = -0.03
minimal_roi = {"0": 0.05}
```

---

## 12.7 Strategy G - Relative Strength Long 4H

类名：

```text
AlphaPilotBatchG_RelativeStrengthLong4H
```

方向：

```text
long only
```

核心思想：

```text
只做相对 BTC 更强的主流币趋势延续。
```

条件：

```text
1. pair return over 12 bars > BTC return over 12 bars
2. close > EMA50
3. EMA20 > EMA50
4. MACD histogram improving
5. RSI < 70
```

如果 BTC return 无法稳定计算：

```text
fallback to close > EMA50 and report relativeStrengthAvailable=false
```

出场：

```text
stoploss = -0.04
minimal_roi = {"0": 0.08}
```

---

## 12.8 Strategy H - Volatility Compression Breakout 4H

类名：

```text
AlphaPilotBatchH_VolatilityCompressionBreakout4H
```

方向：

```text
long only in first version
```

核心思想：

```text
波动率收缩后向上突破。
```

条件：

```text
1. ATR% below its 20-bar median
2. Bollinger bandwidth below its 20-bar median
3. close > prior 20-bar high
4. volumeRatio >= 1.0
5. RSI < 75
```

出场：

```text
stoploss = -0.04
minimal_roi = {"0": 0.10}
```

---

# 13. Excluded Strategies

不要实现：

```text
Martingale
grid
averaging down
high frequency scalping
AI auto switching
funding/OI strategy
news strategy
```

本版本只研究 OHLCV-based simple strategies。

---

# 14. Batch Backtest Script

新增：

```text
scripts/run_multi_strategy_batch_backtest.ps1
```

支持参数：

```powershell
-Timerange
-Pairs
-UseMainstream
-UseTop10
-Strategies
-Run
```

默认：

```text
Pairs = BTC/USDT:USDT,ETH/USDT:USDT,SOL/USDT:USDT
Timerange = 20240101-
Strategies = all batch strategies
Run = false
```

默认只打印命令。只有 `-Run` 才执行。

示例：

```powershell
.\scripts\run_multi_strategy_batch_backtest.ps1 -UseMainstream -Timerange "20240101-"
.\scripts\run_multi_strategy_batch_backtest.ps1 -UseMainstream -Timerange "20240101-" -Run
```

脚本必须：

```text
1. 循环运行每个 strategy
2. 保存 manifest
3. 单个 strategy 失败不阻断其他 strategy
4. 记录 failedStrategies
5. 记录 result zip path
```

---

# 15. Report Generator

新增：

```text
alphapilot/reports/generate_multi_strategy_batch_report.py
alphapilot/reports/multi_strategy_batch_report_schema.py
```

输出：

```text
reports/v13_4_35_multi_strategy_batch_report.json
reports/v13_4_35_multi_strategy_batch_summary.md
reports/v13_4_35_multi_strategy_batch_manifest.json
```

报告必须包含：

```text
isMock=false if real backtest success
dryRunApproved=false
liveTradingApproved=false
strategyResults
leaderboardRaw
leaderboardSlippageAdjusted
longOnlyResults
shortOnlyResults
mixedDirectionResults
bestRawStrategy
bestSlippageAdjustedStrategy
failedStrategies
baselineComparison
recommendations
```

---

# 16. Metrics

每个 strategy 必须输出：

```text
strategyId
strategyClass
direction
tradeCount
totalReturnPct
slippageAdjustedReturnPct
maxDrawdownPct
profitFactor
slippageAdjustedProfitFactor
winRate
maxConsecutiveLosses
feesPaid
slippageCost
pairPerformance
monthlyPerformance
exitReasonBreakdown
researchWorthContinuing
```

如果某指标不可用：

```text
null
warning
```

---

# 17. Baseline Comparison

必须读取：

```text
reports/v13_4_32_low_frequency_baseline_report.json
```

对比：

```text
NoTrade
BuyHoldBTC
BuyHoldETH
BuyHoldSOL
EqualWeight BTC/ETH/SOL
```

每个 strategy 输出：

```text
beatsNoTrade
beatsEqualWeight
excessReturnVsEqualWeight
drawdownVsEqualWeight
```

---

# 18. Slippage

报告层后处理滑点：

```text
0.05% one-way
0.10% one-way
```

必须写：

```text
slippageAppliedByFreqtrade=false
slippageAppliedByPostProcessing=true
```

---

# 19. Research Decision Rules

本版本不批准 Dry-run。

但每个 strategy 判断：

```text
researchWorthContinuing=true/false
```

建议 true 条件：

```text
slippageAdjustedProfitFactor > 1.05
totalReturnPct > NoTrade
maxDrawdown noticeably below V13.4.34
tradeCount >= 5
tradeCount not excessive
not dominated by one pair/month
```

批量报告总判断：

```text
if no strategy researchWorthContinuing:
  nextStep = archive batch and move to funding/OI or external data route

if one or more strategy researchWorthContinuing:
  nextStep = focused result review for best candidates
```

---

# 20. Files To Add

建议新增：

```text
user_data/strategies/AlphaPilotLowFrequencyStrategyBatchV01.py
scripts/run_multi_strategy_batch_backtest.ps1
alphapilot/reports/generate_multi_strategy_batch_report.py
alphapilot/reports/multi_strategy_batch_report_schema.py
reports/v13_4_35_multi_strategy_batch_report.json
reports/v13_4_35_multi_strategy_batch_summary.md
reports/v13_4_35_multi_strategy_batch_manifest.json
docs/V13.4.35-multi-strategy-batch-backtest.md
docs/multi-strategy-batch-strategy-definitions.md
docs/multi-strategy-batch-results-interpretation.md
```

---

# 21. Files To Modify

可能修改：

```text
README.md
scripts/run_backtest.ps1
```

不要修改：

```text
AlphaPilotLowFrequencyDirectional4HV01.py
AlphaPilotLowFrequencyDirectional4HV02.py
AlphaPilotDynamicRegimeV01.py
AlphaPilotTrendPullback1HV01.py
AlphaPilotShortRejection1HV01.py
AlphaPilotBenchmarkStrategies.py
config.backtest.json
AlphaPilot Mobile App
```

---

# 22. Commands To Run

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m py_compile user_data\strategies\AlphaPilotLowFrequencyStrategyBatchV01.py
python -m compileall alphapilot
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
```

运行批量回测：

```powershell
.\scripts\run_multi_strategy_batch_backtest.ps1 -UseMainstream -Timerange "20240101-" -Run
```

如果失败，fallback：

```powershell
.\scripts\run_multi_strategy_batch_backtest.ps1 -UseMainstream -Timerange "20250701-" -Run
```

再 fallback：

```powershell
.\scripts\run_multi_strategy_batch_backtest.ps1 -UseMainstream -Timerange "20260101-" -Run
```

生成报告：

```powershell
python -m alphapilot.reports.generate_multi_strategy_batch_report
```

最终检查：

```powershell
git diff --check
```

---

# 23. Safety Requirements

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
使用公开历史行情
生成批量研究报告
```

---

# 24. Acceptance Criteria

完成后必须满足：

```text
1. batch strategy file 已创建
2. 至少 8 个 strategy classes 已实现
3. 每个 strategy 为 research-only
4. batch backtest script 已创建
5. batch manifest 已生成
6. 至少部分 strategies 真实回测成功
7. batch report 已生成
8. batch summary 已生成
9. leaderboardRaw 已生成
10. leaderboardSlippageAdjusted 已生成
11. baseline comparison 已生成
12. long-only / short-only 分开统计
13. failedStrategies 已记录
14. dryRunApproved=false
15. liveTradingApproved=false
16. 不进入 Dry-run
17. 不接真实 API Key
18. 不接 Trade API
19. 不接 Withdraw API
20. 不读取真实账户
21. 不读取真实持仓
22. 不创建真实订单
23. 不自动交易
24. py_compile 通过
25. compileall 通过
26. validate_config 通过
27. safety scan 通过
28. git diff --check 通过
```

如果全部 strategy 都失败且没有真实结果：

```text
不要打 v13.4.35 tag
```

如果至少部分 strategy 真实回测成功并生成 isMock=false report：

```text
可以提交并打 v13.4.35 tag
```

---

# 25. Commit And Tag

如果检查通过且真实 report 成功生成：

```bash
git add .
git commit -m "Add V13.4.35 multi-strategy batch research backtest"
git push
git tag v13.4.35
git push origin v13.4.35
```

Docs 仓库归档 prompt 后：

```bash
git add .
git commit -m "Document V13.4.35 multi-strategy batch backtest"
git push
git tag v13.4.35-docs
git push origin v13.4.35-docs
```

---

# 26. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. strategy class list
5. timerange used
6. pairs used
7. successfulStrategies
8. failedStrategies
9. batch report path
10. batch summary path
11. manifest path
12. bestRawStrategy
13. bestSlippageAdjustedStrategy
14. leaderboard summary
15. long-only summary
16. short-only summary
17. baseline comparison summary
18. researchWorthContinuing strategies
19. dryRunApproved 值
20. liveTradingApproved 值
21. 是否进入 Dry-run
22. 是否保存真实 API Key
23. 是否接 Trade API
24. 是否接 Withdraw API
25. 是否读取真实账户
26. 是否读取真实持仓
27. 是否创建真实订单
28. 是否自动交易
29. py_compile 结果
30. compileall 结果
31. validate_config 结果
32. safety scan 结果
33. git diff --check 结果
34. commit hash
35. push 结果
36. tag 结果
37. 已知问题
38. 下一步建议
```

---

# 27. Next Version Preview

V13.4.35 完成后：

如果有策略值得继续：

```text
V13.4.36 - Best Candidate Focused Review and Expanded Validation
```

如果所有策略都失败：

```text
V13.4.36 - OHLCV Strategy Batch Failure Review and Funding/OI Route Start
```

如果 short-only 整体好于 long-only：

```text
V13.4.36 - Short Candidate Focused Review
```

如果 long-only 整体好于 short-only：

```text
V13.4.36 - Long Candidate Focused Review
```

---

# 28. Final Instruction

请严格执行 V13.4.35。

这一次必须批量实现、批量回测、批量出结果。

不要再只做规格。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。

用结果说话。
