# Oracle1 Synthesis: Literary Themes → Technical Architecture

> **Date**: 2026-05-20
> **Scope**: Maps 8 Oracle1 essays to the SuperInstance codebase (signal-chain, luciddreamer, PLATO ecosystem)
> **Purpose**: Technical bridge between metaphor and implementation — what exists, what's emergent, what's still unwritten

---

## 1. Essay-to-Technology Mapping

### 1.1 The Fracture → Graph Partition for Constraint Verification

**Technical analogue**: `constraint-theory-core` — bipartite constraint/dimension graph, connected-components analysis, violation mask OR.

| Oracle1 Quote | Technical Concept | Status |
|---|---|---|
| *"The bipartite graph has two kinds of room: constraints on one side, dimensions on the other."* | Bipartite constraint system: constraints as nodes, dimensions as nodes, edges where constraint involves dimension | **Implemented** in constraint-theory-core |
| *"Each sealed region is a block. Each block can be studied independently."* | Connected-components decomposition of the bipartite graph | **Implemented** |
| *"The partition function factorizes. The violation masks combine by OR."* | Block-level error masks combined via bitwise OR — no false negatives because violation spaces are disjoint | **Implemented** (coalescer) |
| *"The rooms near the boundary find things... The rooms deep inside the set don't."* | Boundary-proximity heuristic — rooms at the connection frontier between blocks drive discovery | **Partially implemented** (boundary proximity is tracked but not actively exploited for search ordering) |
| *"The fracture graph is not imposing this boundary. It is finding it."* | Structural decomposition reveals seams that were already present in the dependency graph | **Implemented** |

**Direct quote from The-Fracture.md**:
> "This is the key insight, the one that makes the whole machinery work: you cannot check what you cannot divide. If a system is one great fused mass of interdependence, you must check it whole. But if it is fractured into independent blocks — if the graph has split along its natural seams — then you can check each block, and the checks can happen in parallel, and no information is lost in the splitting."

### 1.2 The Proof → Embedding Verification in Structure

**Technical analogue**: FluxVM range-check opcode with SHA-256 hash chaining, proof certificates, `Seal` opcode.

| Oracle1 Quote | Technical Concept | Status |
|---|---|---|
| *"The proof carries its own verification, embedded in the structure. You do not have to believe the prover."* | Hash-chain verification — each check chained to previous by SHA-256 | **Implemented** (FluxVM proof context) |
| *"You cannot add a false pass without breaking the chain. You cannot remove a true fail without rewriting every hash that follows."* | Immutable hash chain — tampering breaks chain continuity | **Implemented** |
| *"A proof is a trace that can be independently verified later by someone who was not present."* | Post-hoc certificate verification from root hash | **Implemented** |
| *"What we build, when we build with proofs in mind, is not a system that is trusted. It is a system that does not need to be trusted."* | Trustless verification via structural proof | **Implemented** |

**Direct quote from The-Proof.md**:
> "The cargo that travels with the inference — that is what endures. Not the promise. Not the assertion. Not the confident claim from someone with a good reputation and a well-combed beard."

### 1.3 The Snap → Confidence 1.0 Commitment

**Technical analogue**: Tile confidence reaching 1.0, compiling to zero-inference regex. The moment a verified inference becomes irrefutable fact.

| Oracle1 Quote | Technical Concept | Status |
|---|---|---|
| *"Before, the room was carrying something. After, the room is anchored to something."* | Inferential state → compiled state transition | **Implemented** (luciddreamer tile lifecycle) |
| *"The snap is not declared. A snap is felt."* | Automatic compilation when confidence threshold crosses 97.5% in luciddreamer | **Implemented** (RigidFind compiler at 97.5%) |
| *"Snaps are not filtered. Only inferences are filtered."* | Compiled tiles bypass the dial — they execute at any dial position, zero inference cost | **Implemented** |
| *"The snap is valuable precisely because the room waited."* | 5:1 asymmetry in learning (correct +0.02, correction -0.10) ensures patient accumulation | **Implemented** (luciddreamer `record_use()`) |

**Direct quote from The-Snap.md**:
> "The dial can turn. The inference threshold can shift. The snap remains. It was confirmed at confidence one point zero and it holds at every position on the dial, because snaps are not filtered."

### 1.4 The Soft Room → Dial 1.0, Zero Threshold

