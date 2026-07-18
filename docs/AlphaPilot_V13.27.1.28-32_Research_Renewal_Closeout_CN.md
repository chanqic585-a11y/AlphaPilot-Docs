# AlphaPilot V13.27.1.28-32 研究续期收口

## 结论

V28-V32 已按候选中立架构实现并完成一次冻结 Prompt 运行。程序没有复活
V27 候选，也没有读取正式结果后修改候选或门槛。

- Program ID：`automatic_strategy_renewal_v28_4e6ab55a5e949716`
- Parent Program：`automatic_strategy_to_demo_v26_2aff44adf84d039c`
- 最终路由：`blocked_formal_data`
- 历史证据修改：0

## 实现范围

1. 因果资格窗口、事件守恒和分层数据就绪合同。
2. 资本与事件可比的 Benchmark 合同。
3. 方向事件、双腿 Pair、PIT 横截面组合三类候选合同。
4. 有界 Campaign、候选身份、结构修订和结果预算编排。
5. CandidateAdapter 通用正式验证核心及第二个合成候选证明。
6. OKX 同交易所证据或冻结可移植性审计。
7. 未批准、未 ARM、零订单的不可变 Release 与精确 Hash 批准边界。

## 数据裁决

数据目录共 32 份：24 份 OHLCV、8 份 Funding。Funding 的正式语义可用数
为 8，但正式 OHLCV 可用数为 0，因为现有 OHLCV 缺少可验证交易所来源或
PIT 语义。程序因此在候选身份生成前停止：候选、正式运行、结果读取、锁定
OOS、Release、批准、Demo ARM 和订单全部为 0，96 次完整回测预算全部保留。

这不是策略失败，也不是工程失败；它是正确的正式数据阻塞。

## 证据身份

- Prompt SHA-256：
  `18C6BBA3409321985C4C56FD22630F7A67422B98D196FB2C75296F8EB80C317E`
- 数据审计：
  `v28_catalog_readiness_5d4611fe26445c7c9dc2c1b36c49a9b6f77c1ba4a9bcd3a477c34771eeca5b70`
- Eligibility Window Policy：
  `causal_eligibility_window_policy_0eb6fa49ff4e213c574a9095e28062152b436e97268407c5e7f070cb835480bb`
- Benchmark Comparability Policy：
  `benchmark_comparability_policy_b8158bcec00a43b855966001b69ba7696faafbd9d90b3acc1a478749936595e0`
- Artifact Manifest：
  `automatic_strategy_renewal_manifest_8938e251c45470af00e9d138341a25b76406d15f9d639da39f8624b9d6248abb`
- Manifest Artifact：12
- Hash mismatch：0

## 下一次允许动作

只允许基于新的不可变数据快照和目录恢复。OHLCV 必须同时具备可验证来源、
PIT 语义、内容 Hash 和因果 availableAt。不得改写本次闭合结果、降低正式
覆盖门槛或复用失败候选身份。未来若生成 Release，必须停在
`blocked_waiting_exact_release_approval`，由用户批准精确 Release Hash 后才
能进入 Demo ARM。
