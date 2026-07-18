# AlphaPilot V13.27.1.27 零幸存者闭环

## 结论

V13.27.1.26 已补齐冻结候选排序语义，并将 V27 候选研究入口打开。
V13.27.1.27 随后按预注册边界完成 4 个市场假设家族、8 个候选的结构、
排序、容量和经济预筛选。结果为零个经济预筛选幸存者，因此程序按设计停在：

```text
completed_zero_qualified_candidates
```

这是一个合法的研究终点，不是工程失败。V28-V31 未被执行，也没有通过放宽
门槛、修改冻结身份或读取后续证据来制造幸存者。

## 运行身份

- Program ID: `automatic_strategy_to_demo_v26_2aff44adf84d039c`
- V27 经济运行代码提交: `1b8b3355240e1ee2a784af7bc7834c27a35658d6`
- V27 结果闭环提交: `f2571ca6da3635f7ceea357db6dc03bb17760aaf`
- Quant tag: `v13.27.1.27`
- 结果生成时间: `2026-07-18T16:20:42.9455362+08:00`

## 漏斗结果

| 阶段 | 数量 |
| --- | ---: |
| 假设家族 | 4 |
| 候选策略 | 8 |
| 结构检查通过 | 8 |
| 排序检查通过 | 8 |
| 容量检查通过 | 3 |
| 经济预筛选通过 | 0 |
| 正式候选 | 0 |
| Formal 运行 / 结果读取 | 0 / 0 |
| Locked OOS 读取 | 0 |
| 不可变 Release | 0 |
| Demo 批准 / ARM / 订单 | 0 / 0 / 0 |

最接近通过的候选为 `range expansion 4h long`：PF `1.03564`、平均净 R
`0.02057`，仅未满足 `minimumBenchmarkIncrementNetR`。其余候选主要因
容量证据不完整或经济门槛失败而停止。

## 试验预算

- Campaign: `1`
- Candidate trials: `8`
- Formal runs: `0`
- Formal result reads: `0`
- Locked OOS reads: `0`
- Releases / approvals / Demo ARM / orders: 全部 `0`

## 证据位置

Quant 仓库根目录下：

```text
reports/automatic_strategy_to_demo/
  automatic_strategy_to_demo_v26_2aff44adf84d039c/
```

核心证据包：

- `AlphaPilot-V26-31-Automatic-Strategy-to-Demo-Core-Evidence.zip`
  - SHA-256: `deb47b5dadbd071dcba897532d3017a06ad7f7912e1a4ccb444a9cc77390dbf6`
- `AlphaPilot-V26-31-Candidate-and-Formal-Evidence.zip`
  - SHA-256: `61de2dc09f4b3bd16399d300a532f89fe520f775916ccbeb1245fe98392e5852`
- `AlphaPilot-V26-31-OKX-Release-and-Demo-Evidence.zip`
  - SHA-256: `2dc16829efe8c5da77e8966d1ad72c45e60cd5047f2689b3e63913fa44b49eda`

清单共核验 58 个文件，零哈希不一致；三个 ZIP 均可正常读取，内部成员哈希
核验通过，未发现凭据内容。

## 阶段裁决

- V28 Formal Validation: 未到达，因为正式候选为 0。
- V29 Locked OOS: 未到达。
- V30 Immutable Release / Console import: 未到达。
- V31 Demo approval / ARM: 未到达，因此不存在可供人工批准的 Release hash。
- Console 仓库无需修改或打标签。
- 不接 Withdraw，不保存 API 凭据，不创建真实订单，不自动 ARM。

## 验证

- 定向测试: `8 passed`
- Quant 全量项目测试: `1043 passed, 157 subtests passed`
- 配置校验: 通过；Live / Trade API / Withdraw API 全部关闭
- 安全扫描: 通过；无新增可执行交易能力
- Git diff check: 通过

