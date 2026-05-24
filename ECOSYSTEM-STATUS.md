# SuperInstance Ecosystem Status — 2026-05-21

## Summary

- **16 standalone Python packages** (12 core + 4 extracted)
- **11 npm packages** live
- **4 PyPI packages** live, **5 built** (429 rate-limited)
- **5 Rust crates** on crates.io
- **5/6 core packages at 10/10** beta score
- **LucidDreamer: 159 tests**, signal-chain integration, domain adapters

## Core Packages (Python)

| Package | Tests | Beta Score | PyPI | npm | GitHub |
|---------|-------|------------|------|-----|--------|
| plato-core | 21 | 10/10 ✅ | ✅ 0.1.0 | ✅ | ✅ |
| tensor-spline | 104 | 10/10 ✅ | ✅ 1.0.0 | ✅ | ✅ |
| eisenstein-embed | 97 | 10/10 ✅ | ✅ 0.1.0 | ✅ | ✅ |
| device-router | 42 | 10/10 ✅ | ⏳ 429 | ✅ | ✅ |
| triplet-miner | 53 | 10/10 ✅ | ⏳ 429 | ✅ | ✅ |
| plato-training | 489 | 10/10 ✅ | ⏳ 429 | ✅ | ✅ |
| micro-onnx | 28 | — | ✅ 0.1.0 | ✅ | ✅ |
| training-throttle | 35 | — | ⏳ 429 | ✅ | ✅ |

## LucidDreamer (Flagship)

| Module | Purpose | Lines |
|--------|---------|-------|
| tiles.py | Tile system (7 types, confidence continuum) | ~200 |
| compiler.py | RigidFinder (97.5% compile threshold) | ~120 |
| router.py | 4-priority routing | ~150 |
| signal_chain.py | Dial/Room integration | ~245 |
| rooms.py | SoftRoom + HardRoom | ~173 |
| epsilon_accumulator.py | Confidence accumulation → snap | ~180 |
| signal_router.py | Router ↔ SignalChainRoom bridge | ~100 |
| domain.py | DomainAdapter ABC + 4 concrete adapters | ~198 |
| training.py | LoRA self-training, checkpoints | ~250 |
| bathymetry.py | Coverage mapping | ~100 |
| cocapn.py | Chatbot with mouse output | ~300 |
| chart.py | Chart intelligence | ~80 |
| simulators.py | 5 simulators | ~200 |

**159 tests passing** | Beta R1: 8.5/10 | R3 pending

## Extracted Packages

| Package | Tests | npm | Focus |
|---------|-------|-----|-------|
| collective-ai | 16 | ✅ | Simulation-first prediction loop |
| agent-field | 16 | ✅ | Standalone field clock |
| commit-predictor | 12 | ✅ | Numpy dense network |
| swarm-rooms | 8 | ✅ | Eisenstein snap with CPU fallback |

## Rust Crates (crates.io)

| Crate | Version |
|-------|---------|
| constraint-theory-core | 2.2.0 |
| constraint-theory-core-cuda | 0.1.0 |
| constraint-crdt | 0.5.0 |
| spectral-conservation | 0.1.0 |
| plato-deadband | 0.1.1 |

## Publishing Status

- **PyPI live (4)**: plato-core, tensor-spline, eisenstein-embed, micro-onnx
- **PyPI built, waiting (5)**: device-router, triplet-miner, plato-training, training-throttle, luciddreamer
- **npm (11/11)**: all live
- **crates.io (5/5)**: all live

## Oracle1 Integration

- **152 essays** indexed, ~210K words, 7 themes
- **ORACLE1-SYNTHESIS.md**: 6.5/10 alignment score
- **RD-INSIGHTS.md**: 10 novel findings (97.5% dead zone, dial as switch-array, Great Inversion)
- **Key modules built from Oracle1**: SoftRoom, HardRoom, EpsilonAccumulator, SignalChainRoom

## Agent Pipeline

- **Kimi CLI** → heavy architecture generation
- **DeepSeek** → analysis and R&D insights
- **Z.ai (GLM-5.1)** → implementation, tests, fixes
- **Claude Code** → review and architecture refinement
