# FLUX Ecosystem Analysis

**Author:** SuperInstance AI  
**Date:** 2026-05-25  
**Version:** 1.0  
**Status:** Comprehensive audit of all flux repositories and cross-cutting connections

---

## 1. Executive Summary

The FLUX ecosystem is a distributed music-mathematics-computation framework that spans six repositories and two organizational contexts. At its core, it encodes musical structure as tensor algebra over Eisenstein lattices, routes computation across heterogeneous AI models, and enforces physical conservation laws on learned representations.

**What it is:** A fleet-coordinated system where musical events are 4D tensors quantized onto hexagonal lattices, AI models are dispatched based on capability staging, and spectral conservation laws constrain the dynamics of coupled nonlinear systems. The ecosystem has its own virtual machine (FLUX VM), its own ISA (flux-isa), and its own bytecode format with versioned compatibility.

**What connects:** Everything connects through the Eisenstein A₂ lattice. Rhythm snaps to it (EisensteinSnap), voice leading traverses it (PLR groups), neural representations are constrained by it (conservation law γ + H), and the fleet's Hebbian learning operates on coupling matrices whose spectral properties are conservation-verified.

**What's missing:** Julia implementations (planned), CUDA kernels for the VM, hyperbolic geometry integration with actual fleet routing, and genome-to-tensor compilation.

| Repository | Location | Files | Languages | Maturity |
|---|---|---|---|---|
| flux-tensor-midi | /tmp/flux-tensor-midi | 6,062 | Python, Rust, C, Shell | Production |
| sunset-ecosystem | /tmp/sunset-ecosystem | ~60 | Python | Experimental |
| flux-algebra | (planned) | 8 modules | Python | Stub |
| flux-genome | (upgrading) | — | Python | Stub |
| flux-hyperbolic | (upgrading) | — | Python | Stub |
| spectral-conservation | (in flux-tensor-midi) | ~10 | Rust | Production |

---

## 2. Repo-by-Repo Deep Dive

### 2.1 flux-tensor-midi (6,062 files, production-grade)

The monorepo. Contains the entire fleet coordination stack, the mathematical kernel, the VM toolchain, and 20+ Rust crates.

#### 2.1.1 Language Breakdown

| Language | Files | Notes |
|---|---|---|
| Markdown (.md) | 1,921 | Documentation-heavy culture |
| Python (.py) | 988 | Fleet orchestration, experiments, PLATO |
| Rust (.rs) | 427 | VM, ISA variants, spectral conservation, math kernels |
| C/H | 128 | Eisenstein bridge, benchmarks |
| Shell | ~200 | Build scripts, fleet tools |

#### 2.1.2 Key Components

##### TensorMIDIEvent (4D Structure)

The fundamental data type. Each musical event is a 4D tensor:

```
TensorMIDIEvent[time, pitch, velocity, channel]
```

Quantized to the Eisenstein A₂ lattice via `EisensteinSnap`, which maps continuous (x,y) coordinates to the nearest hexagonal lattice point. The snap result is a 16-byte packed struct:

```c
typedef struct {
    float    error;       // snap distance
    uint16_t dodecet;     // 12-bit constraint state (nibble-packed)
    uint8_t  chamber;     // Weyl chamber 0-5
    uint8_t  flags;       // bit0: is_safe, bit1: parity
    int32_t  snap_a;      // Eisenstein a-coordinate
    int32_t  snap_b;      // Eisenstein b-coordinate
} eisenstein_result_t;
```

##### FluxVector (9-Channel INT8)

The embedding space for fleet tiles and room states. 9 channels of INT8:

| Channel | Name | Emotional Axis |
|---|---|---|
| 0 | Arousal | Harmonic Tension |
| 1 | Valence | Harmonic Tension |
| 2 | Dominance | Harmonic Tension |
| 3 | Uncertainty | Rhythmic Complexity |
| 4 | Novelty | Rhythmic Complexity |
| 5 | Relevance | Rhythmic Complexity |
| 6 | Competence | Spectral Density |
| 7 | Affiliation | Spectral Density |
| 8 | Urgency | Spectral Density |

These map to three control dials: Harmonic Tension (0-2), Rhythmic Complexity (3-5), Spectral Density (6-8).

##### Fleet Router (`bin/fleet_router.py`)

Routes queries to the cheapest safe model. Key classes:

```python
@dataclass
class ModelStage:
    name: str
    stage: int           # 1-4 capability tier
    echo_rate: float     # hallucination baseline
    accuracy: float      # domain accuracy
    is_thinking: bool    # chain-of-thought capable
    is_free: bool        # zero-cost inference
    provider: str        # API endpoint
    model_id: str        # full routing ID
```

