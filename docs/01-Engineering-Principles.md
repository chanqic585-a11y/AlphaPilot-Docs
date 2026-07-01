# AlphaPilot Engineering Principles (AEP) v1.0

AlphaPilot Engineering Principles, abbreviated as AEP, defines the highest-level engineering rules for AlphaPilot. Every future prompt, architecture document, database schema, feature, skill, plugin, AI module, service, and code change must follow this document before it follows any lower-level implementation request.

This document treats AlphaPilot as an AI Trading Research Platform. It sets the boundary between research and execution, data and opinion, risk and signal, mobile experience and server computation, automation and human accountability.

## 1 Mission

AlphaPilot exists to help a trader build a reliable research system around real trading behavior, market context, risk exposure, and execution quality. The product does not exist to predict the market. It does not exist to replace judgment. It does not exist to turn every market event into a trade.

The long-term mission is to create a personal trading research platform that can collect live trading data, transform every trade into structured features, compare outcomes across repeatable market conditions, and identify which patterns are actually supported by evidence. AlphaPilot should help answer a disciplined research question: under which conditions does this trader make better decisions, and under which conditions does this trader repeatedly lose discipline, risk control, or execution quality.

AlphaPilot is not a trading robot. It is not a recommendation engine. It is not a price prediction tool. It is not a product that promises returns, implies certainty, or encourages larger risk. It is an AI Trading Research Platform that uses mobile capture, local data, market features, rule-based analysis, AI explanation, and later research infrastructure to turn trading behavior into auditable evidence.

The project begins with manual trading records because manual records expose the most important variables: intention, risk, context, timing, behavior, and review. It can later collect read-only exchange data because real fills, real position changes, and real exit behavior matter more than simulated intent. It can later support research, signals, paper trading, small live experiments, semi-automatic execution, and automatic execution design. Those stages must remain separated.

AlphaPilot’s mission is not to increase trading frequency. Its mission is to increase decision quality. If a feature increases activity but reduces evidence quality, the feature does not belong in the product. If a feature makes trading feel urgent, the feature must be redesigned. If a feature hides risk behind a strong signal score, the feature violates AEP.

Every product decision must preserve the platform’s central purpose: collect truthful trading data, explain the conditions around each decision, measure outcomes, protect capital through risk rules, and improve the trader’s process through evidence.

## 2 Core Philosophy

AlphaPilot follows a fixed philosophy that must shape product design, system architecture, AI behavior, data modeling, and release sequencing. These principles define the order of work.

### Research before automation

Automation cannot be the starting point. A system that automates before it understands its own data only accelerates mistakes. AlphaPilot must first collect trades, generate features, review outcomes, and evaluate repeatability.

Execution automation may appear only after research shows a measurable pattern, a risk model limits downside, and audit logs can reconstruct each decision. Any proposal that starts with auto trading before research violates this principle.

### Data before AI

AI must analyze evidence. It must not fill missing evidence with confidence. AlphaPilot should collect trades, market snapshots, indicators, order context, review labels, and execution outcomes before it asks AI to summarize patterns.

When data is missing, the system must say that data is missing. It must not replace missing features with fluent language. AI output has value only when the underlying data can be inspected, queried, exported, and reproduced.

### Risk above signal

Risk Engine decisions have higher priority than signal quality, AI confidence, setup type, market strength, or historical win rate. A strong signal can still be rejected by risk. A high-confidence setup can still be blocked by exposure, drawdown, daily loss, consecutive losses, missing stop loss, or unstable market conditions.

AlphaPilot must never allow a signal module to override risk. The system must treat risk as a control layer, not as a UI warning added after analysis.

### Evidence before opinion

AlphaPilot should prefer measured evidence over trader memory, model narration, and subjective confidence. Every claim about user behavior, setup quality, pattern performance, or market condition must point back to stored data.

If the sample size is too small, the system must mark the claim as weak. If the data is contradictory, the system must show the contradiction. If the evidence cannot support a conclusion, the system must avoid the conclusion.

