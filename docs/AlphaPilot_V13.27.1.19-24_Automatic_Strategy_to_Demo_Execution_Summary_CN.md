# AlphaPilot V13.27.1.19-24 自动策略至 OKX Demo 工作流执行总结

## 结论

整套工作流已完成，最终路径为：

```text
completed_zero_qualified_candidates
```

这不是工程失败。V19-V24 的数据能力审计、假设注册、候选生成、预筛、正式验证、不可变 Release 判定、Console 导入审计和零 Release 安全路径均已运行并闭合。唯一进入正式验证的候选在冻结资本政策下被判定为 `capital_infeasible`，因此系统正确生成 0 条 Release、0 次批准、Demo 未 ARM、0 个订单。

## 工作流分析

这套设计的主要优点：

1. 数据能力先于策略结论，缺少语义可靠的数据不会被静默替代。
2. 候选经预注册、家族去重和结构认证后才进入有限预算的正式验证。
3. Formal Validation Core 通过 CandidateAdapter 接入候选，未写死 S01 或单一策略。
4. 正式结果只读取一次，Future Locked OOS 保持未读，避免结果驱动调参。
5. Release、批准、风险 Overlay、Demo ARM 和订单是分离的不可变门控对象。
6. 0 Release 是一等公民路径，工程烟测不会伪装成策略证据。

需要改进的不是放宽 Gate，而是下一轮数据语义：当前冻结数据没有可验证的 `quote_turnover` 容量语义，导致 1,258 个已分配正式事件全部被资本政策稳定拒绝。下一轮应先补充或建立独立验证过的成交额/容量数据 Profile，再提出新机制或新 Campaign；不应围绕本次结果微调当前候选。

## 分阶段结果

| 阶段 | 结果 |
|---|---|
| V19 Data Capability | 8 个品种、1h/4h/1d 核心 OHLCV 可用；衍生字段按缺口登记 |
| V20 Hypothesis Factory | 8 个机制家族、16 个候选；每家族最多 2 个变体 |
| V21 Prefilter | 1 个候选进入一次性正式验证；Locked OOS 冻结未读 |
| V22 Formal Validation | 1 次完整回测；状态 `capital_infeasible`；0 个资本接受交易 |
| V23 Release Builder | 0 条合格 Release；无需人工精确 Hash 批准 |
| V24 Console Cutover | 0 导入、0 批准、Demo 未 ARM、0 订单；零 Release 路径通过 |

## 核心身份与计数

- Program ID: `automatic_strategy_demo_f57c443abeaf06c0`
- Campaign: `automatic_strategy_demo_f57c443abeaf06c0_campaign_01`
- Family count: `8`
- Candidate count: `16`
- Prefilter survivor count: `1`
- Formal candidate count: `1`
- Full backtests: `1 / 144`
- Formal run count: `1`
- Result read count: `1`
- Locked OOS reads: `0`
- Formal pass: `0`
- Release count: `0`
- Demo ARM: `false`
- Demo order count: `0`

## 正式证据

- 正式候选：`auto-trend_failure_reversal-4h-short-v2`
- 正式路径：`capital_infeasible`
- 原始正式事件：`1,266`
- 已分配事件：`1,258`
- 稳定容量拒绝：`1,258`
- 资本接受交易：`0`
- 完整折数：`5`
- Funding：8/8 品种为真实公共历史输入
- 主要原因：`verified_quote_turnover_semantics_unavailable`
- Gate relaxation count: `0`

## 安全边界

- Live enabled: `false`
- Trade API enabled: `false`
- Withdraw API enabled: `false`
- API 凭据保存：无
- 私有签名请求头输出：无
- Release 自动批准：无
- Demo 自动 ARM：无
- 工程烟测计入策略证据：否

## 验证

- Quant: `1003 passed, 157 subtests passed`
- Console: `432 passed, 66 subtests passed`
- Quant compileall: 通过
- Console compileall: 通过
- Console JavaScript syntax check: 通过
- Console HTTP smoke: 通过
- ZIP 敏感值扫描：0 命中

## 提交与标签

### Quant

- V19: `7fdbf42f531be5ebb0297ce4d9497b272a2a3b54` / `v13.27.1.19`
- V20: `39bee4e1a6ae502aa947d5317017c9d56f13d3b7` / `v13.27.1.20`
- V21: `36e8479fe004e7d91c0b858ca170538ea469bb89` / `v13.27.1.21`
- V22: `b47ac08` / `v13.27.1.22`
- V23: `10beef71f31468f65e6068acc26890458a353f52` / `v13.27.1.23`
- V24: `85d18f5c6df0012c886c4a7d91376658d0466e72` / `v13.27.1.24`
- 跨阶段证据收口：`8b74719`

### Console

- V24 implementation: `37746c040fed077791b201aa3eb7d112d3c04841`
- V24 evidence: `987981218cd376e6f82c9ed1093c4ce686988af7`
- Tag: `v13.27.1.24`

### Docs

- 冻结 Prompt commit: `48eac2d`
- Prompt SHA-256: `E7C2B6711FDC48CACF731B3C6B8930B6D370BCB802742C6E554C4D82B7E87953`

## 证据包

证据包位于：

```text
D:\Codex-Workspace\AlphaPilot-Workflow-Evidence\automatic_strategy_demo_f57c443abeaf06c0-20260718-111508\
```

- `AlphaPilot-Automatic-Strategy-to-Demo-Core-Evidence.zip`
- `AlphaPilot-Automatic-Strategy-to-Demo-Candidate-Evidence.zip`
- `AlphaPilot-Automatic-Strategy-to-Demo-Release-and-Demo-Evidence.zip`

证据包不包含 API Key、Secret、Passphrase、私有签名请求头、虚拟环境、原始大体积 OHLCV 或真实账户信息。

## 下一轮建议

下一轮不是继续调整本候选，而是先建立带明确单位、来源和时间语义的 `quote_turnover` / capacity Profile。只有新数据 Profile 或真正不同的市场机制出现时，才启动下一 Campaign。仍需保持预算上限、预注册、CandidateAdapter、一次性 Formal、Locked OOS 未读和不可变 Release 边界。
