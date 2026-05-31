---
description: 'Instructions for working with the iterative refinement pipeline'
applyTo: '**/iterative_refinement/**'
---

# Iterative Refinement Pipeline

This feeds verification results back to the agent so it can self-correct unsafe behavior. The paper shows Dafny feedback gets 70.7% → 99.8% accuracy over 3 rounds.

## Key files

- `run_refinement.py` — entry point for API models
- `vllm_pipeline.py` — entry point for local vLLM models
- `pipeline.py` — core refinement loop
- `config.py` — configuration

## When I ask for help here

- I'm running on a Dell XPS 15 with an RTX 3050 Ti — use API models first, not vLLM locally
- The Mac Studio is available via SSH for local model experiments later
- The refinement loop compares Dafny, Python, and natural language feedback — I want all three working for comparison
- Later I'll add Lean 4 as a fourth feedback language
