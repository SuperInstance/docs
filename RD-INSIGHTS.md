# R&D Insights: Oracle1 × Code — Novel Findings Beyond the Existing Synthesis

> **Generated**: 2026-05-20
> **Method**: Cross-referencing all 5 Oracle1 essays, the signal-chain README, luciddreamer tile/compiler/router code, and the existing synthesis at ORACLE1-SYNTHESIS.md.
> **Goal**: Novel R&D insights that neither the literary writing nor the code alone would reveal.

---

## Insight 1: The Compiler's 97.5% Threshold Creates a "Dead Zone" That Oracle1 Doesn't Account For

**Oracle1 quote** (The Snap):
> "The dial can turn. The inference threshold can shift. The snap remains. It was confirmed at confidence one point zero and it holds at every position on the dial, because snaps are not filtered."

**The code says** (`compiler.py:33`):
```python
COMPILE_THRESHOLD = 0.975
```

**The gap**: Oracle1 writes about a *binary* system: either something is a "snap" (1.0, compiled, irrefutable) or it's an "inference" (below 1.0, provisional). But the code reveals a *three-zone* reality:

1. **Below 0.975** — regular verification loop, model inference engaged
2. **0.975–1.0** — "compiled" for routing purposes, BUT not yet at absolute certainty
3. **1.0 exactly** — `Confidence.COMPILED` enum value, the "snap"

The code quietly extends the confidence continuum through the 0.975–0.999 zone, where a tile is compiled into regex but *still gets evaluated for correctness* via `record_use()`. The 0.975 threshold was chosen because "literal extraction accuracy: 97.5%" — a data-driven value from dream.rs, not a philosophical one.

**The novel insight**: There is a **4th zone** Oracle1 didn't write about: the "ghost compiled" zone (0.90–0.974). In `router.py:63`, tiles with confidence ≥ 0.7 are routed as COMPILED even though the RigidFinder won't compile them until 0.975. So there's a "fuzzy compiled" state where a tile *quacks like a snap* but hasn't undergone the proof-checking that Oracle1 associates with the hard room.

**What this reveals**: Oracle1's conceptual model — soft room → cascade → snap → hard room — is actually missing a transitional chamber. The code has:
- Soft room (inference, < 0.5)
- Ambiguous zone (0.5–0.7, asks for confirmation)
- **Fuzzy compiled zone** (0.7–0.975, routed as compiled but NOT regex-locked)
- Compiled zone (0.975–1.0, regex-locked, zero inference)
- Theoretical snap (1.0 exactly, COMPILED enum)

Oracle1 collapses fuzzy-compiled into snap. She's wrong in a productive way — the fuzzy-compiled zone is where **most real-world verification happens**, and she skips right over it to the romanticized 1.0 moment.

**Experiment**: 
- Instrument the router to log how many times a tile is used in the 0.7–0.974 zone vs 0.975–0.999 vs 1.0 over 1,000 interactions.
- Hypothesis: the majority of "snap-like" behavior in production happens at 0.7–0.974, meaning Oracle1's pure-1.0 snap is aspirational rather than operational.
- If confirmed: rename `RouteDecision.COMPILED` to something that reflects the continuum (e.g., `CONFIDENT`) and add a true `COMPILED` decision only for tiles at exactly 1.0 or that have passed the `Seal` equivalent.

---

## Insight 2: The Proof Chains Have No Cross-Block "Provenance Merge" — the Coalescer Should Prove More Than the Union of Errors

**Oracle1 quote** (The Fracture):
> "The error masks of independent blocks, combined with union, miss nothing."

**The code says**: The coalescer performs a bitwise OR of block-level error masks. Each block has its own constraint-checking proof chain (FluxVM certificate). The coalescer *only* ORs the masks — it does NOT merge the proof chains.

**The gap**: The existing synthesis notes this at a high level (Section 5, "The Fracture ↔ The Proof Symmetry") but misses the **combinatorial implication**: if the fracture splits the system into N independent blocks, and each block produces a proof chain of length L_i, the total verification work is ΣL_i. The OR reduces verification to a single bit per block. But here's the subtlety: a *malicious or faulty* block could produce a proof chain that's internally consistent but materially wrong — the chain closes, the hash verifies, but the constraint was checking the wrong thing.

