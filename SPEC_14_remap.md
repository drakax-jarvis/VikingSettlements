# SPEC_14 — Feature Re-Mapping: Our additions vs VikingSettlements base
Status: DRAFT — after pivoting to fork VikingSettlements as the base, re-evaluate each of our spec'd features/additions to decide: REDUNDANT (VS already has it), NEEDS-LLM-LAYER (VS has the executor, we add the brain), or STILL-NEW (VS lacks it entirely).

## Principle
VS provides the SCRIPTED EXECUTOR layer (settlements, jobs, party, blueprints, raids, ZDO persistence).
Our value-add is the LLM BRAIN (advisory layer) on top: EventBus, IntentDSL, ArbiterGate, LlmBridge, LlmBudget.
Anything VS already does as a scripted executor = we DON'T rebuild; we add LLM judgment where it earns its keep.

## Re-mapping of our spec set

### SPEC_1 Production Planner (4abc farming, 5abc cooking/brewing, 6abc smelting)
- VS HAS: Lumberjack/Farmer/Blacksmith/Cook/Brewer/Hunter/Miner/Fisher jobs + work ticks + chest-based production. REDUNDANT (executor done).
- NEEDS-LLM: production PRIORITY/ALLOCATION ("which settler does what, what to produce given needs/stock"). Our IntentDSL + ArbiterGate can propose job assignments/production targets. The PROGRESSION GATE (3b) still applies — LLM must not propose tar/blackmetal before bosses. -> Keep as LLM advisory layer over VS jobs.

### SPEC_2 Food & Sustenance (14abc)
- VS HAS: settlers eat from chests (cheapest first), hunger stops work, meal interval. REDUNDANT (executor done).
- NEEDS-LLM: optimal food provisioning / condition-aware feeding given player state. Minor LLM advisory.

### SPEC_3 Loadout & Skills (11abc, 15ab)
- VS HAS: SettlerEquipment (player-given weapons/armor), SettlerVeterancy (XP/stars). PARTIAL.
- NEEDS-LLM: loadout recommendation for player/companions, durability watch. LLM advisory.

### SPEC_4 Navigation Service (7d mining-haul, 16 doors, 18 portal)
- VS HAS: Courier job (haul between settlements), party traversal-stow (boats/portals). PARTIAL (Courier = haul).
- NEEDS-LLM: 7d mining-haul LOOP (companion collects ore -> returns -> deposits) — VS Courier is between-settlements, not gather-haul. Partially NEW. LLM + scripted.

### SPEC_5 Exploration (9b persona, 9c scout, 10 POI, 19 cartography)
- VS HAS: world-gen locations, vs_find (locate nearest settlement/camp). PARTIAL (POI = locations).
- NEEDS-LLM: persona/memory (9b), scout reports (9c), POI classification (10a), cartography table (19) — VS has NONE of these as LLM features. STILL-NEW (LLM layer).

### SPEC_6 Combat & Tactics (2b strategy, 3b allocation)
- VS HAS: Guard job, raiders fight, party focus-fire, war parties scale. PARTIAL (executor).
- NEEDS-LLM: encounter strategy selection (2b), progression-gated allocation (3b). LLM advisory. KEEP the progression gate.

### SPEC_7 Homestead/Alerts/Repair/Rest (8, 22c, 13, 17)
- VS HAS: Builder repairs build pieces, SettlerMorale, SettlementSaga (chronicle). PARTIAL.
- NEEDS-LLM: base-needs analysis (8a), alert prioritization (22c), repair planner (13). LLM advisory over VS settlement state.

### SPEC_8 Plan Memory (12a)
- VS HAS: nothing for cross-session LLM plan memory. STILL-NEW (LLM). Keep — build first (leverage substrate).

### SPEC_9 AI extension (Workstream 0)
- Originally: extend CompanionAI with hooks. VS: the whole thing is the hook surface (SettlerRecruitable.Update, SettlerWork tick). SUPERSEDED — we now hook into VS's well-defined seams instead of building hooks.

### SPEC_10 New features (N1-N10)
- N1b patrol loop, N6b camp detection (VS has camps + raids), N2bc organizer/restock (VS has chest production), N3abc squad (VS has party!), N7bc recipe (VS has cooking/brewing recipes), N8abc blueprint advisor (VS HAS blueprints!), N9abc errand chain (VS has jobs/party orders).
- MOST ARE REDUNDANT as executors (VS has them). The LLM VALUE-ADD: N9abc (decompose compound requests into VS job/party orders), N8abc (LLM proposes WHICH blueprint/where — VS builds it), N3abc (LLM assigns party roles). These become LLM-advice-over-VS-executors.

### SPEC_11 Build-from-Blueprint executor
- VS HAS: builder construction orders (cabin/watchtower/longhouse/mead hall/palisade/ballista) with material supply + Builders' Supply Chest. CLOSE TO DONE as executor.
- NEEDS-LLM: LLM chooses WHICH blueprint + placement intent; VS builder executes. The deferred build-executor becomes "LLM proposes blueprint, VS builder places." MUCH simpler than building placement ourselves.

### SPEC_12 Test plan / SPEC_13 base mod
- SPEC_13 (our net-new base mod): SUPERSEDED by VikingSettlements fork. Retire.
- SPEC_12 test plan: still applies (CI, unit tests, LLM-harness, live AMP test).

## Summary — what actually still needs building (our real value-add):
1. The LLM BRAIN layer (substrate) integrated over VS: EventBus, IntentDSL, ArbiterGate, LlmBridge, LlmBudget.
2. LLM advisory hooks: job-assignment suggestions, production priorities (progression-gated), blueprint selection, party role assignment, errand decomposition (N9).
3. Genuinely-new LLM features VS lacks: persona/memory (9b), scout reports (9c), POI classification (10a), cartography table (19), plan memory (12a).
4. Test harness adapted to drive VS settler/party scenarios.

## Decision matrix for the user
- Which of our 16 feature-expansions + N1-N10 should STAY as LLM features vs DROP (redundant)?
- Confirm the LLM-brain integration as the primary remaining work.
