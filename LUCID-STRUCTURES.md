# LucidDreamer — Rigid Structure Extractor

*From endless podcasting to compiled intelligence.*

## Origin Story

LucidDreamer was originally designed for **endless podcasting**: an AI that talks to itself overnight, iterating on ideas, debugging code, and exploring concept spaces while humans sleep. The key insight: most of what the model does during these sessions is *repetitive*. It walks the same paths, makes the same inferences, applies the same patterns.

That repetitiveness isn't waste — it's **ore**. Every repeated inference is a candidate for compilation.

## The Core Loop

```
┌─────────────────────────────────────────────────────┐
│  SOFT MODEL (the dreamer)                           │
│                                                     │
│  "Turn port 10 degrees"                             │
│       ↓ inference                                   │
│  [interprets as TURN_PORT(10)]                      │
│       ↓ execution                                   │
│  Autopilot receives command                         │
│       ↓ observation                                 │
│  Captain says "Roger turning 10 degrees"            │
│       ↓ VERIFIED TILE                               │
│                                                     │
│  Now the system knows:                              │
│  - "turn port X" maps to TURN_PORT(X)              │
│  - Captain confirms with "Roger turning X degrees"  │
│  - This pattern is 100% deterministic               │
│       ↓ COMPILATION                                 │
│  Compiled regex replaces inference                  │
│  Token cost: 0                                       │
└─────────────────────────────────────────────────────┘
```

## The Bathymetric Map

Every interaction is a sounding line. The map reveals where the bottom is solid (compiled code works) and where it's deep (inference still needed).

```
Command Space Coverage Map

                    Port    Starboard   Speed   Emergency
                 ──────────────────────────────────────────
Literal match    ████████  ████████    ██████  ███         ← compiled
Abstractions     ██████    ██████      ████    █           ← "1 o'clock"=30°
Captain dialect  █████     ████        ███                 ← "come left"
Unexplored       ░░░░      ░░░         ░░░░░░  ░░░░░░░░   ← still needs model

█ = rigid structure found, compiled, zero tokens
░ = unknown, soft model runs, costs tokens
```

The goal: **tile the ocean floor**. Every sounding that hits rock becomes compiled code. The model only swims in the deep water.

## How It Connects to flux-lucid

flux-lucid already has the machinery we need:

### 1. Dream Module (`dream.rs`) — The Explorer
- **DreamStyle::Surreal** (55% accuracy, high novelty) → Explore the full space of possible interpretations. "What could 'come left' mean? What about 'hard to port'? What about nautical slang?"
- **DreamStyle::Literal** (97.5% accuracy) → Verify real interactions. Captain says "turn port 10", system does TURN_PORT(10), captain confirms. That's a verified tile.
- **DreamStyle::Negative** (77.5% accuracy) → "Turn port" NEVER means speed up. Negative constraints are free knowledge.
- **Amnesia cliff at 10% coverage** → You need at least 10% coverage before generalization works. Below that, you're hallucinating.

### 2. Intent Module (`intent.rs`) — The Communicator
- 9-channel intent encoding captures *why* a command matters, not just *what* it does
- Alignment checking between captain intent and system interpretation
- Draft checking: "Is this command well-formed enough to trust?"

### 3. Constraint Compilation (`intent_compilation.rs`) — The Compiler
- Maps stakes to precision: life-critical autopilot commands get DUAL redundancy, casual queries get INT8
- Mixed-precision batch checking: most commands are advisory (cheap), a few are safety-critical (redundant)
- The throughput multiplier tells you how much you save by compiling

### 4. Head Direction (`head_direction.rs`) — The Spatial Frame
- 12 discrete orientations (every 30°) — maps directly to compass headings
- PositionedAgent = position + heading + speed — exactly what an autopilot needs
- Angular coherence checking for fleet coordination

### 5. Simulation First (`simulation_first.rs`) — The Predictor
- Before executing a command, predict the outcome
- File prediction as a tile, then compare against reality
- When prediction matches: no new tile needed (95% savings)
- When prediction fails: new tile created, model learns

## The Autopilot as Concrete Example

