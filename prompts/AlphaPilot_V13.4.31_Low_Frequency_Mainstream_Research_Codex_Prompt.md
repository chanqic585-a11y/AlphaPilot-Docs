# AlphaPilot V13.4.31 - Low-Frequency Mainstream Coin Research Plan

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.31。

本版本的目标是：**在 V13.4.30 将高频做空研究策略归档后，回到更稳、更简单、更低频的主流币研究路线，制定 BTC / ETH / SOL 的 4h / 1d 低频研究计划，为下一步真正可验证的低频策略研究做准备。**

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
AlphaPilot V13.4.31 - Low-Frequency Mainstream Coin Research Plan
```

中文版本名：

```text
AlphaPilot V13.4.31 - 主流币低频研究计划版
```

---

# 2. Background

当前已经完成：

```text
V13.4.27：Market Regime and Data Integrity Review
V13.4.28：Market Data Coverage Repair and Public Data Expansion Skeleton
V13.4.29：Short Rejection 1H Research Strategy
V13.4.30：Short Rejection Failure Review and Negative Research Rules
```

V13.4.29 做空策略结果：

```text
Expanded backtest:
tradeCount: 5052
totalReturnPct: -99.9966%
maxDrawdownPct: 99.9966%
profitFactor: 0.782
winRate: 29.711%
researchWorthContinuing: false
dryRunApproved: false
liveTradingApproved: false
```

V13.4.30 复盘结论：

```text
AlphaPilotShortRejection1HV01 已归档为 failed_research_current_sample。
高频、宽松条件做空导致 5052 笔交易，成本和止损密度极高。
stop_loss 3532 笔，占 69.9129%，是主要亏损来源。
slippageAdjustedTotalReturnPct = -217.1225%。
不应继续小调硬救该 short 策略。
```

V13.4.30 下一步建议：

```text
V13.4.31 - Low-Frequency Mainstream Coin Research Plan
先回到 BTC / ETH / SOL，4h / 1d，低频研究规格。
```

现在进入 V13.4.31。

---

# 3. Core Goal

V13.4.31 的核心目标：

```text
1. 将研究范围收窄到 BTC / ETH / SOL
2. 将主周期切换到 4h / 1d
3. 明确低频研究假设
4. 设计低频主流币研究框架
5. 明确 long / short 都可以研究，但必须分模块
6. 设计 regime-aware evaluation
7. 设计低频 benchmark 对照
8. 设计下一步 V13.4.32 的数据准备 / 研究实现计划
9. 不写策略代码
10. 不运行回测
11. 不进入 Dry-run
```

一句话目标：

```text
停止在 Top30 高频信号里消耗时间，回到主流币、低频、清晰结构的研究方向。
```

---

# 4. Required Mindset

请遵守以下原则：

```text
1. 简化研究，不再堆太多 gate
2. 低频优先，减少手续费和滑点影响
3. 主流币优先，减少山寨币噪音
4. 先设计研究计划，不急着写策略
5. long / short 都允许研究，但必须分方向评估
6. 熊市不是禁止交易，而是改变方向和风险权重
7. 不把单个小样本结果当成实盘依据
8. 不进入 Dry-run
9. 不接真实交易能力
10. 所有输出必须 research-only
```

---

# 5. Non Goals

V13.4.31 不做：

```text
不实现 Freqtrade 策略
不运行 Freqtrade backtest
不下载新数据
不修改任何已有策略
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
读取本地历史报告
生成低频研究计划
生成研究假设
生成数据需求
生成 benchmark 计划
生成下一版实现计划
更新 README / docs
```

---

# 6. Precondition

执行前必须确认：

```text
1. v13.4.30 tag 已存在
2. reports/v13_4_30_short_rejection_failure_review.json 存在
3. reports/v13_4_30_negative_research_rules.json 存在
4. reports/v13_4_27_market_regime_data_integrity_report.json 存在
5. 当前工作区干净
```

如果不满足：

```text
停止 V13.4.31
提示先完成 V13.4.30 或缺失输入报告
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
reports/v13_4_30_short_rejection_failure_review.json
reports/v13_4_30_negative_research_rules.json
reports/v13_4_27_market_regime_data_integrity_report.json
```

可参考：

```text
reports/v13_4_23_benchmark_suite_report.json
reports/v13_4_24_benchmark_result_review.json
reports/v13_4_22_factor_evaluation_report.json
reports/v13_4_28_market_data_expansion_report.json
```

如果字段缺失：

```text
写入 warnings
不要伪造
```

---

# 9. Research Scope

V13.4.31 必须将下一阶段研究范围明确收窄为：

```text
Pairs:
BTC/USDT:USDT
ETH/USDT:USDT
SOL/USDT:USDT

Primary timeframes:
4h
1d

