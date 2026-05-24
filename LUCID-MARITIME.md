# LucidDreamer — Maritime Intelligence Architecture

*From cloud distillation to edge deployment. Every interaction a tile. Every tile a sounding line.*

## The Core Loop: Distill → Deploy → Verify → Refine

```
┌──────────────────────────────────────────────────────────────────┐
│  CLOUD (Starlink)                                                │
│                                                                  │
│  Heavy model (GPT-4, Claude, Gemini)                            │
│  - Explores full command space overnight                         │
│  - Generates all possible command variations                     │
│  - Creates response templates                                    │
│  - Resolves ambiguous inferences                                 │
│  - Distills to tiles                                             │
│                                                                  │
│  Output: Compiled tiles ≈ 95% of all needed commands             │
└──────────────────────┬───────────────────────────────────────────┘
                       │ tiles pushed down (small JSON/regex sets)
                       ▼
┌──────────────────────────────────────────────────────────────────┐
│  EDGE (Boat, Gemma 3 1B-IT)                                     │
│                                                                  │
│  Compiled tiles: regex + lookup + audio clips                    │
│  - "Turn port 10" → TURN_PORT(10) → "Roger turning 10 degrees"  │
│  - Zero inference needed. Zero tokens.                           │
│                                                                  │
│  Gemma 1B fallback:                                              │
│  - Novel phrasings not in tiles                                  │
│  - Context-dependent commands ("put wind on the beam")           │
│  - Batched ambiguous items for captain review                    │
│                                                                  │
│  Token savings: ~95% of commands hit compiled tiles              │
└──────────────────────┬───────────────────────────────────────────┘
                       │ ambiguous items batched for review
                       ▼
┌──────────────────────────────────────────────────────────────────┐
│  CAPTAIN REVIEW (transit time)                                   │
│                                                                  │
│  On the way to/from fishing grounds:                             │
│  - System presents batched ambiguous items                       │
│  - "I wasn't sure when you said 'come around' — did you         │
│     mean 180° or just 'start turning'?"                          │
│  - Captain provides ground truth                                 │
│  - New tiles created from captain's answers                       │
│  - Next cloud pass: tiles get distilled further                  │
│                                                                  │
│  This is the human-in-the-loop refinement cycle.                 │
│  The captain IS the ground truth oracle.                         │
└──────────────────────────────────────────────────────────────────┘
```

## Application 1: Autopilot Voice Control (ComNav 2001)

### Cloud Distillation Phase
```
GPT-4/Claude explores the autopilot command space:

"turn port 10"        → TURN_PORT(10)
"turn port 20"        → TURN_PORT(20)
"turn starboard 5"    → TURN_STARBOARD(5)
"come left 15"        → TURN_PORT(15)
"hard to port"        → TURN_PORT(45)
"steady"              → HOLD_HEADING
"steer 270"           → SET_HEADING(270)
"turn to 1 o'clock"   → TURN_PORT(30)
"turn to 3 o'clock"   → TURN_PORT(90)
"make your heading northwest" → SET_HEADING(315)
"put her head to the west"    → SET_HEADING(270)

... hundreds more variations, dialects, slang

Output: ~200 compiled regex rules + parameterized patterns
Size: ~50KB (fits on a microcontroller)
```

### Edge Deployment
```
Audio in → STT → Compiled matcher → Action → TTS → Audio out

For compiled commands (95%+):
  "turn port 10" → regex match → TURN_PORT(10)
  Response: pre-generated audio "Roger turning 10 degrees port"
  Latency: <100ms (STT + regex + audio playback)
  Tokens: ZERO
  Model needed: NONE

For novel commands (5%):
  "bring her around easy to port until the wind's abaft the beam"
  → Gemma 1B: "Turn slowly to port until wind is behind us"
  → System: "Turn port slowly until wind at 135°. Confirm?"
  → Captain: "Aye"
  → NEW TILE: "bring her around easy to port until wind abaft beam"
                → TURN_PORT_SLOW(until wind_relative > 135°)
  → Batched for next cloud distillation
```

### Audio Tiling
```
Pre-generated responses (ElevenLabs during cloud phase):
  - "Roger turning {X} degrees port"     — X = 5, 10, 15, 20, 25, 30...
  - "Roger turning {X} degrees starboard"
  - "Steady on heading {X}"
  - "Heading set to {X}"
  - "Confirmed, emergency stop"
  
  Total: ~100 audio clips × ~2 seconds = ~3 minutes of audio
  Storage: ~5MB compressed
  Voice: Matched to captain's preference (learned during first sessions)

Variations generated during downtime:
  - Gemma 1B creates polite/casual/urgent variants
  - Captain picks preferred style during review
  - Selected variants become new audio tiles

  "Roger" vs "Affirmative" vs "Copy" vs "Aye" — captain chooses
```

### The Splines of Truth

Not everything snaps to 100% confidence. The system has a **confidence continuum**:

```
Confidence    Action
─────────────────────────────────────────────────
100%          Execute silently, respond with compiled audio
90-99%        Execute, confirm with "Roger" — no interruption
70-90%        Execute tentatively, ask "Confirm?" within 3 seconds
50-70%        DON'T execute, ask captain to repeat or clarify
<50%          Log as ambiguous, batch for review during transit

The "splines of truth" are the regions between these zones:
- Where does "come left" map? 100% to TURN_PORT, but how many degrees?
  → If context suggests 10-30°, that's 70-90% confidence
  → Ask: "Come left how much, skipper?"
  → Captain says "10" → new tile: "come left" means "ask for degrees"
```

## Application 2: Fish Sorting Vision

### The Setup
```
Deck camera → Vision model (YOLO/classifier) → Species ID
            → Bin sensor (which hold it went into)
            → Photo of each fish
            → Count per species per hold
```

### The Tile Chain
```
Fish on deck → Photo taken → Vision model classifies
                                    │
                                    ▼
                           ┌─── Confidence? ───┐
                           │                    │
                        > 95%               < 95%
                           │                    │
                    Silent count            ALERT deck crew
                    Species: Chinook        "Hey, check that one — 
                    Hold: #3 (salmon)        I think it's a Chinook
                    Tile: VERIFIED            but only 80% sure"
                                              │
                                         Deck crew checks
                                              │
                                    ┌─── Correct? ───┐
                                   YES                 NO
                                    │                  │
                              Tile: VERIFIED     Tile: CORRECTION
                              (model was right)  (model learns)
                                                 Photo saved with 
                                                 correct label
```

### Every Fish Has an Identity
```python
class FishTile:
    photo: Image              # Photo of the fish
    species: str              # Identified species
    confidence: float         # Vision model confidence
    hold: int                 # Which hold it went into
    timestamp: datetime       # When it was sorted
    verifier: str             # "model" or "deck_crew" or "captain"
    weight_estimate: float    # From camera perspective
    tile_type: str            # VERIFIED, CORRECTION, AMBIGUOUS
```

### Captain Review Session
```
During transit, captain opens the fish review dashboard:

"Sort by confidence, lowest first"

Fish #847 — 78% Chinook → went to Hold #3
  [PHOTO] 
  Crew verified: YES → Chinook ✓
  
Fish #892 — 62% Sockeye → went to Hold #2  
  [PHOTO]
  Crew verified: YES → Sockeye ✓
  
Fish #934 — 55% Chum vs Pink → went to Hold #5
  [PHOTO]
  Crew verified: NO RECHECK
  Captain looks: "That's a Pink, not a Chum"
  → CORRECTION tile created
  → Vision model gets new training sample
  → Next deployment: better at Chum vs Pink
```

### Buyer Reconciliation — The Final Anchor
```
Captain's count (tiles):    847 Chinook, 1202 Sockeye, 431 Pink
Buyer's count (delivery):   845 Chinook, 1205 Sockeye, 430 Pink

Difference:
  -2 Chinook (tile count vs buyer count)
  +3 Sockeye
  -1 Pink

This is a SPLINE anchor point. The buyer's count is ground truth.
The system can now:
1. Trace back to which specific fish might be miscounted
2. Look for systematic bias (always over-counting Chinook?)
3. Adjust confidence thresholds for next trip
4. Feed back to vision model training

Every delivery is a calibration event.
```

## Application 3: Species-Specific Holds as Constraint System

This maps directly to the flux-lucid constraint system:

```
Hold #1: Chinook salmon    → constraint: species=chinook, min_weight=10lbs
Hold #2: Sockeye salmon    → constraint: species=sockeye
Hold #3: Pink salmon       → constraint: species=pink
Hold #4: Chum salmon       → constraint: species=chum
Hold #5: Halibut           → constraint: species=halibut
Hold #6: Bycatch/unknown   → constraint: species=unknown OR confidence<50%

When vision model classifies:
  confidence > 95% AND species matches hold → STEEL constraint (execute)
  confidence 80-95% → FIBERGLASS (execute + flag for review)
  confidence 50-80% → OAK (alert deck crew)
  confidence < 50%  → RUBBER (send to bycatch hold, photo for captain)

This is EXACTLY the fitting selection from navigation.rs:
  HoseClamp    = bycatch (low confidence)
  Industrial   = flag for review
  JicFitting   = alert deck crew
  DeepSeaSeal  = verified, count it
```

## The Model Shrinks Over Time

```
Trip 1:  Cloud API does 80% of autopilot commands
         Gemma 1B handles 20% (novel phrasings)
         Audio: all ElevenLabs cloud

Trip 5:  Cloud API distilled 500+ command variations to tiles
         Gemma 1B handles 2% (truly novel situations)
         Audio: 95% pre-generated tiles, 5% local TTS fallback

Trip 20: Cloud API only needed for completely new scenarios
         Gemma 1B handles <1% — essentially just safety confirmations
         Audio: 99% pre-generated, voice matched to captain
         
Trip 50: Could run on a microcontroller.
         The Gemma 1B is overkill.
         The compiled tiles ARE the intelligence.
         The captain's accumulated feedback IS the training data.
```

