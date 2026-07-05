# AlphaPilot V13.4.30 - Short Rejection Failure Review and Negative Research Rules

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.30。

本版本的目标是：**复盘 V13.4.29 Short Rejection 1H 做空研究策略的失败结果，将该策略归档为失败型 research-only 样本，并提炼“后续不要继续做什么”的负样本规则，防止 AlphaPilot 在相同方向上继续无效消耗。**

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
AlphaPilot V13.4.30 - Short Rejection Failure Review and Negative Research Rules
```

中文版本名：

```text
AlphaPilot V13.4.30 - 做空反弹失败策略复盘归档与负样本规则版
```

---

# 2. Background

当前已经完成：

```text
V13.4.27：Market Regime and Data Integrity Review
V13.4.28：Market Data Coverage Repair and Public Data Expansion Skeleton
V13.4.29：Short Rejection 1H Research Strategy
```

V13.4.29 已真实回测并完成：

```text
Strategy: AlphaPilotShortRejection1HV01
Type: research-only short strategy
Direction: short only
Timeframe: 1h
```

V13.4.29 expanded backtest 结果：

```text
tradeCount: 5052
totalReturnPct: -99.9966%
maxDrawdownPct: 99.9966%
profitFactor: 0.782
winRate: 29.711%
researchWorthContinuing: false
dryRunApproved: false
liveTradingApproved: false
```

V13.4.29 安全边界：

```text
未接 Trade API
未接 Withdraw API
未保存 API Key
未读取真实账户 / 持仓
未创建真实订单
未进入 Dry-run
未批准 live trading
未自动交易
```

当前结论：

```text
V13.4.29 是失败型 research-only short 策略实验。
不能继续推进到 Dry-run。
不能继续作为 short 主线。
不能直接小调参数硬救。
```

现在进入 V13.4.30：复盘归档和提炼负样本规则。

---

# 3. Core Goal

V13.4.30 的核心目标：

```text
1. 读取 V13.4.29 short rejection report
2. 复盘该 short strategy 为什么失败
3. 分析交易频率、亏损来源、pair/month 稳定性、exit reason、滑点后表现
4. 将 AlphaPilotShortRejection1HV01 状态归档为 failed_research_current_sample / rejected_for_dry_run
5. 提炼 short strategy negative research rules
6. 生成 short failure review report
7. 生成 short strategy status archive
8. 更新 README / docs
9. 不修改策略代码
10. 不运行 backtest
11. 不进入 Dry-run
```

一句话目标：

```text
把一次失败的做空实验变成可复用的负样本资产，避免以后重复犯同样错误。
```

---

# 4. Required Mindset

请遵守以下原则：

```text
1. 策略失败不是项目失败
2. 复盘不是调参
3. 归档不是删除
4. 归档是暂停主线推进并保留证据
5. 不因为 short 策略失败就否定所有做空方向
6. 不因为做空亏损就马上加更多过滤器硬救
7. 不修改策略逻辑
8. 不运行新回测
9. 不进入 Dry-run
10. 所有结论必须来自 V13.4.29 报告
```

---

# 5. Non Goals

V13.4.30 不做：

```text
不修改 AlphaPilotShortRejection1HV01.py
不修改任何策略参数
不新增做空策略
不运行新的 Freqtrade backtest
不重新跑 V13.4.29
不调 shortScore
不修改 stoploss / ROI / time stop
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
不接机器学习
不接多模型决策
```

允许做：

```text
读取本地 V13.4.29 report
生成失败复盘
生成负样本规则
生成策略状态归档
生成 docs
更新 README
```

---

# 6. Precondition

执行前必须确认：

```text
1. v13.4.29 tag 已存在
2. reports/v13_4_29_short_rejection_1h_report.json 存在
3. reports/v13_4_29_short_rejection_1h_summary.md 存在
4. 当前工作区干净
```

如果不满足：

```text
停止 V13.4.30
提示先完成 V13.4.29
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
reports/v13_4_29_short_rejection_1h_report.json
reports/v13_4_29_short_rejection_1h_summary.md
```

可参考：

```text
reports/v13_4_27_market_regime_data_integrity_report.json
reports/v13_4_28_market_data_expansion_report.json
```

如果字段缺失：

```text
写入 warnings
不要伪造
```

---

# 9. Required Failure Review Dimensions

V13.4.30 必须分析以下维度。

---

## 9.1 Overall Failure

已知：

```text
expanded backtest:
5052 trades
totalReturnPct: -99.9966%
maxDrawdownPct: 99.9966%
profitFactor: 0.782
winRate: 29.711%
```

必须回答：

```text
1. 是否为结构性失败？
2. 是否属于高频低质量做空？
3. 是否存在明显交易过度？
4. 是否应停止作为 short 主线继续推进？
```

推荐结论：

```text
failed_research_current_sample
rejected_for_dry_run
```

---

## 9.2 Trade Frequency Review

必须分析：

```text
5052 trades 是否过高？
1h 策略为何产生如此多交易？
shortScore >= 4 是否仍然太松？
是否说明“少量条件 + quick backtest”发现了条件过宽？
```

注意：

```text
本版本不修改 shortScore，只记录。
```

---

## 9.3 Payoff / Win Rate Review

已知：

```text
winRate: 29.711%
profitFactor: 0.782
```

必须分析：

```text
当前 +5% ROI / 2.5% stop 的理论 payoff 是否被实际交易质量破坏？
胜率是否不足以支持该 payoff？
亏损频率是否过高？
```

---

## 9.4 Short Squeeze / Wrong Timing Risk

做空策略必须检查：

```text
是否经常在反弹初期做空？
是否在强修复行情中被打止损？
是否出现 bull/recovery regime 乱做空？
```

如果 report 缺少 regime 分布：

```text
regimeAttributionAvailable=false
```

并建议未来 short report 增加 per-trade regime tag。

---

## 9.5 Pair / Month Concentration

如果 report 有 pairPerformance / monthlyPerformance，必须分析：

```text
哪些 pair 亏损最多？
是否单一 pair 主导亏损？
哪些月份亏损最多？
是否集中在反弹月或高波动月？
```

如果缺失：

```text
pairMonthlyBreakdownAvailable=false
```

并建议 future report enhancement。

---

## 9.6 Exit Reason Review

如果 report 有 exitReasonBreakdown，必须分析：

```text
stoploss 是否主要亏损来源？
time stop 是否有效？
profitable momentum exit 是否真的保护利润？
ROI 是否很少触发？
```

如果缺失：

```text
exitReasonBreakdownAvailable=false
```

---

## 9.7 Slippage / Cost Review

必须分析：

```text
slippageAdjusted metrics 是否进一步恶化？
交易次数 5052 是否导致成本吞噬？
做空策略是否必须降低交易频率？
```

如果 slippageAdjustedMetrics 缺失：

```text
slippageReviewAvailable=false
```

---

# 10. Short Strategy Status Archive

新增或更新：

```text
reports/v13_4_30_short_strategy_status_archive.json
```

记录：

```json
{
  "strategyId": "alpha_short_rejection_1h_v01",
  "strategyName": "AlphaPilot Short Rejection 1H V0.1",
  "status": "failed_research_current_sample",
  "dryRunApproved": false,
  "liveTradingApproved": false,
  "researchWorthContinuing": false,
  "reason": "Expanded short-only backtest suffered near-total loss with excessive trade count",
  "evidenceReports": [
    "reports/v13_4_29_short_rejection_1h_report.json"
  ],
  "archiveMode": "research_reference",
  "canBeUsedAsBenchmark": true,
  "canBeRevivedIf": [
    "new short thesis with stronger regime filter",
    "funding/OI data support",
    "much lower trade frequency",
    "per-trade regime attribution improves"
  ]
}
```

---

# 11. Negative Research Rules

V13.4.30 必须生成负样本规则文件：

```text
reports/v13_4_30_negative_research_rules.json
```

至少包括：

```text
1. 不要把“少量条件”误解成“无约束高频交易”
2. 做空策略不能只靠 EMA 反弹失败 + MACD/RSI 弱化
3. shortScore 过松会导致过度交易
4. 做空必须监控反弹 / squeeze risk
5. 做空必须引入交易频率上限或更强触发质量
6. 做空不能忽视 regime 背景，但 regime 不一定要硬开关
7. expanded backtest 大幅亏损时不得继续小调硬救
8. research-only 失败策略可归档为 benchmark/reference
9. 任何 short 策略进入下一阶段前必须有 per-trade regime attribution
10. 任何 short 策略必须报告 long/short 分离指标、成本、止损来源
```

---

# 12. Future Short Research Recommendations

V13.4.30 必须给出下一步做空研究建议。

推荐方向：

```text
1. 不继续修 V13.4.29 原策略
2. 等 funding / OI 数据接入后再研究 short
3. 下一次 short 模块必须降低交易频率
4. 下一次 short 模块必须有 stronger trigger，例如 failed breakout / lower high confirmation / relative weakness
5. 下一次 short 模块必须输出 per-trade regime attribution
6. 下一次 short 模块必须先做 BTC/ETH/SOL 或 Top10，不直接 supportedPairs 全量
```

---

# 13. Output Reports

新增：

```text
alphapilot/reports/generate_short_rejection_failure_review.py
alphapilot/reports/short_rejection_failure_schema.py
reports/v13_4_30_short_rejection_failure_review.json
reports/v13_4_30_short_rejection_failure_summary.md
reports/v13_4_30_short_strategy_status_archive.json
reports/v13_4_30_negative_research_rules.json
```

---

# 14. Report Schema

主报告结构建议：

```json
{
  "reportId": "v13_4_30_short_rejection_failure_review",
  "sourceShortReport": "reports/v13_4_29_short_rejection_1h_report.json",
  "strategyId": "alpha_short_rejection_1h_v01",
  "currentStatus": "failed_research_current_sample",
  "dryRunApproved": false,
  "liveTradingApproved": false,
  "overallFailure": {},
  "tradeFrequencyReview": {},
  "payoffReview": {},
  "shortSqueezeRiskReview": {},
  "pairMonthReview": {},
  "exitReasonReview": {},
  "costReview": {},
  "negativeResearchRules": [],
  "futureShortResearchRecommendations": [],
  "nextStepRecommendation": "",
  "warnings": [],
  "generatedAt": "UTC ISO"
}
```

---

# 15. Docs To Add

新增：

```text
docs/V13.4.30-short-rejection-failure-review.md
docs/short-strategy-negative-research-rules.md
docs/failed-strategy-archive-policy.md
docs/future-short-research-recommendations.md
```

README 增加：

```text
## V13.4.30 Short Rejection Failure Review
```

中文说明：

```text
V13.4.30 复盘 V13.4.29 做空研究策略失败结果，归档该策略为 failed_research_current_sample，并提炼后续做空研究的负样本规则。
本版本不调参、不回测、不进入 Dry-run、不接真实 API Key。
```

---

# 16. Commands To Run

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m compileall alphapilot
python -m alphapilot.reports.generate_short_rejection_failure_review
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

不运行 backtest。

---

# 17. Safety Requirements

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
不修改策略代码
```

