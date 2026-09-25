# Module 8 — LLMOps & Serving

> Part of the **AI Engineer Master Curriculum**. Each subtopic file contains:
> - deep-dive theory with math;
> - production failure modes and fixes;
> - runnable reference code and ASCII diagrams;
> - three graded projects (user stories, acceptance criteria, step-by-step builds);
> - a list of papers and tooling.

| # | File | Core question | Key artifacts you'll build |
|---|---|---|---|
| 08.01 | [Model Selection & Routing](08-01-model-selection-routing.md) | Which model(s) should serve each request, at what cost, and what happens when one fails? | Pareto bake-off + TCO memo · cascade/learned router with a quality floor · resilient multi-provider gateway (circuit breakers, hedging) |
| 08.02 | [Caching, Latency & Cost](08-02-caching-latency-cost.md) | Where do the milliseconds and dollars go, and which levers move them safely? | Cost/latency attribution + optimisation sprint · multi-layer cache with correctness guarantees · tail-latency engineering for an agent pipeline |
| 08.03 | [vLLM / TGI](08-03-vllm-tgi.md) | How do you size, deploy, observe, autoscale, and upgrade a self-hosted inference engine? | vLLM on Kubernetes with KEDA queue scaling · TGI → vLLM migration with parity tests · multi-LoRA serving |
| 08.04 | [Quantization & Batching](08-04-quantization-batching.md) | Which number format and batching policy maximise goodput on your GPUs without silent quality loss? | Quantization bake-off with slice-level gates · GPTQ/AWQ from scratch vs LLM Compressor · goodput tuning + Kafka bulk pipeline |
| 08.05 | [Production Lifecycle](08-05-production-lifecycle.md) | How do you release, operate, and govern an LLM system as one versioned unit? | Bundle-based release pipeline with auto-rollback · SLO/burn-rate alerting + incident program · legislative-session readiness (capacity, AI inventory, HB 178 controls) |

## How the pieces fit

```
 request ─► GATEWAY (08.01: routing, fallbacks, budgets) ─► CACHES (08.02: exact / semantic / prefix / provider)
               │                                                   │ miss
               ▼                                                   ▼
          provider APIs                          SELF-HOSTED ENGINE (08.03: vLLM on k8s, KEDA)
                                                   └─ efficiency: quantization + batching (08.04)
 ───────────────────────────────────────────────────────────────────────────────────────────────
 LIFECYCLE (08.05): bundle digest → gates → canary/rollback → SLOs + burn alerts → incidents → governance
 measured by 06.x (evals, online metrics, tracing) · protected by 07.x (guardrails, security)
```

## Suggested order and time budget

`08.01 (≈1.5 wk) → 08.02 (≈1.5 wk) → 08.03 (≈2 wk) → 08.04 (≈2 wk) → 08.05 (≈2 wk)`

Do the 08.05 projects last. They integrate everything from Modules 6–8.

## Facts checked for this module (September 2026)

- **Hugging Face TGI is in maintenance mode.** Only minor fixes and documentation changes are accepted, and HF recommends vLLM or SGLang. 08.03 treats TGI as a migration source.
- **vLLM v0.30.0** was released Sep 22, 2026. Pin versions, because flags and metric names change between releases.
- **LLM Compressor** (`vllm-project/llm-compressor`) supports:
  - W8A8 (INT8/FP8), W4A16, W4AFP8;
  - NVFP4 / MXFP4 / MXFP8;
  - FP8 and NVFP4 KV cache;
  - GPTQ, AWQ, SmoothQuant, AutoRound, and SpinQuant/QuIP rotations.
- **Montana HB 178 (2025)** covers state and local government use of AI:
  - prohibited uses;
  - disclosure for public-facing AI and for unreviewed AI-generated publications;
  - human review of recommendations or decisions affecting a person's rights.

  08.05 §9 maps these to engineering controls. Confirm interpretation with counsel.

## Verification

Every Python snippet was executed, both sequentially per file and **each block standalone** (40 runs, 0 failures). Two GPU-only blocks (LLM Compressor, vLLM offline) are illustrative and were not run.

- **08.01:** Pareto frontier, TCO/break-even, cascade threshold selection, circuit breaker state machine, fallback routing.
- **08.02:**
  - the cache key includes tenant/ACL/versions, and invalidation works;
  - the fan-out tail probability;
  - **hedging cuts p99** on lognormal latencies;
  - end-to-end latency Monte Carlo;
  - cost per success, including cache-read/write and reasoning tokens.
- **08.03:**
  - vLLM capacity sizing: 70B on 2×80 GB gives ≈214k KV tokens in BF16 and ≈428k in FP8;
  - max-QPS-at-SLO search;
  - Prometheus parsing and engine-health rules.
- **08.04:**
  - format simulation: FP8 E4M3 per-tensor error 0.026 vs INT8 0.116 on outlier weights, and NVFP4 0.093 vs MXFP4 0.145; the uniform-noise MSE matches Δ²/12;
  - **GPTQ reduces layer-output error 30–37% vs RTN** on held-out activations;
  - SmoothQuant cuts W8A8 error from 0.087 to 0.015; a Hadamard rotation takes kurtosis from 245 to 0.05;
  - the slice gate **catches a regression hidden by a passing aggregate**;
  - roofline: the INT4 crossover is B≈74 vs 295; KV traffic dominates at long context;
  - **continuous batching doubles capacity** with sub-second p95 TTFT, while static batching diverges.
- **08.05:**
  - bundle diff and compatibility rules (an embedding change without a reindex is rejected);
  - canary analysis rolls back on feedback regression;
  - **multi-window burn-rate alerts page during an incident and auto-reset after it**;
  - autoscaling: hysteresis cuts scale events 20× vs naive, and the **predictive session floor eliminates surge overload**.