### Phase 1: Dream (Explore)
```
Soft model runs unconstrained:
  "turn port 10" → TURN_PORT(10)
  "turn port 20" → TURN_PORT(20)
  "come left 15" → TURN_PORT(15)
  "turn to 1 o'clock" → TURN_PORT(30)
  "hard to port" → TURN_PORT(45)
  "steer 270" → SET_HEADING(270)
  
DreamStyle::Surreal discovers:
  "make your heading northwest" → SET_HEADING(315)
  "come around to the west" → SET_HEADING(270)
  "put the wind on the beam" → depends on wind direction (needs context)
```

### Phase 2: Literal (Verify)
```
Captain says: "Turn port 10"
System interprets: TURN_PORT(10)
Autopilot executes: heading -= 10
Captain confirms: "Roger turning 10 degrees"
→ VERIFIED TILE: confidence=1.0, coverage=+1 sample
```

### Phase 3: Negative (Bound)
```
System learns what "turn port" does NOT do:
  - It does NOT change speed
  - It does NOT alter depth
  - It does NOT toggle autopilot mode
  
These negative constraints are FREE — no inference needed.
They come from observing what the system consistently doesn't do.
```

### Phase 4: Compile (Solidify)
```rust
// After enough tiles, the system compiles:

/// Compiled autopilot voice commands — zero inference, zero tokens
pub static AUTOPILOT_COMMANDS: &[CompiledCommand] = &[
    // Direct mappings (verified tiles from captain interactions)
    CompiledCommand::regex(r"turn port (\d+)", |deg| Action::TurnPort(deg)),
    CompiledCommand::regex(r"turn starboard (\d+)", |deg| Action::TurnStarboard(deg)),
    CompiledCommand::regex(r"steer (\d{1,3})", |hdg| Action::SetHeading(hdg)),
    
    // Abstractions (generalized from multiple tiles)
    CompiledCommand::regex(r"turn to (\d+) o'?clock", |hour| {
        Action::TurnPort((hour % 12) * 30)  // 1 o'clock = 30° port
    }),
    CompiledCommand::exact("hard to port", Action::TurnPort(45)),
    CompiledCommand::exact("steady", Action::HoldHeading),
    
    // Captain-specific dialect (learned from this captain's tiles)
    CompiledCommand::regex(r"come left (\d+)", |deg| Action::TurnPort(deg)),
    CompiledCommand::regex(r"come right (\d+)", |deg| Action::TurnStarboard(deg)),
    
    // Negative constraints (always true)
    // "turn port" NEVER → speed change, depth change, mode toggle
];

/// Personalization layer — learned from captain feedback
pub struct CaptainProfile {
    confirmation_template: String,  // "Roger {action}"
    voice_pace: f64,                // measured (not rushed)
    preferred_units: Units,         // degrees, not radians
    colloquialisms: HashMap<String, Action>,
}
```

### Phase 5: Fallback (Amnesia Cliff)
```rust
// For anything not in the compiled set, fall back to soft model
fn handle_command(input: &str) -> Action {
    // Try compiled commands first (free, instant)
    if let Some(action) = AUTOPILOT_COMMANDS.try_match(input) {
        return action;
    }
    
    // Try captain dialect (learned patterns)
    if let Some(action) = captain_profile.try_match(input) {
        return action;
    }
    
    // Fallback: soft model (costs tokens)
    let action = soft_model.interpret(input);
    
    // Log as potential future tile
    pending_tiles.push(UnverifiedTile {
        input,
        interpreted_as: action.clone(),
        timestamp: now(),
        coverage: 0.0, // unexplored
    });
    
    action
}
```

## The Token Economics

| Phase | Token Cost | Coverage |
|-------|-----------|----------|
| Dream (explore) | High (model runs freely) | 0% → builds map |
| Literal (verify) | 1 inference per command | +1 tile per verification |
| Negative (bound) | FREE (observation only) | +constraints for free |
| Compile (solidify) | ZERO (regex + lookup) | 100% for compiled set |
| Fallback (unknown) | Normal inference | 0% (still exploring) |

**Target: 95%+ of commands hit compiled code.** The soft model only fires for:
- New command patterns (captain uses novel phrasing)
- Context-dependent commands ("put the wind on the beam")
- Ambiguous inputs requiring reasoning

## The Endless Podcasting Connection

The original LucidDreamer concept was: let the model talk to itself overnight, exploring ideas and debugging code. The bathymetric map turns this from "interesting exploration" into **productive mining**:

1. **Overnight**: Model runs in Surreal mode, exploring the full space of possible interpretations. Discovers edge cases, novel phrasings, dialect variations. Each exploration is a DreamFragment.

