# Module 1 — LLM Fundamentals

> Part of the **AI Engineer Master Curriculum**. Each subtopic file contains deep-dive theory with math, production failure modes and fixes, runnable reference code, ASCII diagrams, image-generation prompts, three graded projects (user stories, acceptance criteria, step-by-step builds), and a paper and tooling list.

| # | File | Core question | Key artifacts you'll build |
|---|---|---|---|
| 01.01 | [Transformers & Attention](01-01-transformers-attention.md) | What exactly does a modern decoder compute, and what does it cost? | Llama-style decoder with a verified KV cache · Triton online-softmax kernel · induction-head ablation |
| 01.02 | [Inference & Decoding](01-02-inference-decoding.md) | Where does every millisecond of a request go, and how do you control it? | Mini serving engine (paged KV + continuous batching) · SLO capacity plan · speculative decoding + guaranteed JSON |
| 01.03 | [Tokenization](01-03-tokenization.md) | How does the tokenizer shape cost, context, fairness, and security? | tiktoken-compatible BPE · multilingual cost audit · domain vocabulary extension |
| 01.04 | [Context Window & Limits](01-04-context-window-limits.md) | Why is context limited, and how do you get the *effective* context you need? | "Infinite chat" context manager · PI/NTK/YaRN extension lab · long-context vs RAG benchmark |

## Suggested order and time budget

```
01.03 Tokenization (≈1 wk) ─► 01.01 Transformers (≈2–3 wk) ─► 01.02 Inference (≈2–3 wk) ─► 01.04 Context (≈2 wk)
        │                               │                               │                          │
   BPE project                 decoder project (P1) is          mini engine reuses         packer + RAG harness
   feeds every later project   the base model for 01.02 P1      01.01 P1's model            reuse 01.02 load tester
```

Tokenization comes first because every later project counts, packs, or streams tokens. The 01.01 decoder is reused as the model behind the 01.02 serving engine.

## Minimum hardware

- **CPU / laptop:** all of 01.03, the 01.01 P1 tests, and the 01.04 P1 packer.
- **1 × 24 GB GPU:** 01.01 P1–P3, 01.02 P1 and P3 (small models), and 01.04 P2 (≤1.5B model with LoRA).
- **1–2 × 80 GB GPUs (cloud, by the hour):** 01.02 P2 capacity sweeps, and 01.04 P3 at 70B-class scale.

## Conventions

- Math is written in LaTeX (`$…$` inline, `$$…$$` for blocks) and renders on GitHub, Obsidian, VS Code, and Typora.
- `🖼️ Image prompt` blocks are ready to paste into an image model.
- Code targets Python ≥ 3.10 and PyTorch ≥ 2.5. The reference snippets were executed and checked: cached and chunked prefill equal the full forward pass, the RoPE relative-position property holds, speculative sampling matches the target distribution, BPE round-trips losslessly, and the context packer never exceeds its budget.
- Pin engine versions (vLLM, SGLang, TensorRT-LLM) in your own projects, because flags change between releases.