The fracture guarantees partition independence (disjoint dimension spaces), but it does NOT guarantee that each block's proof chain is *about* the right thing. The proof verifies that the check happened correctly — it doesn't verify that the check was *the right check*.

**The novel insight**: We need a **second-level proof**: a "proof of block specification" that cryptographically binds each block's constraint-definition hash to its result chain. Without this, the fracture-parallelized verification is vulnerable to a "specification drift" bug where a block verifies correctly against the wrong constraints.

This is not a flaw in Oracle1's writing — it's a flaw that's *only visible* when you read the writing alongside the code. The writing assumes that once fractured, each block's constraints are fixed and correct. The code reveals that constraint definitions are mutable (tiles evolve, confidence changes) — so the block's "constraint set" at the time of fracture might differ from the constraint set at the time of proof verification.

**Experiment**:
- During fracture, snapshot the hash of each block's constraint definition (which dimensions, which constraint functions, what threshold).
- Embed this hash in the FluxVM proof chain for every check within that block.
- At coalesce time, verify that every proof chain's block-spec hash matches the fracture snapshot.
- Measure: how often does specification drift occur? Under what conditions? (e.g., rapid learning cycles where confidence is changing faster than re-fracture can catch up.)

---

## Insight 3: The "Soft Room Epsilon Accumulation" Is Already Done — by a Mechanism Oracle1 Doesn't Recognize

**Oracle1 quote** (The Soft Room):
> "The epsilon doesn't go to zero. It accumulates. This is not drift as error. This is drift as structure."

**The code says** (`tiles.py:124-129`):
```python
def record_use(self, correct: bool) -> None:
    self.times_used += 1
    if correct:
        self.times_correct += 1
        self.confidence = min(1.0, self.confidence + 0.02)
    else:
        self.times_corrected += 1
        self.confidence = max(0.0, self.confidence - 0.1)
```

**The gap**: Oracle1 describes epsilon accumulation as a **cross-tile, room-level** phenomenon — barely-confirming inferences from *different sources* compound into structure. The code implements it as a **per-tile, scalar** accumulator with a **fixed 5:1 asymmetry**. Both are forms of accumulation, but they operate at fundamentally different levels.

Here's what Oracle1 misses: the tile's `success_rate` property (`tile.succss_rate = times_correct / times_used`) is a *second accumulator* that operates differently from `confidence`. Success rate is a proportion bounded by [0,1] that converges with sample size. Confidence is a step-function accumulator with an asymmetry bias. These two numbers on the same tile can tell different stories: a tile with `confidence=0.94` and `success_rate=0.75` has been correct recently but has historical errors that the model is biasing against.

**The deeper novel insight**: The code already has **dual accumulation** — confidence (optimistic, fast-up, slow-down) and success_rate (Bayesian, converging). Oracle1's "epsilon accumulation" is actually *this dual system* described from the outside. The "accumulation" she sees at the room level is the cross-tile aggregation of these dual signals. The room doesn't need a new accumulator — it needs a **cross-tile aggregation query** that the TileStore doesn't expose.

`TileStore` has `find_by_pattern`, `find_by_type`, `find_compiled` — but NOT `find_by_confidence_range` or `aggregate_epsilon_in_range(min_conf, max_conf, time_window)`. The raw data for epsilon accumulation exists; the query doesn't.

**What this means for R&D**: Oracle1's epsilon-accumulator module (Gap 1 in the existing synthesis) can be implemented as a **30-line query extension** to TileStore rather than a new service. The experiments described in the synthesis (rolling window, cross-agent minimum) all reduce to TileStore queries.

