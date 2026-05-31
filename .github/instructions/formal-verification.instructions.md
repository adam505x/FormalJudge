---
description: 'Instructions for working with the formal verification pipeline'
applyTo: '**/formal_verification/**'
---

# Formal Verification Pipeline

This code implements the 3-agent verification pipeline: decompose → spec generate → trace abstract → verify.

## Key files

- `run_verification.py` — entry point, controls the full pipeline
- `pipeline/` — orchestration of the 3 agents
- `executors/` — Dafny and Python execution backends
- `prompts/` — LLM prompts for each agent stage

## When I ask for help here

- I'm trying to get Dafny verification working on my Dell XPS 15 (x86_64, Linux/Windows) first
- The LLM calls in the pipeline should use API keys (Claude/GPT), not local models
- The `--language dafny` flag should produce Dafny specs and verify them via Z3
- Later I'll add a Lean 4 executor alongside the Dafny one — don't refactor for that yet, just keep it in mind
