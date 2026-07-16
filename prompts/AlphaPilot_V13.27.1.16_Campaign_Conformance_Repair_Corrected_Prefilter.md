# AlphaPilot V13.27.1.16
# Advisory-R Campaign 实现一致性修复与纠错预筛重放

## 定位

本版本修复 V13.27.1.15 的实现一致性问题，不做参数优化，不进入 Walk-forward、Locked OOS、Release、Demo ARM 或订单阶段。

不可变输入：

```text
V15 Quant commit: 9262a00e808812477671cca850dcafaffc38cc55
V15 tag: v13.27.1.15
V15 Campaign: advisory_r_v15_502e810045e366353db4dbcfa7d08fdf3
V15 preregistration hash: advisory_r_prefilter_preregistration_8eb71c9974d772e6bb9cad6f75843cd68c0e907fef679fc0730a3b3f9f9e7ff5
Snapshot hash: minimal_shared_snapshot_785e47b180c17327dcb35e37d9f12c22e86eae8150e0adf5e1b44fb298f89e3b
Exit policy bounds: exit_policy_bounds_cca864ae009f3be27eb47f41bd3b81019d31b2adc10e57a2cebf9d51ae275238
```

## 补充执行约束

在原始 V13.27.1.16 Prompt 之上，本次执行额外锁定：

1. 新 Campaign 只能是实现纠错版本，候选、参数、Gate、代表币池、成本变化均为 0。
2. 代码提交并推送后才能生成预注册；结果读取前必须冻结 code/compiler hashes。
3. `candidate_events.parquet` 与正式 replay 事件做独立 parity。
4. 每个 simple benchmark 必须独立生成，不能复用候选 PnL。
5. Funding 缺失必须为 `null`。
6. 2R 为 advisory，不是 hard gate；`minimumTargetR=null`。
7. 一级幸存者只允许延期到下一版本正式阶段，不允许在本版本读取 OOS 或创建 Release。
8. 报告必须区分经济失败、仅诊断和实现阻塞。

## 权威执行结果

```text
correctionCampaignId=advisory_r_v16_correction_8ec939e8f7ce17a3d259c72c134d02
preregistrationHash=advisory_r_correction_preregistration_f145806af31b6774c3e3ffa2168b38d712c889b56f6190337ce23e8677f2cbae
codeCommit=b32cabc
candidateCount=10
familyCount=8
eventCount=38738
prefilterSurvivorCount=1
archivedCount=8
diagnosticCount=1
implementationBlockedCount=0
route=formal_stage_deferred_to_future_version
```

一级幸存者：

```text
s01_bear_idiosyncratic_selloff_recovery_4h
```

指标：442 个事件，PF 1.4475，平均净 R 0.1189，最大回撤 25.154%。

仅诊断候选 `s10_orthogonal_weak_signal_matrix_4h` 只因最大回撤门槛失败，但 `diagnosticOnly=true`，不得晋级。

## 一致性结果

```text
V15 original artifact hash mismatch count=0
V15 original files modified=0
implementation conformance=10/10
unused frozen fields=0
unsupported fallback=0
event parity missing/extra/changed=0/0/0
exit event count=38738
exit leg count=43854
exit parity failed events=0
```

## 安全边界

```text
parameterChanges=0
candidateChanges=0
gateChanges=0
universeChanges=0
costChanges=0
lockedOosAccessCount=0
formalEvidenceCount=0
releaseCount=0
demoArm=false
orderCount=0
```

## 报告目录

```text
reports/advisory_r_campaign/advisory_r_v16_correction_8ec939e8f7ce17a3d259c72c134d02/
```

目录包含 Prompt 要求的 21 份 JSON、CSV、Parquet 和 Markdown 证据。

## 最终结论

V15 的“零幸存”不能作为八个机制全部失败的证据，因为旧实现没有忠实执行冻结语义。纠错后出现 1 条一级幸存者，说明实现一致性修复实质改变了研究结论；但它仍只是 Prefilter survivor，必须在下一版本重新预注册正式统计阶段，不能直接进入 Demo 或实盘。
