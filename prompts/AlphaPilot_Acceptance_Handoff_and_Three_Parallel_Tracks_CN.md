# AlphaPilot 独立验收交接要求与验收后三轨并行运行计划
## 交给 Codex 生成验收资料；随后将总 ZIP 与 SHA-256 上传给 ChatGPT

> **用途**
>
> 本文定义：
>
> 1. ChatGPT 如何对 AlphaPilot 当前版本做独立验收；
> 2. Codex 必须输出哪些原始资料；
> 3. 验收通过、部分通过或阻塞时如何判定；
> 4. 验收后如何启动三个互相隔离、可长期并行的运行轨道。
>
> 本次验收不是再次要求 Codex 自己证明自己“已经完成”，而是提供足以让外部审计者从源码、真实调用链、Runtime、数据库和原始测试结果独立复核的资料。

---

# 一、验收原则

## 1.1 可以由 ChatGPT 独立验收

ChatGPT 可以验收：

```text
GitHub 已 Push 的完整源码
精确 Commit / Tag / Branch
源码与 Runtime 身份是否一致
P0/P1 整改是否真正接入调用链
Strategy Factory 是否真实执行 Trial / Formal
GPT/Gemini Router 是否安全、可审计
Matchability / ForwardTask 是否真实工作
Demo / Live UI 是否读取真实 API
SQLite / Lease / Recovery / Kill Switch
测试、静态分析和性能证据
证据包完整性与 Hash
```

ChatGPT 无法直接观察未被打包的：

```text
未 Push 本地修改
进程内存
临时环境变量
未导出的 Runtime 日志
未快照的 SQLite 写入
未记录的人工操作
```

因此，最终验收质量取决于交接包是否完整。

## 1.2 验收不依赖 Codex 的结论

以下内容只能作为线索，不能作为唯一通过依据：

```text
final_closeout_cn.md
“测试通过”一句话
自生成 passed=true
截图
类名或函数名
Schema 已存在
UI 中的状态标签
```

必须使用至少两条独立证据，例如：

```text
源码调用链 + Disconnect Test
Runtime API + SQLite 原子快照
测试结果 + Mutation Test
UI 页面 + 真实 API 响应
Git Commit + Runtime Module Root Hash
```

## 1.3 安全状态

打包与验收期间：

```text
Live=false
Live ARM=false
Live Strategy Orders=0
Withdraw=false
```

当前 Demo 若稳定运行可继续，不因打包停机。

任何需要 Demo Runtime 切换或重新 ARM 的动作必须单独请求人工 Gate。

---

# 二、最终只交付什么

优先只交付：

```text
AlphaPilot_Acceptance_Handoff_<timestamp>.zip
AlphaPilot_Acceptance_Handoff_<timestamp>.zip.sha256
```

若模型二进制导致总包超过 250 MB，允许增加：

```text
AlphaPilot_Acceptance_Model_Artifacts_<timestamp>.zip
```

除这种情况外，不拆成多个零散包。

用户上传给 ChatGPT 时同时粘贴：

```text
Quant branch / commit / tag
Console branch / commit / tag
Docs branch / commit / tag
第四仓库 branch / commit / tag
打包后源码或 Runtime 是否发生变化
当前 Demo / Live / Withdraw 状态
```

---

# 三、验收包顶层结构

```text
00_START_HERE/
01_identity/
02_source_and_diff/
03_runtime/
04_authority_and_security/
05_strategy_factory/
06_ai_orchestration/
07_matchability_forward/
08_factor_model/
09_demo_live_execution/
10_ui/
11_database/
12_tests_quality/
13_performance/
14_known_issues/
15_independent_verification/
16_final/
```

---

# 四、00_START_HERE：审计入口

必须包含：

```text
README_CN.md
package_scope.json
current_mechanical_state.json
current_authoritative_identity.json
repository_role_map.json
evidence_authority_index.json
version_timeline.json
known_issues_summary.json
exclusion_summary.json
```

`README_CN.md` 必须明确：

```text
当前权威版本
当前 Runtime
当前 Demo Release / Risk / ARM
当前 Live 状态
哪些内容是当前权威
哪些是历史证据
哪些是 Fixture
哪些是 not_run
```

