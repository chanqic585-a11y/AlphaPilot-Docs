# AlphaPilot V13.27.7 - Demo Canonical Instrument Fix

## Goal

修复 OKX Demo 全市场扫描在策略判断前错误拒绝全部候选的问题。

## Root Cause

已经规范化的 `ETH-USDT-SWAP` 被重复拼接为无效合约 ID，普通的
`ETH-USDT` 也会产生双连字符。Ticker、K 线和合约元数据因此在策略规则
执行前统一失败。

旧版 10 条 Demo Release 的一次 `0` 命中结果无效，不得用于评价策略。

## Required Behavior

- `ETH`
- `ETHUSDT`
- `ETH/USDT`
- `ETH-USDT`
- `ETH-USDT-SWAP`

以上输入都必须幂等映射为 `ETH-USDT-SWAP`。

修复后重新运行 10 条不可变 Demo Release 的全市场扫描，并区分：

- 真实策略条件未匹配
- 流动性或风险门槛拒绝
- 数据或连接失败
- 订单创建结果

## Safety Boundary

- 只使用 OKX Demo 凭据。
- Raw API Key 只存在于启动进程内存。
- 不保存凭据。
- 不接 Withdraw。
- 不把烟测订单计入策略证据。
- 不绕过不可变 Demo Release 和风险门槛。
