# AlphaPilot V13.4.27 - Market Regime and Data Integrity Review

## 给 Codex 的执行说明

请严格按照本文档执行 V13.4.27。

本版本的目标是：**在继续数据扩展和新策略研究前，先验证本地行情数据是否可靠，并判断最近 6 个月 BTC 大幅下跌是否导致当前 long-only / OHLCV 技术策略普遍失效。**

本版本是数据完整性与市场状态诊断版。
本版本不是策略实现版。
本版本不是调参版。
本版本不是回测执行版。
本版本不是 Dry-run 版。
本版本不是实盘版。
本版本不接真实 API Key。
本版本不自动交易。
本版本不创建真实订单。

完成后请输出完整自检报告。

---

# 0. Important Route Change

之前已经准备过：

```text
V13.4.27 - Research Direction Reset and Data Expansion Specification
```

但现在用户提出了一个关键问题：

```text
最近 6 个月 BTC 从约 12 万美元附近腰斩到约 6 万美元附近，
这是否导致样本期极端，从而影响策略和因子研究结论？
```

因此本版本插入为新的 V13.4.27：

```text
V13.4.27 - Market Regime and Data Integrity Review
```

原本的数据扩展版本顺延为：

```text
V13.4.28 - Research Direction Reset and Data Expansion Specification
```

不要执行旧的 V13.4.27 数据扩展文档，除非用户重新确认。

---

# 1. Version Name

```text
AlphaPilot V13.4.27 - Market Regime and Data Integrity Review
```

中文版本名：

```text
AlphaPilot V13.4.27 - 市场状态与数据完整性诊断版
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
V13.4.25：Strategy Research Factory - Factor Hypothesis Mining
V13.4.26：Factor Hypothesis Validation Dataset
```

V13.4.26 结果：

```text
sampleCount = 124111
validatedHypothesisCount = 6
topSupportedHypotheses = []
unsupportedHypotheses = HYP-001, HYP-002, HYP-004, HYP-006, HYP-007, HYP-008
insufficientSampleHypotheses = []
hypothesesWithPositiveExcessVsBTC = HYP-002
但 HYP-002 PF 仍低于 1，且月度稳定失败，所以不升级
```

当前问题：

```text
这些失败是否因为最近 6 个月是极端熊市 / 去杠杆环境？
本地数据是否存在错误？
是否存在 spot/swap 混用、K线缺失、时间戳错位、极端异常值？
```

因此需要先做：

```text
Market Regime and Data Integrity Review
```

---

# 3. Core Goal

V13.4.27 的核心目标：

```text
1. 验证本地 BTC/ETH/SOL/Top pairs 行情数据完整性
2. 检查 OKX USDT swap pair 格式是否一致
3. 检查 spot/swap 是否混用
4. 检查 K线数量、缺失率、重复 timestamp、异常 OHLCV
5. 检查 BTC 在关键时间点的价格是否符合市场大方向
6. 标记 2026-01 到 2026-07 的 market regime
7. 分析当前研究失败是否集中在 extreme bear / high volatility regime
8. 输出 data integrity report
9. 输出 market regime review report
10. 给出后续研究是否应按 regime 分层的建议
```

一句话目标：

```text
先确认数据没错，再确认失败是否主要来自极端市场状态。
```

---

# 4. Required Mindset

请遵守以下原则：

```text
1. 数据完整性检查优先于继续研究策略
2. 极端市场样本不是数据错误，但必须单独标记
3. 不能因为策略失败就默认数据错
4. 不能因为市场极端就忽略策略失败
5. 不能用未来数据给过去打 regime 标签
6. 不修改策略
7. 不运行新策略回测
8. 不进入 Dry-run
9. 不接真实交易能力
10. 所有结论必须来自本地数据和报告
```

---

# 5. Non Goals

V13.4.27 不做：

```text
不实现交易策略
不修改任何策略参数
不运行 Freqtrade backtest
不下载外部新数据
不接真实 API Key
不接 Trade API
不接 Withdraw API
不读取真实账户
不读取真实持仓
不创建真实订单
不自动交易
不进入 Dry-run
不修改 AlphaPilot Mobile App
不接新闻数据
不接机器学习
不接多模型决策
```

