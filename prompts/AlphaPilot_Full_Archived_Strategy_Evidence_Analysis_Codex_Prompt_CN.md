# AlphaPilot 全量归档失败策略证据库与逐笔归因分析 —— Codex 完整提示词（中文代号版）

> **用途**：让 Codex 对 AlphaPilot 历史上所有已经归档、暂停主线、拒绝进入交易所模拟盘、研究失败或被风险设计直接否决的策略，建立一套完整、统一、可追溯、可比较的失败策略证据库。  
> **核心目标**：不是继续写新策略，也不是重新批量回测，而是弄清每个策略究竟为什么失败、失败发生在哪一层、哪些组件仍可复用、哪些路线禁止重复、哪些策略在新数据或新市场状态下可以重新研究。  
> **语言要求**：所有用户可见状态、失败类型、建议动作、报告标题和结论统一使用中文。内部 Python 枚举或 JSON key 可以保留英文以兼容代码，但必须同时提供中文显示名称。

---

# 1. 任务名称

```text
AlphaPilot 全量归档失败策略证据库与逐笔归因分析
```

中文简称：

```text
全量失败策略归因总账
```

本任务属于：

```text
只读研究分析
```

不属于：

```text
策略开发
参数优化
回测重跑
交易所模拟盘
实盘交易
```

---

# 2. 当前已知情况

当前第一版归档分析只覆盖：

```text
13 条失败、拒绝或负向研究记录
3 个策略家族
2 个周期
```

其中：

```text
11 条有本地原始回测证据
2 条只有结构化报告
12 条被归因为信号优势失败
1 条因风险设计被直接拒绝
```

因此当前报告只能视为：

```text
第一批归档策略归因报告
```

不能视为：

```text
AlphaPilot 所有历史策略的最终总账
```

本任务必须先做**覆盖率审计**，确认是否遗漏了历史策略家族、策略版本、回测 artifact、控制台降级策略或只存在于 Git 历史中的策略。

---

# 3. 最终目标

本任务完成后，必须得到以下成果：

```text
1. 全部归档失败策略清单
2. 策略身份与版本映射
3. 原始回测 artifact 索引
4. 证据可信度分级
5. 统一指标矩阵
6. 逐笔交易指标矩阵
7. 信号级 R / MFE / MAE 归因
8. 成本与执行现实归因
9. 仓位、杠杆与账户路径归因
10. 做多 / 做空拆分
11. 币种拆分
12. 月份 / 季度 / 年份拆分
13. 市场状态拆分
14. 退出原因拆分
15. 0 交易 / 完全阻塞策略专项分析
16. 风险设计直接拒绝策略专项分析
17. 单策略主要与次要失败类型
18. 跨策略共同失败模式
19. 负样本规则库
20. 可复用组件库
21. 策略复活条件
22. 继续归档清单
23. 禁止路线清单
24. 证据缺口与补证据计划
25. 中文总报告与 CSV / JSON 数据文件
```

---

# 4. 核心问题

最终报告必须明确回答：

```text
1. 已归档策略中，有多少是真正信号没有优势？
2. 有多少是原始信号尚可，但手续费和滑点吞噬？
3. 有多少是信号尚可，但仓位、杠杆、并发或复利放大导致账户路径失败？
4. 有多少是交易频率过高？
5. 有多少是市场状态不匹配？
6. 有多少是币种池过宽或单一币种拖累？
7. 有多少是做多方向失败？
8. 有多少是做空方向失败？
9. 有多少是退出逻辑失败？
10. 有多少是小样本有效但扩大后失效？
11. 有多少是数据覆盖、时间对齐、实现或运行时问题？
12. 有多少策略实际上是 0 交易 / 完全被门槛拦截，而不是收益失败？
13. 哪些组件值得复用？
14. 哪些路线禁止继续重复？
15. 哪些策略在什么条件下可以重新研究？
16. 下一阶段应该改信号、风险模型、周期、币种池、市场状态，还是数据源？
```

---

# 5. 仓库与目录

主要分析仓库：

```text
D:\Codex-Workspace\AlphaPilot-Quant-Engine
```

文档归档仓库：

```text
D:\Codex-Workspace\AlphaPilot-Docs
```

可只读参考控制台：

```text
D:\Codex-Workspace\AlphaPilot-Control-Console
```

不要修改手机 App 主逻辑：

```text
D:\Codex-Workspace\trade-discipline-journal
```

重点目录：

```text
D:\Codex-Workspace\AlphaPilot-Quant-Engine\reports
D:\Codex-Workspace\AlphaPilot-Quant-Engine\docs
D:\Codex-Workspace\AlphaPilot-Quant-Engine\user_data\strategies
D:\Codex-Workspace\AlphaPilot-Quant-Engine\user_data\backtest_results
D:\Codex-Workspace\AlphaPilot-Quant-Engine\user_data\config
D:\Codex-Workspace\AlphaPilot-Control-Console\data
D:\Codex-Workspace\AlphaPilot-Docs\prompts
```

