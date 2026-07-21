# AlphaPilot Docs

AlphaPilot Docs stores the roadmap, version prompts, schemas, skills,
and plugin notes for the AlphaPilot product line.

AlphaPilot is a local-first trading discipline and AI copilot project.

It does not provide investment advice.
It does not connect trading permissions.
It does not save trade or withdraw API keys.
It does not place orders automatically.

## Repository structure

- `docs/`: Product roadmap, architecture notes, and design documents
- `prompts/`: Versioned prompts for implementation and product iterations
- `schemas/`: Local data schemas and future export formats
- `skills/`: Reusable Codex skills or operating instructions
- `plugins/`: Plugin specifications and integration notes

## Key documents

- `docs/00-roadmap.md`: Main AlphaPilot roadmap
- `docs/AlphaPilot_软件设计文档_阶段提示词.md`: Original design document and phased prompts
- `roadmap/AlphaPilot_Live_Quant_Roadmap_V2.md`: Live quant system roadmap recorded during V13.1.3
- `roadmap/AlphaPilot_V13.27_Unified_Workflow_Orchestrator_Implementation_Plan.md`: Phased implementation plan for workflow foundation, backtest, local forward, Demo, and Live
- `roadmap/AlphaPilot_V13.27.1_Strategy_Backtest_Workflow_Result.md`: Completed V13.27.1 backtest worker, Strategy-page workflow, verification, commits, and safety boundary
- `roadmap/AlphaPilot_V13.27.1.1_Dual_Data_Warehouse_One_Click_Backtest_Implementation_Plan.md`: Task-by-task TDD plan for dual data storage, official OKX preparation, formal fixed-2R backtesting, and automatic Local Forward entry
- `roadmap/AlphaPilot_V13.27_Unified_Workflow_Orchestrator_Implementation_Plan.md`: Includes the verified V13.27.1.1 completion note: 14-asset research smoke, bounded BTC/ETH/SOL official-data snapshot, 207 Quant tests, 76 Console tests, and no automatic Demo or Live transition
- `architecture/AlphaPilot_Controlled_Execution_Design.md`: Future controlled execution boundary recorded during V13.1.3
- `architecture/AlphaPilot_V13.27_Unified_Workflow_Orchestrator_Design.md`: Unified strategy backtest, local forward, OKX Demo, Live, retry, and concise-console workflow design
- `architecture/AlphaPilot_V13.27.1.1_Dual_Data_Warehouse_One_Click_Backtest_Design.md`: Approved dual data warehouse, timeframe-aware one-click backtest, official validation pack, and automatic Local Forward entry design
- `architecture/AlphaPilot_Strategy_Generation_Core_Principles.md`: Core hypothesis, evidence, risk, ML, overfitting, immutable-release, and failure-preservation rules for future strategy generation
- `prompts/V1.1-fix-current-app.md`: Current app stabilization prompt
- `prompts/V2-ai-trading-copilot.md`: AI Trading Copilot prompt
- `prompts/V3-feature-database.md`: Feature Database prompt
- `prompts/AlphaPilot_V13.4.32_Low_Frequency_Data_Baseline_Codex_Prompt.md`: Low-frequency data preparation and baseline builder prompt
- `prompts/AlphaPilot_V13.4.33_Low_Frequency_Candidate_Spec_Codex_Prompt.md`: Low-frequency candidate specifications and baseline hurdles prompt
- `prompts/AlphaPilot_V13.4.34_Low_Frequency_Directional_Strategy_Codex_Prompt.md`: Low-frequency directional 4h research strategy prompt
- `prompts/AlphaPilot_V13.4.35_Multi_Strategy_Batch_Backtest_Codex_Prompt.md`: Multi-strategy batch research backtest prompt
- `prompts/AlphaPilot_V13.7.44_Simulation_Review_Queue_Codex_Prompt.md`: Local sandbox simulation review queue and strategy promotion suggestion prompt
- `prompts/AlphaPilot_V13.7.45_Closed_Sample_Replay_Codex_Prompt.md`: Closed sample detail and local replay view prompt
- `prompts/AlphaPilot_V13.7.46_Sandbox_Sample_Path_Instrumentation_Codex_Prompt.md`: Local sandbox estimated path instrumentation prompt
- `prompts/AlphaPilot_V13.7.47_Replay_Scoring_Weakness_Labels_Codex_Prompt.md`: Replay scoring and weakness labels prompt
- `prompts/AlphaPilot_V13.7.48_Strategy_Weakness_Action_Board_Codex_Prompt.md`: Strategy weakness action board prompt
- `prompts/AlphaPilot_V13.7.49_Research_Action_Tracker_Codex_Prompt.md`: Research action tracker prompt
- `prompts/AlphaPilot_V13.8_Research_Execution_Pipeline_Codex_Prompt.md`: Research execution pipeline, candidate promotion gate, simulation command center, and testnet readiness checklist prompt
- `prompts/AlphaPilot_V13.8.1_Testnet_Readiness_UI_Boundary_Codex_Prompt.md`: Disabled Testnet readiness UI boundary prompt
- `prompts/AlphaPilot_V13.8.2_Pre_Live_Preparation_Pack_Codex_Prompt.md`: Local pre-live lifecycle rehearsal, risk limits, kill-switch design, and credential-boundary prompt
- `prompts/AlphaPilot_V13.8.3_Pre_Live_Operational_Closure_Codex_Prompt.md`: Local pre-live rehearsal persistence, closure checks, and operational runbook prompt
- `prompts/AlphaPilot_V13.16_Auditable_Market_Data_Foundation_Codex_Prompt.md`: Read-only raw catalog, canonical public-market data, resumable checksums, point-in-time snapshots, and provenance gate prompt
- `prompts/AlphaPilot_V13.17_Point_in_Time_FactorRun_Backtest_Codex_Prompt.md`: Point-in-time FactorRuns, fixed 2R labels, purged walk-forward, locked test, and formal promotion gate prompt
- `prompts/AlphaPilot_V13.18_Actual_Candle_Historical_Path_Replay_Codex_Prompt.md`: Next-bar actual-candle replay, 2R exits, immutable Outcome Ledger, and legacy synthetic evidence boundary prompt
- `prompts/AlphaPilot_V13.19_Real_Time_Local_Forward_Observation_Codex_Prompt.md`: Public-market real-time forward observation, 1000 USDT virtual accounts, restart checkpoints, and collection-gap integrity prompt
- `prompts/AlphaPilot_V13.20_Immutable_Release_Gated_OKX_Demo_Codex_Prompt.md`: Formal promotion gates, immutable OKX Demo releases, internal release-bound scanning, reconciliation, and drift pause prompt
- `prompts/AlphaPilot_V13.21_Live_Safety_Candidate_Boundary_Codex_Prompt.md`: Checksum-bound Live review candidates and a fail-closed, non-executing safety plane prompt
- `prompts/AlphaPilot_V13.22_Offline_Evolution_Feedback_Loop_Codex_Prompt.md`: Evidence-classed offline feedback, failure attribution, bounded shadow factor evolution, and immutable release lineage prompt
- `prompts/AlphaPilot_V13.24_Versioned_Risk_Profile_Codex_Prompt.md`: Immutable configurable risk profiles, portfolio concentration limits, activation audit, and rollback prompt
- `prompts/AlphaPilot_V13.25_Fail_Closed_OKX_Live_Canary_Codex_Prompt.md`: Default-off OKX Live Canary adapter, immutable release, reconciliation, protection, and kill-switch prompt
- `prompts/AlphaPilot_V13.26_Formal_Execution_Outcome_Feedback_Codex_Prompt.md`: Closed Demo/Live outcome reconciliation, checksum export, lineage validation, quarantine, and offline-only feedback prompt
- `prompts/AlphaPilot_V13.27.7_Demo_Canonical_Instrument_Fix_Codex_Prompt.md`: Canonical OKX swap-ID fix and invalid zero-match rescan boundary
- `prompts/AlphaPilot_V13.27.8_Shared_Official_Market_Data_Codex_Prompt.md`: Cross-strategy verified official OHLCV reuse and missing-tail collection boundary
- `prompts/AlphaPilot_Full_Archived_Strategy_Evidence_Analysis_Codex_Prompt_CN.md`: Full archived strategy identity, evidence, trade-level metric, and failure-attribution analysis prompt
- `prompts/AlphaPilot_Candidate_Evidence_Closure_Locked_Validation_Codex_Prompt_CN.md`: Pre-registered candidate deduplication, locked-sample validation, primary 0.25% risk model, cost stress, Monte Carlo, and continue-archive decision prompt
- `prompts/AlphaPilot_V13.27.1.11_Revised_V2_Hierarchical_Gates_Causal_Data_Clean_Holdout_Codex_Prompt_CN.md`: Canonical V13.27.1.11 V2 prompt for hierarchical event/portfolio gates, causal data clocks, same-exchange derivatives evidence, and a one-shot clean holdout.
- `prompts/AlphaPilot_V13.27.1.12_Revised_Formal_Data_Coverage_PIT_Qlib_Readiness_Codex_Prompt_CN.md`: Formal same-exchange derivatives coverage, historical PIT universe, immutable data snapshot, bounded public collection, and pinned Qlib-readiness prompt.
- `prompts/AlphaPilot_V13.27.1.13_Minimal_Data_Layer_Storage_Governance_Bounded_Strategy_Funnel_Codex_Prompt_CN.md`: Exact-root storage governance, reusable minimal data manifests, bounded preregistered hypotheses, and fail-closed prefilter-to-formal routing prompt.
- `prompts/AlphaPilot_V13.27.1.25-26_Capacity_Data_Semantics_and_Frozen_Candidate_Replay_Codex_Prompt_CN.md`: Capacity-data semantics, verified turnover provenance, frozen-candidate capacity certification, and fail-closed V26 replay admission.
- `research/archived-failed-strategy-analysis.md`: Archived failed-strategy inventory, null-preserving metrics, failure attribution, negative rules, and revival policy

