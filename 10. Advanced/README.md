# Module 10 — Advanced

> Part of the **AI Engineer Master Curriculum**. Each subtopic file contains:
> - deep-dive theory with math;
> - production failure modes and fixes;
> - runnable, tested reference code;
> - ASCII diagrams;
> - three graded projects (user stories, acceptance criteria, step-by-step builds);
> - a list of papers, standards, and tooling.

| # | File | Core question | Key artifacts you'll build |
|---|---|---|---|
| 10.01 | [Multimodal AI](10-01-multimodal-ai.md) | How do you use images, documents, and audio faithfully, at a known cost? | Amendment-aware bill PDF understanding · hearing transcription with motion extraction · visual RAG over fiscal notes |
| 10.02 | [Knowledge Graphs](10-02-knowledge-graphs.md) | How do you build a trustworthy, time-aware graph of legislative relationships and query it safely? | MCA cross-reference graph + drafter impact tool · legislative KG with ER and bitemporal edges · graph-grounded Q&A with safe text-to-Cypher |
| 10.03 | [AI App Architecture](10-03-ai-app-architecture.md) | What does a production LLM system look like end to end, in a Java/Kafka/Entra ID shop? | Event-driven bill-processing pipeline (outbox, DLQ, chaos-tested) · Spring AI service with OBO + ACL-aware RAG + MCP tools · architecture review package (C4, ADRs, FMEA, game day) |
| 10.04 | [Product Integration](10-04-product-integration.md) | How much autonomy should a feature have, and how do you ship it so people verify, trust, and adopt it? | AI amendment drafting with legislative diff review · feedback pipeline → dashboard + training data · pilot launch plan with experiment and governance |

## How the pieces fit

```
 inputs: PDFs, scans, audio (10.01) ─► canonical docs w/ provenance ─► KG (10.02) + vector index (03.x/04.x)
                                                                        │
 users ◄── product surfaces (10.04: diffs, streaming, feedback) ◄── AI services (10.03: use-case APIs, Kafka,
                                                                        OBO, budgets) ◄── gateway/serving (08.x)
 feedback & traces ─► evals/datasets (06.x) ─► adaptation (09.x) ─► gated releases (08.05)
```

## Suggested order and time budget

`10.03 (≈2 wk) → 10.01 (≈2 wk) → 10.02 (≈2 wk) → 10.04 (≈1.5 wk)`

Architecture comes first because the other three plug into it. The 10.03 and 10.04 projects make a natural capstone for the whole curriculum.

## Facts checked for this module (September 2026)

- **Anthropic vision:** $\lceil w/28\rceil \times \lceil h/28\rceil$ visual tokens. Images are downscaled to 1568 px / 1568 tokens, or 2576 px / 4784 tokens on high-resolution models (Claude 4.7+).
- **OpenAI** uses 32-px patches times a per-model multiplier on newer models, and a tile rule on GPT-4o-class models. Verify per model before budgeting.
- **Spring AI 2.0.0 GA** (Jun 12, 2026) targets Spring Boot 4.0/4.1 and Framework 7. It brings `ChatClient` + advisor chain, `ToolSearchToolCallingAdvisor`, MCP Java SDK 2.0 with `@McpTool` / `@McpResource` / `@McpPrompt`, and Jackson 3.
- **Apache AGE** is supported on **Azure Database for PostgreSQL flexible server** (enable it in `azure.extensions` + `shared_preload_libraries`).
- **MCP Apps** (Jan 26, 2026) is an official extension: `ui://` resources rendered in sandboxed iframes, with JSON-RPC over `postMessage`. Hosts include Claude, ChatGPT, VS Code, and Goose.
- **Standards:** GQL is ISO/IEC 39075:2024, and SQL/PGQ is ISO/IEC 9075-16:2023.

## Verification

Every Python block was executed, both sequentially per file and **standalone** (36 runs, 0 failures). The Java (Spring AI) and SQL/Cypher snippets are illustrative sketches and were not compiled or run.

- **10.01:**
  - CLIP InfoNCE;
  - image-token estimator (a 256² image = 100 tokens; caps respected);
  - page router: **62% of all-VLM cost** and more faithful;
  - **late-interaction MaxSim R@1 0.98 vs 0.45 for mean pooling**, at 64× the storage;
  - WER/ANLS/numeric match: **ANLS gives 0.8 credit for a 10× fiscal error**;
  - image sanitiser strips EXIF/GPS and bounds size.
- **10.02:**
  - SHACL-style schema validation (4 planted violations caught);
  - cross-reference extraction with range expansion against known sections, low-confidence unknown cites, and a documented recall miss;
  - entity resolution with blocking + union-find (pairwise P = R = 1.0, half the comparisons);
  - **bitemporal as-of queries reproduce four distinct historical views**;
  - Cypher guard (10 cases, including unbounded paths and write smuggling);
  - impact analysis with paths, and PageRank.
- **10.03:**
  - under 15% crash injection, **naive handling sent 52 duplicate emails; idempotent + outbox sent 0**, and the response cache brought LLM calls to the theoretical minimum;
  - tenant token budget: **interactive never deferred**, and settlement raises bulk throughput 31%.
- **10.04:**
  - error-economics autonomy model: a UI redesign flips fiscal figures from *off* to *suggest*;
  - **word-level diffs let partial acceptance create an unproposed date ("September 15")**, and semantic hunk merging prevents it;
  - resumable SSE with Last-Event-ID;
  - implicit-feedback metrics and DPO pair mining.