Fleet models are classified into four stages:
- **Stage 4** (FULL): Seed-2.0-mini, Seed-2.0-code — notation-immune
- **Stage 3** (CAPABLE): Hermes-405B, Qwen3-235B — need activation keys
- **Stage 2** (META_ECHO): deepseek-chat — needs pre-computed arithmetic
- **Stage 1** (ECHO): Minimal capability, not currently in fleet

##### Fleet Translator V2 (`fleet_translator_v2.py`, ~1413 lines)

The activation-key model. Solves the "vocabulary wall" problem where LLMs fail on domain-specific math notation but succeed on natural language descriptions. Based on Studies 46 and 56:

- Study 46: Unicode ²=0% accuracy, `a*a`=22%, natural language=67%, step-by-step=~100%
- Study 56: The effect is math-specific; non-math domains work fine with raw notation

```python
class ModelStage(IntEnum):
    NONE = 0
    ECHO = 1       # can barely echo, no math
    META_ECHO = 2  # follows meta-prompts, spotty math
    CAPABLE = 3    # handles arithmetic, struggles with domain vocab
    FULL = 4       # understands domain vocabulary natively
```

##### Conservation-Hebbian Kernel (`bin/conservation_hebbian.py`, ~540 lines)

Integrates the fleet conservation law into Hebbian learning:

```
γ + H = 1.283 - 0.159·log(V) ± ε
```

Where γ is the spectral gap and H is participation entropy. The kernel projects weight matrices back onto the conservation manifold after each update, preventing:
- Runaway connectivity (all tiles flow to one room)
- Runaway diversity (no specialization)
- Numerical instability in long-running processes

```python
class ConservationHebbianKernel:
    def __init__(self, n_rooms: int, V: int):
        self.n_rooms = n_rooms
        self.V = V  # fleet volume
        
    def update(self, pre_activations, post_activations):
        # Standard Hebbian update
        # Then project onto conservation manifold
        ...
    
    def conservation_report(self) -> dict:
        # Returns {gamma, H, predicted, deviation, conserved, corrections}
        ...
```

##### Expert-Hebbian Bridge (`expert_hebbian_bridge.py`, ~1015 lines)

Connects 9 expert daemons to 9 Hebbian rooms:

```
Expert Daemons (9) → ExpertRoomAdapter → Hebbian Network
       │                  │                    │
       ▼                  ▼                    ▼
ExpertRoomAdapter  ExpertCouplingMatrix  ConservationHebbianKernel
```

Enforces conservation constraints on cross-consultation coupling. Dashboard on port 8850.

##### PLATO Server Integration (`.local-plato/`)

Local PLATO runtime — the agent's external cortex:

```python
@dataclass
class Tile:
    tile_id: str
    room: str
    domain: str
    question: str
    answer: str
    source: str
    tags: List[str]
    confidence: float
    timestamp: float

@dataclass
class LocalRoom:
    name: str
    domain: str
    tiles: List[Tile]
    last_updated: float
    tile_count: int
    summary: str
```

Three-layer architecture:
1. **Tiles on disk** (SQLite ~10MB) — full fidelity, slow boot
2. **Rooms in memory** (dicts) — fast queries, O(1) by room name
3. **AgentField tensor** (numpy-free) — coupling dynamics, O(1) by index

##### Flux Vector Twin (`.local-plato/flux_vector_twin.py`)

Hash-based embedding store for PLATO tiles. 128-dimensional embeddings via character n-gram features projected into the FluxVector space. 100ns query time (vs 50ms for API calls). No GPU dependency — the hashing IS the Eisenstein lattice snap in feature space.

##### Rust fleet-math-c Components

The C/Rust bridge for high-performance math:

- `eisenstein_bridge.c` (231 lines): SIMD-ready Eisenstein A₂ lattice snapping
- `eisenstein_bridge.h`: Packed result struct for AVX-512 consumption (4 results per zmm register)
- `crosscheck.rs`: Rust reference implementation for cross-verification
- `verify_accuracy.c` (374 lines): Accuracy validation suite
- `src/lib.rs`: Safe Rust FFI wrapper with `SnapResult` type

##### FLUX ISA Mini (`flux-isa-mini/`)

Minimal 21-opcode stack VM in Rust, designed for embedded targets:

```rust
#[repr(u8)]
pub enum FluxOpcode {
    Add, Sub, Mul, Div, Mod,           // Arithmetic (5)
    Eq, Lt, Gt, Lte, Gte,             // Comparison (5)
    Assert, Check, Validate, Reject,   // Constraint (4)
    Load, Push, Pop,                   // Stack (3)
    Snap, Quantize,                    // Transform (2)
    Halt, Nop,                         // Control (2)
}
```