## V13.27.1.11 closeout

The V2 research-factory repair is implemented in the Quant repository and closed as `data_not_ready`, not as an engineering failure. Public-source capability checks and local artifact manifests passed, but zero of the two required top-level directions had complete same-exchange historical derivatives and point-in-time universe evidence. The formal campaign, preregistration, clean holdout, Console import, Demo ARM, and order paths therefore remained closed as designed.

## V13.27.1.12 closeout

The formal data-coverage and PIT/Qlib-readiness layer is implemented in the
Quant repository. The explicit public-only audit reused eight existing Binance
funding partitions and verified four OKX public endpoints, but still found zero
of the required two formal-ready top-level directions. Historical PIT evidence
and complete same-exchange data chains remain unavailable, so the strategy
campaign, Qlib campaign, clean holdout, Release, Demo ARM, and order paths all
remained closed. The result is an auditable `data_not_ready` decision with no
threshold relaxation and no trading capability added.

## V13.27.1.13 closeout

V13.27.1.13 is implemented in the Quant repository. Storage governance was
strictly bounded to `D:\Codex-Workspace\回测数据`; the final dry-run and explicit
apply audit deleted zero files because no candidate satisfied every deletion
rule. The minimal data layer reuses existing local evidence through a
manifest-only snapshot for a fixed 20-instrument core universe, shared 1h/4h
cutoffs, and an append-only forward collection plan.

