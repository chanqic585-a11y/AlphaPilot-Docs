# AlphaPilot V13.4.29 - Short Rejection 1H Research Strategy

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.29。

本版本的目标是：**新增一个简单、可解释、research-only 的 1小时做空研究策略 AlphaPilot Short Rejection 1H V0.1，并直接进行真实 Freqtrade smoke / expanded research backtest。**

这次不要再先写一版纯规格文档。
这次也不要堆太多前置条件。
核心原则是：

```text
少量硬风控
简单方向评分
真实回测验证
失败就复盘，不无限调参
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
AlphaPilot V13.4.29 - Short Rejection 1H Research Strategy
```

中文版本名：

```text
AlphaPilot V13.4.29 - 1小时反弹失败做空研究策略版
```

---

# 2. Background

当前项目已经完成：

```text
V13.4.27：Market Regime and Data Integrity Review
V13.4.28：Market Data Coverage Repair and Public Data Expansion Skeleton
```

V13.4.27 结论：

```text
本地 OHLCV 没发现明显 OHLC 破坏。
近期 long-only 技术策略失败，更像是 bear/high-volatility 市场环境 + alpha 验证不足共同导致。
```

V13.4.28 结论：

```text
OHLCV repair command 已执行。
但仍有 4 个缺口：
FET/USDT:USDT 1h
FET/USDT:USDT 4h
TON/USDT:USDT 1h
TON/USDT:USDT 4h

ORDI/USDT:USDT 4h 仍有 extreme return warning。
V13.4.28 状态：completed_with_unresolved_gaps
```

用户当前要求：

```text
做空不一定只在熊市才生效。
只要指标合适，也可以研究开仓做空。
不要限定太多条件，否则系统一直写不完。
```

因此 V13.4.29 不再先做复杂规格，而是直接做一个简单做空研究策略并回测。

---

# 3. Core Goal

V13.4.29 的核心目标：

```text
1. 新增 AlphaPilotShortRejection1HV01.py
2. can_short = True
3. 只研究 short entry，不做 long entry
4. 不把 bear regime 当作硬性开关
5. 使用简单 shortScore 评分入场
6. 避免追空
7. 先跑 BTC / ETH / SOL smoke backtest
8. smoke 成功后跑 supportedPairs expanded backtest
9. 输出 short-only research report
10. dryRunApproved=false
11. liveTradingApproved=false
```

一句话目标：

```text
用简单做空研究策略验证：在当前市场中，反弹失败做空是否比继续做多更有研究价值。
```

---

# 4. Required Mindset

请遵守以下原则：

```text
1. 不要再堆很多 gate
2. Market Regime 只作为背景统计，不作为第一版硬开关
3. 做空策略不能简单反转做多策略
4. 不追空
5. 不马丁
6. 不加仓摊平
7. 不进入 Dry-run
8. 不接真实 API Key
9. 不自动交易
10. 用真实回测结果说话
```

---

# 5. Non Goals

V13.4.29 不做：

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
不实现多模型
不接新闻数据
不接机器学习
不做马丁
不做自动加仓
不做无限补仓
不做 long/short 混合策略
```

允许做：

```text
新增 Freqtrade research strategy
运行 public historical data backtest
生成 short-only report
记录 market regime 背景
记录 excludedPairs / watchlistPairs
更新 README / docs
```

---

# 6. Precondition

执行前必须确认：

```text
1. v13.4.28 tag 已存在
2. reports/v13_4_28_data_coverage_repair_report.json 存在
3. reports/v13_4_28_market_data_expansion_report.json 存在
4. Docker / Freqtrade 可用
5. 当前工作区干净
```

如果 Docker 不可用：

```text
可以提交策略代码和 blocked report
不要声称 V13.4.29 完成
不要打 v13.4.29 tag
```

---

# 7. Data Handling Decision

V13.4.28 仍有 FET / TON 缺口。

本版本不要继续卡在数据修复。

处理规则：

```text
FET/USDT:USDT：暂时 excluded
TON/USDT:USDT：暂时 excluded
ORDI/USDT:USDT：加入 watchlist，保留但报告中标记 extreme_return_warning
```

报告必须记录：

```text
excludedPairs
watchlistPairs
exclusionReasons
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

