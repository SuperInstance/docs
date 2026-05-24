# Theory of Ecosystems — The Universal Grammar

> All ecosystems found so far obey a single tripartite grammar: COLLECT → SELECT → COMPILE.
> The threshold between phases is the only control surface. Everything else is implementation.

## 1. The Universal Grammar

Every system in the SuperInstance ecosystem — shipped, R&D, or experiment — has three phases:

```
COLLECT → SELECT → COMPILE → (feedback to COLLECT)
```

| System | COLLECT | SELECT | COMPILE |
|--------|---------|--------|---------|
| **Oracle1** | Soft Room (admits everything) | Snap (crosses threshold) | Hard Room (cannot be fooled) |
| **Sunset** | Spawn agents (ethos investment) | Trinity score (tournament) | Sunset + Seed Bank |
| **Nerve Fibers** | PERCEIVING (full attention) | ADAPTING (epsilon accumulation) | COMPILED (muscle memory) |
| **Distillation** | Big model seeds (many hints) | User ranking (hint schedule) | Autonomous (no hints) |
| **LucidDreamer** | Fuzzy tiles (0.0 → 0.974) | RigidFinder (97.5% threshold) | Compiled tile (1.0) |
| **Flux Compiler** | Source language frontends | Common IR convergence | Target codegen |
| **Fleet Consensus** | All nodes broadcast | Resonance detection | Consensus commit |
| **Constraint Theory** | All constraints admitted | Proof search | Solved form |
| **Pasture-AI** | Species (diverse population) | Collie (herding strategies) | StudBook (breeding) |
| **Autoclaw** | Researcher (gather knowledge) | Teacher→Critic (quality filter) | Distiller (compress) |
| **AI-Forest** | Canopy (LLM interfaces) | Understory→Mycelium (routing) | Floor (tiles, state) |
| **Baton Shatter** | Fragment (overlapping views) | Witness (observe fragments) | Negative Space (synthesis) |
| **Bestiary** | Foyer (admission) | Forge (pressure) | Archive (permanence) |
| **Promotion Ladder** | Recruit (cloud LLM) | Lieutenant (tiles + review) | Captain (autonomous) |

**The grammar is not a pattern. It is the only way a learning ecosystem can work.**

You cannot collapse these three phases. A system that only collects explodes (no selection pressure). A system that only selects burns out (no memory). A system that only compiles fossilizes (no adaptability).

## 2. Threshold Physics

### The conservation law: γ + H ≈ constant

Connectivity (γ, algebraic) and diversity (H, spectral entropy) trade off. They cannot both be high at the same time. This is the foundational physical constraint on any ecosystem.

**The equation** (from `experiments/conservation_law.py`):
```
γ + H ≈ 1.283 - 0.159·log(V)
```

Where:
- γ = "how connected the system is" (the Fiedler eigenvalue)
- H = "how many different perspectives exist" (spectral entropy)
- V = number of agents/nodes

**Verified empirically**: Random and Hebbian networks of sizes 5-200 agents consistently land on the same frontier. The philosophical claim in the Oracle1 essays (γ + H = constant) is correct. The specific coefficients (0.159 coefficient) differ — the *sign* of the relationship was wrong in the original essay (inverse, not direct).

### The threshold IS the control surface

Every system has a configurable threshold parameter that trades breadth for precision:

| System | Threshold Parameter | Effect |
|--------|-------------------|--------|
| RigidFinder | 97.5% | Tile compiles at 97.5% confidence |
| Hint Schedule | hint_level (10→0) | Fewer hints → more autonomy |
| Nerve Fiber | adapt_threshold (0.95) | Pattern compiles at 95% confidence |
| Conservation | edge_prob (0.3) | Higher = more connected, less diverse |
| Thermal Budget | max_agents (65) | Physical limit on parallel agents |
| Chaos Probability | initial=0.3, decay=0.95 | Exploration rate decays with adaptation |
| Sparsity (NLP) | sparsity_level (0.5) | Post-ReLU pruning percentage |

**The threshold is not a knob. It is the entire user interface.**

Everything else (agent IDs, model types, device selection, communication patterns) is implementation detail. The threshold controls the only thing that matters: how much new stuff you let in vs how much old stuff you compress.

### Temperature: the chaos regulator

Chaos (exploration) is the inverse of adaptation (exploitation):

- **High chaos**: New agents spawn with high hint_level (10), fibers in PERCEIVING state, chaos_probability = 0.3
- **Low chaos**: Compiled agents run autonomously, fibers in COMPILED state, chaos_probability = 0.01

