# Night Roadmaps

## Agent 1: Consolidation — Clean 40 stale repos
- Archive to SuperInstance/graveyard/{repo-name}
- OR delete locally (keep on GitHub)
- Deduplicate AI-Writings
- `find . -name ".git" -type d | wc -l` → target: 90

## Agent 2: sunset-ecosystem → consumes ecosystem
- `pyproject.toml` needs: tensor-spline, eisenstein-embed, device-router, triplet-miner, training-throttle, micro-onnx
- Wire nerve fibers to use these packages instead of simulation
- Add to swarm_runner.py: real device detection, real bitvector matching, real spline normalization

## Agent 3: Distillation loop → first experiment
- 12 agents, 5 generations
- Concrete task: "Generate a concise summary of PLATO's architecture"
- Track: hint_level, quality_score, personalization_tags per generation
- Auto-run via sunset-ecosystem
- Output: results.png, generational-report.md

## Agent 4: PyPI publishing
- Retry all 5 blocked packages
- If 429: exponential backoff + retry every 15min
- Alert when published

## Agent 5: Cross-repo pattern extraction
- Extract shared patterns across all repos
- Common: .gitignore, pyproject.toml, Cargo.toml structure, badge format
- Create `ecosystem-template` repo
- Write `./map-ecosystem.sh` that generates dependency graph

## Agent 6: Theory synthesis
- Read: TRIPARTITE-ARCHAEOLOGY.md, STRUCTURAL-SURVEY.md, all 10 architecture docs
- Identify: is there a general theory of computation-in-ecosystems here?
- Write: `THEORY-OF-ECOSYSTEMS.md`
- Cover: threshold physics, the COLLECT→SELECT→COMPILE universal, temperature in trinity systems
