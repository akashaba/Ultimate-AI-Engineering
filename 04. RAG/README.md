# Module 4 — RAG

> Part of the **AI Engineer Master Curriculum**. Each subtopic file contains deep-dive theory with math, production failure modes and fixes, runnable reference code, ASCII diagrams, image-generation prompts, three graded projects (user stories, acceptance criteria, step-by-step builds), and a paper and tooling list.

| # | File | Core question | Key artifacts you'll build |
|---|---|---|---|
| 04.01 | [Chunking](04-01-chunking.md) | What units should you index and generate from, and how do you compare chunkers fairly? | Fixed-token-budget chunking benchmark · faithful parser (tables, amendatory markup) · parent–child + RAPTOR hierarchy |
| 04.02 | [Hybrid Search](04-02-hybrid-search.md) | How do you run hybrid retrieval as the RAG layer, inside real engines, with correct filters, time, and caching? | Postgres-only vs OpenSearch hybrid RAG · "as-of" legislation RAG · failure-attribution dashboard |
| 04.03 | [Reranking](04-03-reranking.md) | How do you train, run, and *calibrate* rerankers so they decide what the LLM sees? | Distilled domain cross-encoder · LLM reranker lab (bias, cost) · calibrated gate with dynamic k + abstention |
| 04.04 | [Query Rewriting](04-04-query-rewriting.md) | How do you turn messy conversational input into validated retrieval plans without adding latency or errors? | Conversational query planner · multi-hop decomposition vs IRCoT · self-query filter extraction |
| 04.05 | [GraphRAG](04-05-graphrag.md) | When does graph structure beat strong hybrid RAG, and how do you build graphs cheaply and correctly? | Legislative knowledge graph + Text2Cypher · GraphRAG vs hybrid with cost accounting · HippoRAG-style PPR retrieval |

## How Module 4 relates to Module 3 (no repetition, deeper layers)

```
03.04 §2–5  scoring & fusion basics      ──►  04.02  hybrid inside Postgres/OpenSearch/ES, filters, time, caching, attribution
03.04 §6    reranker introduction        ──►  04.03  ranking losses, distillation, LLM strategies & bias, calibration/gating
03.04 §7    query understanding intro    ──►  04.04  query planner, conversational rewriting, decomposition, self-query, drift guards
03.01 §5    chunking (embedding view)    ──►  04.01  parsing, strategies, containment math, fixed-budget evaluation
03.03       CDC, ACLs, versioned chunks  ──►  used throughout (04.02 filters, 04.05 incremental graph updates)
```

## Suggested order and time budget

`04.01 (≈1.5 wk) → 04.02 (≈2 wk) → 04.03 (≈2 wk) → 04.04 (≈2 wk) → 04.05 (≈2.5 wk)`

The projects form one evolving legislative RAG system:
- The 04.01 chunks feed the 04.02 retrieval layer.
- The 04.03 reranker and gate sit on top of that layer.
- The 04.04 planner routes queries into all of them.
- 04.05 adds graph routes for relational and global questions.

## Facts checked for this module (September 2026)

- **Elasticsearch 9.x retrievers:** `rrf` (`rank_constant` default 60, `rank_window_size`, per-retriever `weight`) and `linear` (`weight`, `normalizer: "minmax"`) confirmed from the official docs.
- **OpenSearch** hybrid query + `normalization-processor` / `score-ranker-processor`: the syntax follows the documented pattern, but the docs pages could not be fetched during writing, so **verify it for your version**.
- **LazyGraphRAG** (Microsoft Research, Nov 2024): LLM work deferred to query time; indexing cost comparable to vector RAG; a budgeted relevance search.

## Minimum hardware

- **CPU / laptop:** 04.01 P1–P2, 04.02 P1–P3 (Docker Compose Postgres/OpenSearch), 04.04 P1 and P3, and 04.05 P1 (Neo4j Community) and P3 (networkx).
- **1 × 24 GB GPU:** 04.03 P1 (reranker training/distillation), and 04.03 P2 / 04.04 P2 with open-model LLMs.
- **API budget:** LLM judging and extraction (04.03 P1–P2, 04.05 P1–P2). Use batch APIs and caching.

## Verification

Every Python snippet was executed and tested:
- Chunk-containment formula vs simulation, the recursive/structure/semantic chunkers (with offset integrity), parent merging, and span metrics.
- Failure attribution, source quotas, recency decay, dynamic cut, and the identifier-guarded semantic cache.
- Ranking losses; the sliding-window listwise reranker (correct top 10 in 4 calls); both-order PRP; permutation self-consistency (removes injected position bias); Platt calibration (ECE 0.145 → 0.02).
- HyDE, multi-query RRF, the DAG plan executor with cycle detection, iterative retrieval, and plan validation (restores dropped IDs, drops invented ones and unknown sessions).
- Citation-edge extraction, entity resolution, Louvain + modularity, PPR (matches networkx within 1e-5), local context, global map-reduce, and the Cypher guard.

SQL, OpenSearch/Elasticsearch JSON, and Cypher snippets were reviewed but not executed.