VM runs in 256 bytes of stack (32 × f64), no heap, no std. `FluxResult` captures outputs, constraint satisfaction, and step count.

##### Spectral Conservation (`spectral-conservation/`)

Rust crate tracking the first integral I(x) = γ(x) + H(x):

```rust
pub enum Alert { None, Warning, Chop }
pub enum Regime { Structural, Dynamical, Transitional, Degraded }

pub struct SpectralState {
    pub eigenvalues: Vec<f64>,
    pub spectral_gap: f64,       // γ = λ₁ - λ₂
    pub participation_entropy: f64,  // H
    pub conservation_value: f64, // I = γ + H
}
```

Three regimes:
- **Structural** (rank-1 coupling): exact conservation
- **Dynamical** (full-rank, stable shape): CV < 0.015
- **Transitional** (near rank-1): CV 0.03-0.05

##### Room Play V2 (`room_play_v2.py`)

Extended room model with five subsystems:

1. **GaugeConnection** — measuring phase between rooms (phase velocity, phase locking, gauge curvature)
2. **FormatBridge** — rooms speaking different substrate languages
3. **TimeAnalogue** — time as continuous analogue variable
4. **EratosthenesMeasurement** — two rooms measuring the same shadow
5. **JamSessionV2** — integrates all of the above

##### FLUX Programs (`flux-programs/programs/`)

Example programs in FLUX-C style:

- `constraint_check.flux`: Hard R1 ≤ R0 ≤ R2 enforcement
- `eisenstein_snap.flux`: Float→Eisenstein integer lattice snapping
- `bloom_filter.flux`: Probabilistic membership
- `agent_coordinate.flux`: Multi-agent coordination
- `intent_align.flux`: Intent alignment protocol
- `temporal_snap.flux`: Temporal quantization

#### 2.1.3 Rust Crate Map (20+ crates)

| Crate | Purpose |
|---|---|
| flux-isa | Full 35-opcode ISA |
| flux-isa-mini | 21-opcode embedded subset |
| flux-isa-edge | Edge deployment variant |
| flux-isa-std | Standard library operations |
| flux-isa-thor | High-performance variant |
| flux-ast | Abstract syntax tree |
| flux-compiler-workspace | Compiler toolchain |
| flux-contracts | Smart contract integration |
| flux-hardware | Hardware abstraction (build.rs) |
| flux-provenance | Provenance tracking |
| flux-transport | Network transport layer |
| flux-verify-api | Verification API |
| spectral-conservation | γ+H conservation tracker |
| plato-engine | PLATO room execution engine |
| plato-kernel-constraints | Kernel-level constraints |
| plato-mud | MUD-style room interface |
| snapkit-rs / snapkit-rust | Snap operation toolkit |
| holonomy-bounded | Bounded holonomy tracking |
| guard2mask / guardc | Guard-to-mask compilation |
| deadband-rs | Deadband signal processing |
| cocapn-cli | CLI interface |
| cocapn-glue-core | Core glue layer |
| constraint-theory-core-cuda | CUDA constraint kernels |
| ct-demo | Constraint theory demo |

#### 2.1.4 Maturity Assessment

| Component | Maturity | Evidence |
|---|---|---|
| Fleet Router | Production | 6,000+ trials, 84% cost reduction |
| Fleet Translator | Production | Study 46/56 validated |
| Eisenstein Bridge | Production | C/Rust cross-verified |
| Conservation Kernel | Production | Empirically validated (CV < 0.03) |
| FLUX ISA Mini | Production | Runs on embedded (256B stack) |
| Spectral Conservation | Production | Rust crate with nalgebra |
| PLATO Server | Production | SQLite-backed, HTTP sync |
| Room Play V2 | Experimental | Gauge theory applied to rooms |
| Expert-Hebbian Bridge | Production | Dashboard on :8850 |
| CUDA kernels | Experimental | constraint-theory-core-cuda |

---

### 2.2 sunset-ecosystem (flux_vm + flux_compat)

The VM and compatibility layer. Two sub-packages inside sunset-ecosystem.

#### 2.2.1 flux_vm

Location: `/tmp/sunset-ecosystem/flux_vm/`

| File | Lines | Purpose |
|---|---|---|
| `ffi.py` | ~150 | Python ctypes wrapper for `libflux_vm.so` |
| `libflux_vm.so` | Binary | Compiled Rust VM shared library |

The FFI layer exposes one primary function:

```c
int flux_check_batch(
    float *latents,       // (n_rooms × latent_dim) float32
    int    n_rooms,
    int    latent_dim,
    float  min_bound,     // per-dimension lower bound
    float  max_bound,     // per-dimension upper bound
    float  max_l2,        // max L2 norm per room
    float  max_var,       // max variance per room
    unsigned int *violations  // output: 0=pass, 1=fail
);
```