# 9. Strategy File

新增：

```text
user_data/strategies/AlphaPilotShortRejection1HV01.py
```

策略类：

```python
class AlphaPilotShortRejection1HV01(IStrategy):
    ...
```

策略声明：

```text
strategyId: alpha_short_rejection_1h_v01
strategyName: AlphaPilot Short Rejection 1H V0.1
status: research_backtest_only
```

必须在文件头写：

```text
本策略仅用于研究和回测。
不得用于 Dry-run。
不得用于实盘。
不得接真实 API Key。
不得自动交易。
```

---

# 10. Strategy Base Parameters

必须设置：

```text
timeframe = "1h"
can_short = True
stoploss = -0.025
minimal_roi = {"0": 0.05}
```

说明：

```text
stoploss = -0.025 表示 2.5% 风险距离
minimal_roi = 5%
只研究 short entry
不生成 long entry
```

如果 Freqtrade 对 futures short 需要额外配置，请只在回测命令中使用，不修改默认 config 为 live。

---

# 11. Indicators

在 `populate_indicators` 中计算：

```text
EMA20
EMA50
EMA200
RSI14
MACD
MACD signal
MACD histogram
volume rolling mean 20
volumeRatio
Bollinger Bands 20 / 2
ATR14
recentReturn12h
```

可选：

```text
4h EMA20/EMA50/EMA200
BTC 1h return
```

但注意：

```text
第一版不要因为 informative data 不稳定而阻塞策略。
Market/BTC 只做 audit/background，除非数据稳定。
```

---

# 12. Entry Logic - Short Score

不要使用十几个强制条件。

采用简单 `shortScore`。

每根 K 线计算：

```text
shortScore = 0
```

满足下列条件加分。

---

## 12.1 Rejection Area

价格最近反弹到 EMA20 / EMA50 附近：

```text
high >= ema20 * 0.995
or high >= ema50 * 0.995
```

加 1 分。

---

## 12.2 Close Back Below EMA20

```text
close < ema20
```

加 1 分。

---

## 12.3 MACD Weakening

```text
macdhist < macdhist.shift(1)
```

加 1 分。

---

## 12.4 RSI Weak After Rebound

```text
rsi < 55
and rsi < rsi.shift(1)
```

加 1 分。

---

## 12.5 Volume Confirmation

```text
volumeRatio >= 1.0
```

加 1 分。

---

# 13. Hard Blockers

只保留少量硬性 blocker。

---

## 13.1 No Data

如果必要指标缺失：

```text
no entry
```

---

## 13.2 No Chase After Large Drop

避免追空。

```text
recentReturn12h <= -0.08
```

即最近 12 根 1h K线已经跌超 8%，不新开空。

---

## 13.3 Extreme ATR

如果实现简单，可以使用：

```text
atr_pct > 0.10
```

则不新开空。

如果 ATR 逻辑不稳定，可以先只 audit，不做硬 blocker。

---

# 14. Final Short Entry

第一版规则：

```text
enter_short = 1
if shortScore >= 4
and noChaseAfterLargeDrop = true
and volume > 0
and required data exists
```

注意：

```text
不要求 bear regime
不要求 BTC 必须熊市
不要求 funding / OI
不要求 probability gate
不要求多模型
```

Market regime 只记录，不硬拦。

---

# 15. Exit Logic

## 15.1 Stoploss

```text
stoploss = -0.025
```

## 15.2 Take Profit

```text
minimal_roi = {"0": 0.05}
```

## 15.3 Time Stop

使用 `custom_exit`。

规则：

```text
持仓 >= 8 小时
且 current_profit <= 0
则退出
```

返回：

```text
short_time_stop_8h_not_profitable
```

## 15.4 Profitable Momentum Exit

只在盈利时启用：

```text
current_profit > 0
and macdhist > macdhist.shift(1)
and macdhist.shift(1) > macdhist.shift(2)
```

说明：

```text
做空盈利后，如果下跌动能连续减弱，则退出。
```

返回：

```text
profitable_short_momentum_exit
```

---