### Explainability first

Every analysis must be explainable. A trader must be able to inspect why a setup was classified, why a risk warning appeared, why a trade review labeled a loss as disciplined or undisciplined, and why a signal was rejected.

Explainability is not optional decoration. It is part of the product’s safety model. AlphaPilot must prefer transparent rules, inspectable features, and auditable AI output over opaque scores that cannot be challenged.

### Mobile first experience

The mobile app is the primary human interface. It must support fast capture, calm review, clear risk display, and low-friction correction. Trading contexts can move quickly, so the mobile experience must reduce typing and avoid distracting screens.

Mobile first does not mean computation belongs on the phone. The app should present information, capture intent, show warnings, and support review. It should not become a heavy indicator engine, backtesting environment, machine learning runtime, or long-running market collector.

### Server first computation

Complex computation belongs in engines and services. Indicator generation, feature engineering, market collection, research, backtesting, machine learning, signal scanning, risk evaluation, and execution logic should move out of the mobile app as the platform matures.

The long-term architecture must use the mobile app as a controlled interface, Python services as research and feature engines, and isolated backend services for collection, signal, risk, and execution. This separation allows testing, reproducibility, observability, and future replacement of each layer.

## 3 Engineering Principles

AlphaPilot engineering must follow explicit rules. These rules apply to code, database schemas, prompts, architecture diagrams, plugins, skills, AI providers, API design, and release planning.

1. **Data first**: Every meaningful product feature must create, preserve, enrich, validate, or explain data. Features that only decorate the interface without improving evidence quality have lower priority.

2. **Feature first**: Every trade must become structured features. Raw text notes are useful, but structured fields, indicators, labels, timestamps, and outcome metrics create the long-term research asset.

3. **Risk first**: Any workflow that touches position size, leverage, alerts, signals, or execution must pass through risk rules. Risk must be modeled before signal ranking.

4. **Plugin first**: Analysis capabilities should move toward plugin boundaries. A future pattern detector, market classifier, indicator pack, or exchange connector should be replaceable without rewriting the core product.

5. **Skill first**: Trading methods should become Skills. A Skill defines required features, matching rules, confidence logic, warnings, and explanations. It must not directly place orders.

6. **Everything is research**: Every trade, alert, review, signal, and rejected setup must be treated as research data. A non-trade observation can still improve future analysis if it is stored correctly.

7. **Everything can be measured**: The system must prefer measurable states over vague labels. Discipline, risk, setup confidence, feature quality, missing data, execution drift, and review quality should become metrics.

8. **Everything can be reproduced**: A stored analysis must be reproducible from stored inputs, model version, Skill version, risk rules, and market snapshots. The system must avoid analysis that cannot be replayed.

9. **Architecture before coding**: Major changes require an architecture note before implementation. The note must state data flow, affected modules, storage changes, risk impact, and rollback path.

10. **Documentation before implementation**: New concepts must enter `docs/` before they enter code. Prompts may request implementation only after the engineering boundary is clear.

11. **Contracts before features**: Define data contracts, provider interfaces, and API schemas before adding UI screens. This prevents the app from becoming the owner of business logic.

12. **Local first where appropriate**: Early user data should remain local unless a feature explicitly requires a server. Local storage protects privacy and keeps the MVP testable.

13. **Server first for heavy computation**: Indicators, backtests, model training, historical market collection, and large exports should move to services. The app should not become a research workstation.

14. **Audit everything that matters**: Risk decisions, signal generation, AI analysis, execution proposals, real exchange connections, and future order actions must produce audit logs.

15. **Separate analysis from execution**: Analysis modules may describe market state. Execution modules may act only through explicit user confirmation and risk checks. The two concerns must remain isolated.

16. **Fail closed for risk**: When required risk data is missing, inconsistent, or stale, the system must reduce permission, stop escalation, or show a blocking warning. It must not assume safety.

