# Module 6 — Evaluation

> Part of the **AI Engineer Master Curriculum**. Each subtopic file contains deep-dive theory with math, production failure modes and fixes, runnable reference code, ASCII diagrams, three graded projects (user stories, acceptance criteria, step-by-step builds), and a paper and tooling list.

| # | File | Core question | Key artifacts you'll build |
|---|---|---|---|
| 06.01 | [Evals](06-01-evals.md) | What to measure, with which graders, how many examples, and how to report honest uncertainty? | Eval framework + non-inferiority CI gate · judge validation + PPI · statistical rigor audit |
| 06.02 | [Test Datasets](06-02-test-datasets.md) | How do you build eval data that is representative, trustworthy, clean, and durable? | Golden legislative QA set with datasheet · synthetic generation with quality gates · contamination audit + IRT-compact eval |
| 06.03 | [Online Metrics](06-03-online-metrics.md) | Does it work for real users, and how do you know a change helped? | Metric instrumentation · experimentation framework (SRM, CUPED, mSPRT, interleaving) · drift monitoring with runbooks |
| 06.04 | [Tracing & Debugging](06-04-tracing-debugging.md) | Why did the system do that, and how do you reproduce and fix it? | OTel GenAI instrumentation across Java + Python · trace → replay → regression pipeline · error-analysis program |

## The evaluation loop this module builds

```
   06.04 traces ──► error analysis ──► failure categories ──► 06.02 dataset (new cases, versioned)
        ▲                                                             │
        │                                                             ▼
   06.03 online metrics ◄── ship ◄── 06.01 CI gate (paired CIs, non-inferiority, validated judges)
   (A/B, interleaving, drift)
```

**Consolidates** earlier evaluation pieces: 02.01 §7 (paired prompt tests), 03.01 §4 / 03.04 §8 (IR metrics, pooled judgments), 04.01 §6 / 04.02 §1, §8 (evidence recall, failure attribution), 04.03 §5 (calibration), 05.02 §8 (pass^k, trajectories), 05.06 §7 (human feedback signals).

## Suggested order and time budget

`06.01 (≈2 wk) → 06.02 (≈2 wk) → 06.04 (≈1.5 wk) → 06.03 (≈2 wk)`

Tracing comes before online metrics because the 06.03 projects join events to trace IDs.

## Facts checked for this module (September 2026)

- **OpenTelemetry GenAI semantic conventions** are still under active development (not yet stable). Attribute names are from `opentelemetry-semantic-conventions` 0.65b0:
  - `gen_ai.provider.name` (replacing `gen_ai.system`).
  - `gen_ai.usage.{input,output}_tokens`, `gen_ai.usage.cache_read.input_tokens`, `gen_ai.usage.cache_creation.input_tokens`, `gen_ai.usage.reasoning.output_tokens`.
  - `gen_ai.input.messages` / `gen_ai.output.messages` / `gen_ai.system_instructions` (opt-in content), and `gen_ai.tool.*`, `gen_ai.agent.name`.
  - Operations: `chat`, `embeddings`, `retrieval`, `execute_tool`, `invoke_agent`, `invoke_workflow`, `create_agent`, `generate_content`, `text_completion`.
- Message-content capture is off by default in the instrumentations (OTel blog, May 2026).

## Verification

Every Python snippet was executed and tested:
- **Evals:** graders and runner; Wilson CIs; clustered SEs (naive SE understated by ~3× on correlated items) and cluster bootstrap; paired sample size (1,306 for δ = 3 pts at 15% discordance); Holm; Cohen's κ; Rogan–Gladen (recovers the true rate exactly); **PPI** (covers the true rate where the judge alone is biased); non-inferiority gate.
- **Datasets:** stratified sampling; post-stratification; **Fleiss' κ (matches the Wikipedia worked example, 0.210)**; distinct-n; MinHash dedupe; n-gram contamination; leak-free group splits; **2PL IRT recovery (Spearman 0.92)**; order-invariant dataset hashing.
- **Online metrics:** SRM; two-proportion CIs; **CUPED (variance ratio = theoretical 1 − ρ² = 0.638)**; **peeking vs mSPRT (false-positive rate 29% vs 1.3% with 20 peeks)**; team-draft interleaving (detects the better ranker in 2000/2000 simulated queries); Thompson sampling; PSI; EWMA (detects a 3-point shift within 2 periods, no false alarms).
- **Tracing:** OTel spans with GenAI attributes via the real SDK (in-memory exporter), including error status; deterministic PII redaction that keeps citations; tail sampling; cassette record/replay with miss reporting; trace diffing; error-analysis prioritisation.