允许做：

```text
读取本地公开 OHLCV
读取本地研究报告
生成数据完整性报告
生成市场状态标签
生成 regime-aware research recommendation
更新 README / docs
```

---

# 6. Precondition

执行前必须确认：

```text
1. v13.4.26 tag 已存在
2. reports/v13_4_26_hypothesis_validation_report.json 存在
3. reports/v13_4_26_hypothesis_validation_summary.md 存在
4. 本地存在 OHLCV 数据，或能生成 insufficient data report
5. 当前工作区干净
```

如果不满足：

```text
停止 V13.4.27
提示先完成 V13.4.26 或补齐本地 OHLCV 数据
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

# 8. Input Data

优先读取：

```text
user_data/data/
```

重点数据：

```text
BTC/USDT:USDT
ETH/USDT:USDT
SOL/USDT:USDT
Top pairs used in V13.4.21 / V13.4.26
```

Timeframes：

```text
1h
4h
可选 15m
```

重点 timerange：

```text
20260101-
```

如果更长数据存在，也可读取：

```text
20251001-
20250701-
```

但本版本最低要求是诊断最近研究所用数据区间。

---

# 9. Data Integrity Checks

新增数据完整性检查模块。

必须检查以下内容。

---

## 9.1 Pair Format Consistency

检查 pair 是否统一为：

```text
XXX/USDT:USDT
```

目标市场：

```text
OKX USDT swap
```

检查异常：

```text
XXX/USDT spot
XXX/USD
XXX-USDT-SWAP
重复映射
pair 名称不一致
```

---

## 9.2 Candle Count Check

对每个 pair/timeframe 计算：

```text
expectedCandleCount
actualCandleCount
missingCandleCount
missingRate
firstTimestamp
lastTimestamp
```

例如 1h：

```text
天数 × 24 ≈ expected candles
```

要求：

```text
missingRate > 5% 标记 warning
missingRate > 20% 标记 invalid
```

---

## 9.3 Timestamp Check

检查：

```text
timestamp 是否递增
是否重复 timestamp
是否存在大 gap
是否存在 timezone 不一致
是否存在非 UTC timestamp
```

---

## 9.4 OHLC Validity Check

检查：

```text
open > 0
high > 0
low > 0
close > 0
high >= low
high >= open
high >= close
low <= open
low <= close
volume >= 0
```

异常记录：

```text
invalidOhlcRows
zeroPriceRows
negativeVolumeRows
```

---

## 9.5 Extreme Return Check

检查单根异常涨跌：

```text
absReturn1h > 30%
absReturn4h > 50%
```

这不一定是错误，但必须记录：

```text
extremeReturnRows
extremeReturnWarnings
```

---

## 9.6 Volume Check

检查：

```text
volume = 0 连续区间
volume 异常尖峰
volume 缺失
quoteVolume 是否估算
```

---

# 10. BTC Key Price Sanity Check

本版本不能联网下载外部行情，但可以检查本地 BTC 数据是否符合大方向。

检查本地 BTC：

```text
2025-10 附近是否存在高位区域，如果数据存在
2026-01 初价格区间
2026-04 价格区间
2026-06 / 2026-07 价格区间
```

生成：

```text
btcLocalPriceCheck
```

字段：

```text
date
nearestTimestamp
close
returnFromPriorKeyPoint
notes
```

如果本地没有 2025-10 数据：

```text
write warning: insufficient_pre_2026_history_for_ath_check
```

重点不是确认精确新闻价格，而是发现明显异常：

```text
BTC close = 6000
BTC close = 600000
价格跳变数量级错误
spot/swap 大幅异常
```

---

# 11. Market Regime Labeling

新增 market regime 标注模块。

Regime 类型：

```text
bull
bear
sideways
high_volatility
crash
recovery
unknown
```

可以多标签：

```text
bear + high_volatility
sideways + high_volatility
```

---

## 11.1 BTC Regime Rules

基于 BTC 1h / 4h 数据。

建议规则：

```text
BTC below 4h EMA200 and 4h EMA20 < EMA50 -> bear
BTC above 4h EMA200 and EMA20 > EMA50 -> bull
BTC 4h close range within band and low trend slope -> sideways
BTC 1h or 4h realized volatility above percentile threshold -> high_volatility
BTC rolling 3d return <= -10% -> crash
BTC rolling 7d return >= +10% after drawdown -> recovery
```

阈值可记录为 design values，不要用于交易。

---

## 11.2 Dynamic Universe Breadth Proxy

如果动态 universe snapshots / factor panel 可用，计算：

```text
percentage of pairs positive over 24h
average return across selected pairs
median return across selected pairs
percentage above EMA50
percentage above EMA200
```

用于判断：

```text
altcoin breadth weak / strong
```

---

# 12. Regime-Aware Failure Review

必须结合 V13.4.26 / V13.4.23 / V13.4.9 等报告，输出：

```text
策略失败是否集中在 bear regime？
策略失败是否集中在 high_volatility regime？
long-only 策略是否在 crash/bear 中应该 avoid？
NoTrade 是否在 bear regime 中成为合理选择？
```

如果无法精确映射历史 trades：

```text
write limitation
```

但必须给出后续需求：

```text
future reports must include regime tags per trade / sample
```

---

# 13. Output Files

新增：

```text
alphapilot/data_quality/__init__.py
alphapilot/data_quality/ohlcv_integrity_checker.py
alphapilot/data_quality/data_quality_schema.py
alphapilot/market_regime/__init__.py
alphapilot/market_regime/market_regime_labeler.py
alphapilot/market_regime/market_regime_schema.py
alphapilot/reports/generate_market_regime_data_integrity_review.py
alphapilot/reports/market_regime_data_integrity_schema.py
reports/v13_4_27_market_regime_data_integrity_report.json
reports/v13_4_27_market_regime_data_integrity_summary.md
reports/v13_4_27_btc_regime_labels.json
reports/v13_4_27_data_quality_by_pair.json
docs/V13.4.27-market-regime-data-integrity-review.md
docs/ohlcv-data-integrity-checks.md
docs/market-regime-labeling-methodology.md
docs/regime-aware-research-recommendation.md
```

---

# 14. Report Schema

主报告：

```json
{
  "reportId": "v13_4_27_market_regime_data_integrity_review",
  "sourceReports": [],
  "timerange": "20260101-",
  "pairsChecked": [],
  "timeframesChecked": [],
  "dataIntegrity": {},
  "btcKeyPriceSanity": {},
  "marketRegime": {},
  "dynamicUniverseBreadth": {},
  "regimeAwareFailureReview": {},
  "recommendations": [],
  "dryRunApproved": false,
  "liveTradingApproved": false,
  "generatedAt": "UTC ISO"
}
```

---

# 15. Required Conclusions

报告必须明确回答：

```text
1. 本地 OHLCV 是否存在明显数据错误？
2. 是否发现 spot/swap 混用风险？
3. K线完整性是否足够支持当前研究？
4. BTC 最近 6个月是否处于 bear / high_volatility / crash-heavy 样本？
5. 当前 long-only 策略失败是否可能受极端市场状态影响？
6. 是否需要后续所有报告加入 regime-aware 分析？
7. 是否建议继续执行数据扩展路线？
```

---

# 16. Recommendations

推荐输出包括：

```text
1. 所有后续策略报告必须按 regime 分层
2. 所有 long-only 策略在 bear/crash regime 中必须允许 no-trade
3. 后续 data expansion 仍然必要
4. 低频主流币研究应优先使用 regime-aware 样本
5. OHLCV-only 失败不能简单归因数据错误
6. 若数据完整性不合格，先修数据再研究策略
```

---

# 17. Commands To Run

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m compileall alphapilot
python -m alphapilot.reports.generate_market_regime_data_integrity_review
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

不运行 backtest。

---

# 18. README / Docs Update

README 增加：

```text
## V13.4.27 Market Regime and Data Integrity Review
```

中文说明：

```text
V13.4.27 用于验证本地 OHLCV 数据完整性，并标记近期 BTC 极端下跌样本的市场状态。
本版本不写策略、不回测、不进入 Dry-run、不接 API Key。
```

新增 docs：

```text
docs/V13.4.27-market-regime-data-integrity-review.md
docs/ohlcv-data-integrity-checks.md
docs/market-regime-labeling-methodology.md
docs/regime-aware-research-recommendation.md
```

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
不下载外部新数据
不修改 AlphaPilot Mobile App
不修改策略代码
```