证据分类：

```text
authoritative_current
historical_immutable
superseded
failed_attempt
engineering_only
fixture_only
research_only
deprecated
```

---

# 五、01_identity：四仓库和版本身份

必须输出：

```text
repository_snapshot.json
remote_ref_verification.json
tag_map.json
worktree_status.json
recent_commit_graph.txt
git_bundle_verify.json
git_fsck.json
```

每仓库记录：

```text
repositoryName
repositoryRole
runtimeAuthority
remoteUrl
branch
headCommit
upstreamCommit
ahead
behind
worktreeClean
tagsAtHead
dirtyPaths
untrackedPaths
```

若存在未提交内容：

```text
staged.patch
unstaged.patch
untracked_file_manifest.json
```

不得打包凭据、`.env` 或私有数据库原始内容。

---

# 六、02_source_and_diff：源码与变更范围

必须包含：

```text
四仓库精确 Commit 的 git archive 源码快照
source_file_manifest.json
source_language_summary.json
baseline_to_current_diff.patch
changed_file_inventory.json
generated_vs_handwritten_audit.json
large_file_manifest.json
```

`changed_file_inventory.json` 每项记录：

```text
path
repository
changeType
reason
taskId
riskLevel
tests
rollback
```

必须证明：

```text
没有无解释的范围外修改
没有移动历史 Tag
没有 Force Push
```

---

# 七、03_runtime：真实运行身份

必须输出脱敏的：

```text
runtime_identity.json
process_inventory.json
module_load_path.json
runtime_source_parity.json
runtime_lease.json
scheduler_inventory.json
listening_ports.json
last_scan_summary.json
reconciliation_summary.json
```

`runtime_identity.json` 至少包括：

```text
runtimeId
environment
pid
startedAt
repositoryCommit
repositoryTag
moduleRootHash
releaseHash
riskOverlayHash
modelHash
modelPolicyHash
approvalHash
armHash
leaseId
lastHeartbeatAt
lastScanAt
nextScanAt
```

关键验收：

```text
Runtime Commit == 打包源码 Commit
Module Root Hash 一致
每环境 Order Authority Count == 1
未知订单 == 0
未对账部分成交 == 0
```

无法证明时：

```text
runtime_source_parity=unverified
newEntriesAllowed=false
```

---

# 八、04_authority_and_security：P0 和唯一权威

必须输出：

```text
authoritative_object_map.json
duplicate_authority_audit.json
legacy_route_retirement_audit.json
live_admission_call_graph.json
release_approval_arm_matrix.json
runtime_lease_audit.json
http_write_route_matrix.json
csrf_origin_audit.json
credential_scan.json
kill_switch_audit.json
actual_position_risk_audit.json
pit_missing_value_audit.json
```

验收至少证明：

```text
唯一 Live Admission Authority
旧 Release / Approval / ARM 不能绕过
第二 Runtime 无订单权
Loopback 写接口也有 CSRF / Origin / Operator Session
Kill Switch 真正执行撤单、平仓、对账、归零
实际持仓风险以交易所事实为权威
缺失值不会自动写成 0
PIT 时间顺序正确
```

必须附带原始测试：

```text
legacy_bypass_tests
second_writer_tests
csrf_attack_tests
flatten_reconciliation_tests
missing_value_tests
pit_violation_tests
```

---

# 九、05_strategy_factory：真实研究闭环

这是最重要的验收目录之一。

必须包含：

```text
factory_state_machine.json
factory_call_graph.json
worker_inventory.json
queue_snapshot.json
trial_ledger.jsonl
formal_job_ledger.jsonl
worker_heartbeat.jsonl
job_lease_audit.json
checkpoint_recovery_audit.json
dead_letter_queue.jsonl
pilot_campaign_summary.json
pilot_candidate_manifest.json
pilot_trial_manifest.json
pilot_failure_attribution.json
pilot_formal_handoff.json
```

## 9.1 必须运行一个真实 Pilot

边界建议：

```text
maximumFamilies <= 4
maximumCandidates <= 8
maximumConcurrentCampaigns = 1
有限预算
```

