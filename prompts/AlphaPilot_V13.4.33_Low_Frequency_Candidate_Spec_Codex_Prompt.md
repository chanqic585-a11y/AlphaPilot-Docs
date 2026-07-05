# AlphaPilot V13.4.33 - Low-Frequency Strategy Candidate Specification and Baseline Hurdles

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.33。

本版本的目标是：**基于 V13.4.32 生成的 BTC / ETH / SOL 低频数据与 NoTrade / BuyHold / EqualWeight 基线，设计第一批低频主流币策略候选规格，并明确每个候选必须打败的 baseline hurdle。**

本版本是 report-only / spec-only。
本版本不是策略代码实现版。
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
AlphaPilot V13.4.33 - Low-Frequency Strategy Candidate Specification and Baseline Hurdles
```

中文版本名：

```text
AlphaPilot V13.4.33 - 低频策略候选规格与基线门槛版
```

---

# 2. Background

当前已经完成：

```text
V13.4.31：Low-Frequency Mainstream Coin Research Plan
V13.4.32：Low-Frequency Data Preparation and Baseline Builder
```

V13.4.31 已定义低频研究范围：

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

V13.4.32 已完成：

```text
BTC/ETH/SOL 4h / 1d public OHLCV 数据准备
6 个 pair/timeframe 全部 valid
NoTrade baseline
BuyHoldBTC baseline
BuyHoldETH baseline
BuyHoldSOL baseline
EqualWeight BTC/ETH/SOL baseline
regime-aware baseline breakdown
```

V13.4.32 明确：

```text
未来低频主动策略必须打败 NoTrade / BuyHold / EqualWeight 基线。
```

现在进入 V13.4.33。

---

# 3. Core Goal

V13.4.33 的核心目标：

```text
1. 读取 V13.4.32 low-frequency baseline report
2. 提取 NoTrade / BuyHoldBTC / BuyHoldETH / BuyHoldSOL / EqualWeight 基线
3. 生成低频策略必须打败的 baseline hurdles
4. 设计第一批低频策略候选规格
5. 每个候选只保留 4-6 个核心条件，避免过度复杂化
6. long / short 都可以研究，但必须分方向报告
7. regime 作为方向评分背景，不作为唯一硬开关
8. 生成 V13.4.34 实现与研究回测计划
9. 不写策略代码
10. 不运行回测
11. 不进入 Dry-run
```

一句话目标：

```text
先把低频策略候选写清楚，并明确它们必须超过哪些基准线，再进入代码实现。
```

---

# 4. Required Mindset

请遵守以下原则：

```text
1. 简单优先，不堆 10+ 个 gate
2. 每个候选第一版最多 4-6 个核心条件
3. 策略候选不是交易策略
4. spec 通过不代表可以 Dry-run
5. 不因为熊市就禁止做空，也不因为牛市就禁止做空
6. regime 是方向评分背景，不是唯一硬开关
7. 所有候选必须明确 baseline hurdle
8. 不运行 backtest
9. 不接真实交易能力
10. 所有输出必须 research-only
```

---

# 5. Non Goals

V13.4.33 不做：

```text
不实现 Freqtrade 策略
不运行 Freqtrade strategy backtest
不下载新数据
不调参
不修改现有策略
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
读取 V13.4.32 baseline report
生成策略候选规格
生成 baseline hurdle
生成实现计划
生成 docs / reports
更新 README
```

---

# 6. Precondition

执行前必须确认：

```text
1. v13.4.32 tag 已存在
2. reports/v13_4_32_low_frequency_data_report.json 存在
3. reports/v13_4_32_low_frequency_baseline_report.json 存在
4. reports/v13_4_32_low_frequency_baseline_summary.md 存在
5. 当前工作区干净
```

如果不满足：

```text
停止 V13.4.33
提示先完成 V13.4.32
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
reports/v13_4_32_low_frequency_data_report.json
reports/v13_4_32_low_frequency_baseline_report.json
reports/v13_4_32_low_frequency_baseline_summary.md
```

可参考：

```text
reports/v13_4_31_low_frequency_research_plan.json
reports/v13_4_27_market_regime_data_integrity_report.json
reports/v13_4_30_negative_research_rules.json
```

如果字段缺失：

```text
写入 warnings
不要伪造
```

---

# 9. Baseline Hurdles

V13.4.33 必须生成 baseline hurdle。

---

## 9.1 Universal Hurdles

所有候选未来回测必须满足：

```text
dryRunApproved = false
liveTradingApproved = false
mustBeatNoTrade = true
mustReportVsBuyHold = true
mustReportVsEqualWeight = true
mustReportRegimeBreakdown = true
mustReportLongShortBreakdown = true
mustReportSlippageAdjustedMetrics = true
```

---

## 9.2 NoTrade Hurdle

候选必须至少证明：

```text
slippageAdjustedReturnPct > 0
maxDrawdownPct 可接受
tradeCount 不为 0 且不过度
```

否则不如不交易。

---

## 9.3 Pair-Specific BuyHold Hurdle

对于单币种候选：

```text
BTC strategy 必须与 BuyHoldBTC 比较
ETH strategy 必须与 BuyHoldETH 比较
SOL strategy 必须与 BuyHoldSOL 比较
```

不要求第一版一定跑赢 BuyHold，但必须报告：

```text
excessReturnVsPairBuyHold
drawdownReductionVsPairBuyHold
riskAdjustedComparison
```

---

## 9.4 EqualWeight Hurdle

对于组合候选：

```text
必须与 EqualWeight BTC/ETH/SOL baseline 比较
```

至少报告：

```text
excessReturnVsEqualWeight
drawdownReductionVsEqualWeight
monthlyStabilityVsEqualWeight
```

---

## 9.5 Regime Hurdle

候选必须报告：

```text
bull performance
bear performance
sideways performance
crash/high-volatility performance
noTradeRatio by regime
```

特别要求：

```text
bear / crash 中如果方向错误，必须有低 exposure 或明确避险机制。
```

---

# 10. Candidate Strategy Specs

V13.4.33 至少生成 4 个候选规格。

每个候选字段：

```text
candidateId
name
direction
timeframe
pairs
coreConditions
exitConcept
riskConcept
baselineHurdles
expectedStrength
knownRisk
validationPlan
invalidationRules
status
```

status 必须为：

```text
spec_only
research_only
```

---

# 11. Candidate A - 4H EMA Trend Long

```text
candidateId: LF-CAND-A-4H-EMA-TREND-LONG
name: 4H EMA Trend Long
direction: long
timeframe: 4h
pairs: BTC/ETH/SOL
```

核心条件不超过 5 个：

```text
1. 4h close > EMA200
2. 4h EMA20 > EMA50
3. close pullback near EMA20 or EMA50
4. close reclaims EMA20
5. volume not collapsing
```

退出概念：

```text
stoploss: 2.5% - 4%
take profit: 1.5R - 2R 或 trailing
time stop: 5-10 根 4h
```

适用：

```text
bull / recovery / neutral-positive
```

但 regime 不作为唯一硬开关，只作为评分背景。

---

# 12. Candidate B - 4H Bear Rejection Short

```text
candidateId: LF-CAND-B-4H-BEAR-REJECTION-SHORT
name: 4H Bear Rejection Short
direction: short
timeframe: 4h
pairs: BTC/ETH/SOL
```

核心条件不超过 5 个：

```text
1. 4h price rejects EMA20 or EMA50 area
2. close falls back below EMA20
3. MACD histogram weakens
4. RSI below 55 or falling
5. no chase after large drop
```

退出概念：

```text
stoploss: 2.5% - 4%
take profit: 1.5R - 2R
time stop: 5-10 根 4h
```

适用：

```text
bear / weak recovery / failed bounce
```

但不限定只有 bear 才能做空。

---

# 13. Candidate C - 1D Regime + 4H Entry Directional Router

```text
candidateId: LF-CAND-C-1D-REGIME-4H-ENTRY-ROUTER
name: 1D Regime + 4H Entry Directional Router
direction: long_or_short
timeframe: 1d regime + 4h entry
pairs: BTC/ETH/SOL
```

核心条件：

```text
1. 1d regime gives directional bias
2. 4h confirms direction
3. avoidScore not high
4. longScore or shortScore exceeds threshold
5. entry not extended
```

定位：

```text
不是单一策略，而是 low-frequency directional router。
```

后续实现时可先只做 report-only scoring。

---

# 14. Candidate D - 4H Breakout Retest Long/Short

```text
candidateId: LF-CAND-D-4H-BREAKOUT-RETEST
name: 4H Breakout / Breakdown Retest
direction: long_or_short
timeframe: 4h
pairs: BTC/ETH/SOL
```

Long 草案：

```text
1. break recent N-bar high
2. retest holds breakout level
3. volume not collapsing
4. close resumes upward
```

Short 草案：

```text
1. break recent N-bar low
2. retest fails below breakdown level
3. volume not collapsing
4. close resumes downward
```

风险：

```text
假突破
样本少
需要清晰定义 support/resistance
```

---

# 15. Candidate E - NoTrade / Defensive Regime Module

```text
candidateId: LF-CAND-E-NOTRADE-DEFENSIVE-REGIME
name: NoTrade as Active Decision
direction: no_trade_filter
timeframe: 1d / 4h
pairs: BTC/ETH/SOL
```

目的：

```text
不是赚钱模块，而是减少错误交易。
```

条件草案：

```text
1. crash/high-volatility regime
2. direction scores conflict
3. liquidity / spread unavailable
4. extreme candle or data anomaly
```

输出：

```text
no_trade
observe_only
```

该候选必须作为所有其他候选的防御层之一。

---

# 16. Directional Score Framework

V13.4.33 必须定义简化方向评分。

```text
longScore: 0-5
shortScore: 0-5
avoidScore: 0-5
```

用途：

```text
不是最终交易信号
只是未来候选实现时的统一解释层
```

要求：

```text
每个 score 使用少量因素
不要超过 5 个输入
```

例如：

```text
longScore:
trend up
pullback quality
reclaim
volume health
regime supportive