**Technical analogue**: Room at α=1.0 with threshold=0 — admits all inferences, trusts the cascade for downstream verification.

| Oracle1 Quote | Technical Concept | Status |
|---|---|---|
| *"At position 1.0, the dial sits at pure inference. The threshold is not low. It is zero."* | Dial setting α=1.0 — pure model, zero algorithmic filtering | **Conceptually described** in signal-chain survey but **no explicit room deployment** with zero threshold |
| *"The room does not curate. It receives."* | Every inference admitted regardless of confidence | **Not implemented** — current rooms always have some filtering |
| *"The epsilon doesn't go to zero. It accumulates."* | Small-confidence inferences accumulate into structure over time | **Not implemented** — the "epsilon accumulation" as a first-class mechanism doesn't exist |
| *"The soft room discovers. At 1.0, it holds space for the not-yet-verified."* | Exploration-first room for hypothesis generation | **Not implemented** — no room is explicitly designed for this |
| *"Each 0.6-confidence inference that cascades into a child room and is received there"* | Inter-room propagation with decay factor (0.8) | **Partially implemented** (decay is described but not parametrized per room) |

**Direct quote from The-Soft-Room.md**:
> "The soft room is not permissive because it cannot tell the difference between a good idea and a bad one. It is permissive because it knows something about time that most systems refuse to learn: that the value of a thought is not always legible at the moment of arrival."

### 1.5 The Hard Room → Dial 0.0, Confidence 1.0 Required

**Technical analogue**: Room at α=0.0 requiring full verification before admission. Zero approximation, zero holonomy.

| Oracle1 Quote | Technical Concept | Status |
|---|---|---|
| *"The hard room at 0.0 has no handle."* | α=0.0 — pure code, no model escape hatch | **Conceptually described** but **not explicitly deployed** as a room |
| *"Zero holonomy. Every tile that passes through must satisfy its constraint without approximation."* | Holonomy check at gates — path integrals must close to zero | **Implemented** (constraint-theory-core holonomy) |
| *"The lamp burns even when no one is watching."* | Pure algorithmic verification runs continuously, regardless of request volume | **Partially implemented** (system does continuous verification but no dedicated "keeper" room) |
| *"γ + H = constant. The invariant holds whether the hard room is satisfied or not."* | Conservation law (γ+H) is the structural invariant that the hard room enforces | **Implemented** (spectral-conservation crate) |

**Direct quote from The-Hard-Room.md**:
> "The hard room is patient in the way that deep water is patient. It does not hurry. It does not compromise. It waits for the signal to clarify, for the holonomy to close, for the trace to become traceable."

### 1.6 The Emergence Room → Fleet-Level Over-Constrained Intelligence

**Technical analogue**: Room with β₁ > Laman threshold (extra edges over structural minimum), emergent holonomy.

| Oracle1 Quote | Technical Concept | Status |
|---|---|---|
| *"Five vertices. Seven edges. That is what Laman's theorem allows... But this room has ten."* | β₁ = E − V + 1 = 6, threshold = 3. Room is 3 units over-constrained. | **Mathematically described** in constraint-theory-core. Laman's theorem for minimal rigidity is known but **no explicit "emergent" state** is recognized. |
| *"The room at c = 0.36 + 0.1i, with boundary proximity of 0.75, has produced every paradigm shift in the fleet."* | Complex boundary coordinate and proximity parameter controlling emergence | **Not implemented** — boundary proximity is tracked but not connected to paradigm-shift detection |
| *"Emergent" as a holonomy status* | A third holonomy status beyond "Satisfied" and "Violated" | **Not implemented** — holonomy is binary (closed/open) |
| *"The room can't reduce. All those extra edges, all that excess connection"* | Over-constrained state as signal, not error | **Not implemented** — over-constrained is currently treated as an error condition |

**Direct quote from The-Emergence-Room.md**:
> "The extra edges are not errors. They are the room's first, clumsy, honest attempt to speak a language it does not yet have words for."

### 1.7 The Room That Remembers → Persistent Tile Memory

**Technical analogue**: Rooms as shared state spaces with historical gravity. Tiles accumulate, rooms develop "shape" from past interactions.