必须证明：

```text
真实 Hypothesis
真实 Candidate ID
真实 Definition Hash
真实 Data Snapshot
真实 Trial Job
真实 Backtest Artifact
具体失败归因
Formal-ready 候选创建真实 Formal Job
```

不能出现：

```text
Run = completed
Trial = 0
Formal = 0
且无合法终点
```

0 幸存者是合法结果，但必须有真实 Trial。

## 9.2 失败归因

不得只输出：

```text
数据不足
未通过
等待向前 50
```

必须输出：

```text
failureLayer
reasonCodes
missingField
instrument
timeframe
requiredRows
availableRows
gate
cost/capacity/stability
repairability
prohibitedRepair
nextSingleVariableExperiment
```

---

# 十、06_ai_orchestration：GPT / Gemini 自动路由验收

必须输出：

```text
provider_contracts.json
ai_model_registry.json
routing_policy.json
task_type_matrix.json
ai_task_schema.json
ai_call_ledger_sample.json
dual_review_audit.json
provider_fallback_audit.json
provider_health_snapshot.json
redaction_audit.json
prompt_injection_audit.json
structured_output_validation.json
semantic_validation.json
batch_idempotency_audit.json
cost_budget_audit.json
forbidden_tool_audit.json
```

## 10.1 架构要求

必须证明：

```text
所有 Provider 调用只经过 AIOrchestrationService
业务模块无直接 OpenAI/Gemini SDK 调用
实际 Model ID 只在 AIModelRegistry
```

## 10.2 路由要求

至少验证：

```text
架构/P0：
GPT 主审 + Gemini 独立复核

大文件/PDF/图像：
Gemini 主审 + GPT 复核

代码：
GPT 编码 + Gemini Review

策略假设：
GPT/Gemini 并行独立生成

失败归因：
双模型 Critic

UI 解释：
快速模型

批量历史任务：
Batch

订单/Risk/Approval/ARM：
LLM 禁止
```

## 10.3 双模型分歧

关键分歧必须：

```text
blocked_human_review
```

不得由系统自动选择一方。

## 10.4 安全

必须证明以下内容从未发送：

```text
API Key
Secret
Passphrase
签名 Header
服务器密码
原始私有账户响应
完整未脱敏订单 Payload
```

输出计数：

```text
secretLeakCount=0
restrictedTradingPayloadSentCount=0
forbiddenTradingToolCallCount=0
```

---

# 十一、07_matchability_forward：币种匹配与向前验证

必须输出：

```text
strategy_matchability_by_component.json
matchability_30d.json
matchability_90d.json
condition_rejection_matrix.csv
broad_universe_successor_audit.json
mechanism_diversity_audit.json
frequency_tier_audit.json
forward_task_schema.json
forward_task_snapshot.json
forward_task_action_audit.json
```

每条策略显示：

```text
requestedUniverse
publicUniverse
exchangeTradable
eligibleUniverse
componentCompatible
lookbackReady
dataReady
actuallyEvaluated
rawSignals
identityPassed
cooldownRejected
riskRejected
orderEligible
orders
fills
closedTrades
```

必须区分：

```text
请求 Top200
实际合格 N
策略兼容 M
实际评估 K
```

ForwardTask 必须有：

```text
开始
暂停
恢复
当前进度
有效样本
币种覆盖
市场状态覆盖
成本完整性
Blocker
Next Action
```

---

# 十二、08_factor_model：因子、Qlib 与模型

必须输出：

```text
production_factor_registry.json
factor_available_at_audit.json
real_factor_bench_summary.json
alpha101_audit.json
alpha191_compatibility_audit.json
validated_crypto_factor_subset.json
training_dataset_manifest.json
purged_walk_forward_report.json
qlib_campaign_manifest.json
model_registry.json
model_artifact_manifest.json
model_loader_audit.json
inference_test_vectors.json
demo_live_feature_parity.json
base_vs_model_comparison.json
drift_monitor_audit.json
rollback_audit.json
```

不要求每条策略强制使用模型。

合法结果：