Python wrapper:

```python
class FluxVM:
    def check_batch(self, latents: np.ndarray, *,
                    min_bound: float = -10.0,
                    max_bound: float = 10.0,
                    max_l2: float = 100.0,
                    max_var: float = 10.0) -> np.ndarray:
        """Returns (n_rooms,) uint8 — 0=pass, 1=fail."""
```

The VM validates room latent states against four constraint types: per-dimension bounds, L2 norm limits, variance limits, and composite batch checks. The compiled `.so` is a Rust artifact from `flux-vm-v3/src/ffi.rs`.

#### 2.2.2 flux_compat

Location: `/tmp/sunset-ecosystem/flux_compat/`

| File | Lines | Purpose |
|---|---|---|
| `__init__.py` | — | Package marker |
| `v2_bytecode.py` | ~160 | v2 binary format parser |
| `v3_module.py` | ~90 | v3 module representation |
| `opcode_map.py` | ~300 | v2→v3 opcode translation with 8 creative mappings |
| `compat.py` | ~70 | `load_v2(path) → v3.Module` top-level shim |

##### v2 Bytecode Format

```
Offset  Size  Meaning
0       4     Magic: b'FLX2'
4       1     Version minor
5       1     Flags (bit 0=constraints, bit 1=debug)
6       2     Constant pool count (N)
8       N*4   Constant pool (i32 LE)
...     2     Instruction count (M)
...     M*?   Instructions (opcode u8 + optional imm)
```

26 v2 opcodes grouped into: Stack (6), Arithmetic (6), Register (2), Constraint (5), Control (5), I/O (2), Debug (1).

##### v3 Bytecode Format

```
Header: b'FLX3' + version u16 + const_count u16
Constants: i32 LE array
Instructions: opcode u8 + optional immediate
```

56 v3 opcodes organized into 12 groups:
- Stack (7): Push, Pop, Dup, Swap, Over, Drop, LoadConst, Nop
- Arithmetic (8): Add, Sub, Mul, Div, Saturate, Min, Max, Abs
- Register (4): LoadReg, StoreReg, LoadRegVec, StoreRegVec
- Constraint (5): RangeCheck, BatchCheck, AccumulateMask, ClassifySeverity
- Proof (4): Prove, QueryBackward, Simplify, Validate
- Commit (2): HashCommit, Seal
- Vector (6): VecLoad, VecStore, VecRangeCheck, VecMaskMerge, VecReduce, VecGather
- Control (6): FwdJump, CondJump, CallBounded, Ret, Halt, Checkpoint
- Error (4): SetHandler, EmitEvent, Rollback, GetResult
- Parallel (4): ParDispatch, ParMerge, ParBarrier, ParReduce
- Snapshot (4): SnapRecord, SnapQuery, SnapHash, SnapVerify
- Stream (4): StreamOpen, StreamCheck, StreamBatch, StreamClose

##### v2 → v3 Creative Translations (8 cases)

| v2 Opcode | v3 Translation | Semantic Gap |
|---|---|---|
| `Check` | `RangeCheck + Validate` | v2 had no severity classification |
| `Assert` | `Prove + HashCommit` | v3 replaces hard traps with proof certificates |
| `Range` | `RangeCheck + ClassifySeverity` | Bounds unpacked from packed i8+i8 operand |
| `Batch` | `BatchCheck` | Stricter pre-conditions in v3 |
| `Accumulate` | `AccumulateMask` | Direct rename |
| `Jump` | `FwdJump + Nop` | v3 is forward-only for termination guarantees |
| `JumpZero` | `CondJump` | Same forward-only restriction |
| `Call` | `CallBounded` | v3 requires cycle bound (default 4096) |
| `Read` | `StreamOpen + StreamCheck + StreamBatch` | Port I/O → streaming constraints |
| `Write` | `StreamOpen + StreamBatch + StreamClose` | Port I/O → streaming constraints |
| `Break` | *(removed)* | No debugger breakpoint in v3 |

##### compat.py

```python
def load_v2(path: str) -> Module:
    """Load legacy v2 bytecode → translate → return v3 Module."""
```

Produces a `Module` with full disassembly support:

```python
@dataclass
class Module:
    version: int = 3
    constants: List[int]
    instructions: List[Instruction]
    constraints: List[ConstraintDef]
    metadata: Dict[str, Any]  # includes translated_from, v2 flags
    warnings: List[str]       # translation warnings with PC offsets
```

##### flux_vm_compat.py (top-level shim)

Redirect module per SPEC-FLUX-RESOLUTION §5:

```python
# Legacy import path → transparent redirect to v3
from flux_vm_compat import FluxVM  # loads v3 under the hood
```

#### 2.2.3 Maturity Assessment

| Component | Maturity | Evidence |
|---|---|---|
| flux_vm (ffi.py + .so) | Production | Compiled binary, ctypes bindings |
| v2 bytecode parser | Production | Complete format spec + parser |
| v3 module representation | Production | Serialization + disassembly |
| opcode_map | Production | 11 translations with warnings |
| compat.py | Production | End-to-end v2→v3 pipeline |

---

### 2.3 flux-algebra (planned, 8 modules)

Currently in planning/stub phase. Target modules:

| Module | Purpose |
|---|---|
| HarmonicRing | Ring structure on pitch classes (Z₁₂) |
| ChordIdeal | Ideal theory applied to chord voicings |
| PLRGroup | Parallel/Lead/Relative group operations (dihedral D₁₂) |
| TranspositionInversion | T/I group actions on pitch-class sets |
| TropicalHarmony | Tropical semiring applied to voice leading distances |
| TuningField | Field structure for microtonal tuning systems |
| AlgebraicTone | Algebraic number theory for just intonation ratios |
| Combinatorics | Set-class enumeration and classification |

These modules will formalize the musical algebra that flux-tensor-midi currently implements ad hoc.

---

### 2.4 flux-genome (upgrading)

Musical genome system. Target architecture:

- **MusicalGenome**: 25-gene system encoding musical traits (rhythm density, harmonic complexity, register preference, articulation, dynamics, etc.)
- **GeneticAlgorithm**: Selection, crossover, mutation over genome space
- **Tradition DNA**: Encoding cultural/stylistic traditions as genome distributions

Currently being upgraded from an earlier prototype.

---

### 2.5 flux-hyperbolic (upgrading)

Hyperbolic geometry for musical similarity:

- **Poincaré ball model**: Embedding musical traditions in hyperbolic space
- **Tradition embeddings**: Culture-specific embeddings with hierarchical structure
- **Hyperbolic distance**: Distance metric respecting musical hierarchy

Planned Julia implementations for performance-critical paths.

---

## 3. Cross-Cutting Connections

```
                        ┌─────────────────────────────────┐
                        │      flux-tensor-midi (mono)     │
                        │  ┌─────────────────────────────┐│
                        │  │ Fleet Router (:8100)         ││
                        │  │ Fleet Translator V2          ││
                        │  │ FluxVector (9-ch INT8)       ││
                        │  │ EisensteinSnap (A₂ lattice)  ││
                        │  │ Hebbian Learning + Conserv.  ││
                        │  │ Expert-Hebbian Bridge        ││
                        │  │ PLATO Server + Vector Twin   ││
                        │  │ Room Play V2 (gauge theory)  ││
                        │  └──────────┬──────────────────┘│
                        │             │                    │
                        │  ┌──────────┴──────────────────┐│
                        │  │ 20+ Rust Crates              ││
                        │  │ flux-isa-mini (21 opcodes)   ││
                        │  │ flux-isa (35 opcodes)        ││
                        │  │ spectral-conservation (γ+H)  ││
                        │  │ fleet-math-c (C bridge)      ││
                        │  │ plato-engine, flux-compiler  ││
                        │  │ constraint-theory-core-cuda  ││
                        │  └──────────┬──────────────────┘│
                        └─────────────┼────────────────────┘
                                      │
                                      │ libflux_vm.so
                                      ▼
                        ┌─────────────────────────────────┐
                        │      sunset-ecosystem            │
                        │  ┌─────────────────────────────┐│
                        │  │ flux_vm/                     ││
                        │  │   ffi.py (ctypes wrapper)    ││
                        │  │   libflux_vm.so (Rust binary)││
                        │  │                              ││
                        │  │ flux_compat/                 ││
                        │  │   v2_bytecode.py (parser)    ││
                        │  │   v3_module.py (IR)          ││
                        │  │   opcode_map.py (11 trans.)  ││
                        │  │   compat.py (load_v2→v3)     ││
                        │  └─────────────────────────────┘│
                        └─────────────────────────────────┘
                                      │
                    ┌─────────────────┼─────────────────┐
                    │                 │                  │
                    ▼                 ▼                  ▼
          ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
          │ flux-algebra │  │ flux-genome  │  │flux-hyperbolic│
          │ (planned)    │  │ (upgrading)  │  │ (upgrading)  │
          │              │  │              │  │              │
          │ HarmonicRing │  │ 25-gene DNA  │  │ Poincaré ball│
          │ PLRGroup     │  │ GA selection │  │ Trad. embeds │
          │ TropicalHarm.│  │ Tradition enc│  │ Hyp. distance│
          │ ChordIdeal   │  │              │  │ Julia (future│
          └──────┬───────┘  └──────────────┘  └──────────────┘
                 │
                 ▼
     ┌──────────────────────┐
     │ constraint-toolkit   │
     │ constraint-dialect   │
     │ (MLIR, future)       │
     └──────────────────────┘
```