允许：

```text
读取本地公开 OHLCV
读取本地研究报告
生成数据完整性报告
生成市场状态标签
更新 docs
```

---

# 20. Acceptance Criteria

完成后必须满足：

```text
1. OHLCV integrity checker 已创建
2. data quality schema 已创建
3. market regime labeler 已创建
4. market regime schema 已创建
5. 生成 market regime + data integrity report
6. 生成 summary
7. 生成 BTC regime labels
8. 生成 data quality by pair
9. 检查 pair format consistency
10. 检查 candle count / missing rate
11. 检查 timestamp consistency
12. 检查 OHLC validity
13. 检查 extreme returns
14. 检查 volume anomalies
15. 输出 BTC key price sanity
16. 输出 regime-aware failure review
17. 输出 recommendations
18. dryRunApproved=false
19. liveTradingApproved=false
20. 不写策略
21. 不运行 backtest
22. 不下载外部数据
23. 不进入 Dry-run
24. 不接 API Key
25. 不自动交易
26. compileall 通过
27. validate_config 通过
28. safety scan 通过
29. git diff --check 通过
```

---

# 21. Commit And Tag

如果检查通过：

```bash
git add .
git commit -m "Add V13.4.27 market regime and data integrity review"
git push
git tag v13.4.27
git push origin v13.4.27
```

