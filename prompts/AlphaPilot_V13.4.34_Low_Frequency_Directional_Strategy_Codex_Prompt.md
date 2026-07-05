# AlphaPilot V13.4.34 - Low-Frequency Long/Short 4H Research Strategy Implementation and Backtest

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.34。

本版本的目标是：**直接实现一个简单、低频、主流币、双向 4h research strategy，并跑出真实回测结果。**

这一次不做纯规格版。  
这一次不继续加研究层。  
这一次必须产出结果。  

核心原则：

```text
少条件
低频
主流币
long / short 分开统计
真实回测
对比 NoTrade / BuyHold / EqualWeight
结果不好就归档，不无限调参
```

本版本不是 Dry-run。  
本版本不是实盘。  
本版本不接真实 API Key。  
本版本不自动交易。  
本版本不创建真实订单。  

完成后请输出完整自检报告。

---

# 1. Version Name

```text
AlphaPilot V13.4.34 - Low-Frequency Long/Short 4H Research Strategy
```

中文版本名：

```text
AlphaPilot V13.4.34 - 低频主流币 4小时多空研究策略版
```

---

# 2. Background

当前已经完成：

```text
V13.4.31：Low-Frequency Mainstream Coin Research Plan
V13.4.32：Low-Frequency Data Preparation and Baseline Builder
V13.4.33：Low-Frequency Candidate Specification and Baseline Hurdles
```

现在不再继续纯设计。

本版本直接实现并回测：

```text
BTC / ETH / SOL
4h
long + short
低频方向策略
```

---

# 3. Core Goal

V13.4.34 的核心目标：

```text
1. 新增 AlphaPilotLowFrequencyDirectional4HV01.py
2. can_short = True
3. 实现简单 4h long 条件
4. 实现简单 4h short 条件
5. 不接 probability gate
6. 不接 factor gate
7. 不接 funding / OI
8. 不接 AI / 新闻
9. 跑 BTC/ETH/SOL 真实 research backtest
10. 输出 raw / slippage-adjusted metrics
11. 输出 long / short 分开统计
12. 输出 pair / month / regime 分解
13. 输出 NoTrade / BuyHold / EqualWeight 对比
14. dryRunApproved=false
15. liveTradingApproved=false
```

一句话目标：

```text
直接看低频 4h 多空策略有没有最基础的研究价值。
```

---

# 4. Required Mindset

请遵守以下原则：

```text
1. 不再堆很多 gate
2. 不再先写一版纯规格
3. 不因为结果不好临时改参数
4. 不因为结果好就进入 Dry-run
5. long / short 必须分开报告
6. 结果必须和 NoTrade / BuyHold / EqualWeight 对比
7. 回测失败就记录原因
8. 策略失败就归档，不无限调参
```

---

# 5. Non Goals

V13.4.34 不做：

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
不运行 Top30
不做全市场
不做新币
不做参数搜索
```

允许做：

```text
新增 research-only Freqtrade 策略
运行 BTC/ETH/SOL 4h public historical backtest
生成 report
对比 V13.4.32 baselines
更新 README / docs
```

---

# 6. Precondition

执行前必须确认：

```text
1. v13.4.33 tag 已存在
2. reports/v13_4_32_low_frequency_baseline_report.json 存在
3. reports/v13_4_33_low_frequency_candidate_spec_report.json 存在
4. BTC/ETH/SOL 4h 数据可用
5. Docker / Freqtrade 可用
6. 当前工作区干净
```

如果 Docker 不可用：

```text
可以提交策略代码和 blocked report
不要声称 V13.4.34 完成
不要打 v13.4.34 tag
```

---

# 7. Strategy File

新增：

```text
user_data/strategies/AlphaPilotLowFrequencyDirectional4HV01.py
```

策略类：

```python
class AlphaPilotLowFrequencyDirectional4HV01(IStrategy):
    ...
```

策略声明：

```text
strategyId: alpha_low_frequency_directional_4h_v01
strategyName: AlphaPilot Low Frequency Directional 4H V0.1
status: research_backtest_only
```

文件头必须写：

```text
本策略仅用于研究和回测。
不得用于 Dry-run。
不得用于实盘。
不得接真实 API Key。
不得自动交易。
```

---

# 8. Strategy Base Parameters

必须设置：

```text
timeframe = "4h"
can_short = True
stoploss = -0.03
minimal_roi = {"0": 0.06}
```

说明：

```text
止损：3%
止盈：6%
方向：long + short
周期：4h
```

---

# 9. Indicators

在 `populate_indicators` 中计算：

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
recentReturn3Bars
```

可选但不强制：

```text
Bollinger Bands
```

第一版不要因为可选指标阻塞策略。

---

# 10. Long Entry Logic

只保留少量条件。

计算：

```text
longScore = 0
```

满足以下条件加分。

---

## 10.1 Trend Up

```text
close > ema200
ema20 > ema50
```

加 1 分。

---

## 10.2 Pullback / Reclaim

```text
low <= ema20 * 1.015
or low <= ema50 * 1.015
```

并且：

```text
close > ema20
```

加 1 分。

---

## 10.3 Momentum Improving