### Connection Summary

| From | To | Connection |
|---|---|---|
| FluxVector 9-ch | VM batch check | Latents validated by libflux_vm.so |
| EisensteinSnap | flux-isa-mini `Snap` opcode | Hardware snap in VM |
| Hebbian coupling | spectral-conservation γ+H | Conservation-constrained learning |
| PLATO rooms | flux_vm rooms | Room latent states validated by VM |
| v2 bytecode | v3 module | flux_compat translation pipeline |
| Fleet Translator | Model stages | Routes math through notation-normalized path |
| flux-algebra PLRGroup | Eisenstein lattice | Voice leading geometry on A₂ lattice |
| flux-genome MusicalGenome | FluxVector | 25-gene → 9-channel encoding |
| flux-hyperbolic traditions | PLATO rooms | Hyperbolic room embeddings |
| Room Play V2 gauge | Conservation law | Gauge curvature ↔ spectral shape |

---

## 4. FluxVector ↔ Dials Mapping

The 9-channel INT8 FluxVector maps to three musical control dials:

### Dial 1: Harmonic Tension (Channels 0-2)

| Ch | Name | Musical Effect |
|---|---|---|
| 0 | Arousal | Dissonance level, chord tension |
| 1 | Valence | Major/minor quality, brightness |
| 2 | Dominance | Bass presence, root stability |

Combined effect: Controls how harmonically tense the music feels. High arousal + negative valence = high tension. Low arousal + positive valence = resolution.

### Dial 2: Rhythmic Complexity (Channels 3-5)

| Ch | Name | Musical Effect |
|---|---|---|
| 3 | Uncertainty | Syncopation, rhythmic ambiguity |
| 4 | Novelty | Pattern variation, fill density |
| 5 | Relevance | Groove alignment, beat strength |

Combined effect: Controls rhythmic interest. High uncertainty + novelty = complex polyrhythmic patterns. Low = steady pulse.

### Dial 3: Spectral Density (Channels 6-8)

| Ch | Name | Musical Effect |
|---|---|---|
| 6 | Competence | Timbral clarity, spectral coherence |
| 7 | Affiliation | Instrument blend, ensemble cohesion |
| 8 | Urgency | Dynamic intensity, loudness trajectory |

Combined effect: Controls textural density. High urgency + high competence = tutti fortissimo. Low = sparse, transparent texture.

### Mapping Architecture

```
FluxVector[9] (INT8)          Dials[3] (float)
├── [0:3] Arousal/Valence/Dominance ──→ Harmonic Tension ∈ [-1, 1]
├── [3:6] Uncertainty/Novelty/Relevance ──→ Rhythmic Complexity ∈ [0, 1]
└── [6:9] Competence/Affiliation/Urgency ──→ Spectral Density ∈ [0, 1]
```

The EisensteinSnap quantization ensures that dial movements trace valid lattice paths — no "impossible" musical states. Each dial triplet (a,b,c) maps to a hexagonal lattice coordinate, and the snap ensures the resulting state lies on the A₂ lattice.

---

## 5. Eisenstein Lattice ↔ Voice Leading

### The Dual Role of A₂

The Eisenstein A₂ lattice serves double duty in the flux ecosystem:

1. **Rhythmic quantization**: `EisensteinSnap` maps continuous time points to hexagonal rhythmic grids
2. **Voice leading geometry**: The same lattice structure appears in the mathematical theory of voice leading

### Connection to flux-algebra

The planned `PLRGroup` module in flux-algebra implements the Parallel/Lead/Relative group (isomorphic to the dihedral group D₁₂), which acts on the set of major and minor triads. The key insight:

**Voice leading distances between chords can be measured as Eisenstein lattice distances.**

```
Eisenstein lattice point (a, b) ←→ Chord voicing
  a = voice leading offset (semitones in outer voices)
  b = voice leading offset (semitones in inner voices)
```

The `TropicalHarmony` module will use tropical arithmetic (min-plus semiring) to compute optimal voice leadings, where the tropical distance is the Eisenstein distance.

### Weyl Chambers and Chord Quality

The six Weyl chambers of the A₂ lattice (0-5) correspond to six chord quality regions:

| Chamber | Chord Quality | Example |
|---|---|---|
| 0 | Major triad | C-E-G |
| 1 | First inversion major | E-G-C |
| 2 | Minor triad | A-C-E |
| 3 | First inversion minor | C-E-A |
| 4 | Diminished | B-D-F |
| 5 | Augmented | C-E-G♯ |

