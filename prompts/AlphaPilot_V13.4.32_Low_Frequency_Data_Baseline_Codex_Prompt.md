# AlphaPilot V13.4.32 - Low-Frequency Data Preparation and Baseline Builder

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.32。

本版本的目标是：**基于 V13.4.31 的低频主流币研究计划，准备 BTC / ETH / SOL 的 4h / 1d 研究数据，检查数据完整性，并生成 NoTrade / BuyHoldBTC / BuyHoldETH / BuyHoldSOL / EqualWeight 主流币持有基线，为后续低频研究策略提供必须打败的基准线。**

本版本不是策略实现版。
本版本不是 Freqtrade 策略回测版。
本版本不是 Dry-run 版。
本版本不是实盘版。
本版本不接真实 API Key。
本版本不自动交易。
本版本不创建真实订单。

允许下载或补齐公开 OHLCV 数据。
不允许接入私有交易接口。
不允许读取真实账户或持仓。

完成后请输出完整自检报告。

---

# 1. Version Name

```text
AlphaPilot V13.4.32 - Low-Frequency Data Preparation and Baseline Builder
```

中文版本名：

```text
AlphaPilot V13.4.32 - 低频主流币数据准备与基线构建版
```

---

# 2. Background

当前已经完成：

```text
V13.4.27：Market Regime and Data Integrity Review
V13.4.28：Market Data Coverage Repair and Public Data Expansion Skeleton
V13.4.29：Short Rejection 1H Research Strategy
V13.4.30：Short Rejection Failure Review and Negative Research Rules
V13.4.31：Low-Frequency Mainstream Coin Research Plan
```

V13.4.31 已明确下一阶段研究范围：

```text
Pairs:
BTC/USDT:USDT
ETH/USDT:USDT
SOL/USDT:USDT

Primary timeframes:
4h
1d

Optional timeframe:
1h
```

V13.4.31 已确认：

```text
currentStatus: research_plan_only
dryRunApproved: false
liveTradingApproved: false
nextStepRecommendation: V13.4.32 - Low-Frequency Data Preparation and Baseline Builder
```

因此 V13.4.32 只做低频数据准备和持有基线，不写策略，不运行 Freqtrade 策略回测。

---

# 3. Core Goal

V13.4.32 的核心目标：

```text
1. 准备 BTC / ETH / SOL 的 4h / 1d OHLCV 数据
2. 可选准备 1h 数据作为未来 entry refinement
3. 检查低频数据完整性
4. 生成 NoTrade baseline
5. 生成 BuyHoldBTC baseline
6. 生成 BuyHoldETH baseline
7. 生成 BuyHoldSOL baseline
8. 生成 EqualWeight BTC/ETH/SOL buy-hold baseline
9. 生成 regime-aware baseline breakdown
10. 输出 low-frequency data preparation report
11. 输出 low-frequency baseline report
12. 不写交易策略
13. 不运行 Freqtrade strategy backtest
14. 不进入 Dry-run
```

一句话目标：

```text
在写任何低频策略前，先准备干净的数据，并明确简单持有和不交易的基准线。
```

---

# 4. Required Mindset

请遵守以下原则：

```text
1. 没有干净数据，不写策略
2. 没有基线，不评价策略
3. NoTrade 和 BuyHold 是所有主动策略必须打败的基准
4. 低频研究不追求每天都有信号
5. 允许长时间 no trade
6. 不运行策略回测
7. 不进入 Dry-run
8. 不接真实交易能力
9. 公开 OHLCV 可以下载
10. 缺失数据必须记录，不得伪造
```

---

# 5. Non Goals

V13.4.32 不做：

```text
不实现 Freqtrade 策略
不运行 Freqtrade strategy backtest
不实现 long / short 策略代码
不调参
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
不接机器学习模型
不接新闻系统
不接多模型交易决策
```

允许做：