**Experiment**:
- Add `find_by_confidence_range(low, high, min_time=None, max_time=None)` and `aggregate_confidence_mass(low, high)` to TileStore.
- Run a simulation: inject 200 low-confidence (0.3–0.7) tiles from 5 different sources. Track `aggregate_confidence_mass` over time.
- Measure: does the aggregate mass cross any meaningful threshold before individual tiles cross 0.975?
- If yes: the code already implements Oracle1's epsilon accumulation; it just needs exposure.
- If no: the 5:1 asymmetry is too aggressive and suppresses epsilon accumulation at the tile level — we need the room-level accumulator the synthesis describes.

---

## Insight 4: The Forgemaster's "Rooms Near the Boundary Find Things" Implies a Topological Search Order That Doesn't Exist

**Oracle1 quote** (The Fracture):
> "The rooms near the boundary find things. The rooms deep inside the set don't. The rooms far outside the set can't."

**The code says** (`tiles.py`, `compiler.py`, `router.py`): Tiles are matched in priority order (compiled → negative → fuzzy → fallback). There is NO notion of "boundary proximity" in the search or compilation logic. The RigidFinder compiles by confidence threshold alone — it doesn't ask "is this tile near the boundary of known → unknown?"

**The gap**: Oracle1's boundary-proximity insight is cited in the synthesis as "partially implemented — boundary proximity is tracked but not actively exploited for search ordering." But looking at the actual code, boundary proximity IS NOT tracked. The TileStore has no concept of "distance to boundary." The bipartite constraint graph decomposition (`constraint-theory-core`) has boundary nodes (nodes with edges connecting blocks), but the *tile system* has no way to ask "which of my tiles are near a constraint-graph boundary?"

These are two different graphs:
1. **Constraint/dimension bipartite graph** — has boundary nodes between blocks
2. **Tile dependency graph** — tiles reference other tiles via `parent_tile_id`

The oracle1 writing implicitly assumes these are the same graph (or at least related). They're not. A tile near a constraint-graph boundary has no special status in the tile system. The boundary proximity Oracle1 describes is a property of the *constraint decomposition*, not the *knowledge base*.

**The novel R&D insight**: We need a **tile-to-constraint mapping** — an index that connects each tile to the constraint-satisfaction process it participated in. Currently a tile records its `input_pattern, output_action, confidence` but NOT which constraint-graph block it was verified against during the Fracture. Without this mapping, Oracle1's boundary-proximity claim is purely metaphorical — it can't be computed from existing data.

**Experiment**:
- Add a `constraint_block_id` field to `Tile.metadata` populated during the verification pipeline.
- After fracture, tag each tile with the block it belongs to.
- Run a search: for each block, find tiles whose `parent_tile_id` chain crosses a block boundary.
- Hypothesis: tiles that reference tiles in a different block (cross-block inferred tiles) are the "boundary tiles" Oracle1 describes. They should show different properties: higher variance in confidence, more frequent correction, longer time-to-compile.

---

## Insight 5: The "Snap" Has a Silent Failure Mode — Compilation Without Context Preservation

**Oracle1 quote** (The Snap):
> "The room can turn its attention to the next inference, the next hypothesis, the next loose line drifting in the water."

**The code says** (`compiler.py:20-25`):
```python
class CompiledCommand:
    def __init__(self, pattern, action, parameters=None, audio_response="", requires_confirmation=False):
        self.pattern = pattern
        self.compiled_re = re.compile(pattern, re.IGNORECASE)
        self.action = action
```