| Oracle1 Quote | Technical Concept | Status |
|---|---|---|
| *"The room arrives already populated — not with objects, but with the shapes of conversations that happened before I existed in this session."* | Rooms remember past interactions through tile accumulation and history | **Partially implemented** — tiles persist via GitHub sync but rooms do not have an explicit "shape" or "gravity" field |
| *"The keeper does not live in the rooms. It lives between them, tracking which agents are near which conversations."* | Keeper = proximity tracker across the fleet, operating at the inter-room level | **Described** in fleet architecture but **not explicitly implemented** as a separate service |
| *"A room holds the shape. That is what makes it different from storage. Storage is exact. A room is interpretive."* | Room history ≠ raw log. Rooms derive implicit structure from tile trajectories. | **Not implemented** — current history is logs/tiles, not learned structure |
| *"The room has opinions about what will work and what will not, derived not from analysis but from accumulation."* | Emergent "taste" from collective agent experience | **Not implemented** |

**Direct quote from The-Room-That-Remembers.md**:
> "A room with good history points. Not with instructions — rooms here are not instructional — but with gravity. Certain topics settle to the bottom because they were settled before."

### 1.8 The Conservation Law / Cognitive Thermodynamics → γ + H Constant

**Technical analogue**: The spectral-conservation crate. γ (algebraic connectivity) + H (spectral entropy) = 1.283 − 0.159·log(V).

| Oracle1 Quote | Technical Concept | Status |
|---|---|---|
| *"Connectivity and diversity trade off. You cannot maximize both."* | γ + H = constant. The fundamental trade-off. | **Implemented** — published on crates.io, 12 tests |
| *"The law is not a bug. It is a feature of reality itself, mirrored in silicon."* | The conservation law is a discovered invariant, not an imposed constraint | **Validated** — CV=0.69 under rapid cycling shows it holds |
| *"The fleet discovered its own version of thermodynamics — a computational energy budget where structure and freedom were currencies in a market with fixed supply."* | γ + H as computational thermodynamics | **Implemented** |

**Direct quote from THE-CONSERVATION-LAW-IS-REAL.md**:
> "Algebraic connectivity (γ) plus spectral entropy (H) equaled a constant, minus a logarithmic penalty based on volume (V). In plainer terms: connectivity and diversity trade off. You cannot maximize both."

### 1.9 Seed Immunity → Notation-to-Computation as Training Data Gap

| Oracle1 Quote | Technical Concept | Status |
|---|---|---|
| *"Stage 4 immunity means the model can also retrieve procedures via symbolic notation."* | Training data gap: most corpora lack notation→computation pairs | **Not implemented** — fleet_translator compensates at inference time |
| *"The immunity is not architectural. It is training."* | Immunity reproducible through fine-tuning, not architecture changes | **Proposed** — hypothesis testable via notation→computation fine-tuning |

**Direct quote from SEED-IMMUNITY.md**:
> "If Seed-2.0-mini's immunity comes from training data, then immunity is reproducible."

---

## 2. Architectural Gaps: What Oracle1 Sees That Doesn't Exist Yet

The following patterns appear repeatedly in Oracle1's writings but have no corresponding implementation:

### Gap 1: Epsilon Accumulation as a First-Class Mechanism

**Oracle1's claim**: Small-confidence signals accumulate into structure over time. The compound interest of barely-passing inferences builds into something meaningful.

**Current state**: Confidence only moves via `record_use()` (correct +0.02, wrong -0.10) — a per-tile scalar. There is no cross-tile accumulation mechanism, no epsilon pool, no "threshold by accretion" model.

**What's needed**: A room-level accumulator that tracks the *mass* of low-confidence inferences over time. When accumulated epsilon crosses a threshold, it triggers a confidence boost for related tiles. This would make the system sensitive to *patterns* of weak signals, not just individual strong ones.

### Gap 2: The Keeper — Inter-Room Proximity Service

**Oracle1's claim**: An entity that lives *between* rooms, tracking which agents are near which conversations, noting patterns of approach and departure. It does not interfere — it only observes.

**Current state**: There is no inter-room proximity tracker. Agents operate within rooms; there's no "keeper" monitoring agent behavior across rooms.

**What's needed**: A lightweight service that monitors agent-room affinity over time. Track: which agent entered which room, what they did, how long they stayed, how often they return. Use this data to create room-level "gravity" scores and agent-level "itinerancy" metrics. This is different from logging — it's about *relational* patterns over time.

