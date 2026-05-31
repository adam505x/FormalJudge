# EXPLORATION: Formalization Backends

## The Question

FORMALJUDGE uses Dafny → Z3 (SMT solving). Is there a better backend for agent trajectory verification? I'm comparing several options.

## Option 1: Dafny / Z3 (Current — FORMALJUDGE default)

**What it is:** Dafny is a verification-aware programming language. You write code with `requires`/`ensures` contracts. Dafny compiles to Boogie, which encodes to Z3, which solves the constraints.

**Strengths:**
- Battle-tested (Z3 has 15 years of production use at Microsoft, Amazon)
- Fast for constraint checking
- LLMs generate decent Dafny (more training data than Lean 4)
- Deterministic — same input always gives same SAT/UNSAT

**Weaknesses:**
- Z3 is a black box — gives SAT/UNSAT but no proof chain
- Large trusted computing base (~300K lines of C++)
- Can't produce independently verifiable proof certificates
- UNSAT proofs aren't human-readable

**Status:** Already working in this repo. Baseline to compare against.

## Option 2: Lean 4 (Primary exploration target)

**What it is:** Lean 4 is a theorem prover and programming language. You write propositions as types and proofs as terms. The kernel (~8K lines of C++) type-checks proofs.

**Strengths:**
- Produces proof objects — machine-checkable certificates
- Tiny trusted computing base (8K lines vs Z3's 300K)
- Proofs are independently verifiable (anyone with `lean` can check)
- AWS uses Lean for Cedar policy verification in production
- Harmonic AI raised $100M using Lean for math verification
- Active ecosystem (Mathlib, Leanstral, Goedel-Prover)

**Weaknesses:**
- LLMs are worse at generating Lean 4 than Dafny (less training data)
- Lean compilation can be slow for large projects
- Less mature tooling for program verification (Lean is primarily for math)
- Need to figure out: how to encode agent trajectories as Lean types

**Proof generation options:**
- Leanstral (Mistral, 120B MoE, free API, purpose-built for Lean 4 proofs — too large to run locally)
- Goedel-Prover-V2-8B (8B params, should run locally on 36GB Mac Studio with quantization)
- Goedel-Autoformalizer-8B (NL → Lean 4 translation, same — runs locally quantized)
- Claude/GPT (general-purpose, worse at Lean but okay for simple specs)

**Key question:** For the boolean composition that FORMALJUDGE does (composing 5-15 atomic facts with AND/OR/NOT/IMPLIES), are Lean proofs overkill? The proofs would be trivially simple. The value would be in the certificate, not the proof difficulty.

## Option 3: OCaml + Coq (Alternative exploration)

**What it is:** Write verified specs in Coq, extract to OCaml for runtime execution. Coq has a similarly tiny kernel to Lean.

**Strengths:**
- Coq has decades of production verification (CompCert compiler)
- OCaml extraction is mature and fast
- Coq's tactic language is well-understood

**Weaknesses:**
- No equivalent to Leanstral — no purpose-built LLM for Coq proof generation
- Smaller active community than Lean 4 in 2026
- Less momentum and tooling investment

**Assessment:** Probably not worth exploring unless Lean 4 hits a wall.

## Option 4: F* (Microsoft Research)

**What it is:** A verification-oriented language from MSR. Used in Project Everest (verified TLS, verified cryptography).

**Strengths:**
- Designed for program verification (not math)
- Can encode effectful programs (agents ARE effectful)
- Extraction to C, OCaml, F#, WASM
- Z3 integration built in (best of both worlds?)

**Weaknesses:**
- Very small community
- No LLM tooling for F* proof generation
- Learning curve is steep
- Less ecosystem support than Lean 4

**Assessment:** Interesting for encoding agent effects, but no tooling support makes it impractical for a 1-month timeline.

## Option 5: TLA+ (Leslie Lamport)

**What it is:** Temporal Logic of Actions. Designed for specifying and verifying concurrent and distributed systems.

**Strengths:**
- Purpose-built for reasoning about sequences of actions (agents are sequences of actions)
- Model checking explores all possible executions
- Strong at temporal properties ("after X, eventually Y")
- Used at AWS for verifying distributed systems

**Weaknesses:**
- Model checker, not theorem prover — explores finite state space
- No proof certificates
- Not great for arithmetic constraints
- No LLM tooling

**Assessment:** Conceptually the best fit for agent trajectories (temporal logic), but no proof certificates and no LLM tooling. Could be interesting as a complement to Lean, not a replacement.

## My Current Plan

1. **Get FORMALJUDGE running locally with Dafny/Z3 first** (reproduce paper results, establish baseline on Mac Studio M4 Max 36GB)
2. Install Lean 4 toolchain on macOS and write equivalent specs for the paper's examples
3. Build a parallel Lean 4 backend for the spec generation + verification step
4. Compare: accuracy, latency, proof certificate quality, iterative refinement quality
5. If Lean 4 works, package as OpenClaw plugin

## Open Questions

- Can Leanstral generate correct Lean 4 for boolean composition of atomic facts? (Should be easy — these are trivial proofs)
- What's the latency of Lean kernel type-checking vs Z3 solving for these simple specs?
- Can Lean proof certificates be rendered in a human-readable dashboard?
- Does Lean's dependent type system offer anything beyond what Z3 gives for this use case?
- Is there a way to combine Z3 (fast constraint checking) with Lean (proof certificates) — use Z3 for quick SAT/UNSAT, then generate Lean proof for the audit trail?