```text
macdhist > macdhist.shift(1)
```

加 1 分。

---

## 10.4 RSI Not Overheated

```text
rsi < 65
```

加 1 分。

---

## 10.5 Volume Healthy

```text
volumeRatio >= 0.8
```

加 1 分。

---

## 10.6 No Chase Long

硬 blocker：

```text
recentReturn3Bars >= 0.10
```

如果最近 3 根 4h 已经涨超 10%，不新开多。

---

## 10.7 Final Long Entry

```text
enter_long = 1
if longScore >= 4
and recentReturn3Bars < 0.10
and volume > 0
and required data exists
```

---

# 11. Short Entry Logic

只保留少量条件。

计算：

```text
shortScore = 0
```

满足以下条件加分。

---

## 11.1 Trend Down

```text
close < ema200
ema20 < ema50
```

加 1 分。

---

## 11.2 Rejection / Failed Bounce

```text
high >= ema20 * 0.985
or high >= ema50 * 0.985
```

并且：

```text
close < ema20
```

加 1 分。

---

## 11.3 Momentum Weakening

```text
macdhist < macdhist.shift(1)
```

加 1 分。

---

## 11.4 RSI Not Oversold

```text
rsi > 35
```

加 1 分。

---

## 11.5 Volume Healthy

```text
volumeRatio >= 0.8
```

加 1 分。

---

## 11.6 No Chase Short

硬 blocker：

```text
recentReturn3Bars <= -0.10
```

如果最近 3 根 4h 已经跌超 10%，不新开空。

---

## 11.7 Final Short Entry

```text
enter_short = 1
if shortScore >= 4
and recentReturn3Bars > -0.10
and volume > 0
and required data exists
```

---

# 12. Exit Logic

## 12.1 Stoploss

```text
stoploss = -0.03
```

## 12.2 ROI

```text
minimal_roi = {"0": 0.06}
```

## 12.3 Time Stop

实现 `custom_exit`：

```text
持仓 >= 10 根 4h
且 current_profit <= 0
则退出
```

返回：

```text
time_stop_10x4h_not_profitable
```

如果 custom_exit 获取 candle count 不稳定，可以用 open time：

```text
持仓时间 >= 40 小时
且 current_profit <= 0
```

---

## 12.4 Profitable Momentum Exit

只在盈利时启用。

Long：

```text
current_profit > 0
and macdhist < macdhist.shift(1)
and macdhist.shift(1) < macdhist.shift(2)
```

返回：

```text
profitable_long_momentum_exit
```

Short：

```text
current_profit > 0
and macdhist > macdhist.shift(1)
and macdhist.shift(1) > macdhist.shift(2)
```

返回：

```text
profitable_short_momentum_exit
```

---

# 13. Audit Columns

加入审计列，前缀：

```text
ap_lf_audit_
```

至少包含：

```text
ap_lf_audit_long_score
ap_lf_audit_short_score
ap_lf_audit_no_chase_long
ap_lf_audit_no_chase_short
ap_lf_audit_final_long_entry
ap_lf_audit_final_short_entry
ap_lf_audit_skip_reason
```

skip reasons：

```text
data_missing
long_score_too_low
short_score_too_low
chase_long
chase_short
entry_long_passed
entry_short_passed
unknown
```

---

# 14. Backtest Scope

必须跑：

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

如果 20240101- 运行失败或数据不足，fallback：

```text
20250701-
```

再 fallback：

```text
20260101-
```

必须记录实际使用 timerange。

---

# 15. Report Requirements

新增报告生成器：

```text
alphapilot/reports/generate_low_frequency_directional_report.py
alphapilot/reports/low_frequency_directional_report_schema.py
```

输出：

```text
reports/v13_4_34_low_frequency_directional_4h_report.json
reports/v13_4_34_low_frequency_directional_4h_summary.md
```

报告必须包含：

```text
isMock=false if real backtest success
dryRunApproved=false
liveTradingApproved=false
strategyId
pairs
timeframe
timerange
tradeCount
longTradeCount
shortTradeCount
totalReturnPct
slippageAdjustedTotalReturnPct
maxDrawdownPct
profitFactor
slippageAdjustedProfitFactor
winRate
maxConsecutiveLosses
longMetrics
shortMetrics
pairPerformance
monthlyPerformance
regimeBreakdown if available
exitReasonBreakdown
NoTrade comparison
BuyHoldBTC comparison
BuyHoldETH comparison
BuyHoldSOL comparison
EqualWeight comparison
researchWorthContinuing
warnings
```

---

# 16. Baseline Comparison

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

输出：

```text
excessReturnVsNoTrade
excessReturnVsEqualWeight
drawdownVsEqualWeight
whetherBeatsBaseline
```

---

# 17. Slippage

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

如果无法计算：

```text
slippageAdjustedMetrics=null
warning
```

---

# 18. Research Decision Rules

本版本不批准 Dry-run。

但必须输出：

```text
researchWorthContinuing=true/false
```

建议 true 条件：

```text
slippageAdjustedProfitFactor > 1.05
maxDrawdown 可接受
tradeCount >= 10
long 或 short 至少一个方向明显优于基线
没有单一 pair 完全主导
```

