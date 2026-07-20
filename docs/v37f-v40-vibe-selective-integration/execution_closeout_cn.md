# AlphaPilot V37F-V40 Vibe 选择性集成执行收口

## 结论

本轮以 `HKUDS/Vibe-Trading` 固定提交
`7d42de944466e1a1f12f0df3933624fe665dee3c` 为来源，只做 clean-room
选择性吸收。AlphaPilot 的证据门、Formal Gate、不可变 Release、Demo
批准和风险边界继续保持唯一权威。

## 已完成

- V37F：预算对账、Formal Gate 单一判定源和 Fold 语义修复。
- V37G：固定 Vibe 来源、许可证和组件采纳决策，加入 Strategy Acquisition Manager。
- V37H：生成候选 AST 沙箱、有限 Factor Lab、候选去重和来源审计。
- V37I：执行 2 个 campaign、4 个机制家族、5 个候选的有界研究。
- V37J：Formal 路由只接收通过候选；本轮没有候选进入 Formal。
- V38：新增只读 Strategy Lab、OKX Demo 服务端时间同步、私有 WebSocket
  客户端、REST 权威对账、运行时阻断器和独立 Workflow Validation Demo。

## 实际结果

- locked OOS 读取：0
- Formal 候选：0
- 不可变 Demo Release：0
- Demo ARM：false
- 策略订单：0
- V39：`not_run_zero_release`
- V40：`disabled`

零候选是合法终局，不允许通过放宽 Gate、复用 holdout、伪造 Release 或把工程烟测
计入策略证据来强行推进。

## V38 运行边界

- Strategy Lab 是只读投影，不修改候选、Gate、Release、批准或订单。
- Workflow Validation Demo 仅验证工程链路，不计入策略资格、Formal Pass 或实盘晋级。
- Demo 凭据只允许进程内使用，报告和日志不得保存或输出凭据。
- Demo REST 请求继续使用 `x-simulated-trading: 1`；Withdraw 能力不存在。
- 私有网络调用本轮未执行，因为没有凭据且没有可执行 Release。

## 来源取舍

采纳：策略开发生命周期、策略资产投影、部分 Alpha/算子思想、生成代码沙箱、
多维候选相似度。

延后：衰减监控、影子账户归因，直到拥有足够独立 periodic bench 或真实闭合 Demo
交易。

拒绝：整仓合并、跨交易所数据替代 Formal、通用 Vibe Gate、原地自动改参、
Vibe broker/live connector、无限 LLM 策略守护进程。