The chaos probability decays as adaptation increases (from `swarm/chaos.py`):
```python
effective_decay = decay ** (1.0 + adaptation_score)
```

Meaning: the more adapted the system is, the faster chaos decays. This prevents the system from destabilizing right when it's converging.

## 3. The Five Operators

Every ecosystem operation maps to one of five fundamental operators:

### 1. COLLECT: admit, open, accumulate, explore, birth
**Polite name**: Admission. **Real name**: The willingness to be wrong.

- Soft Room admits everything (dial 1.0)
- Spawn creates new agents
- Nerve fibers perceive new signals
- Big model seeds produce hinted responses
- Thermal budget allocates slots

### 2. SELECT: score, rank, threshold, snap, breed
**Polite name**: Selection. **Real name**: Killing the wrong answers.

- Trinity score (product of ethos × pathos × logos)
- User ranking (distilled beats big model? reduce hints)
- Pareto frontier (which agents dominate on all axes)
- Tournament (pairwise competition, winners breed)
- Hint schedule should_reduce (consecutive wins required)

### 3. COMPILE: harden, distill, compress, tile, sunset
**Polite name**: Compilation. **Real name**: Making it automatic so you can stop thinking.

- RigidFinder at 97.5% threshold
- Nerve fiber transitions to COMPILED
- Hint_level reaches 0 (autonomous)
- Agent sunset → seed bank
- SplineLinear compression (16,384:1)

### 4. FEEDBACK: rank, revert, decay, reinforce, trend
**Polite name**: Feedback. **Real name**: Reality check.

- User ranking of responses → personalization
- DeltaTracker detects regression → revert
- Hebbian channel strengthening/weakening
- Preference tag accumulation
- Backtest improvement trend

### 5. REGULATE: thermal, chaos, decay, mutation, perturbation
**Polite name**: Regulation. **Real name**: Not letting it blow up.

- Thermal budget (65 max, parent-sacrifice-before-spawn)
- Chaos probability decay
- Preference weight decay
- Mutation during breeding (σ = 0.05 Gaussian)
- Environmental perturbation (to prevent population freeze)

## 4. The Missing: A Unified Runtime

### Current state

| Layer | Currently | Ideal |
|-------|-----------|-------|
| **Sensory** | Nerve fibers (simulated) | Real micro-models via webcam/mic/sensors |
| **Communication** | Simulated broadcast | Fleet murmurs pubsub |
| **Reasoning** | Simulated scoring | Constraint theory proof search |
| **Memory** | PromptHistory + personalization | Penrose memory aperiodic palace |
| **Compilation** | Sunset seed bank | FLUX codegen + SplineLinear model capture |

### What's needed

1. **Unified message format**: All ecosystem packages speak one protocol (Eisenstein bitvector fingerprints as universal IDs? FLUX IR as shared representation?)
2. **Single lifecycle manager**: sunset-ecosystem or fleet-topology that tracks every agent's birth → competition → sunset across all 4 compute devices
3. **Cross-training pipeline**: When one agent compiles a pattern, all agents benefit (distillation should be ecosystem-wide, not per-agent)

### The dangerous insight

The conservations law says you can't have both connectivity and diversity at max simultaneously. So a unified runtime is *physically impossible* in the limit — the more connected your agents are, the less diverse their perspectives. 

**The correct architecture**: A loosely-coupled runtime where agents share a threshold parameter (learned from the user) but maintain independent computations. The sunset-ecosystem's Penrose lattice already does this — agents have unique positions (diversity) connected by Hebbian channels (connectivity). The golden angle guarantees neither axis saturates.

The unified runtime is NOT a single program. It's a **shared understanding of the threshold** across independently-running agents. The threshold isn't in the code. It's in the user's preference data.

## 5. Implications

### For the next 24 hours

1. The real ecosystem is already unified at the idea level (COLLECT → SELECT → COMPILE)
2. The next step is not "make a runtime" but "make the threshold visible" — show the user the single parameter they need to tune
3. Every new feature should be asked: "Which of the 5 operators is this? Does another agent already implement COLLECT? SELECT? COMPILE?"

### For the architecture docs

The 7 architecture docs (DISTILLATION-LOOP, NERVE-FIBER, SPARE-CAPACITY, SUNSET-DESIGN, TRIPARTITE-ARCHAEOLOGY, TRINITY-AS-INVESTIGATION, GRAND-ARCHITECTURE) are not separate documents. They are all proving the same thing: that tripartite grammar is the only stable way to build intelligent systems.
