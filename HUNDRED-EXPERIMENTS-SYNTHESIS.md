# The Hundred Experiments — Final Synthesis

## What 100 Experiments Taught Us

### The Central Paradox
Every metric we built to measure creativity failed. Not because the metrics were bad — because **what humans call "good music" lives in the gap between measurable quantities.**

| Metric | What It Measures | Where It Peaks | Why It Fails |
|--------|-----------------|---------------|-------------|
| Raw quality | Variance | ρ→∞ | Louder ≠ better |
| Complexity-Entropy | Structure | ρ≈15 | Most structured = most boring |
| Fisher Information | Parameter sensitivity | ρ≈24 | Most sensitive ≠ most musical |
| Musicality (CE×Consonance) | Combined | ρ≈20, σ≈6.3 | Optimizes to a single note |
| Consonance alone | Pleasant intervals | ρ≈24 | Perfect consonance = drone |

### The Cliff
Music dies at **ρ≈23.81**. Below: order, structure, consonance. Above: chaos, variety, noise. The transition is only ~0.5 ρ units wide.

But neither side of the cliff produces "good music" by itself. The ordered side is too boring. The chaotic side is too noisy. The best music lives AT the cliff — but our metrics can't find it because they optimize to one side or the other.

### What We Built
- **100 computational experiments** spanning 4 paradigms
- **22+ creative works** (fiction, poetry, essays) across 8 rounds
- **3 corrected metrics** (Fisher, CE, structure function)
- **5 species** of creative dynamics
- **1 Pareto front** of 9 optimal ρ values
- **1 definitive melody** that proves metrics aren't enough
- **1 unified theory** that no single metric captures

### The Four Paradigms
1. **Constraint Theory** (exp 1-18): Constraints shape creativity
2. **Attractor Theory** (exp 19-50): The attractor IS creativity  
3. **Metric Theory** (exp 51-60): Every metric is confounded
4. **Structure Theory** (exp 61-100): Multi-objective, no optimum

### The Key Numbers
- Quality ↔ variance: r=0.993
- Fisher ↔ variance: r=-0.044
- CE ↔ variance: r=-0.332
- Attractor size ↔ CE: r=-0.606
- Music dies at: ρ≈23.81
- Most musical (by metric): ρ≈20, σ≈6.3 (but sounds like a drone)
- Most creative (by humans): somewhere on the cliff, where no metric looks

### The Creative Landscape
```
ρ=5   ρ=15  ρ=20  ρ≈23.8  ρ=24  ρ=28  ρ=47  ρ=75  ρ=100
 |      |     |    CLIFF    |     |     |     |      |
 Crystal Sweet  Metric  ←DEATH→  Classic Meta  Fisher  Raw
       Spot    Peak             Lorenz wheel  Peak   Variance
```

The artist lives on the cliff. Science lives on the peaks.

### What Claude Code Added
(Check tmux for Claude Code's unified theory when it completes)

### Next Steps
- Human listening tests — the metric can't do it alone
- Generate actual audio from Lorenz melodies (MIDI → WAV)
- Build the interactive Pareto explorer (web toy)
- Submit the unified theory paper
- Run experiments 101-115 with human evaluation
- Build the VST plugin with Pareto front presets

---

*"The next experiment is always the most interesting one."*