Secondary / informative:
1h for entry refinement, optional
```

第一版不研究：

```text
Top30
全市场
新币
低流动性山寨币
高频 15m / 1h 大量交易
```

除非仅作为对照，不作为主线。

---

# 10. Research Direction Principles

低频主流币研究必须遵守：

```text
1. 交易频率必须显著低于 1h 高频策略
2. 不要求每天都有信号
3. 允许长时间 no trade
4. long / short 都可以研究
5. short 不限定必须熊市，但必须有方向证据
6. bear / crash 中 long 默认降权
7. bull / recovery 中 short 默认降权
8. 所有研究都必须分 regime 报告
9. 所有研究必须和 NoTrade / BuyHoldBTC 比较
10. 所有研究必须做滑点后评估
```

---

# 11. Low-Frequency Research Hypotheses

V13.4.31 至少生成以下研究假设。

---

## 11.1 LF-HYP-001 BTC/ETH/SOL 4h Trend Following

假设：

```text
在主流币上，4h 趋势跟随可能比 1h 高频指标策略更稳定。
```

研究条件草案：

```text
4h close > EMA200
4h EMA20 > EMA50
4h trend slope positive
回调后重新站上 EMA20
```

方向：

```text
long only in bull / recovery / neutral-positive
```

验证重点：

```text
是否减少交易次数
是否滑点后仍比 NoTrade / BuyHoldBTC 更好
```

---

## 11.2 LF-HYP-002 BTC/ETH/SOL 4h Bear Rejection Short

假设：

```text
在主流币上，4h 反弹失败做空可能比 1h 宽松做空更稳定。
```

研究条件草案：

```text
4h close < EMA200
4h EMA20 < EMA50
价格反弹到 EMA20 / EMA50 附近
收盘重新走弱
```

方向：

```text
short only
不限定必须 bear，但 bear 背景加权更高
```

验证重点：

```text
是否显著降低 trade count
是否避免追空
是否在 bull/recovery 中少做空
```

---

## 11.3 LF-HYP-003 1d Regime + 4h Entry

假设：

```text
使用 1d 判定大环境，4h 寻找入场，比单纯 1h 更稳。
```

研究条件草案：

```text
1d regime = bull / bear / sideways
4h 只在匹配方向时寻找机会
```

验证重点：

```text
regime-aware no trade 是否降低回撤
```

---

## 11.4 LF-HYP-004 Breakout Retest on Mainstream Coins

假设：

```text
主流币 4h 突破回踩确认比均线追踪更有结构优势。
```

研究条件草案：

```text
突破最近 N 根高点 / 低点
回踩不破突破位
成交量不萎缩
```

方向：

```text
long breakout retest
short breakdown retest
```

验证重点：

```text
假突破率
胜率
reward/risk
```

---

## 11.5 LF-HYP-005 NoTrade as Active Decision

假设：

```text
在 bear / crash / high volatility regime 中，低频策略应主动 no trade 或显著降低交易。
```

验证重点：

```text
bear/crash 中 exposure 是否低
no trade 是否优于主动交易
```

---

# 12. Long / Short Handling

V13.4.31 必须明确：

```text
做空不只限熊市。
做多也不只限牛市。
但 market regime 必须影响方向评分和风险权重。
```

建议方向评分：

```text
longScore
shortScore
avoidScore
```

第一版研究计划中不实现评分代码，只定义：

```text
score inputs
score interpretation
evaluation requirements
```

方向决策：

```text
longScore 高且 avoidScore 低 → long candidate
shortScore 高且 avoidScore 低 → short candidate
avoidScore 高 → no trade
```

---

# 13. Minimal Conditions Philosophy

本版本必须明确后续策略避免过度复杂化：

```text
每个方向第一版最多 4-6 个核心条件
不堆 10+ 个 gate
用真实回测决定是否继续
失败就复盘 / 降级，不无限调参
```

---

# 14. Benchmark Requirements

低频研究必须比较：

```text
NoTrade
BuyHoldBTC
BuyHoldETH
BuyHoldSOL
Simple 4h EMA Trend
Simple 4h Bollinger Rebound
Simple 4h Breakout Retest
```

V13.4.31 只写计划，不实现 benchmark。

---

# 15. Evaluation Metrics

低频研究报告必须设计以下指标：

```text
tradeCount
tradesPerMonth
totalReturnPct
slippageAdjustedReturnPct
profitFactor
slippageAdjustedProfitFactor
maxDrawdownPct
winRate
maxConsecutiveLosses
averageHoldingHours
exposureTimePct
regimeBreakdown
longShortBreakdown
noTradeRatio
benchmarkComparison
```

必须特别关注：

```text
交易次数是否降低
bear/crash 中是否减少错误方向交易
是否跑赢 NoTrade / BuyHoldBTC
滑点后是否仍有优势
```

---

# 16. Data Requirements

低频研究需要：

```text
BTC/ETH/SOL 4h OHLCV
BTC/ETH/SOL 1d OHLCV
BTC/ETH/SOL 1h optional
market regime labels
NoTrade / BuyHold baselines
```

可选未来数据：

```text
fundingRate
openInterest
spread proxy
orderbook depth
```

V13.4.31 只记录需求，不下载数据。

---

# 17. Output Reports

新增：

```text
alphapilot/research_factory/low_frequency_research_schema.py
alphapilot/research_factory/low_frequency_research_plan.py
alphapilot/reports/generate_low_frequency_research_plan.py
alphapilot/reports/low_frequency_research_plan_schema.py
reports/v13_4_31_low_frequency_research_plan.json
reports/v13_4_31_low_frequency_research_summary.md
docs/V13.4.31-low-frequency-mainstream-research-plan.md
docs/low-frequency-research-hypotheses.md
docs/mainstream-coin-research-scope.md
docs/regime-aware-long-short-research.md
```

---

# 18. Report Schema

主报告结构建议：

```json
{
  "reportId": "v13_4_31_low_frequency_research_plan",
  "currentStatus": "research_plan_only",
  "scope": {
    "pairs": ["BTC/USDT:USDT", "ETH/USDT:USDT", "SOL/USDT:USDT"],
    "primaryTimeframes": ["4h", "1d"],
    "optionalTimeframes": ["1h"]
  },
  "hypotheses": [],
  "longShortFramework": {},
  "benchmarkRequirements": [],
  "evaluationMetrics": [],
  "dataRequirements": [],
  "nextStepRecommendation": "V13.4.32 - Low-Frequency Data Preparation and Baseline Builder",
  "dryRunApproved": false,
  "liveTradingApproved": false,
  "generatedAt": "UTC ISO"
}
```

---

# 19. README / Docs Update

README 增加：

```text
## V13.4.31 Low-Frequency Mainstream Coin Research Plan
```

中文说明：

```text
V13.4.31 将研究范围收窄到 BTC/ETH/SOL 的 4h/1d 低频方向，设计 long/short 均可研究的 regime-aware 低频研究计划。
本版本不写策略、不回测、不进入 Dry-run、不接 API Key。
```

新增 docs：

```text
docs/V13.4.31-low-frequency-mainstream-research-plan.md
docs/low-frequency-research-hypotheses.md
docs/mainstream-coin-research-scope.md
docs/regime-aware-long-short-research.md
```

---

# 20. Commands To Run

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m compileall alphapilot
python -m alphapilot.reports.generate_low_frequency_research_plan
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

不运行 backtest。

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
不运行 backtest
不下载外部数据
不修改 AlphaPilot Mobile App
不修改策略代码
```