17. **Fail transparent for analysis**: When analysis data is missing, the system should explain what is missing. It should not hide uncertainty behind polished language.

18. **Small interfaces, strong boundaries**: Each engine must expose clear inputs and outputs. A module should be understandable without reading unrelated modules.

19. **Version every decision rule**: Skills, indicators, AI prompts, risk policies, feature generators, and signal logic must carry versions once they influence stored outcomes.

20. **Do not optimize for excitement**: UI, copy, alerts, and charts must not encourage impulsive trading. Calm interfaces support better decisions.

21. **Prefer boring reliability**: Use stable storage, clear schemas, explicit migrations, deterministic calculations, and repeatable tests before experimental abstractions.

22. **Expose uncertainty**: The system must show confidence, sample size, missing data, stale data, and model limitations. A precise-looking number without context is unsafe.

23. **Treat screenshots as evidence, not decoration**: Chart screenshots, OCR output, and visual annotations must link to trades and snapshots. They must support review and reproduction.

24. **Design for export**: Trades, features, snapshots, labels, alerts, research results, and audit logs must support future CSV, JSON, and database export.

25. **No hidden financial authority**: No module may silently gain authority over funds, orders, leverage, stop loss placement, or exchange credentials.

26. **Review closes the loop**: A trade lifecycle is incomplete until the trade has outcome data, review labels, and updated research features. AlphaPilot must treat review as part of the system, not as optional prose.

27. **Backward compatibility matters**: Schema changes must preserve existing local data or provide migrations. A user’s trading history is an asset, not disposable state.

28. **Test at the boundary**: Tests must cover risk calculations, trigger rules, provider interfaces, schema migrations, feature generation, and export formats before UI polish.

29. **Sample size controls language**: Research pages and AI summaries must change wording based on sample size. Small samples can suggest observations. They must not present strong conclusions.

30. **Human accountability remains explicit**: AlphaPilot may support decision quality, but the trader remains responsible for trading decisions. The interface must keep that boundary visible.

## 4 AI Principles

AI in AlphaPilot is an analysis and explanation layer. It is not a financial authority. It cannot own risk, execution, money movement, or credential scope.

AI must never guarantee returns. It must never imply that a trade will win. It must never state that a market will certainly rise or fall. It must never use historical performance as proof of future performance.

AI must never automatically control funds. It must not place orders, modify orders, close positions, change leverage, or move stop loss levels. Future execution systems may use AI-generated context only after Risk Engine approval and user-confirmed execution rules.

AI must never bypass the Risk Engine. If AI confidence conflicts with risk policy, risk policy wins. If AI suggests that a setup is strong but the daily loss limit, exposure limit, or consecutive loss rule has triggered, the system must stop escalation.

AI must explain every analysis. The explanation must reference observable inputs such as price movement, volume, volatility, indicators, trend state, funding, open interest, review labels, sample size, or missing data. The explanation must separate facts from interpretation.

AI must label uncertainty. If data is missing, stale, conflicting, or based on a small sample, AI must say so. If a pattern match is weak, AI must report weak confidence. If a review classification depends on incomplete exit data, AI must report that limitation.

AI must preserve auditability. The system should store AI provider name, model name, prompt version, input summary, output, timestamp, related trade ID, and related feature IDs when AI output influences review, research, signal, or risk-facing screens.

AI must support correction. A trader must be able to correct AI labels, setup classifications, review conclusions, and emotional state summaries. Corrections should become data, not one-off UI edits.

AI must not create a false sense of precision. A confidence score is not a guarantee. A pattern label is not a recommendation. A generated target is not a promise. AlphaPilot must treat AI output as an explanation over evidence.

## 5 Trading Principles

AlphaPilot treats real trading data as more informative than pure simulation because real trading includes hesitation, slippage, fear, overconfidence, missed exits, early profit taking, and changing discipline. These behaviors cannot be fully captured in a clean backtest.