允许：

```text
读取本地 V13.4.29 报告
生成失败复盘
生成负样本规则
更新 docs
```

---

# 18. Acceptance Criteria

完成后必须满足：

```text
1. 生成 short rejection failure review JSON
2. 生成 short rejection failure summary MD
3. 生成 short strategy status archive JSON
4. 生成 negative research rules JSON
5. 明确 currentStatus=failed_research_current_sample
6. 明确 dryRunApproved=false
7. 明确 liveTradingApproved=false
8. 明确 researchWorthContinuing=false 或等价结论
9. 分析 trade frequency
10. 分析 payoff / win rate
11. 分析 short squeeze / wrong timing 风险
12. 分析 pair/month 或写 unavailable warning
13. 分析 exit reason 或写 unavailable warning
14. 分析 cost / slippage 或写 unavailable warning
15. 输出 future short research recommendations
16. README / docs 更新
17. 不修改策略代码
18. 不运行 backtest
19. 不进入 Dry-run
20. 不接真实 API Key
21. 不接 Trade API
22. 不接 Withdraw API
23. 不读取真实账户
24. 不读取真实持仓
25. 不创建真实订单
26. 不自动交易
27. compileall 通过
28. validate_config 通过
29. safety scan 通过
30. git diff --check 通过
```

---

