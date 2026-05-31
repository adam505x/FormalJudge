# Copilot Instructions

## Project Goal

I'm adapting FORMALJUDGE for OpenClaw agent verification. Catching agent hallucinations using formal verification (Dafny/Z3 now, exploring Lean 4 later) instead of LLM-as-judge.

## Hardware

### Primary — Dell XPS 15 (developing here)
- 11th Gen Intel Core i7-11800H @ 2.30GHz (8 cores / 16 threads, turbo 4.6GHz)
- 31 GiB RAM
- NVIDIA RTX 3050 Ti Mobile + Intel TigerLake-H integrated
- Architecture: x86_64
- Running local models is not the priority here — use API keys for LLM calls

### Secondary — Mac Studio (SSH access)
- Apple M4 Max, 14 cores (10P + 4E), 36GB unified memory
- macOS Apple Silicon ARM — no CUDA
- Available via SSH for later experiments with local models (Goedel-Prover-V2-8B, etc.)
- Don't target this machine yet unless I ask

## Current Phase: Getting It Running Locally on the Laptop

Priority is getting the existing Dafny/Z3 pipeline running end-to-end on AgentSafetyBench on the Dell XPS using API calls for the LLM steps (Claude, GPT, or similar). Don't suggest Lean 4 changes, OpenClaw integration, or architecture refactors yet — just help me get the baseline working.

## Timeline

I have ~2 weeks for the full project. Help me move fast without cutting corners.

## Preferences

- I'm new to formal verification tooling (Dafny, Z3, Lean) — explain installation steps explicitly
- When I ask about Lean 4 or alternative backends later, reference EXPLORATION_GOALS.md and FORMALJUDGE_REFERENCE.md in the repo root
- If something requires a local model, suggest the API alternative first, then mention the Mac Studio as an option for running it locally later

## Roadmap (don't jump ahead)

- **Short term (now):** Install Dafny + Z3 on the laptop. Set up API keys for LLM calls. Run AgentSafetyBench verification pipeline end-to-end. Reproduce paper results.
- **Medium term:** Adapt the pipeline to verify OpenClaw agent trajectories. Build an OpenClaw plugin using `before_tool_call` hooks. Consider running local models on the Mac Studio via SSH.
- **Long term:** Swap Dafny/Z3 for Lean 4 backend. Use Leanstral (API) or Goedel-Prover-V2-8B (on Mac Studio) for proof generation. Compare accuracy, latency, and proof certificate quality.
