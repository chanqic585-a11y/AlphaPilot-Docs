# V2 AI Trading Copilot Prompt

## Role

你是 Senior Product Designer + Senior React Native Engineer + Quant Trading Software Engineer。

目标不是继续增加交易日记功能，而是把产品升级为真正帮助交易者减少亏损的 AI Trading Copilot。

## Product Positioning

产品名称暂定：

- Trade Coach AI
- Trading Copilot

定位：

> 一个不会替你交易、不会预测行情、不会自动下单，而是陪伴交易者完成整个交易生命周期的 AI 副驾驶。

原则：

- 减少输入。
- 增加 AI 自动分析。
- 减少人为记录。
- 增加数据自动收集。

## Core Philosophy

交易员最大的敌人不是市场，而是自己。

产品必须围绕：

- 纪律
- 情绪
- 风险
- 执行

UI 风格参考 Apple Health、Notion、Linear、Arc Browser。不要像 Binance、OKX 或 TradingView。

颜色：

- 白色
- 灰色
- 深蓝
- 少量橙色警告

## New Trade Flow

废弃旧的十几个字段表单作为主流程。

点击【新建交易】，只输入：

1. 币种
2. 方向：做多 / 做空
3. 入场价格
4. 仓位金额
5. 杠杆，最高 5 倍

点击【开始记录】。

目标：10 秒内完成记录。

## AI Auto Analysis

保存交易后，AI 自动分析并记录：

- 当前价格
- 最近行情
- 成交量
- 波动率
- 均线
- RSI
- MACD
- ATR
- 趋势
- 支撑
- 压力
- 止损建议
- 第一目标
- 第二目标

注意：只做客观分析，不预测未来，不推荐买卖。

禁止生成：

- 建议买
- 建议卖
- 一定会上涨
- 一定会下跌

## Pattern Detection

AI 自动判断当前形态，并给出可信度，例如：

- 首次回踩突破
- 平台突破
- 下降趋势
- 上涨趋势
- 震荡整理
- 放量突破
- 缩量回踩

## Screenshots

创建交易后预留接口自动保存：

- 1 分钟 K 线截图
- 5 分钟 K 线截图
- 15 分钟 K 线截图
- 1 小时 K 线截图

如果当前版本无法自动截图，先预留接口。

## Risk

自动计算：

- 仓位风险
- 账户风险
- 止损距离
- RR

如果风险超过用户设置，只提醒“风险超过你的规则”，不要禁止交易。最终决定权属于用户。

## OKX Public Alerting

继续使用 OKX 公共行情接口，只读，不下单，不连接交易权限。

用户输入止损和止盈后，APP 自动监听：

- 距离止损 2%：提醒
- 距离止盈 1%：提醒

做多：

- 当前价 <= 止损价：止损提醒
- 当前价 >= 止盈价：止盈提醒

做空：

- 当前价 >= 止损价：止损提醒
- 当前价 <= 止盈价：止盈提醒

## AI Review

交易结束后，不要求用户写长篇总结。

AI 自动生成复盘：

- 开仓位置是否合理
- 形态是否匹配
- 成交量是否正常
- 趋势是否保持
- 是否提前止盈
- 是否移动止损
- 如果按原计划执行，理论结果如何

区分：

- 策略亏损
- 纪律亏损
- 市场随机波动
- 高质量亏损

## Trading Memory

长期分析 100 / 200 / 500 笔交易，找出用户真正赚钱和亏钱的模式。

示例：

- 最赚钱：15 分钟、首次回踩、上市 6 小时内、成交量增加、OI 增加。
- 最差：追高交易，胜率低。

## Development Requirements

- React Native + Expo + TypeScript。
- SQLite 继续作为本地数据库。
- 分析模块单独封装。
- 行情接口独立封装。
- AI 模块预留 Provider 接口。
- 每完成一个模块运行 TypeScript 检查。

