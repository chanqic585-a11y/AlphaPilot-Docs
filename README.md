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
- `architecture/AlphaPilot_Controlled_Execution_Design.md`: Future controlled execution boundary recorded during V13.1.3
- `architecture/AlphaPilot_V13.27_Unified_Workflow_Orchestrator_Design.md`: Unified strategy backtest, local forward, OKX Demo, Live, retry, and concise-console workflow design
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

## Safety boundary

All AlphaPilot documentation must preserve these rules:

- Do not provide buy or sell recommendations
- Do not predict future prices
- Do not promise profit
- Do not save trade-permission API keys
- Do not save withdraw-permission API keys
- Do not place orders automatically