The bounded campaign preregistered three hypotheses and opened only the first
55% development segment for prefiltering. One hypothesis was rejected as
non-novel, one remained diagnostic-only because PIT/1d evidence was
insufficient, and the event-driven hypothesis failed profitability,
month-consistency, and drawdown gates after 644 events. No hypothesis survived
to formal validation, so locked OOS was not opened and no Release, Demo ARM,
order, private exchange API, Trade API, or Withdraw API was created or used.

## Safety boundary

All AlphaPilot documentation must preserve these rules:

- Do not provide buy or sell recommendations
- Do not predict future prices
- Do not promise profit
- Do not save trade-permission API keys
- Do not save withdraw-permission API keys
- Do not place orders automatically

## V13.27.1.19-24 Automatic Strategy-to-Demo closeout

The bounded V19-V24 workflow is complete. It produced one formal candidate,
which was classified as `capital_infeasible` under the frozen capacity policy,
so the correct terminal route is `completed_zero_qualified_candidates` with
zero Release, zero approval, Demo not armed, and zero orders. See
`docs/AlphaPilot_V13.27.1.19-24_Automatic_Strategy_to_Demo_Execution_Summary_CN.md`
for the stage results, evidence identities, test results, and evidence-package
locations.

## V13.27.1.25 Capacity semantics closeout