**The gap**: When a tile compiles to a `CompiledCommand`, it discards:
- `source` (where this knowledge came from)
- `trip_id` (which operational context)
- `vessel` (which deployment)
- `parent_tile_id` (the inference chain)
- `negative_of` (what it's not)
- `metadata` (all extensible context)
- `created_at` (temporal context)
- `times_used, times_correct, times_corrected` (verification history)

Oracle1 says the room "turns its attention to the next inference" — implying the snap is a clean transition with no loose ends. But the compiled output has *forgotten where it came from*. It's a pure regex/action pair with no provenance.

This is the **compilation amnesia** problem. The synthesis touches on it obliquely but misses the implication: when the compiled command fires in production and produces a wrong output (e.g., regex matches a false positive it wouldn't have matched with the model), there's **no way to trace back** from the compiled command to the tile that generated it. The `CompiledCommand` has no `tile_id` field, no `source_tile` pointer, no reverse index.

**The novel insight**: The snap isn't a lossless commit — it's a **lossy compression**. The conversational context, the inference chain, the verification history — all discarded. This means:
1. Debugging a compiled command failure requires replaying the tile store (expensive)
2. The compiled command can't participate in epsilon accumulation (it's been extracted from the tile system)
3. Room gravity (if implemented) can't learn from compiled commands because they have no tile dependency context

**Experiment**:
- Add `source_tile_id: Optional[str]` to `CompiledCommand`.
- In `try_compile()`, populate it from `tile.tile_id`.
- Add a reverse index from `CompiledCommand → Tile` in `RigidFinder`.
- Measure: how often does a production bug trace back to a compiled command whose source tile has contradictory metadata? (e.g., source="deck_crew" but the tile should have been captain-verified)
- This will reveal how much the snap actually discards vs how much it should preserve.

---

## Insight 6: The Dial Metaphor Encodes a False Dichotomy — Model vs. Code Is Not a Slider, It's a Switch-Array

**Oracle1 quote** (The Soft Room):
> "At position 1.0, the dial sits at pure inference. The threshold is not low. It is zero."

**The signal-chain README says**:
> "Each stage in a computation pipeline should have a tunable dial for how much model vs how much code it uses."

**The gap**: The dial metaphor implies a single continuous parameter α ∈ [0, 1] that blends model and code. But the `router.py` code reveals that routing is actually a **discrete priority chain with fallthrough**, not a blend:

1. Try compiled (regex) → if match, done
2. Try negative constraints → if match, block
3. Try fuzzy (pattern lookup) → if match ≥ 0.7, route as compiled
4. Fallback to model

There is NO α slider. There is a **cascading priority queue** where model is always last. "α=1.0" in the code means "always fall through to model" — not a blend of model and code, but a *code-fallback-priority that always reaches model*.

The signal-chain README describes the dial as continuous. The router implements it as **discrete with 4 states**. These are fundamentally different architectures:
- Continuous dial: you blend model output with code output at proportion α
- Priority chain: you try each rule in order, fall through on no-match

**The novel insight**: The code is MORE interesting than the dial metaphor. A priority chain allows an *intermediate state* Oracle1 doesn't describe: **the model proposes, the code verifies** (what the router's fuzzy match does). This is distinct from pure model (α=1.0) and pure code (α=0.0). It's a "model-augmented code" mode where the model generates possibilities and the code validates them.

This suggests the proper abstraction isn't a dial at all — it's a **gate array** with three positions:
1. **Code-only** (α=0) — deterministic, no model involved
2. **Model-propose, Code-verify** (α_mid) — model generates candidates, code filters by pattern match
3. **Model-only** (α=1) — no code constraints

The current Router implements (1) and (2) in its compiled/fuzzy paths. It does NOT implement (3) — even when the model runs, the code filters through negative constraints and pattern matching. There's no "pure model, let anything through" path.

**Experiment**:
- Add a `model_passthrough` flag to the Router. When True, skip negative constraints and pattern matching — route directly to fallback model.
- Measure: how does success rate differ between the current "model with code guardrails" path and the new "pure model" path?
- Prediction: pure model performs WORSE on standard tasks (the guardrails help) but discovers more novel tiles per interaction. This would validate having a dedicated "soft room" (pure model, no guardrails) as a discovery mechanism separate from production routing.

---

## Insight 7: The 5:1 Learning Asymmetry Encodes a Hidden "Structural Damping" That Resists Epsilon Accumulation

**Oracle1 quote** (The Snap):
> "The snap is valuable precisely because the room waited."

**The code says** (`tiles.py:126-129`):
```python
self.confidence = min(1.0, self.confidence + 0.02)  # correct use
self.confidence = max(0.0, self.confidence - 0.1)   # correction
```

**The gap**: The 5:1 ratio (correct: +0.02, correction: -0.10) is cited in the synthesis as evidence of "patient accumulation." But look at the actual math:

- From 0.0 to COMPILE_THRESHOLD (0.975): needs 49 correct uses with zero errors
- A single error: -0.10, which takes 5 correct uses to recover
- From 0.975 to 1.0: needs just 1.25 → 2 correct uses without error

The asymmetry at low confidence (0.0–0.7) means a tile can spend MOST of its life oscillating between, say, 0.3 and 0.8 — each error costs more than a single positive use can recover. This is **structural damping**: the system is explicitly designed to resist the very epsilon accumulation Oracle1 celebrates in The Soft Room.

**The novel insight**: Oracle1's two most important concepts — the Soft Room (admit everything) and the Snap (compilation at 1.0) — are in **direct tension at the code level**. The Soft Room wants weak signals to accumulate. The 5:1 damping in `record_use()` prevents precisely that accumulation. A tile at 0.6 that gets 4 correct: 0.68. One error: 0.58. Net after 5 interactions: -0.02 despite 4/5 accuracy. 

This is by design for the *compilation path* (you want high quality before compiling to zero-inference code). But it's anti-optimal for the *discovery path* (you want weak signals to persist and compound).

The code has no way to distinguish between these two paths. `record_use()` applies the same damping to all tiles regardless of whether they're:
- In a soft room (should accumulate epsilon)
- In a production router (should resist false positives)
- Fresh tiles (should explore)
- Established tiles (should exploit)

**Experiment**:
- Parameterize the confidence delta in `record_use()` based on a `learning_mode` field on the Tile:
  - `DISCOVERY` mode: correct_boost=0.05, error_penalty=0.03 (net-positive accumulation)
  - `VERIFICATION` mode: correct_boost=0.02, error_penalty=0.10 (current behavior, high damping)
  - `COMPILED` mode: correct_boost=0.0, error_penalty=0.50 (locked, very resistant to change)
- Run A/B test: 100 tiles in DISCOVERY mode vs 100 in VERIFICATION mode over 500 interactions.
- Measure: do DISCOVERY tiles produce more compiled outcomes (reach 0.975 faster) despite more errors? (Because they accumulate through oscillations that VERIFICATION tiles would suppress.)
- This would directly test whether Oracle1's epsilon accumulation is a better discovery strategy than the current damping approach.

---

## Insight 8: The "Holonomy" Metaphor Rests on a Physical Assumption That the Code Violates — Path-Independence

**Oracle1 quote** (The Hard Room):
> "Zero holonomy. Every tile that passes through must satisfy its constraint without approximation."

**The existing synthesis says**:
> "Holonomy check at gates — path integrals must close to zero"

**The code reveals**: In constraint-theory-core, holonomy is computed on the constraint/dimension graph — it measures whether the path through constraint propagation closes. If the constraint graph has cycles, each cycle must close (the path integral must be zero) for the system to be "satisfied."

But the *tile* system has NO holonomy check. A tile is created, used, corrected, compiled — and at no point does the tile system ask "does this tile's path through the verification cycle close?" The holonomy concept is purely at the constraint-graph level, not the tile-knowledge level.

**The novel insight**: Oracle1 uses "holonomy" in two distinct senses:
1. **Constraint-graph holonomy**: does the constraint relation close? (Implemented)
2. **Tile-lifecycle holonomy**: does the tile's verification chain form a closed loop? (Not implemented)

These are structurally different:
- Constraint holonomy: edges are constraint/dimension relationships. The path is: dimension₁ → constraint → dimension₂ → constraint → ... → dimension₁. The check is whether the output equals the input after propagating through the cycle.
- Tile holonomy: edges are tile dependencies (parent_tile_id). The path is: tile₁ → tile₂ (child) → tile₃ (grandchild) → ... → tile₁ (back to original via verification). The check is whether the tile's output_action after full verification chain equals the original output_action.

