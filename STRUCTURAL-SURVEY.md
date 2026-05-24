# Structural Survey — The SuperInstance Meta-Ecosystem

## 1. What Exists: 130 Unique Repos

### Tier 1: Shipped Products (17)
- **Python/PyPI (4 live)**: plato-core, tensor-spline, eisenstein-embed, micro-onnx
- **PyPI blocked (5)**: plato-training, device-router, triplet-miner, training-throttle, luciddreamer (all 429)
- **npm (11 live)**: 7 core + 4 extracted (collective-ai, agent-field, commit-predictor, swarm-rooms)
- **crates.io (5 live)**: deadband-rs + 4 others
- **Monolith**: sunset-ecosystem (360 tests, unpushed to PyPI)

### Tier 2: Active R&D (50+)
- **Flux ecosystem** (21 repos): A multi-language compiler family (ALGOL, COBOL, Chapel, Fortran, MUMPS, PL/I, SNOBOL → common IR). flux-engine-c is the highest-rated (10/10), flux-isa for hardware targeting
- **Fleet ecosystem** (9 repos): Resonance/consensus (fleet-resonance 4.7GB), murmurs/pubsub, router, health monitor, stack
- **Constraint theory** (12 repos): Rust+Python cross-language constraint solver, ecosystem monorepo, MLIR bridge, LLVM bridge, demos
- **Guard systems** (2): guardc-v3, guard2mask

### Tier 3: Stale / Inactive (~40)
- lucineer (32 days), luciddreamer-os (36), pasture-ai (36), autoclaw (37)
- Various old attempts: tri-quarter-toolbox (82 days), claude/ (20K-day git test)

## 2. Higher-Level Patterns (From Step Back)

### A. The Meta-Pattern: COLLECT → SELECT → COMPILE
Found in EVERY ecosystem. The tripartite structure is the universal grammar:

| System | COLLECT | SELECT | COMPILE |
|--------|---------|--------|---------|
| Oracle1 | Soft Room | Snap | Hard Room |
| Sunset | Ethos | Trinity Score | Agent Lifecycle |
| Flux | Lang frontends | Common IR | Codegen |
| Fleet | All nodes | Resonance | Consensus |
| Constraint | All constraints | Proof search | Solved form |
| LucidDreamer | Tiles (fuzzy) | RigidFinder (97.5%) | Compiled tile |
| Nerve Fibers | Sensory tile | Hebbian routing | Compiled pathway |
| Distillation | Big model seeds | User ranking | Hint removal |
| Eusocial | Soft→Hard dial | EpsilonAccumulator | Adapter |
| Promotion | Recruit | Lieutenant | Captain |

**The physics**: Each tier has a configurable THRESHOLD that trades breadth for precision. 97.5% in RigidFinder, 0.3 spare capacity in backtesting, hint_level in distillation. The threshold IS the control surface.

### B. The Rhizome Problem
145 repos but they don't talk to each other. Each has its own CI, own versioning, own test suite. This is a CULTURE of extraction (build standalone packages) without a CONTRACTION mechanism (re-merge what's shared).

**Evidence**: 
- Every flux-* language has a separate Cargo.toml with the same base dependencies
- Every fleet-* package has its own health check pattern
- constraint-theory-py exists as both monorepo and individual crates
- 3 copies of AI-Writings (different casing)

### C. The Dead Zone
**~40 repos (30%) haven't been touched in 8+ days.** These are:

| Category | Count | Examples |
|----------|-------|----------|
| Old failed starts | 8 | tri-quarter-toolbox, autoclaw |
| Abandoned bridges | 7 | constraint-theory-* older variants |
| Ghost repos | 5 | lucineer, pasture-ai |
| Duplicate clones | 3 | AI-Writings/Ai-writings/ai-writings |
| One-shot experiments | 15 | multi-model-adversarial-testing etc. |

### D. Missing: A Unified Runtime
The sunset-ecosystem is the closest thing to a single orchestration layer, but it doesn't consume the other packages yet. The nerve fibers should be feeding into fleet's pubsub. The constraint solver should be the "logos room" logic engine. The flux compiler should be generating the micro-models.

## 3. Structural Roadmap

### Phase 1: Consolidation (do first)
1. **Prune dead repos** — archive 40 stale repos to SuperInstance/archive org
2. **Deduplicate AI-Writings** — keep 1 repo, drop 2
3. **Unify flux frontend boilerplate** — extract shared `flux-core` crate
4. **Unify fleet health patterns** — extract `fleet-base` crate

### Phase 2: Integration (build next)
1. **sunset-ecosystem consumes the other packages** — dependency matrix
2. **Nerve fibers → fleet pubsub** — nerve broadcasts are fleet murmurs
3. **Constraint solver = logos room** — proof search as logical investigation
4. **User ranking → personalization → reinforcement learning** — closes the loop

### Phase 3: Automation (ship)
1. **Automated PyPI + npm + crates.io pipeline** — single `./publish-all.sh`
2. **Cross-repo CI/CD** — when ship-repo changes, downstream packages auto-release
3. **Dependency graph visualization** — `./map-ecosystem.sh` generates mermaid