---

# 6. 执行前预检

执行前必须：

```text
1. 读取 Quant README
2. 读取 Docs README
3. 读取仓库 AGENTS / 项目规则
4. 读取规矩文档
5. 读取踩坑日志
6. 检查 Quant / Docs / Console / App git status
7. 列出 Quant 当前未提交文件
8. 列出所有 Git tag
9. 列出近期 strategy / report 相关 commit
10. 确认当前工作区不会被覆盖
```

建议命令：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status --short
git tag --list
git log --all --decorate --oneline -- user_data/strategies reports docs
```

如果工作区不干净：

```text
先输出未提交文件清单
区分正式报告、logs、临时 artifact
不得删除或覆盖用户本地结果
不得自动 reset
```

不要使用 Bash heredoc：

```text
python - <<'PY'
```

PowerShell 下应使用项目脚本、Python module 或 here-string 管道。

---

# 7. 安全边界

本任务必须保持：

```text
不保存真实 API Key
不接 Trade API
不接 Withdraw API
不读取真实账户
不读取真实持仓
不创建真实订单
不创建交易所测试网订单
不执行 exchange Dry-run
不执行实盘
不自动交易
不修改策略参数
不修改策略代码
不修改回测配置
不运行新策略回测
不修改 AlphaPilot Mobile App
```

允许：

```text
读取现有策略代码
读取已有回测 ZIP / JSON
读取已有结构化报告
读取本地模拟盘历史状态
读取 Git 历史
生成只读分析报告
```

---

# 8. 默认不重跑回测

本任务默认：

```text
不运行任何新的 Freqtrade backtest
```

如果发现关键策略只有文档、没有真实 artifact：

```text
1. 标记证据缺口
2. 生成“定向补证据计划”
3. 写明建议补跑的策略、周期、交易对、时间范围
4. 本任务内不要执行补跑
```

只有用户单独批准后，才允许在后续版本执行定向补跑。

---

# 9. 全量策略自动发现

必须扫描并关联以下来源：

```text
reports 中的 strategy status archive
reports 中的 failure review
reports 中的 smoke / expanded / comparative / benchmark / simulation report
docs 中的策略说明和失败复盘
user_data/strategies 中的策略类
user_data/backtest_results 中的 ZIP / JSON / last-result 指针
Git tag / commit 历史
Control Console 中的暂停 / 降级 / 归档策略状态
```

已知可能存在但不能视为完整清单的策略家族：

```text
放量反弹系列
趋势回调系列
动态市场状态系列
概率门 / 零交易系列
基准策略组
1小时反弹失败做空系列
4小时低频方向系列
多策略批量回测系列
本地模拟盘中已降级或归档的策略
马丁 / 无限加仓等风险设计拒绝项
```

必须自动发现，不要只硬编码以上名称。

---

# 10. 覆盖率审计

先生成：

```text
策略覆盖率审计
```

必须对比：

```text
策略文件中发现的策略类
reports 中发现的 strategyId
status archive 中发现的 strategyId
Git 历史中发现的策略名
原始 backtest artifact 中发现的 strategy
控制台中发现的归档 / 降级策略
```

输出：

```text
已纳入策略
遗漏策略
重复策略
别名策略
孤立策略文件
孤立报告
孤立 artifact
缺失策略 ID
缺失版本
缺失 commit / tag
```

必须计算：

```text
发现策略总数
已分析策略数
覆盖率
有原始 artifact 的策略数
只有结构化报告的策略数
只有文档 / 代码证据的策略数
证据完全缺失策略数
```

---

# 11. 策略身份标准化

为每个策略建立唯一规范身份：

```text
策略 ID
策略中文名称
策略原类名
策略家族
版本
方向
周期
市场类型
策略文件路径
Commit
Tag
首次出现版本
最后一次研究版本
当前中文状态
```

策略别名必须合并，例如：

```text
类名
strategyId
报告内名称
文档名称
控制台显示名
```

必须建立：

```text
别名 -> 规范 strategyId
```

不要让同一个策略因命名不同被重复计算。

---

# 12. 中文状态规范

用户可见报告统一使用：

```text
当前样本失败
拒绝进入交易所模拟盘
仅保留为研究参考
暂停主线推进
等待更多数据
可在特定市场状态重新研究
可在新数据条件下重新研究
可在新周期下重新研究
作为基准策略保留
仅复用组件
禁止路线
证据不足，暂不能评价
实现问题，暂不能评价
```

内部枚举可以使用英文，但必须输出 `中文状态名称`。

---

# 13. 证据可信度分级

每个策略必须有证据等级。

## 一级证据：最高可信

```text
本地存在真实 Freqtrade ZIP / JSON
isMock=false
逐笔交易可提取
timerange / pairs / timeframe 明确
配置可追溯
```

## 二级证据

```text
由真实回测转换生成的 AlphaPilot JSON
来源路径明确
但本地原始 artifact 不存在或不可读
```

## 三级证据

```text
Markdown 总结
README
人工摘要
生命周期汇总
```

## 四级证据

```text
策略代码
Prompt
注释
mock / sample report
```

规则：

```text
一级证据优先于二级
二级优先于三级
三级优先于四级
不同来源冲突时必须记录
不得静默采用对自己有利的数据
```

证据等级 3 / 4：

```text
不能支持策略晋级
不能支持实盘结论
```

---

# 14. Artifact 索引

为每个策略建立证据索引：

```text
策略文件
原始回测 ZIP
原始回测 JSON
.last_result 指针
转换后的 AlphaPilot JSON
Markdown 总结
失败复盘报告
状态归档
Commit
Tag
控制台模拟记录
```

输出：

```text
artifactExists
artifactReadable
artifactType
artifactPath
artifactHash
sourceReportPath
evidenceLevel
```

如果 ZIP 中包含多个策略：

```text
按策略拆分索引
```

---

# 15. 策略身份与回测范围强制字段

每条策略至少包含：

```text
策略 ID
策略名称
策略版本
策略家族
策略文件
Commit
Tag
当前状态
交易方向
周期
市场类型
交易所
请求交易对
实际支持交易对
排除交易对
回测开始时间
回测结束时间
实际天数
isMock
Freqtrade 版本
配置路径
配置哈希
原始 artifact 路径
证据等级
证据完整度
```

缺失字段：

```text
null
不可用
证据不足
```

禁止用 0 代替缺失。

---

# 16. 策略参数快照

每条策略尽量提取：

```text
stoploss
minimal_roi
trailing stop
timeframe
can_short
leverage
stake_amount
stake_currency
max_open_trades
position sizing
是否固定仓位
是否复利
是否全仓
order types
手续费
滑点假设
protections
cooldown
entry tags
exit tags
```

参数来源优先级：

```text
真实回测 config
策略文件
结构化报告
文档
```

---

# 17. 统一基础指标矩阵

每条有交易结果的策略至少提取：

```text
交易数
做多交易数
做空交易数
每月交易数
每天交易数
原始总收益
滑点后总收益
年化收益，如可用
胜率
Profit Factor
滑点后 Profit Factor
平均盈利
平均亏损
平均盈亏比
期望值
最大回撤
回撤持续时间
最大连续亏损
平均持仓时间
中位持仓时间
市场暴露时间
换手率
手续费
滑点成本
资金费率成本，如有
最大同时持仓
最大保证金占用
```

如源数据没有：

```text
保持 null
写入证据缺口
```

---

# 18. 不要只看账户总收益

必须将失败分为三层：

```text
第一层：信号优势层
第二层：执行成本层
第三层：账户与风险路径层
```

不能因为总收益接近 -100% 就直接断言：

```text
信号一定完全无效
```

也不能因为平均信号略好就忽略账户路径失败。

---

# 19. 信号级指标

如果逐笔交易可用，必须计算：

```text
初始止损距离
单笔 R
平均净 R
平均盈利 R
平均亏损 R
理论盈亏比
实际盈亏比
最大浮盈 MFE
最大浮亏 MAE
MFE / MAE 比
是否先到 +1R
是否先到 +2R
是否先到 -1R
到达 +1R / +2R / -1R 的时间
入场后立即反向比例
止损前曾达到正浮盈比例
盈利交易回撤比例
亏损交易最大有利波动
```

建议：

```text
单笔 R = 单笔收益百分比 / 初始止损百分比
```

期望 R：

```text
期望 R =
胜率 × 平均盈利 R
-
亏损率 × 平均亏损 R
```

如果初始止损距离缺失：

```text
用策略 stoploss 估算
标记“估算 R”
```

如果无法计算：

```text
信号级分析不可用
```

---

# 20. 信号层通过 / 失败规则

默认分析规则，可配置但必须记录：

## 信号优势失败

```text
Profit Factor < 1
或平均净 R <= 0
或扩大验证后长期负期望
```

## 信号优势可能存在

```text
平均净 R > 0
Profit Factor > 1
且至少在非小样本中保持
```

## 小样本不泛化

```text
Smoke 为正
但 Expanded / Holdout / 更长时间范围明显为负
```

必须输出：

```text
信号层结论
结论依据
证据可信度
```

---

# 21. 成本与执行现实归因

每个策略必须分析：

```text
手续费总额
滑点估算总额
平均每笔手续费
平均每笔滑点
资金费率成本，如有
原始收益与成本后收益差
原始 PF 与成本后 PF 差
买卖价差代理
交易频率
每月 / 每天 / 每币种交易数
```

判断规则：

## 原始 PF < 1

```text
信号本身已失败
成本只是进一步恶化
```

## 原始 PF > 1，但成本后 PF < 1

```text
成本敏感型失败
```

## 交易数很高、原始 PF 接近 1

```text
薄优势被过度交易吞噬
```

用户可见标签：

```text
信号无优势
成本敏感
过度交易
交易频率可接受
成本证据不足
```

---

# 22. 成本压力结果低于 -100% 的处理

如果报告层后处理滑点后收益出现：

```text
-150%
-217%
-229%
```

必须明确标记：

```text
这是成本压力累计指标
不是带清算逻辑的真实账户最终权益
```

必须分开输出：

```text
原始回测权益结果
报告层成本压力收益
是否包含清算模型
是否允许账户净值低于 0
```

不要把报告层压力值解释为真实可提现账户余额。

---

# 23. 账户、仓位与杠杆归因

必须读取或提取：

```text
初始本金
stake_amount
风险比例
杠杆
max_open_trades
最大同时持仓
是否复利
是否固定仓位
是否全仓
最大保证金占用
日最大亏损
账户最大回撤
账户恢复时间
是否接近归零
```

必须区分：

## 信号正，但账户路径失败

可能标记：

```text
仓位模型放大亏损
杠杆放大亏损
并发风险失败
复利路径失败
```

## 信号本身负

```text
不能仅通过降低仓位拯救
```

`风险模型失败` 只能表示：

```text
观察到的账户路径不可接受
```

不能自动解释为：

```text
仓位算法存在代码错误
```

---

# 24. 过度交易分析

必须计算标准化交易频率：

```text
每月交易数
每天交易数
每币种每月交易数
单位市场暴露下的交易数
```

不要仅用绝对交易数跨周期比较。

分析阈值必须写入配置和报告，且注明：

```text
仅用于研究分类，不是交易阈值
```

过度交易判断至少考虑：

```text
交易频率
原始 PF
成本后 PF
平均净 R
重复入场
冷却是否存在
```

---

# 25. 做多 / 做空拆分

所有双向策略必须分别输出：

```text
做多交易数
做多收益
做多 PF
做多成本后 PF
做多胜率
做多平均 R
做多最大回撤
做多最大连续亏损
做多止损次数