允许：

```text
读取本地研究报告
生成低频研究计划
生成 docs
更新 README
```

---

# 22. Acceptance Criteria

完成后必须满足：

```text
1. 生成 low frequency research plan JSON
2. 生成 low frequency research summary MD
3. 明确 pairs = BTC/ETH/SOL
4. 明确 timeframes = 4h / 1d
5. 至少生成 5 个低频研究假设
6. 明确 long/short 都可研究
7. 明确 market regime 不是硬性唯一开关，而是方向评分背景
8. 明确每个方向第一版最多 4-6 个核心条件
9. 明确 benchmark requirements
10. 明确 evaluation metrics
11. 明确 data requirements
12. dryRunApproved=false
13. liveTradingApproved=false
14. README / docs 更新
15. 不写策略
16. 不运行 backtest
17. 不进入 Dry-run
18. 不接真实 API Key
19. 不接 Trade API
20. 不接 Withdraw API
21. 不读取真实账户
22. 不读取真实持仓
23. 不创建真实订单
24. 不自动交易
25. compileall 通过
26. validate_config 通过
27. safety scan 通过
28. git diff --check 通过
```

---

# 23. Commit And Tag

如果检查通过：

```bash
git add .
git commit -m "Add V13.4.31 low-frequency mainstream coin research plan"
git push
git tag v13.4.31
git push origin v13.4.31
```

Docs 仓库归档 prompt 后：

```bash
git add .
git commit -m "Document V13.4.31 low-frequency research plan"
git push
git tag v13.4.31-docs
git push origin v13.4.31-docs
```

---

# 24. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. 输入报告路径
5. low frequency research plan 路径
6. summary 路径
7. research scope
8. hypotheses 摘要
9. long/short framework 摘要
10. benchmark requirements
11. evaluation metrics
12. data requirements
13. nextStepRecommendation
14. 是否写策略
15. 是否运行 backtest
16. 是否下载数据
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

V13.4.31 完成后，下一步建议：

```text
V13.4.32 - Low-Frequency Data Preparation and Baseline Builder
```

目标：

```text
1. 准备 BTC/ETH/SOL 4h/1d 数据
2. 生成 NoTrade / BuyHoldBTC / BuyHoldETH / BuyHoldSOL baseline
3. 生成 simple 4h baseline specs
4. 不写正式策略
5. 可运行 research-only baseline report
```

---

# 26. Final Instruction

请严格执行 V13.4.31。

本版本只做 BTC/ETH/SOL 4h/1d 低频主流币研究计划。

不要写策略。

不要运行 backtest。

不要下载数据。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。