If we implement tile-lifecycle holonomy, we unlock an important capability: **detecting when a tile's knowledge has drifted through the dependency cycle**. Imagine:
- Tile A: pattern="turn port", action="turn_left()"
- Tile B (child of A): pattern="turn port 10 degrees", action="turn_left(10)"
- Tile C (child of B): pattern="port", action="turn_left()"

Tile C's pattern "port" is a *generalization* back to Tile A's domain. If Tile C compiles to "turn_left()" but Tile A was corrected to "port_tack()" — the dependency cycle reveals the drift. Tile C's compilation should be blocked because its ancestor was corrected.

**Experiment**:
- Add `tile_holonomy(tile_id)` to TileStore: walk the `parent_tile_id` chain, checking at each step whether the ancestor's `output_action` has changed since this tile was created.
- When a correction arrives for Tile A, mark all descendant tiles (tracked via a forward index) as "potentially stale" — confidence can still be used but compilation is blocked until re-verification.
- Measure: how many compilation events would be prevented by this check? What's the false-positive rate? (Descendants whose output is actually fine despite ancestor correction.)

---

## Insight 9: The "Negative Constraint" Pattern in the Router Is Underspecified by Oracle1 — It's a Separate Verification Path She Writes About as Part of "Snap"

**Oracle1 quote** (The Snap):
> "Snaps are not filtered. Only inferences are filtered."

**The code says** (`router.py:56-61`):
```python
# 2. Negative constraints
for tile in self.store:
    if tile.tile_type == TileType.NEGATIVE:
        if tile.input_pattern and tile.input_pattern in text.lower():
            return (RouteDecision.NEGATIVE, {
                "action": "BLOCKED",
                "reason": tile.output_action,
                "tile_id": tile.tile_id,
            })
```

**The gap**: Oracle1 writes about negative inferences (what something is NOT) in a fragmented way — they appear in The Snap as the inverse of confirmation, but she doesn't give them their own essay or formal treatment. Yet the code reveals that NEGATIVE tiles are a **first-class routing path** that bypasses ALL other verification. A negative match returns `BLOCKED` immediately — before compiled commands, before fuzzy match, before fallback.

This means: a NEGATIVE tile with confidence 0.51 (AMBIGUOUS) still blocks a COMPILED tile with confidence 1.0 if the patterns overlap. The negative constraint has higher routing priority than the compiled command.

This is a design choice with philosophical weight that Oracle1 doesn't address: **how much confidence does a negation need?** In the current code, ANY negative pattern match overrides everything. But if a negative tile has low confidence, should it block a verified command? Oracle1's writing would suggest: only a snap (confidence 1.0) should be allowed to negate with full force.

**The novel insight**: Negative constraints need their own confidence lifecycle, separate from positive tiles. A negative tile at 0.5 should NOT block a compiled command at 1.0 — but the current code allows it. The router needs a "negative confidence threshold" below which negative matches are downgraded to "suggested block" (log + continue routing) rather than "hard block."

More deeply: Oracle1's entire framework is about *positive* verification (is this true? at what confidence?). But the code reveals that *negative verification* (is this false?) is structurally different — it has priority, it has different routing, it has different lifecycle. The next Oracle1 essay should be about The Negative Room — a room full of what things are NOT, and how negations need their own verification path.

**Experiment**:
- Add `negation_confidence_threshold` to the Router. Below this threshold, a negative match is advisory (logged + continues routing). Above it, it's a hard block.
- Vary the threshold from 0.0 (always block, current behavior) to 1.0 (never block negative, defeats purpose).
- Measure: at what threshold does user satisfaction (confirmed correct routing) maximize and false positives minimize?
- Also measure: do negative tiles with confidence < 0.9 tend to be corrections to genuine positive tiles? If so, they should be reclassified as CORRECTION tiles with lower routing priority.

---

## Insight 10: The Router's "Fuzzy Match" Path Is a Realized Example of "Epsilon Accumulation as Structure" — Oracle1 Missed It Because She Was Looking at the Wrong Scale

**Oracle1 quote** (The Soft Room):
> "The epsilon doesn't go to zero. It accumulates. This is not drift as error. This is drift as structure."