V25 preserves the completed V19-V24 result and adds a clarification sidecar:
the previous zero-trade route was a capacity-data contract failure, not valid
strategy-performance evidence. All 24 required local datasets have verified
direct quote-turnover semantics, and real-signal capacity certification passes
for the frozen candidate without reading economic results.

V26 correctly remains unstarted because two mandatory frozen ranking-feature
definitions are unresolved. No Formal claim or budget was consumed, and no
Release, approval, Demo ARM, Live path, or order was created. A V26 tag must not
be created for this blocked route.

The paragraph above records the historical V25 checkpoint. It is superseded by
the completed V26 and V27 work below.

## V13.27.1.26-27 Automatic Strategy-to-Demo closeout

V26 resolved the two frozen ranking-feature definitions without mutating the
V25 candidate evidence. V27 then evaluated four hypothesis families and eight
bounded candidates. Eight passed structural and ranking checks, three passed
strict capacity certification, and zero passed the economic prefilter.

The legal terminal route is `completed_zero_qualified_candidates`. V28-V31
were not reached: no Formal result was read, no Locked OOS evidence was opened,
no immutable Release was generated, and no Demo approval, ARM, or order was
created. See
`docs/AlphaPilot_V13.27.1.27_Zero_Survivor_Closeout_CN.md` for the complete
counts, evidence identities, package hashes, verification, and stage ruling.

## V13.27.1.28-32 Research Renewal closeout

The authoritative prompt is
`prompts/AlphaPilot_V13.27.1.28-32_Research_Renewal_to_OKX_Demo_Codex_Prompt_CN.md`.
The candidate-neutral V28-V32 contracts and bounded orchestration are
implemented in the Quant repository. The frozen Program
`automatic_strategy_renewal_v28_4e6ab55a5e949716` closed as
`blocked_formal_data`: 24 OHLCV datasets exist, but none has both verified
exchange provenance and point-in-time semantics. No candidate identity or
formal result was created, all 96 backtests remain available, and Release,
approval, Demo ARM, and order counts remain zero.

The workflow now supports OKX same-exchange evidence or frozen portability
audits and immutable exact-hash Release requests, but these paths were not
entered. Resume only with a new immutable verified-PIT OHLCV catalog; never
rewrite this result or revive V27 candidates.

## V13.27.1.14 Advisory-R Exit Policy

The authoritative implementation prompt is
`prompts/AlphaPilot_V13.27.1.14_Advisory_R_Exit_Policy_Implementation.md`.
V13.27.1.14 adds a versioned Advisory-R exit-policy contract, four bounded exit
modes, causal simulator/report integration, and Console v1/v2 Demo import
compatibility. It does not run a strategy campaign, open holdout evidence,
create or approve a release, ARM Demo, place an order, or change Live gates.

## V13.27.1.15 Advisory-R Strategy Prefilter

