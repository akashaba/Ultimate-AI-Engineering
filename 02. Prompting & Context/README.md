# Module 2 — Prompting & Context

> Part of the **AI Engineer Master Curriculum**. Each subtopic file contains deep-dive theory with math, production failure modes and fixes, runnable reference code, ASCII diagrams, image-generation prompts, three graded projects (user stories, acceptance criteria, step-by-step builds), and a paper and tooling list.

| # | File | Core question | Key artifacts you'll build |
|---|---|---|---|
| 02.01 | [Prompting & In-Context Learning](02-01-prompting-in-context-learning.md) | Why does ICL work, and how do you ship prompt changes on evidence rather than vibes? | Eval-driven prompt harness with paired significance tests · dynamic few-shot + calibration study · prompt-injection red team with layered defence |
| 02.02 | [Context Engineering](02-02-context-engineering.md) | What should the model see on *every* call across turns, tools, memory, and agents, and at what cost? | Context compiler with manifest + ablations · long-term memory service · compaction strategy benchmark |
| 02.03 | [Structured Outputs & Schemas](02-03-structured-outputs-schemas.md) | How do you turn model output into a typed, validated, versioned contract? | Grounded extraction pipeline with field-level evals · cross-provider conformance suite · Spring Boot structured-output service |
| 02.04 | [Constrained Decoding](02-04-constrained-decoding.md) | How do grammar-constrained engines work, how fast can they be, and how do they bias the output? | Regex→DFA→token-index engine · grammar-constrained SQL with schema-aware identifiers · distortion measurement and correction |

## How Module 2 connects to Module 1

```
01.02 sampling / §6 masking ──────────────────────────────► 02.04 constrained decoding (engine internals)
01.03 tokens, special tokens, healing ──► 02.01 §6 injection ──► 02.04 §5 token-boundary problems
01.04 budget, packer, long ctx vs RAG ──► 02.02 context as a multi-turn pipeline (memory, tools, caching, agents)
                                02.03 schemas & validation  ◄──► 02.04 how schemas become grammars
```

## Suggested order and time budget

`02.01 (≈2 wk) → 02.03 (≈1.5 wk) → 02.02 (≈2 wk) → 02.04 (≈2 wk)`

Structured outputs come before context engineering because the 02.02 projects use strict schemas for citations, memory extraction, and hand-offs. Constrained decoding comes last because it is the engine underneath 02.03.

## Provider facts used (checked September 2026 — re-verify before building)

- **Anthropic:** `output_config.format` (JSON Schema) and `strict: true` on tools; no beta header. `messages.parse()` gives typed Pydantic results. Required properties are emitted before optional ones. No `pattern`, numeric or length bounds, or recursion in schemas. Grammars are cached for 24 h. Prompt-cache writes cost 1.25× (5 min) or 2× (1 h) the base input price, and reads 0.1× on most models (lower on some).
- **OpenAI:** Responses API `text.format` (`type: json_schema`, `strict: true`) and `responses.parse(text_format=...)`. In strict mode every property must be listed as required (use `null` unions for optional values) and `additionalProperties: false` is mandatory.

## Minimum hardware

- **CPU / laptop:** all of 02.01 P1 and P3, 02.02 P1–P2 (with an API model), 02.03 P1–P3, and 02.04 P1 with a small model.
- **1 × 24 GB GPU:** 02.01 P2 (label log-probs from an open model), 02.04 P2 (vLLM/SGLang grammar backends), and 02.04 P3.

## Conventions

LaTeX math (`$…$` / `$$…$$`), `🖼️ Image prompt` blocks, and Python ≥ 3.10. Every reference snippet not tied to a live API was executed and tested:
- The MMR selector, calibration, self-consistency, and McNemar/bootstrap tests.
- RRF, memory scoring, observation masking, cache break-even arithmetic, and the context compiler.
- The repair loop with grounding checks, the partial-JSON parser, and field scoring.
- The regex token index against a naive oracle, the trie-based CFG mask, bitmask packing, and the distortion example.