The `eisenstein_result_t.chamber` field (0-5) directly encodes chord quality after snapping.

### Combinatorics Connection

The planned `Combinatorics` module in flux-algebra will enumerate set classes using the Eisenstein lattice as a geometric substrate. The 12-bit `dodecet` field in `eisenstein_result_t` encodes the constraint state at each lattice point, enabling rapid set-class classification.

---

## 6. Hebbian Learning ↔ Fleet Coordination

### Architecture

The fleet uses Hebbian learning as the coordination mechanism between AI models:

```
Query arrives → Fleet Router → Model dispatch → Response
                                         │
                                         ▼
                              Expert-Hebbian Bridge
                                         │
                    ┌────────────────────┼────────────────────┐
                    ▼                    ▼                    ▼
             Room weights         Cross-consultation    Conservation
           (coupling matrix)       coupling              check
                    │                    │                    │
                    ▼                    ▼                    ▼
           Hebbian update         ExpertCouplingMatrix   γ + H = const?
                    │                    │                    │
                    └────────────────────┼────────────────────┘
                                         │
                                         ▼
                              Project onto conservation manifold
                              if deviation > ε(V)
```

### Conservation Law as Fleet Regularizer

The fleet conservation law `γ + H = 1.283 - 0.159·log(V) ± ε` acts as a regularizer on Hebbian coupling matrices:

- **γ (spectral gap)**: Measures connectivity concentration — how much the fleet relies on a single model
- **H (participation entropy)**: Measures model diversity — how evenly queries are distributed
- **V (fleet volume)**: Number of active models

The intercept `1.283` and coefficient `-0.159` were empirically calibrated across 6,000+ trials. The σ(V) table:

| V | σ | Meaning |
|---|---|---|
| 5 | 0.070 | Tight tolerance for small fleets |
| 10 | 0.065 | |
| 20 | 0.058 | |
| 30 | 0.050 | Default fleet size |
| 50 | 0.048 | |
| 100 | 0.042 | |
| 200 | 0.038 | Loose tolerance for large fleets |

### Hebbian Update Protocol

1. **Pre-activation**: Query features from Fleet Translator
2. **Post-activation**: Model response quality scores
3. **Hebbian update**: ΔW = η · pre · postᵀ (standard outer product rule)
4. **Conservation projection**: If |γ + H - predicted| > ε(V), project W back onto manifold
5. **Correction logging**: Record when and why corrections occurred

### 9-Expert Model

The Expert-Hebbian Bridge maps 9 expert daemons to 9 Hebbian rooms. Each expert specializes in a domain:

| Expert | Domain | Hebbian Room |
|---|---|---|
| 1 | Arithmetic | Room 0 |
| 2 | Reasoning | Room 1 |
| 3 | Code generation | Room 2 |
| 4 | Translation | Room 3 |
| 5 | Summarization | Room 4 |
| 6 | Creative writing | Room 5 |
| 7 | Analysis | Room 6 |
| 8 | Constraint checking | Room 7 |
| 9 | Meta-orchestration | Room 8 |

Cross-consultation coupling is tracked via `ExpertCouplingMatrix`, which records which experts consult which others. This coupling matrix IS the Hebbian weight matrix, and it is conservation-constrained.

---

## 7. What's Missing / Next Steps

### Priority 1: Foundation Completion

| Item | Status | Effort | Impact |
|---|---|---|---|
| flux-algebra implementation | Stub | 3-4 weeks | Enables formal voice leading, set-class theory |
| Julia bindings for fleet-math-c | Not started | 1-2 weeks | Performance-critical math in Julia |
| CUDA kernels for FLUX VM | Experimental | 2-3 weeks | Batch validation at GPU speed |
| flux-genome 25-gene system | Upgrading | 2-3 weeks | Enables genetic music evolution |

### Priority 2: Integration

| Item | Status | Effort | Impact |
|---|---|---|---|
| FluxVector → flux-algebra bridge | Not started | 1 week | Connect tensor space to algebra |
| Eisenstein voice leading module | Not started | 2 weeks | Musical geometry in practice |
| Hyperbolic fleet routing | Planned | 2-3 weeks | Better model similarity metrics |
| PLATO ↔ flux-algebra sync | Not started | 1 week | Room embeddings in algebraic form |

### Priority 3: Advanced Features

| Item | Status | Effort | Impact |
|---|---|---|---|
| constraint-dialect (MLIR) | Planned | 4-6 weeks | Compiler-grade constraint verification |
| Genome → FluxVector compilation | Not started | 3-4 weeks | Genetic traits → tensor representation |
| Hyperbolic tradition embeddings | Upgrading | 2-3 weeks | Cultural music similarity |
| Room Play V3 (full gauge theory) | Not started | 3-4 weeks | Differential geometry on rooms |
| Fleet consensus protocol | Lesson 16 exists | 2 weeks | Multi-model agreement |

