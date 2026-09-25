# Module 3 — Representations

> Part of the **AI Engineer Master Curriculum**. Each subtopic file contains deep-dive theory with math, production failure modes and fixes, runnable reference code, ASCII diagrams, image-generation prompts, three graded projects (user stories, acceptance criteria, step-by-step builds), and a paper and tooling list.

| # | File | Core question | Key artifacts you'll build |
|---|---|---|---|
| 03.01 | [Embeddings](03-01-embeddings.md) | How are embedding models built and trained, and how do you choose, compress, and operate them on *your* data? | Domain fine-tuning with hard negatives + Matryoshka · model/compression Pareto study · zero-downtime embedding migration (Kafka + Spring Boot) |
| 03.02 | [Similarity Search](03-02-similarity-search.md) | How do ANN indexes trade recall for speed and memory, and how do you pick and tune one? | LSH/IVF/PQ/HNSW from scratch vs FAISS & hnswlib · filtered search across selectivities · 500M-vector capacity plan |
| 03.03 | [Vector Databases](03-03-vector-databases.md) | How do you run vectors as a correct, secure, scalable *database*? | pgvector store with Debezium CDC, RLS, and iterative scans · vector DB bake-off · multi-tenant vector platform with a recall canary |
| 03.04 | [Hybrid Retrieval](03-04-hybrid-retrieval.md) | How do you combine lexical, sparse, dense, and late-interaction signals into a cascade that wins on every query type? | Hybrid legislative search engine · fusion & reranking Pareto study · LLM-assisted relevance judgments + domain reranker |

## How Module 3 connects to earlier modules

```
01.01 encoders / pooling ─────────► 03.01 embedding models
02.01 §7 paired significance ─────► 03.01 §4, 03.04 §8 retrieval evaluation
02.02 §3 RRF, context assembly ───► 03.04 fusion & cascades (the deep dive)
02.03 structured outputs ─────────► 03.04 §7 query rewriting / filter extraction, §8 LLM judges
03.01 ─► 03.02 ─► 03.03 ─► 03.04  ─►  (next: RAG systems end to end)
```

## Suggested order and time budget

`03.01 (≈2 wk) → 03.02 (≈2 wk) → 03.03 (≈2 wk) → 03.04 (≈2 wk)`

The 03.01 fine-tuned model and eval set are reused in 03.02 (as benchmark data), 03.03 (as the stored vectors), and 03.04 (as the dense leg).

## Facts checked for this module (September 2026)

- **pgvector 0.8.x** (latest release 0.8.6, July 2026) supports HNSW and IVFFlat, `vector`/`halfvec`/`sparsevec`/`bit` types, `binary_quantize`, and iterative index scans (`hnsw.iterative_scan`, `hnsw.max_scan_tuples`).
- Vendor feature sets for dedicated vector databases change quickly. 03.03 §3 describes architectures and tells you to verify the specifics before deciding.

## Minimum hardware

- **CPU / laptop:** all from-scratch code (the 03.02 implementations run at 10k–100k scale), 03.03 P1 in Docker Compose, and 03.04 P1 with a small reranker.
- **1 × 24 GB GPU:** 03.01 P1–P2 (fine-tuning and bulk embedding), 03.04 P2–P3 (reranker training and batch reranking).
- **Cloud VMs / Kubernetes (by the hour):** 03.02 P3 (NVMe for DiskANN), 03.03 P2–P3 (bake-off, multi-tenant platform).

## Conventions and verification

LaTeX math, `🖼️ Image prompt` blocks, and Python ≥ 3.10 (SQL and Java snippets for the Postgres/Spring path). Every Python snippet was executed and tested:
- Pooling and embedding (on a tiny random BERT), InfoNCE/MRL losses, int8/binary quantization, and IR metrics (checked by hand).
- Exact top-k, LSH, IVF, PQ/ADC, and HNSW (recall 0.92 / 0.98 / 0.995 at ef = 10 / 50 / 150 vs exact search), plus the filter planner.
- The CDC ingest handler (idempotent replay, hash-based re-embed skipping, stale-event rejection, deletes), the capacity planner, and the recall canary.
- BM25 (verified by hand), convex/z-score/theoretical fusion, α tuning, the query router, MaxSim, SPLADE encoding, and the async cascade with timeout degradation.

The SQL and Java snippets were reviewed but not executed (no Postgres in the sandbox).