The authoritative prompt is
`prompts/AlphaPilot_V13.27.1.15_Strategy_Generation_and_Prefilter.md`.
V13.27.1.15 froze ten candidates across eight mechanism families, assigned one
primary exit policy to each candidate, and ran a causal representative-universe
prefilter with target R recorded as advisory rather than used as an admission
gate. The result contained zero formal survivors, which is a valid hard stop.
No holdout was opened, no release was generated, Demo remained unarmed, no
order was created, and V13.27.1.16 through V13.27.1.18 were not executed.

## V13.27.1.34C Public Data Scheduler

The authoritative prompt is
`prompts/AlphaPilot_V13.27.1.34C_Public_Data_Scheduler.md`.
V34C adds a durable, hash-audited foreground scheduler around the V34B public
OKX collectors. Its two-cycle pilot proved due-task selection, incremental
funding resume behavior, quality reporting, cycle chaining, and preservation of
all V34A/V34B artifacts. It remained data-only: candidate, Formal, result-read,
Locked-OOS, Release, approval, Demo ARM, and order counts all stayed zero.

## V13.27.1.35 Standard Replication Background Research

The authoritative implementation record is
`prompts/AlphaPilot_V13.27.1.35_Standard_Replication_Background_Research.md`.
V35 adds a bounded, restartable background research service for six registered
strategy families. It freezes nine candidates, marks two families as data
blocked, and stops at `ready_for_prefilter`. The execution track remains
isolated: no private account read, credential storage, Trade API, Withdraw API,
Release approval, Demo ARM or order side effect is introduced.

## V37F-V40 Vibe Selective Integration

The authoritative workflow is
`prompts/AlphaPilot_V37F-V40_Vibe_Selective_Integration_Master_Workflow_Codex_Prompt_CN.md`.
The source analysis, adoption map, package manifest and executed closeout are in
`docs/v37f-v40-vibe-selective-integration/`.

The workflow selectively adapts research-only ideas from the pinned MIT-licensed
`HKUDS/Vibe-Trading` source. It does not merge that repository, replace
AlphaPilot gates, or import its broker/live connectors. V37F-V37J completed with
zero Formal candidates and zero releases. V38 completed the read-only Strategy
Lab and Demo engineering core; V39 was not run because no immutable release was
eligible, and V40 remains disabled.

## V13.27.1.41-V13.27.1.45 Mechanism Breakthrough and Demo Smoke

The authoritative workflow is
`prompts/AlphaPilot_V13.27.1.41-45_Mechanism_Breakthrough_and_Real_OKX_Demo_Smoke_Codex_Prompt_CN.md`.
Track R completed two bounded campaigns with four immutable candidates and no
prefilter survivor. Formal, Locked OOS, Release, Demo strategy execution, Live,
and Withdraw all remained unused. Track P implemented the exact-hash engineering
smoke contract boundary, but the real private Demo smoke was not run because
process-only credentials were not injected into the execution process. The
delivery therefore reports a blocked product track instead of invented network,
order, fill, position, or reconciliation evidence.

## V13.27.1.61 Global Remediation

The implementation plan is
`docs/superpowers/plans/2026-07-22-global-remediation-continuous-demo-autonomous-research-ui-v2.md`.
The program re-audits the latest frozen Quant, Console, and Docs heads before
changing code, protects the existing Demo runtime from hot edits, and adds
single-runtime authority, PIT, risk-unit, HTTP-write, SQLite, autonomous
research, Shadow Parity, and minimal Demo/Live preview controls. Adaptive
Learning remains truthfully blocked until its real model evidence closes; Live
and Withdraw remain disabled.

## V13.27.1.62 Control Plane and Strategy Factory

The authoritative implementation record is
`prompts/AlphaPilot_V13.27.1.62_Control_Plane_Runtime_Truth_Strategy_Factory_CN.md`.
V62 adds ledger-backed Strategy, Demo, and Live projections, immutable
versioned strategy execution policies, an explicit safe first-policy bootstrap,
bounded autonomous research outcomes, and concise desktop/mobile control
surfaces. It does not hot-patch the currently armed Demo process, approve or
ARM a Release, create an order, enable Live, or add Withdraw capability.