## System Architecture (Hardware)

```
┌─────────────────────────────────────────────────────┐
│  WHEELHOUSE                                         │
│                                                     │
│  ┌──────────┐  ┌──────────┐  ┌──────────────────┐  │
│  │ Mic +    │  │ Speaker  │  │ Display          │  │
│  │ Push-to  │  │          │  │ (fish review,    │  │
│  │ Talk     │  │          │  │  autopilot,      │  │
│  └────┬─────┘  └────▲─────┘  │  counts)         │  │
│       │             │         └────────▲─────────┘  │
│       ▼             │                  │            │
│  ┌─────────────────────────────────────────────┐    │
│  │ Edge Computer (Raspberry Pi 5 or Jetson)    │    │
│  │                                             │    │
│  │ - STT (Whisper tiny, local)                 │    │
│  │ - Compiled tiles (regex + lookup)           │    │
│  │ - Gemma 3 1B-IT (fallback)                  │    │
│  │ - Audio tiles (pre-generated responses)     │    │
│  │ - Fish photo database                       │    │
│  │ - Count reconciliation                      │    │
│  └─────────┬───────────────────┬───────────────┘    │
│            │                   │                     │
│            ▼                   ▼                     │
│  ┌──────────────┐  ┌──────────────────────┐         │
│  │ ComNav 2001  │  │ Deck cameras         │         │
│  │ (NMEA 2000) │  │ (fish sorting vision)│         │
│  └──────────────┘  └──────────────────────┘         │
│                                                     │
│            ▲ Starlink (intermittent)                 │
│            │                                        │
│            │ Cloud sync:                            │
│            │ - New tiles up                         │
│            │ - Ambiguous items down for review      │
│            │ - Model updates (infrequent)            │
│            │ - Buyer reconciliation data             │
└─────────────────────────────────────────────────────┘
```

## The Data Flow: Full Trip Cycle

```
BEFORE TRIP (port, Starlink up):
  Cloud distills accumulated tiles from all captains
  New command variations pushed to all boats
  Vision model updates pushed if species season changed
  Audio variations generated for new responses

DURING TRIP (at sea, Starlink intermittent):
  Autopilot: compiled tiles + Gemma 1B fallback
  Fish sorting: local vision model + deck crew verification
  Ambiguous items batched locally
  Captain review sessions during transit
  
AFTER TRIP (port, Starlink up):
  All new tiles uploaded to cloud
  Fish photos + counts uploaded
  Buyer reconciliation data received
  Vision model retrained on corrections
  New tiles distilled from this trip's data
  Captain feedback integrated into personalization

SEASON END:
  Aggregate data across all trips
  Vision model achieves 99%+ on local species
  Autopilot commands cover 99.9% of phrasings
  Captain's personal dialect fully compiled
  System ready for next season with zero cloud dependency
```

## Connection to Existing Ecosystem

| Component | Role in Maritime System |
|-----------|------------------------|
| **flux-lucid dream.rs** | Cloud-side exploration of command space (DreamStyle::Surreal for discovery) |
| **flux-lucid intent.rs** | 9-channel intent encoding for command classification |
| **flux-lucid intent_compilation.rs** | Mixed-precision constraint checking (species → hold mapping) |
| **flux-lucid head_direction.rs** | 12-orientation compass heading system |
| **flux-lucid simulation_first.rs** | Predict command outcome before executing (safety) |
| **eisenstein-embed** | Bitvector matching for fast tile lookup on edge hardware |
| **tensor-spline** | Compress tile data for minimal edge storage |
| **device-router** | Route inference to Gemma 1B (local) vs cloud API (Starlink) |
| **training-throttle** | Manage cloud API usage — don't burn Starlink bandwidth on exploration |
| **plato-core** | Tile provenance — every fish, every command, every correction has a history |
| **AIR** | Nightly synthesis — process the day's data while captain sleeps |
| **luciddreamer-agent** | Dream journal adapted to trip journal — captain's log of ground truth |
| **cocapn-oneiros** | Generate new command/species scenarios to explore during downtime |
| **luciddreamer-os** | Wheelhouse dashboard — the UI for the whole system |

## The Big Insight

The system doesn't need to be smart. It needs to be **compiled**.

A fishing boat captain gives maybe 50 distinct commands per trip. The autopilot has maybe 20 buttons. The fish sorting has maybe 10 species. The entire "intelligence" of the system fits in:

- ~200 regex rules for voice commands
- ~100 audio clips for responses
- ~10 species classifiers for vision
- ~1 Gemma 3 1B-IT for "I don't know what that means"

Total edge compute needed: less than a Raspberry Pi 5.

The cloud API's job is to **distill** — take the infinite space of possible commands and compress it to the finite set the captain actually uses. The Gemma 1B's job is to handle the long tail. The captain's job is to be the ground truth oracle.

Every fish, every command, every correction is a tile. Every tile makes the system better. Over a season, the system becomes the captain.