# 16. Audit Columns

为策略加入审计列，前缀：

```text
ap_short_audit_
```

至少包含：

```text
ap_short_audit_rejection_area
ap_short_audit_close_below_ema20
ap_short_audit_macd_weakening
ap_short_audit_rsi_weak
ap_short_audit_volume_confirm
ap_short_audit_short_score
ap_short_audit_no_chase
ap_short_audit_final_short_entry
ap_short_audit_skip_reason
```

skip reasons：

```text
data_missing
score_too_low
chase_after_large_drop
extreme_atr
entry_signal_passed
unknown
```

---

# 17. Backtest Scope

本版本可以直接执行 smoke + expanded。

---

## 17.1 Smoke Backtest

Pairs：

```text
BTC/USDT:USDT
ETH/USDT:USDT
SOL/USDT:USDT
```

Timerange：

```text
20260401-
```

Timeframe：

```text
1h
```

---

## 17.2 Expanded Backtest

如果 smoke 成功，再跑：

```text
supportedPairs from V13.4.28
exclude FET/TON
watchlist ORDI
timerange 20260101-
timeframe 1h
```

如果 expanded 太重，可以先跑 supported Top10，并在 report 中说明。

---

# 18. Report Requirements

新增 report generator：

```text
alphapilot/reports/generate_short_rejection_report.py
alphapilot/reports/short_rejection_report_schema.py
```

输出：

```text
reports/v13_4_29_short_rejection_1h_report.json
reports/v13_4_29_short_rejection_1h_summary.md
```

报告必须包含：

```text
isMock=false if real backtest success
dryRunApproved=false
liveTradingApproved=false
strategyId
pairs
timerange
tradeCount
shortTradeCount
totalReturnPct
slippageAdjustedTotalReturnPct
maxDrawdownPct
profitFactor
slippageAdjustedProfitFactor
winRate
maxConsecutiveLosses
exitReasonBreakdown
pairPerformance
monthlyPerformance
regimeBackground if available
excludedPairs
watchlistPairs
warnings
```

如果没有真实 backtest result：

```text
不要生成 isMock=false
不要打 tag
```

---

# 19. Slippage

必须加入报告层滑点后处理。

默认：

```text
0.05% one-way
```

可额外输出压力：

```text
0.10%
0.20%
```

必须写：

```text
slippageAppliedByFreqtrade = false
slippageAppliedByPostProcessing = true
```

如果无法计算：

```text
slippageAdjustedMetrics = null
warning
```

---

# 20. Quality Gate

本版本不批准 Dry-run。

但报告判断是否值得继续研究。

Research gate 建议：

```text
slippageAdjustedProfitFactor > 1.05
maxDrawdown 可接受
tradeCount >= 20
maxConsecutiveLosses 可接受
no single pair dominates
```

即使满足：

```text
dryRunApproved=false
```

---

# 21. Files To Add

建议新增：

```text
user_data/strategies/AlphaPilotShortRejection1HV01.py
alphapilot/reports/generate_short_rejection_report.py
alphapilot/reports/short_rejection_report_schema.py
scripts/run_short_rejection_backtest.ps1
reports/v13_4_29_short_rejection_1h_report.json
reports/v13_4_29_short_rejection_1h_summary.md
docs/V13.4.29-short-rejection-1h-research-strategy.md
docs/short-rejection-1h-strategy-rules.md
docs/short-strategy-risk-notes.md
```

---

# 22. Files To Modify

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
AlphaPilotVolumeReboundV02Candidates.py
config.backtest.json
AlphaPilot Mobile App
```

---

# 23. PowerShell Script

新增：

```text
scripts/run_short_rejection_backtest.ps1
```

支持：

```powershell
-Smoke
-Expanded
-Timerange
-Pairs
-UseSupportedPairs
-Run
```

默认只打印命令。

示例：

```powershell
.\scripts\run_short_rejection_backtest.ps1 -Smoke
.\scripts\run_short_rejection_backtest.ps1 -Smoke -Run
.\scripts\run_short_rejection_backtest.ps1 -Expanded -UseSupportedPairs -Timerange "20260101-" -Run
```

---

# 24. Commands To Run

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m py_compile user_data\strategies\AlphaPilotShortRejection1HV01.py
python -m compileall alphapilot
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
```