2. **Morning**: Human reviews the exploration log. Confirms or rejects findings. Each confirmation becomes a verified tile. Each rejection is negative knowledge.

3. **During operations**: Compiled code handles 95%+ of interactions. Soft model handles the rest. Every soft-model interaction is logged as a potential tile.

4. **Continuous**: The bathymetric map grows. More ocean floor gets tiled. Token costs decrease over time. The system gets faster and cheaper as it learns.

## What We Need to Build

### New Module: `rigid_finder` (in flux-lucid or standalone)

```rust
/// Find rigid structures in soft model behavior.
/// 
/// A rigid structure is a region of the model's behavior space
/// where the output is deterministic — the same input always
/// produces the same output, regardless of temperature or context.
pub struct RigidFinder {
    /// Tiles collected so far
    tiles: Vec<VerifiedTile>,
    /// Dream fragments from exploration
    fragments: Vec<DreamFragment>,
    /// Current coverage map
    bathymetry: BathymetricMap,
    /// Compilation threshold (default: 0.975 from dream experiments)
    compile_threshold: f64,
}

impl RigidFinder {
    /// Explore: run model in dream mode to discover potential rigid structures
    pub fn explore(&mut self, input_space: &[&str]) -> Vec<DreamFragment>;
    
    /// Verify: check if a captain interaction confirms a rigid structure
    pub fn verify(&mut self, input: &str, action: Action, confirmed: bool) -> Option<VerifiedTile>;
    
    /// Compile: convert verified tiles to deterministic code
    pub fn compile(&self) -> Vec<CompiledCommand>;
    
    /// Bathymetry: visualize coverage map
    pub fn coverage_map(&self) -> BathymetricMap;
    
    /// Check if an input hits compiled code or needs inference
    pub fn classify(&self, input: &str) -> InputClass;
}
```

### Integration Points

- **eisenstein-embed**: Bitvector matching for fast tile lookup (1965 design, 93.8% typo accuracy)
- **tensor-spline**: Compress compiled tiles for minimal memory footprint
- **training-throttle**: Manage exploration vs exploitation — don't burn tokens exploring when compiled coverage is already high
- **plato-core**: Verified tiles become PLATO TrainingTiles with full provenance
- **device-router**: Compile tiles to run on the right device (NPU for regex matching, CPU for lookup)

## The ComNav 2001 Example (Full Flow)

```
1. Captain presses TALK button on mic
2. Audio → Speech-to-Text → "turn port 10 degrees"
3. Compiled matcher: HIT! regex r"turn port (\d+)" → TURN_PORT(10)
4. System responds via TTS: "Roger turning 10 degrees port"
5. ComNav 2001 receives TURN_PORT(10) via NMEA 2000 or simrad interface
6. Autopilot adjusts heading 10° port
7. Captain observes course change on wheelhouse display
8. Trust reinforced → tile confidence stays at 1.0

Total tokens used: 0 (compiled code path)
Latency: <1ms (regex match + serial output)
```

vs. the uncompiled path:

```
1. Captain says "put her head to the northwest"  
2. Compiled matcher: MISS (novel phrasing)
3. Soft model interprets: "northwest" → SET_HEADING(315°)
4. System asks: "Set heading to 315°, confirm?" (low confidence)
5. Captain confirms: "Aye"
6. New tile created: "put her head to X" → SET_HEADING(compass(X))
7. Next time: compiled code handles it

Tokens used: ~150 (one inference + confirmation)
But this tile now saves tokens forever.
```

## The Big Picture

LucidDreamer isn't just an autopilot voice controller. It's a **general-purpose system for converting soft model behavior into hard code**:

- **Autopilot voice control**: Nautical commands → compiled regex
- **Smart home**: "Turn off the living room lights" → compiled action
- **Code review**: Common patterns → automated checks
- **Customer service**: FAQ responses → compiled templates
- **Any repetitive inference task**: If the model does it more than once, compile it

The bathymetric map is the key abstraction. It tells you:
- Where you can safely compile (high coverage, high confidence)
- Where you still need the model (low coverage)
- Where you should explore more (interesting boundary regions)

The dream module's experimental constants are your compilation thresholds:
- 97.5% literal accuracy → safe to compile
- 77.5% negative accuracy → safe to add negative constraints
- 10% coverage minimum → don't generalize from too few samples
- Compression frontier → how much context to keep in compiled tiles