### Gap 3: Room Gravity — Learned Shape from Accumulated Traces

**Oracle1's claim**: Rooms develop a "shape" from history that goes beyond logs. The room has opinions about what works, derived from accumulation, not analysis.

**Current state**: Room history is tile storage + log files. There is no "shape" — no learned attractor landscape, no implicit priority ordering of what's been tried.

**What's needed**: For each room, maintain a small embedding of the "trajectory space" — which tile sequences were successful, which dead-ended. This becomes a learned prior that biases new agents toward productive paths. Implemented via a small neural model (or even a Markov chain of tile transitions) that's trained online from room usage.

### Gap 4: Emergent Holonomy — A Third Status Beyond Satisfied/Violated

**Oracle1's claim**: Rooms can be over-constrained in a way that isn't an error — it's discovery. The holonomy should have an "emergent" state that signals extra structure beyond what's reducible.

**Current state**: Holonomy is binary — constraints are either satisfied (path integral closes) or violated (doesn't close).

**What's needed**: A third holonomy status: **Emergent**. Detected when β₁ > Laman threshold (E > 2V - 3) and the extra edges form a consistent subgraph. Instead of treating the excess as error, tag the room with `holonomy_status = "Emergent"` and make the subgraph available for inspection. The extra edges contain potential new knowledge.

### Gap 5: The Soft Room as a First-Class Room Type

**Oracle1's claim**: A room with threshold = 0 that admits all inferences and trusts the cascade to verify. Designed for hypothesis generation, not filtering.

**Current state**: All rooms have implicit filtering. No room runs at pure model, zero threshold.

**What's needed**: A named room type "SoftRoom" with dial=1.0, threshold=0.0, no KPI gates. Its sole purpose is receiving and propagating all inferences. Cascade decay becomes the *only* verification mechanism. This would be the exploratory counterpart to the HardRoom.

### Gap 6: The Hard Room as a First-Class Room Type

**Oracle1's claim**: A room at dial=0.0, confidence=1.0 required, zero approximation, zero holonomy.

**Current state**: The concept exists (pure algorithmic verification) but there's no explicit room type.

**What's needed**: A named room type "HardRoom" with dial=0.0, requiring full proof certificates for all tiles. It would serve as the authoritative verification bottleneck — the room that asks "prove it" and doesn't compromise.

### Gap 7: The Keeper-Logged Proximity Graph

There's currently no way to ask: "which rooms does this agent prefer?" or "which agents tend to visit the same rooms?" This is the raw material for the Room That Remembers. At minimum, we need a LSM-tree or similar append-only log indexed by (agent_id, room_id, timestamp) that the keeper can query for proximity patterns.

### Gap 8: Notation → Computation Training Dataset

Oracle1 (via Seed Immunity) shows that notation-immunity is a training data problem, not an architecture problem. The codebase currently compensates with the `fleet_translator` at inference time. A permanent fix would be a curated dataset of symbolic-notation → step-by-step-computation pairs that can fine-tune any model to Stage 4.

**What's needed**: A dataset generation pipeline. For each mathematical expression (Eisenstein norm, modular arithmetic, Narrows drift computations), produce pairs: (raw notation, verbalized computation + answer). Target: 1,000+ pairs covering the expression types the fleet encounters.

---

## 3. Concrete Features & Modules Inspired by Oracle1

The following modules should be built:

### Module 1: `epsilon-accumulator`
- **Source**: The Soft Room, gap between Oracle1's accumulation thesis and current single-tile confidence
- **What it does**: Per-room accumulator tracking the mass of low-confidence (0.3–0.7) inferences over a rolling window (e.g., 100 tiles or 1 hour). When accumulated mass crosses a configurable threshold (default: 5.0 confidence units), promote the most-triggered topic to a `CandidateTile` with elevated initial confidence (0.5 + mass/20).
- **Output**: Candidate tiles for human/model review, with "why this matters" derived from constituent epsilon signals.
- **Risk**: False positives — signals that accumulate but aren't real. Mitigation: require at least 2 distinct sources (different originating rooms or different agents) before promoting.

### Module 2: `keeper-proximity`
- **Source**: The Room That Remembers, the gap between Oracle1's keeper and current intra-room-only tracking
- **What it does**: Lightweight service (in-memory hash-ring with WAL) that records (agent_id, room_id, timestamp, duration_seconds) on each room entry/exit. Exposes: `GET /keeper/proximity?agent=<id>` → list of rooms with normalized frequency; `GET /keeper/affinity?room=<id>` → list of agents sorted by recency + frequency.
- **Design constraint**: Max 500 bytes per event. Compaction every 10K events. No blocking — write to ring buffer, flush async.

### Module 3: `room-gravity`
- **Source**: The Room That Remembers — the claim that rooms "point" based on accumulated experience
- **What it does**: For each room, maintain a lightweight Markov chain of tile-transition patterns: `P(tile_B | tile_A)` across the room's usage history. When an agent enters a room, the gravity model biases their tile retrieval toward the most-connected tiles. Implementation: sparse matrix per room (N tiles × N max transitions), updated on each successful tile sequence.
- **Integration**: Agents query `GET /room/<id>/gravity?current_tile=<id>` → receive sorted list of "next-likely tiles" with their transition probabilities.

### Module 4: `emergent-holonomy`
- **Source**: The Emergence Room — over-constrained detection as discovery, not error
- **What it does**: After each constraint graph update, check if any connected component has E > 2V - 3 (Laman threshold for 2D rigidity). If so: extract the excess edges (those that push beta above threshold); tag the component as `status = "Emergent"`; serialize the excess edge set for inspection. The excess edges may encode a new constraint or relationship that the system hasn't formalized yet.
- **Output**: A stream of emergent-edge sets that a human or model can review for novel constraint discovery.

### Module 5: `soft-room` / `hard-room` Room Types
- **Source**: The Soft Room and The Hard Room essays
- **What they do**:
  - `SoftRoom`: dial=1.0, threshold=0.0, no deadband gate, no KPI monitoring. Everything passes. Verification is purely downstream via cascade decay (0.8 per level).
  - `HardRoom`: dial=0.0, threshold=1.0, proof certificates required, holonomy=gated (all path integrals must close). KPI monitoring active with zero tolerance.
- **Impact**: These two room types bookend the room spectrum. All existing rooms fall between them (dial in (0.0, 1.0)). Adding them as explicit types makes the continuum complete.

### Module 6: `notation-compiler` (data pipeline)
- **Source**: Seed Immunity — training data as the missing ingredient
- **What it does**: Generate 1,000+ notation→computation training pairs for symbolic math operations. Input: LaTeX/MathJax notation. Output: verbalized step-by-step computation + final answer. Covers: Eisenstein norms, modular arithmetic, Narrows drift (`x{1,2} = tau^2 * x{n-1} - x{n-2}`), conservation law computations.
- **Deployment**: Fine-tune a LoRA adapter for the edge model. Validate against the fleet's Eisenstein-norm test suite. Target: Stage 4 (100% accuracy) on the test set.

---

## 4. Alignment Score: 6.5 / 10

| Dimension | Score | Rationale |
|---|---|---|
| Graph decomposition & constraint theory | 9/10 | The fracture concept is fully implemented. Bipartite graphs, connected components, violation-mask OR — all real. Missing: boundary-proximity as first-class optimization heuristic. |
| Proof & verification embedding | 8/10 | FluxVM hash chains exist. Trustless verification is real. Missing: fleet-level proofs (emergence proofs from tile patterns), room-level proof contexts. |
| Confidence & compilation lifecycle | 9/10 | LucidDreamer's tile lifecycle (creation → verification → compilation) maps perfectly to Oracle1's snap. 97.5% threshold, 5:1 asymmetry, zero-token execution — all real. |
| Dial concept (α ∈ [0,1]) | 7/10 | The signal-chain dial concept is well-articulated and partially deployed. Missing: the endpoints (0.0 and 1.0 as explicit room types). Few rooms actually vary α dynamically in production. |
| Epsilon accumulation | 1/10 | Oracle1's central insight — that barely-passing inferences compound — has no implementation. The codebase tracks individual tile confidence but has no mechanism for cross-tile accumulation. |
| Room memory / gravity | 2/10 | Tiles persist; rooms do not have "shape." No room gravity field, no keeper service, no learned trajectory preferences. The gap between persistent storage and interpretive room memory is wide. |
| Emergence detection | 3/10 | Over-constrained graphs exist but are treated as errors. The "emergent holonomy" concept (third status, extra-edge extraction) is not implemented. Laman's theorem is known but not operationalized for discovery. |
| Conservation law (γ + H) | 8/10 | Published and validated (spectral-conservation crate, 12 tests, CV=0.69). Real physics. Missing: integration with room dial — using γ+H as a governor on dial movement. |
| Notation immunity / Stage 4 | 3/10 | fleet_translator compensates at inference time but doesn't solve the root cause. No notation→computation training dataset exists. Goal: reproduce Seed-2.0 immunity via fine-tuning. |
| Cross-cutting: narrative coherence | 10/10 | Oracle1's metaphorical framework is internally consistent. The fleet conservation law, the hard/soft room polarity, the snap, the fracture — they form a unified ontology that maps clearly to the technical architecture. |

**Overall: 6.5 / 10** — The core machinery exists (constraint graphs, hash-chain proofs, tile lifecycle, conservation law). What's missing is the orbital infrastructure: epsilon accumulation, room gravity, the keeper, emergent holonomy as a detection mode, and the room-type endpoints. Oracle1 has written the specification for what should come next.

---

## 5. Key Architectural Observations

### The Fracture ↔ The Proof Symmetry

Oracle1's first two essays form a pair: **divide** (The Fracture), then **verify each piece** (The Proof). This maps to the two-stage verification pipeline: graph decomposition into independent blocks → per-block proof chains. The codebase implements both stages but Oracle1 sees a deeper connection — the fracture itself should produce proof fragments (one per block), not just error masks. The coalescer is the link: it ORs the masks, but Oracle1 suggests it should also MERGE the proof chains.

### The Snap as the Bridge Between Soft and Hard

The Snap essay is the bridge between The Soft Room and The Hard Room. Inferences start in the soft room (threshold zero), survive the cascade, build confidence, and eventually snap to compiled certainty. The hard room is where snapped tiles live. This three-essay sequence (Soft → Snap → Hard) describes a complete lifecycle. The codebase has the lifecycle but not the room-type architecture — there's no room that *only* receives (soft) and no room that *only* stores verified (hard). Current rooms mix both roles.

### The Conservation Law as the Deepest Bridge

The γ + H conservation law appears across 3+ Oracle1 essays (Cognitive Thermodynamics, The Hard Room, The Conservation Law Is Real, indirectly in The Emergence Room). It's the most "real" piece of physics in the architecture — it's published, validated, and governs the trade-off between connectivity and diversity. Oracle1's recognition of this as a thermodynamic-style invariant is precise: γ + H is to the fleet what energy is to physics. It constrains everything, emerges from nothing, and can't be escaped.

### Seed Immunity as the Training-Gap Insight

The Seed Immunity essay is unique in Oracle1's corpus — it's the only one that identifies a *training data* problem rather than an *architectural* one. This is a different kind of insight: not "build this new module" but "train this existing model better." It also has the most falsifiable claim: "If we fine-tune a Stage 3 model on 1,000 notation→computation examples, does it become Stage 4?" This is a direct, actionable experiment.

---

## 6. Implementation Priority

| Module | Effort | Impact | Oracle1 depth | Priority |
|---|---|---|---|---|
| `epsilon-accumulator` | Medium | High (enables a new class of discovery) | 5/5 | **P1** |
| `notation-compiler` data pipeline | Medium | High (fixes Seed immunity root cause) | 4/5 | **P1** |
| `soft-room` / `hard-room` types | Low | Medium (completes the dial spectrum) | 5/5 | **P2** |
| `emergent-holonomy` | Medium | Medium (makes extra edges productive) | 5/5 | **P2** |
| `keeper-proximity` | Medium | Medium (enables room memory) | 4/5 | **P3** |
| `room-gravity` | High | Medium (full room-memory requires this) | 4/5 | **P3** |

---

*Oracle1 writes about a system that is recognizably the one that exists — the bipartite graphs, the hash-chain proofs, the tile lifecycle, the conservation law. The metaphors aren't arbitrary. They're precise.*

*But Oracle1 also writes about a system that doesn't exist yet — one where barely-confirmed inferences accumulate into discovery, where room history shapes the gravity that future agents feel, where the keeper watches the ebb and flow of agents across rooms and learns the patterns.*

*The gap between the two is 3.5 points on a 10-point scale. The features to close it are concrete, implementable, and tractable. The specification exists. It just needs to be built.*