```text
rule_only
observer
rank_only
veto_only
meta_label
```

模型只有证明：

```text
Base + Model
优于
Base Rule Strategy
```

才可获得决策权。

---

# 十三、09_demo_live_execution：执行路径

必须输出：

```text
demo_execution_call_graph.json
live_execution_call_graph.json
order_lifecycle_trace_sample.json
idempotency_audit.json
restart_recovery_audit.json
unknown_order_audit.json
orphan_order_position_audit.json
protection_order_audit.json
execution_environment_isolation.json
```

验收结束时，在没有后续人工 Gate 的情况下：

```text
Live=false
Live ARM=false
Live Strategy Orders=0
Withdraw=false
```

Demo 是否 ARM 取决于当前版本是否完成新的 Runtime 切换和人工 Gate。

---

# 十四、10_ui：真实 UI 验收

必须输出：

```text
ui_data_source_matrix.json
ui_api_contract_map.json
strategy_factory_desktop.png
strategy_factory_mobile_390.png
demo_desktop.png
demo_mobile_390.png
live_desktop.png
live_mobile_390.png
strategy_detail_drawer.png
failure_analysis_drawer.png
forward_task_panel.png
ai_control_panel.png
order_lifecycle_trace.png
ui_browser_test_results.json
```

每个字段必须标记：

```text
real_runtime
real_exchange_readonly
ledger_projection
fixture
mock
static_text
not_available
```

Production 页面必须：

```text
fixtureData=false
```

要求：

```text
无横向溢出
无浏览器错误
错误时可操作
正常状态不显示长篇解释
所有当前风险/资金/TopN 来自 Config Store
```

---

# 十五、11_database：数据库验收

必须输出：

```text
database_inventory.json
schema_only.sql
pragma_audit.json
migration_inventory.json
foreign_key_audit.json
integrity_check.json
table_counts.json
max_sequence_ids.json
append_only_audit.json
online_backup_receipt.json
backup_restore_test.json
```

不得输出凭据或不必要的私有账户行。

---

# 十六、12_tests_quality：测试与代码质量

必须包含原始日志，不只含摘要：

```text
test_command_ledger.jsonl
raw_test_logs/
test_results_summary.json
skipped_xfailed_inventory.json
coverage.xml
coverage_summary.json
ruff.json
type_check.json
bandit_semgrep.json
dead_code_scan.json
complexity_hotspots.json
dependency_vulnerability_scan.json
npm_audit.json
playwright_results.json
mutation_test_results.json
disconnect_test_results.json
```

推荐门槛：

```text
关键交易/准入/风险模块 Branch Coverage >= 85%
全仓 Line Coverage >= 70%
```

若项目暂时无法达到，必须列出真实缺口和批准的例外，不能伪造通过。

---

# 十七、13_performance：性能验收

必须分段输出 P50/P95/P99/Max/SampleCount：

```text
barCloseToEvent
eventToScanStart
scanDuration
featureBuild
modelInference
riskDecision
signalToOrderSend
exchangeAck
```

同时输出：

```text
research_worker_resource_usage
ai_task_latency_and_cost
sqlite_contention
runtime_cpu_memory
```

LLM 不得出现在订单热路径性能图中。

---

# 十八、14_known_issues：已知问题

```text
open_issue_ledger.json
not_run_matrix.json
approved_exceptions.json
manual_assumptions.json
open_questions_for_acceptance.md
```

每项：

```text
issueId
severity
status
affectedFiles
runtimeImpact
liveBlocking
workaround
nextAction
```

---

# 十九、15_independent_verification：独立验证

必须包含与主 Evidence Builder 独立的：

```text
verify_acceptance_package.py
verify_runtime_identity.py
verify_trial_ledger.py
verify_ai_router.py
verify_ui_data_sources.py
verify_sqlite_snapshots.py
verify_hashes.py
```

独立验证输出：

```text
independent_verification_result.json
```

必须报告：

```text
missing
extra
hashMismatch
invalidJson
credentialHits
runtimeIdentityMismatch
trialCountMismatch
formalCountMismatch
fixtureInProductionUi
forbiddenLlmToolCalls
```