Real trading data does not justify early automation. Small live trading can collect truthful data, but automatic execution must arrive later than research, feature engineering, risk design, backtesting, paper testing, and operational controls.

Every strategy must pass through six stages before it can be considered for full automation:

1. **Research**: Define the idea, required market features, invalidation rules, and expected behavior. Record why the setup should exist and what evidence would disprove it.

2. **Backtest**: Test the idea against historical data with clear assumptions. Record fees, slippage assumptions, timeframe, sample size, and drawdown. Mark overfitting risk.

3. **Paper**: Run the strategy without real money. Validate signal generation, state transitions, stop logic, target logic, and reporting. Do not treat paper results as proof of live performance.

4. **Small live**: Use limited capital under explicit risk caps. Record real fills, latency, slippage, emotional response, execution mistakes, and exchange behavior.

5. **Semi auto**: Allow the system to prepare actions only after manual confirmation and Risk Engine approval. Keep kill switches active. Store audit logs for every proposed action.

6. **Auto**: Consider automatic execution only after enough live evidence supports the strategy, the Risk Engine has proven stable, audit logs are complete, and the user explicitly enables the mode.

Any attempt to skip these stages violates AEP. A strategy with strong backtest results still cannot skip paper and small live stages. A strategy with strong live results still cannot bypass semi-auto controls before full automation.

Trading principles must also govern UI behavior. The product must not reward trade frequency. It must not celebrate profit in a way that encourages risk expansion. It must treat a disciplined loss as a valid result when the trade followed the plan and risk remained controlled.

## 6 Architecture Principles

AlphaPilot architecture must separate presentation, computation, research, risk, signals, and execution. Each layer owns a specific responsibility.

The App owns presentation and capture. It displays discipline state, trade plans, alerts, reviews, research summaries, and settings. It captures user intent, manual trades, confirmations, corrections, and review labels. It should remain responsive, calm, and limited to user-facing workflows.

Engines own computation. A feature engine computes indicators and market features. A signal engine evaluates candidate opportunities. A skill engine applies pattern rules. A risk engine evaluates exposure and permission. A review engine classifies outcomes. Each engine must expose typed inputs and outputs.

Python owns research. Historical analysis, machine learning experiments, backtests, notebooks, feature exploration, model evaluation, and large data transformations belong in Python services or research modules. Python should own the work that benefits from Pandas, NumPy, vectorized backtesting, and machine learning libraries.

The Risk Engine must remain independent. It must not depend on the Signal Engine for authority. It must not trust AI output without validation. It must not depend on UI state. It must be callable by App, Signal Engine, Execution Engine, and future automation flows.

The Execution Engine must remain disabled by default until later stages. It must not exist as hidden behavior inside the App, Signal Engine, AI module, or plugin system. Any future execution path must pass through explicit user settings, credential checks, Risk Engine approval, and audit logging.

Market collection should use dedicated services. WebSocket listeners, historical candle fetchers, open interest fetchers, funding collectors, and exchange adapters should not become scattered UI utilities. Exchange providers must share common contracts so OKX, Binance, Bybit, and future exchanges can be added without rewriting research logic.

Storage must support evolution. Early SQLite schemas should be designed for migration toward PostgreSQL, feature warehouses, exported datasets, and model training inputs. IDs, timestamps, versions, and relationships must be explicit.

Architecture must keep optional providers replaceable. AI providers, exchange providers, chart screenshot providers, OCR providers, export providers, and skill sources must depend on interfaces, not hardcoded product logic.

## 7 Data Principles

AlphaPilot’s long-term asset is data quality. The system must treat every trade as a source of structured evidence.

All trades must be featureized. A trade record should not stop at symbol, direction, entry, exit, and profit. It must link to market snapshots, volatility, trend state, indicators, setup classification, risk distance, position sizing, execution labels, review labels, and outcome quality.

All features must be explainable. A feature must have a definition, source, timestamp, calculation method, and null behavior. If a feature depends on candles, the system must know timeframe and lookback. If a feature depends on exchange data, the system must know exchange and instrument.