Smoke：

```powershell
.\scripts\run_short_rejection_backtest.ps1 -Smoke -Run
```

Expanded：

```powershell
.\scripts\run_short_rejection_backtest.ps1 -Expanded -UseSupportedPairs -Timerange "20260101-" -Run
```

Generate report：

```powershell
python -m alphapilot.reports.generate_short_rejection_report
```

Final check：

```powershell
git diff --check
```

---

# 25. Safety Requirements

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
生成 short-only research report
```

---

# 26. Acceptance Criteria

完成后必须满足：

```text
1. AlphaPilotShortRejection1HV01.py 已创建
2. can_short=True
3. timeframe=1h
4. 只生成 enter_short，不生成 enter_long
5. shortScore 已实现
6. no chase after large drop 已实现
7. stoploss 2.5% 已实现
8. ROI 5% 已实现
9. 8h time stop 已实现
10. profitable momentum exit 已实现或有 fallback 说明
11. audit columns 已实现
12. smoke backtest 成功或失败原因明确
13. expanded backtest 成功或失败原因明确
14. report 已生成
15. report dryRunApproved=false
16. report liveTradingApproved=false
17. excludedPairs 包含 FET/TON 或说明
18. watchlistPairs 包含 ORDI 或说明
19. 不进入 Dry-run
20. 不接真实 API Key
21. 不接 Trade API
22. 不接 Withdraw API
23. 不读取真实账户
24. 不读取真实持仓
25. 不创建真实订单
26. 不自动交易
27. py_compile 通过
28. compileall 通过
29. validate_config 通过
30. safety scan 通过
31. git diff --check 通过
```

如果真实 smoke backtest 未成功：

```text
不要打 v13.4.29 tag
```

如果 smoke 成功且 report 生成：

```text
可以提交并打 v13.4.29 tag
```

Expanded 如果失败但 smoke 成功：

```text
可以提交，但必须在 report 中标记 expandedFailed。
是否打 tag 取决于 smoke 是否真实成功且失败原因清楚。
```

---

# 27. Commit And Tag

如果检查通过且真实 smoke report 成功生成：

```bash
git add .
git commit -m "Add V13.4.29 Short Rejection 1H research strategy"
git push
git tag v13.4.29
git push origin v13.4.29
```

Docs 仓库归档 prompt 后：

```bash
git add .
git commit -m "Document V13.4.29 short rejection strategy"
git push
git tag v13.4.29-docs
git push origin v13.4.29-docs
```

---

# 28. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. strategy file path
5. strategy parameters
6. shortScore 条件说明
7. hard blockers 说明
8. exit logic 说明
9. audit columns 说明
10. smoke backtest 是否运行
11. smoke backtest 结果
12. expanded backtest 是否运行
13. expanded backtest 结果
14. report 路径
15. isMock 值
16. dryRunApproved 值
17. liveTradingApproved 值
18. excludedPairs
19. watchlistPairs
20. 是否进入 Dry-run
21. 是否保存真实 API Key
22. 是否接 Trade API
23. 是否接 Withdraw API
24. 是否读取真实账户
25. 是否读取真实持仓
26. 是否创建真实订单
27. 是否自动交易
28. py_compile 结果
29. compileall 结果
30. validate_config 结果
31. safety scan 结果
32. git diff --check 结果
33. commit hash
34. push 结果
35. tag 结果
36. 已知问题
37. 下一步建议
```

---

# 29. Next Version Preview

V13.4.29 完成后：

如果 short research 表现不错：

```text
V13.4.30 - Short Rejection Result Review and Directional Router Plan
```

如果 short research 表现很差：

```text
V13.4.30 - Short Strategy Failure Review and Archive as Benchmark
```

如果 smoke 通过但 expanded 崩溃：

```text
V13.4.30 - Pair Scope / Regime Scope Review
```

---

# 30. Final Instruction

请严格执行 V13.4.29。

本版本直接做一个简单做空研究策略并回测。

不要堆过多条件。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。

失败就复盘，不无限调参。
