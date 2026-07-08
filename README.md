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

## Safety boundary

All AlphaPilot documentation must preserve these rules:

- Do not provide buy or sell recommendations
- Do not predict future prices
- Do not promise profit
- Do not save trade-permission API keys
- Do not save withdraw-permission API keys
- Do not place orders automatically