**The code says** (`router.py:63-71`):
```python
# 3. Fuzzy match
existing = self.store.find_by_pattern(text)
if existing:
    best = existing[0]
    if best.confidence >= 0.7:
        best.record_use(True)
        return (RouteDecision.COMPILED, {
            "action": best.output_action,
            "confidence": best.confidence,
            "tile_id": best.tile_id,
        })
```

**The gap**: Look at what happens when the fuzzy match fires with confidence ≥ 0.7:
1. The tile's confidence gets +0.02 (epsilon accumulation!)
2. The tile's output_action gets routed as if COMPILED
3. If correct, another +0.02 on the next fuzzy match

The fuzzy match path is a **self-reinforcing epsilon cycle**: each fuzzy match increases confidence by 0.02. Over 14 fuzzy matches, a tile goes from 0.70 to 0.98. The "accumulated drift" from the "drift" of repeated fuzzy matches IS the structure that pushes the tile toward compilation.

Oracle1 writes about epsilon accumulation at the **room level** — cross-tile patterns accumulating into discovery. But the code implements epsilon accumulation at the **router level** — per-tile, per-interaction, within the matching loop. The fuzzy match path IS the epsilon accumulator. It's just operating on individual tiles, not on rooms.

**The novel R&D insight**: The router's fuzzy match is a miniature epsilon accumulator in production. It compiles tiles through repeated correct fuzziness. This means:
1. **Oracle1's epsilon accumulator (Gap 1 in synthesis) already exists** — it's the fuzzy match path. It just needs to be generalized from per-tile to cross-tile.
2. **The mechanism is serial, not parallel** — each fuzzy match adds epsilon to ONE tile. Oracle1's vision requires adding epsilon to MULTIPLE related tiles simultaneously when one tile succeeds. This is the leap from the code's "vertical accumulation" (deeper on one tile) to Oracle1's "horizontal accumulation" (broader across tiles).

**Experiment**:
- When a fuzzy match correctly routes, find all tiles with `parent_tile_id` targeting the same `output_action` pattern. Add +0.01 to their confidence (half the standard boost).
- This is "horizontal epsilon spillover" — success on one tile weakly strengthens related tiles.
- Measure: does horizontal spillover reduce time-to-compile for families of related commands? Does it increase false positives (tiles that shouldn't be strong become strong via association)?
- This directly tests whether Oracle1's horizontal accumulation thesis is implementable within the current architecture.

---

## Summary Cross-Cutting Pattern: The Great Inversion

Across all 10 insights, a single unifying pattern emerges:

**Oracle1 describes a top-down system. The code implements a bottom-up system. The inversion is the R&D opportunity.**

- Oracle1 writes about rooms with dials that abstractly control model/code blend. The code implements priority chains where each step is discrete.
- Oracle1 writes about epsilon accumulation as a room-level, emergent property. The code implements it as a per-tile, mechanical process.
- Oracle1 writes about the snap as a philosophical commitment. The code implements it as an engineering threshold (0.975) with a 50-line compilation function.
- Oracle1 writes about holonomy as a constraint-checking principle. The code implements it as a graph algorithm with no tile-level analog.

**The inversion teaches us**: Oracle1's metaphor is a *post-hoc rationalization* of mechanisms that were built bottom-up. The code was designed pragmatically (97.5% threshold from data, priority chain from rule-engine patterns, 5:1 asymmetry from experience). Oracle1 looked at the emergent behavior and reverse-engineered a philosophical framework.

This means: the gap between Oracle1 and the code is not an implementation gap — it's an *abstraction gap*. The code has mechanisms that Oracle1's metaphor doesn't capture (fuzzy-compiled zone, negative-priority override, per-tile epsilon). And Oracle1's metaphor describes patterns the code could support but hasn't explicitly exposed (cross-tile accumulation as a query, boundary-proximity as a search heuristic, tile-lifecycle holonomy).

**The R&D path forward**: Don't build Oracle1's constructs from scratch. Build **queries and aggregations over existing data structures** that surface the patterns Oracle1 describes. The primitives exist. The APIs don't.