# 19. Commit And Tag

如果检查通过：

```bash
git add .
git commit -m "Add V13.4.30 Short Rejection failure review"
git push
git tag v13.4.30
git push origin v13.4.30
```

Docs 仓库归档 prompt 后：

```bash
git add .
git commit -m "Document V13.4.30 short rejection failure review"
git push
git tag v13.4.30-docs
git push origin v13.4.30-docs
```

---

# 20. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. 输入 short report 路径
5. failure review report 路径
6. failure summary 路径
7. strategy status archive 路径
8. negative research rules 路径
9. currentStatus
10. researchWorthContinuing 值
11. dryRunApproved 值
12. liveTradingApproved 值
13. trade frequency review 摘要
14. payoff review 摘要
15. short squeeze risk review 摘要
16. pair/month review 摘要
17. exit reason review 摘要
18. cost review 摘要
19. negative research rules 摘要
20. future short research recommendations
21. 是否修改策略代码
22. 是否运行 backtest
23. 是否进入 Dry-run
24. 是否保存真实 API Key
25. 是否接 Trade API
26. 是否接 Withdraw API
27. 是否读取真实账户
28. 是否读取真实持仓
29. 是否创建真实订单
30. 是否自动交易
31. compileall 结果
32. validate_config 结果
33. safety scan 结果
34. git diff --check 结果
35. commit hash
36. push 结果
37. tag 结果
38. 已知问题
39. 下一步建议
```

---

# 21. Next Version Preview

V13.4.30 完成后，建议不要继续直接写 short 策略。

下一步建议：

```text
V13.4.31 - Low-Frequency Mainstream Coin Research Plan
```

目标：

```text
1. 回到 BTC/ETH/SOL
2. 使用 4h / 1d
3. 降低交易频率
4. 只做 research specification
5. 接入 regime-aware evaluation
6. 不写策略代码
```

或者：

```text
V13.4.31 - Funding/OI Public Data Collector
```

如果用户想继续做空研究，则应先补 Funding / OI 数据。

推荐默认：

```text
V13.4.31 - Low-Frequency Mainstream Coin Research Plan
```

因为当前 1h 高交易频率策略多次失败。

---

# 22. Final Instruction

请严格执行 V13.4.30。

本版本只复盘并归档 V13.4.29 short rejection 策略失败样本。

不要调参。

不要修改策略。

不要运行 backtest。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。

失败策略归档不是删除，而是保留为研究资产和负样本规则来源。