Docs 仓库归档 prompt 后：

```bash
git add .
git commit -m "Document V13.4.27 market regime data integrity review"
git push
git tag v13.4.27-docs
git push origin v13.4.27-docs
```

---

# 22. Self Review Output

完成后输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表，如果没有删除写无
4. timerange
5. pairsChecked
6. timeframesChecked
7. dataIntegrity summary
8. pair format consistency 结果
9. missing rate summary
10. invalid OHLC summary
11. extreme return summary
12. BTC key price sanity summary
13. market regime distribution
14. dynamic universe breadth summary
15. regime-aware failure review
16. recommendations
17. 是否写策略
18. 是否运行 backtest
19. 是否下载外部数据
20. 是否进入 Dry-run
21. 是否保存真实 API Key
22. 是否接 Trade API
23. 是否接 Withdraw API
24. 是否读取真实账户
25. 是否读取真实持仓
26. 是否创建真实订单
27. 是否自动交易
28. compileall 结果
29. validate_config 结果
30. safety scan 结果
31. git diff --check 结果
32. commit hash
33. push 结果
34. tag 结果
35. 已知问题
36. 下一步建议
```

---

# 23. Next Version Preview

V13.4.27 完成后，再进入：

```text
V13.4.28 - Research Direction Reset and Data Expansion Specification
```

但需要根据 V13.4.27 结果决定重点：

```text
如果数据完整性有问题：
  V13.4.28 = Data Repair / Data Coverage Fix

如果数据完整性基本正常，但样本为 bear/high volatility：
  V13.4.28 = Regime-Aware Data Expansion

如果研究失败主要来自 OHLCV-only 信息不足：
  V13.4.28 = Funding Rate and Open Interest Data Skeleton
```

---

# 24. Final Instruction

请严格执行 V13.4.27。

本版本只做市场状态与数据完整性诊断。

不要写策略。

不要调参。

不要运行回测。

不要下载外部数据。

不要进入 Dry-run。

不要实盘。

不要接 API Key。

不要自动交易。