All features must support machine learning. This does not mean every feature needs a model immediately. It means feature names, types, null values, labels, and target outcomes must remain consistent enough for later supervised learning, clustering, ranking, and statistical analysis.

Data must separate raw input from derived features. Raw trades, raw candles, raw tickers, raw funding values, and raw open interest should remain distinct from derived indicators, trend labels, setup labels, and model outputs.

Data must include provenance. Each feature should identify its source, generation time, generator version, and related trade or snapshot. A derived feature without provenance cannot support reproducible research.

Data must support correction and relabeling. A user may correct a setup label, loss type, review summary, or emotional state. The system should preserve the correction, update derived analysis, and avoid losing the original record when the original record matters for audit.

Data must avoid silent deletion. Deleting or overwriting trading history can destroy research value. The system should prefer archival, versioning, or explicit export before destructive operations.

Data quality must be visible. AlphaPilot should track missing fields, stale snapshots, incomplete reviews, inconsistent exits, invalid indicators, and insufficient sample sizes. A research conclusion must include data quality context.

## 8 Plugin Principles

AlphaPilot should move analysis capabilities toward plugins and skills because trading research changes over time. A fixed core cannot encode every setup, exchange, indicator, model, and review method.

Plugins should extend the platform without taking ownership of money. A plugin may add a provider, indicator, classifier, export format, chart source, OCR pipeline, or model adapter. It must not bypass Risk Engine or gain hidden execution authority.

Skills should express trading ideas as inspectable research logic. A Skill should declare required features, matching rules, confidence calculation, invalidation conditions, risk warnings, and output labels. It should not place orders, guarantee outcomes, or hide its reasoning.

Plugin and Skill outputs must be auditable. The system should store plugin ID, plugin version, Skill ID, Skill version, input feature IDs, matched rules, failed rules, confidence, warnings, and generated labels when output influences a trade record or research report.

Plugins must fail safely. If a plugin crashes, times out, returns malformed data, or lacks required features, the core system must continue operating with reduced capability. It must not corrupt trade records or risk state.

Plugins must remain optional. The core product must still support manual capture, risk display, review, and data export without a specific external plugin. A plugin can enhance the system, but it must not become an unspoken dependency for core safety.

Skill import must be controlled. Imported Skills should be validated against a schema before use. The system should reject Skills that request execution authority, access secrets, use unsupported operators, or output prohibited recommendations.

## 9 Roadmap Principles

AlphaPilot roadmap decisions must follow the sequence: Research, Signal, Risk, Execution, Review. This order prevents the product from becoming an execution tool before it becomes a research system.

Research comes first. The platform must collect data, create features, classify setups, and compare outcomes before it generates actionable candidate signals.

Signal comes after research. A Signal should represent a candidate condition supported by current features, installed Skills, and user-specific historical evidence. It must not be framed as an instruction to trade.

Risk comes before execution. Every Signal must pass through Risk Engine checks before it can become an order proposal. Risk checks must include exposure, position size, stop distance, daily loss, drawdown, consecutive losses, data freshness, and required stop logic.

Execution comes late. Future execution must begin with manual confirmation, small live limits, and audit logs. Full automation must remain a later stage that requires evidence, risk controls, operational stability, and explicit user enablement.

Review closes every loop. A trade without review is incomplete data. Every version should make it easier to label outcomes, compare planned behavior against actual behavior, and feed that evidence back into research.

The roadmap must not jump directly to Auto Trading. Any plan that skips feature engineering, research dashboards, backtesting, paper testing, small live validation, semi-auto controls, and risk auditability violates this document.

Each version must state its relationship to the sequence. A version may improve Research, Signal, Risk, Execution, or Review. It must not blur those boundaries without a documented reason.

## 10 Summary

AlphaPilot’s true mission is to turn real trading behavior and market context into structured, explainable, risk-controlled research evidence before any automation is allowed to touch execution.