```text
下载公开 OHLCV
读取本地 OHLCV
检查数据完整性
计算 report-only baseline
生成 reports / docs
更新 README
```

---

# 6. Precondition

执行前必须确认：

```text
1. v13.4.31 tag 已存在
2. reports/v13_4_31_low_frequency_research_plan.json 存在
3. reports/v13_4_31_low_frequency_research_summary.md 存在
4. 当前工作区干净
```

如果不满足：

```text
停止 V13.4.32
提示先完成 V13.4.31
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

# 8. Data Scope

必须准备或检查：

```text
BTC/USDT:USDT
ETH/USDT:USDT
SOL/USDT:USDT
```

必须检查：

```text
4h
1d
```

可选检查：

```text
1h
```

默认研究 timerange：

```text
20240101-
```

如果本地数据或交易所下载不支持这么长，至少尝试：

```text
20250701-
20260101-
```

但报告必须记录实际可用范围。

---

# 9. Public Data Download

如果 4h / 1d 数据缺失，允许使用现有公开数据脚本下载：

```powershell
.\scripts\download_data.ps1 -Pairs "BTC/USDT:USDT,ETH/USDT:USDT,SOL/USDT:USDT" -Timeframes "4h,1d" -Timerange "20240101-" -Run
```

如果脚本不支持 1d，则尝试：

```powershell
.\scripts\download_data.ps1 -Pairs "BTC/USDT:USDT,ETH/USDT:USDT,SOL/USDT:USDT" -Timeframes "4h" -Timerange "20240101-" -Run
```

并在报告中写明：

```text
1d data unavailable or download unsupported
```

如果需要 1h optional：

```powershell
.\scripts\download_data.ps1 -Pairs "BTC/USDT:USDT,ETH/USDT:USDT,SOL/USDT:USDT" -Timeframes "1h" -Timerange "20240101-" -Run
```

要求：

```text
1. 只下载公开历史行情
2. 不使用 API Key
3. 不接私有接口
4. 下载失败必须记录原因
5. 不伪造成功
```

---

# 10. Data Integrity Checks

对每个 pair/timeframe 计算：

```text
firstTimestamp
lastTimestamp
actualCandleCount
expectedCandleCount
missingCandleCount
missingRatePct
duplicateTimestampCount
invalidOhlcRows
zeroPriceRows
negativeVolumeRows
extremeReturnRows
volumeZeroStreaks
dataQualityStatus
```

dataQualityStatus：

```text
valid
warning
invalid
unavailable
```

判断规则建议：

```text
missingRatePct <= 5%: valid
5% < missingRatePct <= 20%: warning
missingRatePct > 20%: invalid
invalidOhlcRows > 0: invalid
```

---

# 11. Baseline Definitions

本版本实现 report-only baseline，不实现交易策略。

---

## 11.1 NoTrade Baseline

定义：

```text
return = 0
maxDrawdown = 0
tradeCount = 0
exposureTimePct = 0
```

用途：

```text
主动策略必须证明比不交易更值得承担风险。
```

---

## 11.2 BuyHoldBTC / ETH / SOL

对每个 pair：

```text
buyHoldReturnPct = lastClose / firstClose - 1
maxDrawdownPct = max drawdown of buy-hold equity curve
volatilityPct = realized volatility
bestDrawupPct
worstDrawdownPct
```

注意：

```text
这不是交易策略，不生成订单。
只是 report-only baseline。
```

---

## 11.3 EqualWeight Mainstream BuyHold

定义：

```text
BTC / ETH / SOL 等权持有
```

初始权重：

```text
1/3 each
```

可用最简单方式：

```text
对每个 timestamp 使用三者 normalized return 的平均值
```

如果某 pair 时间戳不完全对齐：

```text
使用 inner join
记录 dropped rows
```

---

# 12. Regime-Aware Baseline Breakdown

如果 V13.4.27 regime labels 可用，必须输出：

```text
bull regime baseline performance
bear regime baseline performance
sideways regime baseline performance
crash / high-volatility if available
```

如果无法对齐 regime labels：

```text
regimeBreakdownAvailable = false
warning
```

不要伪造 regime 标签。

---

# 13. Low-Frequency Baseline Report

新增报告生成器：

```text
alphapilot/reports/generate_low_frequency_baseline_report.py
alphapilot/reports/low_frequency_baseline_schema.py
```

输出：

```text
reports/v13_4_32_low_frequency_data_report.json
reports/v13_4_32_low_frequency_baseline_report.json
reports/v13_4_32_low_frequency_baseline_summary.md
```

---

# 14. Report Schema

主 baseline report 结构：

```json
{
  "reportId": "v13_4_32_low_frequency_baseline",
  "scope": {
    "pairs": ["BTC/USDT:USDT", "ETH/USDT:USDT", "SOL/USDT:USDT"],
    "timeframes": ["4h", "1d"],
    "timerangeRequested": "20240101-",
    "timerangeAvailable": {}
  },
  "dataQuality": {},
  "baselines": {
    "noTrade": {},
    "buyHoldBTC": {},
    "buyHoldETH": {},
    "buyHoldSOL": {},
    "equalWeightMainstream": {}
  },
  "regimeBreakdown": {},
  "benchmarkRequirementsForFutureStrategy": [],
  "dryRunApproved": false,
  "liveTradingApproved": false,
  "generatedAt": "UTC ISO"
}
```

---

# 15. Benchmark Requirements For Future Strategy

报告必须生成后续低频策略必须打败的标准。

建议：

```text
1. 必须跑赢 NoTrade
2. 必须在滑点后风险调整表现优于 BuyHoldBTC 或 EqualWeight baseline
3. 最大回撤必须显著低于简单持有，或者收益明显超过简单持有
4. 交易次数不能过高
5. bear / crash regime 中必须能减少错误暴露
6. long / short 必须分开报告
```

---

# 16. Files To Add

建议新增：

```text
alphapilot/low_frequency/__init__.py
alphapilot/low_frequency/low_frequency_data_schema.py
alphapilot/low_frequency/low_frequency_data_checker.py
alphapilot/low_frequency/baseline_builder.py
alphapilot/reports/generate_low_frequency_baseline_report.py
alphapilot/reports/low_frequency_baseline_schema.py
scripts/build_low_frequency_baselines.ps1
reports/v13_4_32_low_frequency_data_report.json
reports/v13_4_32_low_frequency_baseline_report.json
reports/v13_4_32_low_frequency_baseline_summary.md
docs/V13.4.32-low-frequency-data-baseline-builder.md
docs/low-frequency-data-quality-checks.md
docs/no-trade-buyhold-mainstream-baselines.md
docs/future-low-frequency-strategy-requirements.md
```

---

# 17. Files To Modify

可能修改：

```text
README.md
docs/mainstream-coin-research-scope.md
docs/low-frequency-research-hypotheses.md
```

不要修改：

```text
user_data/strategies/*.py
user_data/config/config.backtest.json
AlphaPilot Mobile App
```

---

# 18. PowerShell Script

新增：

```text
scripts/build_low_frequency_baselines.ps1
```

支持参数：

```powershell
-Timerange
-Pairs
-Timeframes
-RunDownload
```

默认：

```text
Timerange = 20240101-
Pairs = BTC/USDT:USDT,ETH/USDT:USDT,SOL/USDT:USDT
Timeframes = 4h,1d
RunDownload = false
```

脚本默认只生成 baseline，不强制下载。

如果加 `-RunDownload` 才执行公开数据下载。

---

# 19. Commands To Run

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m compileall alphapilot
```

如需补数据：

```powershell
.\scripts\download_data.ps1 -Pairs "BTC/USDT:USDT,ETH/USDT:USDT,SOL/USDT:USDT" -Timeframes "4h,1d" -Timerange "20240101-" -Run
```

生成 baseline：

```powershell
python -m alphapilot.reports.generate_low_frequency_baseline_report
powershell -ExecutionPolicy Bypass -File scripts\build_low_frequency_baselines.ps1
```

安全检查：

```powershell
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

不运行 Freqtrade strategy backtest。

---

# 20. Safety Requirements

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
不运行 Freqtrade strategy backtest
不修改 AlphaPilot Mobile App
不修改策略代码
```

允许：

```text
下载公开 OHLCV
读取本地公开 OHLCV
计算 report-only baselines
生成 reports / docs
```

---

# 21. Acceptance Criteria

完成后必须满足：

```text
1. low_frequency package 已创建
2. data checker 已创建
3. baseline builder 已创建
4. baseline report generator 已创建
5. PowerShell script 已创建
6. BTC/ETH/SOL 4h/1d 数据检查完成
7. data report 已生成
8. NoTrade baseline 已生成
9. BuyHoldBTC baseline 已生成
10. BuyHoldETH baseline 已生成
11. BuyHoldSOL baseline 已生成
12. EqualWeight baseline 已生成
13. regime-aware breakdown 已生成或 unavailable warning
14. future strategy benchmark requirements 已生成
15. README / docs 更新
16. 不写策略
17. 不运行 Freqtrade strategy backtest
18. 不进入 Dry-run
19. 不接真实 API Key
20. 不接 Trade API
21. 不接 Withdraw API
22. 不读取真实账户
23. 不读取真实持仓
24. 不创建真实订单
25. 不自动交易
26. compileall 通过
27. validate_config 通过
28. safety scan 通过
29. git diff --check 通过
```

如果数据严重不足：

```text
生成 insufficient data report
不要打 v13.4.32 tag
```

如果 baseline report 成功生成：

```text
可以提交并打 v13.4.32 tag
```

---

# 22. Commit And Tag

如果检查通过且 baseline report 成功生成：

```bash
git add .
git commit -m "Add V13.4.32 low-frequency data baselines"
git push
git tag v13.4.32
git push origin v13.4.32
```

Docs 仓库归档 prompt 后：

```bash
git add .
git commit -m "Document V13.4.32 low-frequency baselines"
git push
git tag v13.4.32-docs
git push origin v13.4.32-docs
```

---

# 23. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. timerange requested
5. timerange available
6. pairs checked
7. timeframes checked
8. data quality summary
9. whether data was downloaded
10. NoTrade baseline
11. BuyHoldBTC baseline
12. BuyHoldETH baseline
13. BuyHoldSOL baseline
14. EqualWeight baseline
15. regime breakdown availability
16. future benchmark requirements
17. data report path
18. baseline report path
19. baseline summary path
20. 是否写策略
21. 是否运行 Freqtrade strategy backtest
22. 是否进入 Dry-run
23. 是否保存真实 API Key
24. 是否接 Trade API
25. 是否接 Withdraw API
26. 是否读取真实账户
27. 是否读取真实持仓
28. 是否创建真实订单
29. 是否自动交易
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

# 24. Next Version Preview

V13.4.32 完成后，下一步建议：

```text
V13.4.33 - Low-Frequency Simple Baseline Strategy Research
```

目标：

```text
1. 实现 research-only Simple 4h EMA Trend
2. 实现 research-only Simple 4h Breakout Retest
3. 实现 research-only Simple 4h Bear Rejection
4. 跑 BTC/ETH/SOL 4h research backtest
5. 与 V13.4.32 baselines 比较
6. 仍然不进入 Dry-run
```

如果数据不足：

```text
V13.4.33 - Low-Frequency Data Repair
```

---

# 25. Final Instruction

请严格执行 V13.4.32。

本版本只做 BTC/ETH/SOL 低频数据准备和 report-only baseline 构建。

不要写策略。

不要运行 Freqtrade strategy backtest。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。