做空交易数
做空收益
做空 PF
做空成本后 PF
做空胜率
做空平均 R
做空最大回撤
做空最大连续亏损
做空止损次数
```

必须回答：

```text
做多拖累
做空拖累
两边都失败
一边有局部优势
证据不足
```

---

# 26. 币种归因

每个币种至少输出：

```text
交易数
做多 / 做空数
收益
PF
胜率
平均 R
最大回撤
止损次数
手续费
滑点
占总盈利比例
占总亏损比例
```

必须计算：

```text
单一币种收益集中度
单一币种亏损集中度
主流币与山寨币差异
BTC / ETH / SOL 与 Top10 / Top30 差异
```

用户可见结论：

```text
全币种失败
币种池过宽
单一币种拖累
仅主流币可能有效
仅部分币种局部有效
币种样本不足
```

不得通过事后删除亏损币改写历史回测结论。

---

# 27. 时间稳定性归因

按以下维度拆分：

```text
月份
季度
年份
工作日 / 周末，如可用
时段，如可用
```

必须分析：

```text
收益是否集中在少数月份
亏损是否集中在极端月份
是否只有小样本阶段盈利
扩大后是否失效
是否存在明显训练期过拟合
```

输出中文标签：

```text
月度稳定
月度不稳定
收益集中
亏损集中
小样本不泛化
长期不泛化
时间证据不足
```

---

# 28. 市场状态归因

统一市场状态：

```text
牛市
熊市
震荡
急跌
修复
高波动
未知
```

每个状态尽量输出：

```text
交易数
做多 / 做空数
收益
PF
胜率
平均 R
最大回撤
市场暴露比例
不交易比例
错误方向交易数
```

必须回答：

```text
做多策略是否在熊市还大量交易？
做空策略是否在牛市或修复行情频繁交易？
NoTrade 是否在某些状态明显更好？
策略是否只有某个市场状态局部有效？
```

如果历史交易没有逐笔状态：

```text
标记“逐笔市场状态归因不可用”
不要猜测
```

---

# 29. 退出原因归因

退出原因统一为中文：

```text
止损
止盈
时间退出
动能退出
反向信号退出
数据结束退出
其他退出
```

每类输出：

```text
次数
总收益
平均收益
平均 R
平均持仓时间
占总亏损比例
```

必须判断：

```text
止损是否密集
止盈是否很少触发
时间退出是否有效
动能退出是否过早
盈利是否被过早截断
亏损是否拖太久
```

---

# 30. Entry Tag / Exit Tag 归因

如果策略存在多个入场标签或模块：

```text
按 entry_tag 拆分
```

如果存在多个退出标签：

```text
按 exit_tag 拆分
```

每个标签输出：

```text
交易数
收益
PF
平均 R
MFE
MAE
胜率
```

这样可以判断：

```text
整个策略失败
还是某一个模块拖累
```

---

# 31. 0 交易 / 完全阻塞策略专项分析

0 交易策略不能按普通亏损策略分析。

必须输出信号漏斗：

```text
总 K 线评估数
动态币种池入选数
数据准备完成数
市场状态分类数
趋势模块候选数
均值回归模块候选数
突破模块候选数
概率表查询次数
概率查询命中次数
概率查询未命中次数
样本不足桶数
概率门通过数
流动性门通过数
风控门通过数
最终信号数
实际交易数
阻塞原因分布
```

可能状态：

```text
信号链路无候选
概率门完全拦截
样本覆盖不足
数据缺失
实现问题
门槛过严
证据不足
```

禁止标记为：

```text
收益失败
```

---

# 32. 风险设计直接拒绝策略专项分析

例如：

```text
马丁
无限加仓
逆势摊平
```

无需强行补：

```text
收益
PF
胜率
```

应记录：

```text
风险放大方式
最大理论敞口
连续亏损后的仓位变化
是否可能无限加仓
是否违反 AlphaPilot 风控原则
拒绝原因
是否永久禁止
```

状态：

```text
禁止路线
```

---

# 33. 数据与实现问题检查

必须检查：

```text
isMock
时间范围
pair mapping
现货 / 永续混用
K线缺失
重复时间戳
未来函数
lookahead
informative timeframe 对齐
策略别名错配
bucket key mismatch
概率表缺失
.last_result 指针
ZIP 内 JSON 读取
Freqtrade 版本兼容
entry / exit pricing 配置
```

如果问题来自数据或实现：

```text
不要归为信号失败
```

应标记：

```text
数据覆盖不足
实现或运行时问题
暂不能评价
```

---

# 34. Benchmark 对比

有条件时，每个策略都应对比：

```text
NoTrade
BuyHoldBTC
BuyHoldETH
BuyHoldSOL
EqualWeight BTC/ETH/SOL
简单基准策略
```

输出：

```text
是否跑赢 NoTrade
相对 BuyHold 超额收益
相对 EqualWeight 超额收益
回撤是否降低
风险调整后是否改善
```

不能只看：

```text
策略自身总收益
```

---

# 35. 失败类型分类

每个策略可以有多个失败类型，但必须明确：

```text
主要失败类型
次要失败类型
```

中文分类：

```text
信号本身无优势
小样本不泛化
交易频率过高
手续费与滑点吞噬
仓位模型放大亏损
杠杆放大亏损
组合并发风险失败
复利路径失败
市场状态不匹配
做多方向失败
做空方向失败
币种池过宽
单一币种拖累
退出逻辑失败
止损密集
止盈过远
退出过早
样本不足
概率门完全拦截
数据覆盖不足
实现或运行时问题
基线对比失败
长期稳定性不足
风险不可接受
风险设计直接拒绝
```

每个失败类型必须有：

```text
证据
来源文件
可信度
因果是否已证明
```

归因属于：

```text
描述性证据分类
```

不能宣称：

```text
已经证明唯一因果
```

---

# 36. 失败严重程度

中文等级：

```text
灾难性失败
严重失败
无明显优势
成本敏感
局部可能有效
证据不足
实现问题，不能评价
风险设计直接拒绝
```

建议规则：

## 灾难性失败

```text
最大回撤接近 100%
或账户接近归零
或长期大规模亏损
```

## 严重失败

```text
PF 明显低于 1
高回撤
多个维度持续失败
```

## 无明显优势

```text
PF 接近 1
成本后转负
长期不稳定
```

## 局部可能有效

```text
某些币种 / 方向 / 市场状态有稳定迹象
但全样本不合格
```

---

# 37. 归因可信度

每个归因输出：

```text
高可信
中可信
低可信
证据不足
```

建议：

```text
一级证据 + 逐笔数据 -> 高可信
一级证据但缺少逐笔状态 -> 中可信
二级结构化报告 -> 中或低可信
文档 / 代码推断 -> 低可信
```

---

# 38. 跨策略共同失败模式

必须生成跨策略分析：

```text
哪些指标组合反复失败
哪些周期反复过度交易
哪些做空逻辑反复止损密集
哪些做多逻辑在熊市反复失败
哪些退出方式反复无效
哪些币种反复拖累
哪些市场状态反复造成大亏
哪些策略在成本后统一崩溃
哪些策略小样本好、扩大后失败
```

每个模式输出：

```text
模式名称
出现次数
涉及策略
证据报告
是否禁止重复
是否允许作为新假设研究
```

---

# 39. 负样本规则库

至少合并和生成以下规则：

```text
1. 不晋级 Profit Factor 低于 1 或平均净 R 非正的策略。
2. 不用小幅阈值修改拯救结构性失败。
3. 解释原始收益前必须检查手续费和滑点。
4. 高频负优势属于噪音放大，不是分散化。
5. 缺失证据不能当作 0 或通过证据。
6. 信号层与账户风险层必须独立通过。
7. 拒绝马丁和逆势无限摊平。
8. 归档策略复活必须创建新版本和新证据。
9. 不允许做多 / 做空混合结果掩盖单方向失败。
10. 不通过删除亏损币改写历史结果。
11. 不在熊市让做多策略持续高暴露。
12. 不在牛市 / 修复行情让做空策略持续高暴露。
13. 不把 0 交易策略归类为收益失败。
14. 不无限小调灾难性失败策略。
15. 不忽略 NoTrade / BuyHold / EqualWeight 基线。
```

---

# 40. 可复用组件库

整体策略失败，不代表所有组件无效。

必须分析：

```text
数据质量检查
市场状态标签
信号审计
不追涨 / 不追空
冷却机制
流动性闸门
止损强制存在
时间退出
MFE / MAE 记录
多空拆分
币种集中度检查
相对强弱
波动率风险过滤
严格趋势筛选
成交量质量
早期失败退出
布林回收结构
突破回踩结构
NoTrade 防御状态
```

每个组件输出：

```text
组件名称
来源策略
支持证据
反对证据
建议用途
是否可直接复用
是否需要重新验证
```

中文状态：

```text
可复用
仅作风险过滤
仅作研究特征
需要重新验证
不建议复用
```

---

# 41. 策略复活政策

归档不是删除。

每个策略必须给出：

```text
是否允许重新研究
必须满足什么条件
```

允许的复活路径：

```text
创建新的不可变策略 ID / 版本
提出实质不同的假设
补齐关键证据
证明平均净 R > 0
证明 PF > 1
通过真实手续费和滑点压力
通过留出集
通过 Walk-forward
账户风险层独立通过
重新进入普通晋级流程
```

禁止：

```text
修改原归档报告
把小幅调参当作新证据
用留出集反复选参数
因为一个币或一个月表现好就复活
复活马丁或逆势无限摊平
直接把分析报告当作交易所模拟盘或实盘发布依据
```

---

# 42. 复活条件示例

可用条件：

```text
加入 Funding Rate / Open Interest 数据
切换到 4h / 1d
收窄到 BTC / ETH / SOL
只保留做多
只保留做空
仅在特定市场状态研究
降低交易频率
修复仓位模型
获取更长历史样本
补充逐笔市场状态标签
增加真实执行成本数据
```

中文状态：

```text
继续归档
作为基准策略保留
仅复用组件
在新数据条件下可重新研究
在特定市场状态下可重新研究
在新周期下可重新研究
禁止路线
```

---

# 43. 输出文件

建议新增代码：

```text
alphapilot/reports/full_archived_strategy_inventory.py
alphapilot/reports/archived_strategy_evidence_index.py
alphapilot/reports/archived_strategy_trade_extractor.py
alphapilot/reports/archived_strategy_metrics_normalizer.py
alphapilot/reports/archived_strategy_failure_attribution_v2.py
alphapilot/reports/archived_strategy_failure_schema_v2.py
alphapilot/reports/generate_full_archived_strategy_analysis.py
```

必须生成：

```text
reports/full_archived_strategy_inventory.json
reports/full_archived_strategy_inventory.csv
reports/full_archived_strategy_coverage_audit.json
reports/full_archived_strategy_evidence_index.json
reports/full_archived_strategy_evidence_gaps.json
reports/full_archived_strategy_metrics_matrix.json
reports/full_archived_strategy_metrics_matrix.csv
reports/full_archived_strategy_trade_level_metrics.jsonl
reports/full_archived_strategy_trade_level_sample.csv
reports/full_archived_strategy_signal_funnel_matrix.json
reports/full_archived_strategy_failure_attribution.json
reports/full_archived_strategy_failure_matrix.csv
reports/full_archived_strategy_cross_strategy_patterns.json
reports/full_archived_strategy_negative_rules.json
reports/full_archived_strategy_reusable_components.json
reports/full_archived_strategy_revival_candidates.json
reports/full_archived_strategy_continue_archive.json
reports/full_archived_strategy_prohibited_routes.json
reports/full_archived_strategy_analysis_summary.md
```

如果逐笔完整文件太大：

```text
保存到 reports/generated/
加入 .gitignore
提交 sample + summary + manifest
```

---

# 44. 每策略独立报告

建议为每个策略生成：

```text
docs/archived-strategies/{strategyId}.md
```

每份包含：

```text
策略身份
策略假设
策略参数
证据索引
回测范围
基础指标
信号级指标
成本归因
账户风险归因
做多 / 做空归因
币种归因
时间归因
市场状态归因
退出原因归因
主要失败类型
次要失败类型
失败严重程度
可复用组件
不能继续重复的规则
复活条件
当前中文状态
```

---

# 45. 主报告结构

建议：

```json
{
  "报告名称": "AlphaPilot 全量归档失败策略证据库与逐笔归因分析",
  "生成状态": "研究分析完成",
  "覆盖率审计": {},
  "发现策略总数": 0,
  "已分析策略数": 0,
  "策略覆盖率": 0,
  "一级证据策略数": 0,
  "二级证据策略数": 0,
  "三级证据策略数": 0,
  "四级证据策略数": 0,
  "证据不足策略数": 0,
  "策略清单": [],
  "统一指标": [],
  "逐笔信号指标": {},
  "失败归因": [],
  "共同失败模式": [],
  "负样本规则": [],
  "可复用组件": [],
  "复活候选": [],
  "继续归档": [],
  "禁止路线": [],
  "安全边界": {
    "交易所模拟盘批准": false,
    "实盘批准": false,
    "交易接口启用": false,
    "提现接口启用": false
  },
  "生成时间": "UTC ISO"
}
```

---

# 46. 分阶段执行

## 阶段一：发现与覆盖率审计

```text
扫描策略、报告、artifact、状态归档、Git 历史
建立规范 strategyId
列出遗漏和孤立项
```

## 阶段二：证据索引

```text
策略 -> ZIP / JSON / 报告 / 文档 / Commit / Tag
验证文件存在和可读性
```

## 阶段三：统一指标

```text
标准化字段
真实报告与 mock 分离
不同周期 / 范围分离
```

## 阶段四：逐笔证据提取

```text
从已有 ZIP / JSON 提取交易
计算 R / MFE / MAE
关联 pair / direction / exit reason / regime
```

## 阶段五：单策略归因

```text
信号层
成本层
账户风险层
方向
币种
时间
市场状态
退出逻辑
数据 / 实现
```

## 阶段六：跨策略归因

```text
共同失败模式
负样本规则
可复用组件
复活条件
禁止路线
```

## 阶段七：输出报告

```text
JSON
CSV
Markdown
每策略独立文档
```

---

# 47. 针对缺失证据的处理

如果缺失关键逐笔数据：

```text
1. 不自动重跑
2. 写入 evidence gaps
3. 指定缺少什么
4. 指定影响了哪些结论
5. 指定建议补跑配置
6. 等用户批准
```

补证据计划字段：

```text
策略 ID
缺失证据
建议交易对
建议周期
建议 timerange
建议配置
是否需要真实回测
优先级
```

---

# 48. 报告完整度分数

建议为每个策略计算：

```text
证据完整度 0-100
```

参考：

```text
策略身份完整：10
原始 artifact：20
逐笔交易：20
成本数据：10
账户配置：10
币种 / 时间拆分：10
市场状态拆分：10
退出原因拆分：10
```

用户可见等级：

```text
证据完整
证据较完整
证据一般
证据不足
无法评价
```

---

# 49. 验证命令

在 Quant 仓库执行：

```powershell
cd D:\Codex-Workspace\AlphaPilot-Quant-Engine
git status
python -m compileall alphapilot
python -m alphapilot.reports.generate_full_archived_strategy_analysis
python -m alphapilot.scripts.validate_config
powershell -ExecutionPolicy Bypass -File scripts\check_safety.ps1
git diff --check
```

本任务不得执行：

```text
freqtrade backtesting
download-data
dry-run
trade
```

只读取现有 artifact。

---

# 50. 安全扫描

必须确认没有新增：

```text
API Key 存储
Trade API
Withdraw API
真实账户读取
真实持仓读取
真实订单创建
交易所模拟盘执行
实盘执行
自动交易
策略参数修改
策略代码修改
```

允许出现：

```text
安全声明
否定语境
历史报告字段
现有策略中的 backtest 接口
```

---

# 51. 验收标准

完成后必须满足：

```text
1. 已自动发现全部历史归档 / 暂停 / 失败 / 拒绝策略
2. 已生成覆盖率审计
3. 已生成规范策略 ID 与别名映射
4. 已生成证据索引
5. 已标记证据等级
6. 已生成证据完整度
7. 已生成统一指标矩阵
8. 已读取所有可用原始 artifact
9. 已生成逐笔信号指标或明确证据不足
10. 已区分信号失败与账户风险失败
11. 已分析交易频率与成本
12. 已拆分做多与做空
13. 已分析币种集中度
14. 已分析月份 / 季度 / 年份稳定性
15. 已分析市场状态
16. 已分析退出原因
17. 已分析 entry tag / exit tag
18. 已专项分析 0 交易策略
19. 已专项分析风险设计拒绝策略
20. 已分析数据与实现问题
21. 已生成主要 / 次要失败类型
22. 已生成严重程度
23. 已生成归因可信度
24. 已生成共同失败模式
25. 已生成负样本规则库
26. 已生成可复用组件库
27. 已生成复活条件
28. 已生成继续归档清单
29. 已生成禁止路线清单
30. 已生成证据缺口和补证据计划
31. 未修改任何策略代码
32. 未修改任何策略参数
33. 未运行新回测
34. 未进入交易所模拟盘
35. 未接真实 API Key
36. 未接 Trade API
37. 未接 Withdraw API
38. 未读取真实账户
39. 未读取真实持仓
40. 未创建真实订单
41. 未自动交易
42. compileall 通过
43. validate_config 通过
44. safety scan 通过
45. git diff --check 通过
```

---

# 52. 提交与 Tag

如果报告成功生成并通过检查：

```bash
git add .
git commit -m "Add full archived strategy failure evidence analysis"
git push
```

Tag 规则：

```text
使用当前 Quant 仓库下一个未占用版本号。
不得覆盖已有 tag。
如果用户没有指定版本号，可以先不打 tag。
```

Docs 仓库：

```bash
git add .
git commit -m "Document full archived strategy failure analysis"
git push
```

---

# 53. Codex 最终自检输出

执行结束后请输出：

```text
1. 新增文件列表
2. 修改文件列表
3. 删除文件列表
4. 发现策略总数
5. 已分析策略数
6. 策略覆盖率
7. 一级证据策略数
8. 二级证据策略数
9. 三级证据策略数
10. 四级证据策略数
11. 证据不足策略数
12. 有逐笔交易策略数
13. 信号本身无优势策略数
14. 成本敏感策略数
15. 仓位 / 杠杆 / 账户路径失败策略数
16. 过度交易策略数
17. 市场状态不匹配策略数
18. 币种池问题策略数
19. 做多方向失败策略数
20. 做空方向失败策略数
21. 退出逻辑问题策略数
22. 小样本不泛化策略数
23. 0 交易 / 完全阻塞策略数
24. 数据 / 实现问题策略数
25. 风险设计直接拒绝策略数
26. 共同失败模式摘要
27. 负样本规则摘要
28. 可复用组件摘要
29. 可复活策略摘要
30. 继续归档策略摘要
31. 禁止路线摘要
32. 证据缺口摘要
33. 主报告路径
34. 汇总报告路径
35. 策略清单路径
36. 证据索引路径
37. 指标矩阵路径
38. 逐笔指标路径
39. 失败矩阵路径
40. 覆盖率审计路径
41. 是否修改策略
42. 是否修改参数
43. 是否运行新回测
44. 是否接 API Key
45. 是否接 Trade API
46. 是否接 Withdraw API
47. 是否读取真实账户
48. 是否读取真实持仓
49. 是否创建订单
50. 是否执行交易所模拟盘
51. 是否自动交易
52. compileall 结果
53. validate_config 结果
54. safety scan 结果
55. git diff --check 结果
56. commit hash
57. push 结果
58. tag 结果，如有
59. 已知问题
60. 下一步建议
```

---

# 54. 最终指令

请严格执行本提示词。

本次不要继续生成新策略。  
本次不要调参。  
本次不要重新批量回测。  
本次不要改变策略状态。  
本次只分析已有的归档、暂停、拒绝和失败策略证据。

最终不能只输出：

```text
这些策略失败了
```

必须输出：

```text
为什么失败
失败发生在哪一层
证据有多完整
信号是否有优势
成本放大了多少
账户路径放大了多少
哪些方向 / 币种 / 月份 / 市场状态拖累
哪些退出逻辑有问题
哪些组件还能复用
哪些路线禁止重复
哪些策略未来在什么条件下可重新研究
```

归档不是删除。

归档策略必须被转化为：

```text
失败证据
基准策略
负样本规则
可复用组件
策略复活条件
未来研究边界
```