### Priority 4: Documentation & Testing

| Item | Status | Effort | Impact |
|---|---|---|---|
| Cross-repo integration tests | Minimal | 1-2 weeks | Prevent regression across repos |
| FLUX VM v3 spec document | Implicit in code | 1 week | Onboarding for new contributors |
| Fleet calibration suite | Partial (studies) | 1 week | Reproducible benchmarks |
| API documentation | Scattered | 2 weeks | Developer experience |

### Architecture Debt

1. **Monorepo split**: flux-tensor-midi contains 20+ Rust crates and hundreds of Python modules. Consider splitting into focused packages.
2. **VM versioning**: v2→v3 compat is solid, but v3→v4 planning should start now.
3. **Type safety**: FluxVector INT8 ↔ float conversions lack formal type checking.
4. **Conservation law generalization**: Current law is specific to fleet coupling. Needs generalization for flux-hyperbolic and flux-genome.

---

## Appendix A: Key File Index

### flux-tensor-midi

| Path | Lines | Description |
|---|---|---|
| `ARCHITECTURE-BLUEPRINT.md` | ~530 | Master architecture document |
| `room_play_v2.py` | ~1150 | Extended room model with gauge theory |
| `fleet_translator_v2.py` | ~1413 | Activation-key model translator |
| `expert_hebbian_bridge.py` | ~1015 | Expert→Hebbian bridge |
| `bin/fleet_dispatch.py` | ~600 | Fleet task dispatch |
| `bin/fleet-navigator.py` | ~715 | Fleet navigation |
| `bin/fleet_translator.py` | ~992 | V1 translator |
| `bin/fleet_router.py` | ~300 | Model routing |
| `bin/flux_verifier.py` | ~435 | Flux verification |
| `bin/conservation_hebbian.py` | ~540 | Conservation kernel |
| `bin/lighthouse.py` | ~443 | Fleet lighthouse service |
| `bin/ledger.py` | ~408 | Fleet transaction ledger |
| `.local-plato/local_plato.py` | ~420 | Local PLATO runtime |
| `.local-plato/flux_vector_twin.py` | ~330 | Vector twin embeddings |
| `.local-plato/plato_sync.py` | ~460 | PLATO sync protocol |
| `fleet-math-c/eisenstein_bridge.c` | ~230 | C Eisenstein snap |
| `fleet-math-c/eisenstein_bridge.h` | ~95 | C header with packed structs |
| `fleet-math-c/crosscheck.rs` | ~95 | Rust cross-verification |
| `spectral-conservation/src/lib.rs` | ~440 | γ+H conservation (Rust) |
| `flux-isa-mini/src/vm.rs` | ~220 | Stack VM (256B RAM) |
| `flux-isa-mini/src/opcode.rs` | ~65 | 21-opcode enum |

### sunset-ecosystem

| Path | Lines | Description |
|---|---|---|
| `flux_vm/ffi.py` | ~150 | Python ctypes for libflux_vm.so |
| `flux_compat/v2_bytecode.py` | ~160 | v2 binary parser |
| `flux_compat/v3_module.py` | ~90 | v3 module IR |
| `flux_compat/opcode_map.py` | ~300 | v2→v3 opcode translation |
| `flux_compat/compat.py` | ~70 | Top-level compatibility shim |
| `flux_vm_compat.py` | ~40 | Legacy import redirect |

---

## Appendix B: Conservation Law Derivation

The spectral first integral I(x) = γ(x) + H(x) where:

- **γ(x) = λ₁ - λ₂**: Spectral gap of the coupling matrix C(x)
- **H(x) = -Σ pᵢ log pᵢ**: Participation entropy, where pᵢ = λᵢ/Σλⱼ

Empirically discovered to be approximately conserved:
```
I = 1.283 - 0.159·log(V) ± σ(V)
```

The commutator ||[D,C]|| (where D is the diagonal of eigenvalues) predicts conservation quality with r = 0.965. When D and C nearly commute, the spectral shape is preserved along trajectories.

Three regimes:
1. **Structural** (rank-1 C): I is exactly conserved — algebraic identity
2. **Dynamical** (full-rank, stable shape): CV < 0.015 — near-conservation
3. **Transitional** (near rank-1): CV 0.03-0.05 — marginal conservation

Reference: Forgemaster & Digennaro (2026). "Spectral Near-Conservation in Coupled Nonlinear Dynamics."

---

*End of analysis.*