如果不满足：

```text
researchWorthContinuing=false
```

---

# 19. Files To Add

建议新增：

```text
user_data/strategies/AlphaPilotLowFrequencyDirectional4HV01.py
alphapilot/reports/generate_low_frequency_directional_report.py
alphapilot/reports/low_frequency_directional_report_schema.py
scripts/run_low_frequency_directional_backtest.ps1
reports/v13_4_34_low_frequency_directional_4h_report.json
reports/v13_4_34_low_frequency_directional_4h_summary.md
docs/V13.4.34-low-frequency-directional-4h-research-strategy.md
docs/low-frequency-directional-4h-strategy-rules.md
docs/low-frequency-directional-results-interpretation.md
```

---

# 20. Files To Modify

可能修改：

```text
README.md
scripts/run_backtest.ps1
```

不要修改：

```text
AlphaPilotDynamicRegimeV01.py
AlphaPilotTrendPullback1HV01.py
AlphaPilotVolumeReboundV01.py
AlphaPilotShortRejection1HV01.py
AlphaPilotBenchmarkStrategies.py
config.backtest.json
AlphaPilot Mobile App
```

---

# 21. PowerShell Script

新增：

```text
scripts/run_low_frequency_directional_backtest.ps1
```

支持：

```powershell
-Timerange
-Pairs
-Run
```

默认只打印命令。

示例：

```powershell
.\scripts\run_low_frequency_directional_backtest.ps1 -Timerange "20240101-"
.\scripts\run_low_frequency_directional_backtest.ps1 -Timerange "20240101-" -Run
```

---

# 22. Commands To Run

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m py_compile user_data\strategies\AlphaPilotLowFrequencyDirectional4HV01.py
python -m compileall alphapilot
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
```

运行 research backtest：

```powershell
.\scripts\run_low_frequency_directional_backtest.ps1 -Timerange "20240101-" -Run
```

如果失败，fallback：

```powershell
.\scripts\run_low_frequency_directional_backtest.ps1 -Timerange "20250701-" -Run
```

再 fallback：

```powershell
.\scripts\run_low_frequency_directional_backtest.ps1 -Timerange "20260101-" -Run
```

生成报告：

```powershell
python -m alphapilot.reports.generate_low_frequency_directional_report
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
生成 research report
```

---

# 24. Acceptance Criteria

完成后必须满足：

```text
1. AlphaPilotLowFrequencyDirectional4HV01.py 已创建
2. timeframe=4h
3. can_short=True
4. enter_long 已实现
5. enter_short 已实现
6. no chase long / short 已实现
7. stoploss=3%
8. ROI=6%
9. time stop 已实现
10. profitable momentum exit 已实现或有 fallback
11. audit columns 已实现
12. BTC/ETH/SOL 4h research backtest 成功或失败原因明确
13. report 已生成
14. report isMock=false
15. report dryRunApproved=false
16. report liveTradingApproved=false
17. long / short 分开统计
18. baseline comparison 已生成
19. slippage-adjusted metrics 已生成
20. 不进入 Dry-run
21. 不接真实 API Key
22. 不接 Trade API
23. 不接 Withdraw API
24. 不读取真实账户
25. 不读取真实持仓
26. 不创建真实订单
27. 不自动交易
28. py_compile 通过
29. compileall 通过
30. validate_config 通过
31. safety scan 通过
32. git diff --check 通过
```

如果真实回测未成功：

```text
不要打 v13.4.34 tag
```

如果真实回测成功并生成 isMock=false report：

```text
可以提交并打 v13.4.34 tag
```

---

# 25. Commit And Tag

如果检查通过且真实 report 成功生成：

```bash
git add .
git commit -m "Add V13.4.34 low-frequency directional research strategy"
git push
git tag v13.4.34
git push origin v13.4.34
```

Docs 仓库归档 prompt 后：

```bash
git add .
git commit -m "Document V13.4.34 low-frequency directional strategy"
git push
git tag v13.4.34-docs
git push origin v13.4.34-docs
```

---

# 26. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. strategy file path
5. strategy parameters
6. long entry 条件说明
7. short entry 条件说明
8. exit logic 说明
9. audit columns 说明
10. backtest timerange
11. backtest 是否成功
12. report 路径
13. isMock 值
14. dryRunApproved 值
15. liveTradingApproved 值
16. total metrics 摘要
17. long metrics 摘要
18. short metrics 摘要
19. baseline comparison 摘要
20. researchWorthContinuing 值
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

V13.4.34 完成后：

如果结果有研究价值：

```text
V13.4.35 - Low-Frequency Directional Result Review and Expanded Validation
```

如果结果无研究价值：

```text
V13.4.35 - Low-Frequency Directional Failure Review and Archive
```

如果 long 好 short 差：

```text
V13.4.35 - Long-Only Low-Frequency Refinement
```

如果 short 好 long 差：

```text
V13.4.35 - Short-Only Low-Frequency Refinement
```

---

# 28. Final Instruction

请严格执行 V13.4.34。

这一次要有真实回测结果。

不要再只做规格。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。

用结果说话。