shortScore:
trend down or rejection
failed bounce
momentum weakening
no chase
regime supportive

avoidScore:
crash
extreme volatility
data issue
direction conflict
liquidity unavailable
```

---

# 17. Validation Plan For V13.4.34

V13.4.33 必须生成 V13.4.34 计划。

推荐：

```text
V13.4.34 - Low-Frequency Candidate Implementation and Research Backtest
```

但为了不一次写太多，V13.4.34 可以只实现两个候选：

```text
Candidate A: 4H EMA Trend Long
Candidate B: 4H Bear Rejection Short
```

Scope：

```text
BTC/ETH/SOL
4h
timerange: 20240101-
```

输出：

```text
candidate comparison report
baseline comparison
regime breakdown
dryRunApproved=false
```

---

# 18. Files To Add

建议新增：

```text
alphapilot/low_frequency/low_frequency_candidate_schema.py
alphapilot/low_frequency/low_frequency_candidate_specs.py
alphapilot/low_frequency/directional_score_spec.py
alphapilot/reports/generate_low_frequency_candidate_spec_report.py
alphapilot/reports/low_frequency_candidate_spec_schema.py
reports/v13_4_33_low_frequency_candidate_spec_report.json
reports/v13_4_33_low_frequency_candidate_spec_summary.md
docs/V13.4.33-low-frequency-candidate-specification.md
docs/low-frequency-baseline-hurdles.md
docs/low-frequency-directional-score-framework.md
docs/v13_4_34_candidate-implementation-plan.md
```

---

# 19. Files To Modify

可能修改：

```text
README.md
docs/future-low-frequency-strategy-requirements.md
docs/low-frequency-research-hypotheses.md
```

不要修改：

```text
user_data/strategies/*.py
user_data/config/config.backtest.json
AlphaPilot Mobile App
```

---

# 20. Report Schema

主报告结构建议：

```json
{
  "reportId": "v13_4_33_low_frequency_candidate_specs",
  "sourceBaselineReport": "reports/v13_4_32_low_frequency_baseline_report.json",
  "currentStatus": "spec_only",
  "dryRunApproved": false,
  "liveTradingApproved": false,
  "scope": {},
  "baselineHurdles": {},
  "candidates": [],
  "directionalScoreFramework": {},
  "v13_4_34Plan": {},
  "warnings": [],
  "generatedAt": "UTC ISO"
}
```

---

# 21. README / Docs Update

README 增加：

```text
## V13.4.33 Low-Frequency Candidate Specification
```

中文说明：

```text
V13.4.33 基于 V13.4.32 的低频基线，设计 BTC/ETH/SOL 4h/1d 低频候选策略规格与 baseline hurdles。
本版本不写策略、不回测、不进入 Dry-run、不接 API Key。
```

新增 docs：

```text
docs/V13.4.33-low-frequency-candidate-specification.md
docs/low-frequency-baseline-hurdles.md
docs/low-frequency-directional-score-framework.md
docs/v13_4_34_candidate-implementation-plan.md
```

---

# 22. Commands To Run

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m compileall alphapilot
python -m alphapilot.reports.generate_low_frequency_candidate_spec_report
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

不运行 backtest。

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
不运行 backtest
不下载外部数据
不修改 AlphaPilot Mobile App
不修改策略代码
```

允许：

```text
读取本地 baseline report
生成候选规格
生成 docs
更新 README
```

---

# 24. Acceptance Criteria

完成后必须满足：

```text
1. low frequency candidate schema 已创建
2. candidate specs 已创建
3. directional score spec 已创建
4. candidate spec report generator 已创建
5. candidate spec report 已生成
6. candidate spec summary 已生成
7. baseline hurdles 已定义
8. 至少 4 个 strategy candidates
9. 包含 NoTrade defensive module
10. 每个候选 status=spec_only/research_only
11. 定义 V13.4.34 plan
12. dryRunApproved=false
13. liveTradingApproved=false
14. README / docs 更新
15. 不写策略
16. 不运行 backtest
17. 不下载数据
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

---

# 25. Commit And Tag

如果检查通过：

```bash
git add .
git commit -m "Add V13.4.33 low-frequency candidate specifications"
git push
git tag v13.4.33
git push origin v13.4.33
```

Docs 仓库归档 prompt 后：

```bash
git add .
git commit -m "Document V13.4.33 low-frequency candidate specifications"
git push
git tag v13.4.33-docs
git push origin v13.4.33-docs
```

---

# 26. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. source baseline report
5. candidate spec report 路径
6. summary 路径
7. baseline hurdles 摘要
8. candidate A 摘要
9. candidate B 摘要
10. candidate C 摘要
11. candidate D 摘要
12. candidate E 摘要
13. directional score framework 摘要
14. V13.4.34 plan
15. 是否写策略
16. 是否运行 backtest
17. 是否下载数据
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

# 27. Next Version Preview

V13.4.33 完成后，下一步建议：

```text
V13.4.34 - Low-Frequency Candidate Implementation and Research Backtest
```

建议 V13.4.34 只实现：

```text
Candidate A: 4H EMA Trend Long
Candidate B: 4H Bear Rejection Short
Candidate E: NoTrade Defensive Module
```

暂不实现：

```text
Candidate C Router
Candidate D Breakout Retest
```

避免一次写太多。

---

# 28. Final Instruction

请严格执行 V13.4.33。

本版本只做低频策略候选规格与 baseline hurdles。

不要写策略。

不要运行 backtest。

不要下载数据。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。