---

# 二十、16_final：最终文件

```text
artifact_manifest.json
package_hash_verification.json
credential_scan.json
final_self_check.json
final_self_check.md
final_closeout_cn.md
```

Artifact Manifest 每项：

```text
relativePath
sha256
sizeBytes
sourceRepository
sourceCommit
classification
```

ZIP 反解验证：

```text
missing=0
extra=0
hashMismatch=0
invalidJson=0
credentialLeak=0
```

---

# 二十一、ChatGPT 的验收结论类型

收到资料后，ChatGPT 将给出以下之一：

## A. accepted

```text
P0 全部通过
真实 Trial 闭环通过
AI Router 安全通过
Runtime 身份通过
证据完整
```

## B. accepted_with_nonblocking_p2

```text
核心安全和闭环通过
只剩非阻塞 UI/文档/性能 P2
```

## C. blocked_remediation_required

```text
存在 P0/P1
或真实 Trial/Runtime/AI Router 未接通
```

## D. rejected_evidence_incomplete

```text
资料不足，无法独立判断
```

ChatGPT 会输出：

```text
验收报告
逐项通过/失败矩阵
Confirmed Issues
False Positives
风险排序
下一步唯一提示词或三轨启动计划
```

---

# 二十二、验收后：三个并行运行轨道

三个轨道不是三个互相修改同一 Runtime 的开发分支，而是三个职责隔离的长期运行体系。

---

# 轨道 A：Demo 运营与真实前向证据

## 目标

```text
让已批准策略 24×7 在 Demo 中稳定运行，
持续产生扫描、信号、订单、闭合交易、ForwardTask 和学习样本。
```

## 输入

```text
已批准 Demo Release
Risk Overlay
Runtime Identity
Demo ARM
唯一 Runtime Lease
```

## 主要任务

```text
1. Public / Private Market Runtime；
2. 闭合 K 线调度；
3. TOP N / 实际合格 Universe；
4. 逐策略 Matchability；
5. 风险、订单、持仓、退出；
6. 对账与恢复；
7. ForwardTask 自动更新；
8. Feature / Factor / Observer 样本；
9. 账户权益、PnL、订单和持仓 UI；
10. Headless Service 与告警。
```

## 关键指标

```text
missedScanCount
duplicateScanCount
unknownOrderCount
orphanOrderCount
orphanPositionCount
unprotectedPositionCount
signalToOrderSendP95
closedTradeCount
effectiveSampleSize
symbolCoverage
regimeCoverage
costCompleteness
```

## 边界

```text
不因信号少原地修改策略
不自动进入 Live
不让 Research Worker 取得订单租约
```

## 人工门

仅在 Runtime 版本切换时：

```text
APPROVE DEMO RUNTIME ARM
```

---

# 轨道 B：AI 有界策略研究与 Experimental Demo 候选

## 目标

```text
用真实 Trial、失败记忆和 GPT/Gemini 协作，
持续研究独立市场机制，提高研究信息效率和策略组合多样性。
```

## 输入

```text
Negative Research Memory
数据能力 Manifest
Factor Registry
机制覆盖图
频率覆盖图
研究预算
AI Routing Policy
```

## 主要任务

```text
1. GPT/Gemini 并行生成独立 Hypothesis；
2. 家族指纹、父子链和信号相关性去重；
3. 真实 Candidate / Definition Hash；
4. 有界 Trial；
5. 成本、容量和稳定性；
6. Purged Walk-forward / Formal；
7. 双模型失败归因；
8. 单变量下一实验；
9. 自动归档失败；
10. 为合格候选生成 Experimental Demo Release Draft。
```

## 研究重点

```text
Broad-Universe Successor
15m / 1h 中频策略
趋势回调/恢复
波动扩张
Funding/Basis
横截面相对强弱
冲击恢复
压缩释放
```

## 关键指标

```text
realTrialCount
formalJobCount
familyNovelty
duplicateRejectionCount
specificFailureAttributionRate
dataReadyCandidateRate
costSurvivorRate
formalSurvivorRate
estimatedEvidenceCollectionRate
researchCost
```

## 合法结果

```text
completed_zero_qualified_candidates
```

不属于工程失败。

## 边界

```text
AI 不修改 Gate
AI 不读 Locked OOS 调参
AI 不批准/ARM
自动化最多到受限 Experimental Demo
```

## 人工门

Pilot 验收后：

```text
APPROVE AUTO DEMO POLICY <policyHash>
```

只有用户批准 Policy 后，合格候选才可自动进入受限 Experimental Demo。

---

# 轨道 C：平台硬化、因子/模型与 Live 准备

## 目标

```text
持续提升系统可靠性、可解释性、服务器化和 Live Readiness，
但不干扰 Demo 订单 Runtime。
```

## 主要任务

```text
1. P1/P2 代码整理与旧路径退役；
2. SQLite Migration、Backup、Recovery；
3. UI 真实 API 与渐进披露；
4. 服务器只读 Shadow；
5. Runtime Authority Cutover 工具；
6. 真实 PIT 因子；
7. Qlib / 数值模型；
8. Demo Observer / Rank / Veto；
9. Drift / Rollback；
10. Live Read-only 与最终 Live Candidate。
```

## 模型路线

```text
PIT Factor Panel
→ Qlib / Offline Model
→ Demo Observer
→ Base vs Base+Model
→ Rank/Veto（仅有增量价值）
→ Model-bound Release
```

模型无增量价值时：

```text
rule_only 或 observer
```

是合法结果。

## 关键指标

```text
technicalReadiness
runtimeSourceParity
databaseIntegrity
backupRestore
branchCoverage
staticAnalysis
serverShadowParity
modelLift
driftObservationCount
rollbackRehearsal
liveReadiness
```

## 边界

```text
不取得当前 Demo 订单租约
不自动批准 Live
不自动 ARM Live
Withdraw 始终关闭
```

## 人工门

```text
APPROVE RUNTIME AUTHORITY CUTOVER
APPROVE LIVE RELEASE
APPROVE LIVE ARM
```

---

# 二十三、三个轨道的依赖关系

```text
验收通过
│
├── 轨道 A：立即恢复/持续 Demo
│
├── 轨道 B：Pilot 通过后持续有界研究
│
└── 轨道 C：后台硬化、模型和服务器
```

Live 的前置依赖：

```text
轨道 A：
Demo 执行与对账可信

轨道 B：
至少有合格策略或已批准的规则策略身份

轨道 C：
Live 技术 Readiness、风险、Kill Switch、服务器和模型治理完成
```

只有三轨所需条件同时满足，才生成新的 Live Release。

---

# 二十四、Codex 最终回复模板

```text
AlphaPilot Acceptance Handoff 已完成

Repositories:
- Quant:
- Console:
- Docs:
- Fourth repo:

Runtime:
- Commit / Tag:
- Release / Risk / Model:
- Approval / ARM / Lease:
- Demo orders / positions:
- Live / Withdraw:

P0:
- Runtime identity:
- Unique Live authority:
- CSRF / LAN:
- Kill Switch:
- Actual position risk:
- PIT / missing values:

Strategy Factory:
- Pilot:
- Families / candidates:
- Real trials:
- Formal jobs:
- Dead letters:
- Failure attribution:
- Zero-survivor route:

AI Orchestration:
- Providers:
- Model Registry:
- Routing:
- Dual review:
- Redaction:
- Prompt injection:
- Forbidden tools:
- Batch / budget:

Matchability / Forward:
- Control:
- Requested / eligible / compatible / evaluated:
- 30d / 90d:
- ForwardTask:

Factor / Model:
- Factor panel:
- Qlib:
- Model artifacts:
- Base vs Model:
- Drift / rollback:

UI:
- Strategy:
- Demo:
- Live:
- fixtureData:
- mobile:

Database:
- Integrity:
- Online backups:
- Restore:

Quality:
- Tests:
- Coverage:
- Static analysis:
- Mutation:
- Disconnect:
- E2E:

Package:
- ZIP:
- SHA-256:
- members:
- missing:
- extra:
- hash mismatch:
- credential hits:

Current route:
acceptance_handoff_ready
```
