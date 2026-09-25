# AI Engineer Master Curriculum

> A production-oriented study guide for becoming an expert AI engineer: **10 modules, 43 subtopics, 129 hands-on projects**, about 20,000 lines of dense technical material.
> Every subtopic covers:
> - the underlying mechanics and math;
> - production failure modes and their fixes;
> - runnable, **tested** reference code;
> - architecture diagrams and image-generation prompts;
> - three graded projects;
> - the foundational papers (exact titles) and tooling.
>
> Built September 2026. Many projects are tailored to **legislative systems** (bills, MCA statutes, fiscal notes, committee hearings) on a **Java/Spring Boot · Kafka · React · Kubernetes · Postgres/pgvector · Azure (Entra ID)** stack.

---

## Repository Layout

```
ultimate-ai-engineering/
├── README.md                          ← you are here (master index)
├── 01. LLM Fundamentals        (4 subtopics)
├── 02. Prompting & Context     (4)
├── 03. Representations         (4)
├── 04. RAG                     (5)
├── 05. Tools & Agents          (6)
├── 06. Evaluation              (4)
├── 07. Safety                  (3)
├── 08. LLMOps & Serving        (5)
├── 09. Adaptation              (4)
└── 10. Advanced                (4)
```

Each module folder has its own `README.md`, containing:
- the subtopic table;
- how the pieces fit;
- the suggested order within the module;
- the facts verified at the time of writing;
- the verification summary for its code.

---

## Modules at a Glance

| # | Module | What you master | Est. time |
|---|---|---|---|
| 01 | [LLM Fundamentals](/01.%20LLM%20Fundamentals/README.md) | Transformer internals, the inference cost model, tokenizers, context limits | 7–9 wk |
| 02 | [Prompting & Context](/02.%20Prompting%20&%20Context/README.md) | Evidence-driven prompting, context engineering, typed outputs, grammar-constrained decoding | 7.5 wk |
| 03 | [Representations](/03.%20Representations/README.md) | Embeddings, ANN indexes (HNSW/IVF/PQ), vector databases (pgvector), hybrid retrieval | 8 wk |
| 04 | [RAG](/04.%20RAG/README.md) | Chunking, hybrid search, reranking, query rewriting, GraphRAG | 10 wk |
| 05 | [Tools & Agents](/05.%20Tools%20&%20Agents/README.md) | Tool calling, agent workflows, memory/state, MCP, A2A, human-in-the-loop | 10 wk |
| 06 | [Evaluation](/06.%20Evaluation/README.md) | Evals with honest statistics, test datasets, online metrics and A/B, tracing | 7.5 wk |
| 07 | [Safety](/07.%20Safety/README.md) | Guardrails, AI security (OWASP LLM/Agentic), prompt-injection containment | 5.5 wk |
| 08 | [LLMOps & Serving](/08.%20LLMOps%20&%20Serving/README.md) | Routing, caching and cost, vLLM on Kubernetes, quantization and batching, release lifecycle | 9 wk |
| 09 | [Adaptation](/09.%20Adaptation/README.md) | Fine-tuning (SFT/DPO/GRPO), PEFT, LoRA/QLoRA, distillation | 7 wk |
| 10 | [Advanced](/10.%20Advanced/README.md) | Multimodal, knowledge graphs, AI app architecture, product integration | 7.5 wk |

**Total:** roughly **80 weeks** at a steady part-time pace if you complete every project. Reading plus one project per subtopic takes roughly half that.

---

## Full Subtopic Index

### Module 1 — LLM Fundamentals
| # | Subtopic | Core question |
|---|---|---|
| 01.01 | [Transformers & Attention](/01.%20LLM%20Fundamentals/01-01-transformers-attention.md) | What exactly does a modern decoder compute, and what does it cost? |
| 01.02 | [Inference & Decoding](/01.%20LLM%20Fundamentals/01-02-inference-decoding.md) | Where does every millisecond of a request go, and how do you control it? |
| 01.03 | [Tokenization](/01.%20LLM%20Fundamentals/01-03-tokenization.md) | How does the tokenizer shape cost, context, fairness, and security? |
| 01.04 | [Context Window & Limits](/01.%20LLM%20Fundamentals/01-04-context-window-limits.md) | Why is context limited, and how do you get the *effective* context you need? |

### Module 2 — Prompting & Context
| # | Subtopic | Core question |
|---|---|---|
| 02.01 | [Prompting & In-Context Learning](/02.%20Prompting%20&%20Context/02-01-prompting-in-context-learning.md) | Why does ICL work, and how do you ship prompt changes on evidence rather than vibes? |
| 02.02 | [Context Engineering](/02.%20Prompting%20&%20Context/02-02-context-engineering.md) | What should the model see on every call, across turns, tools, memory, and agents? |
| 02.03 | [Structured Outputs & Schemas](/02.%20Prompting%20&%20Context02-03-structured-outputs-schemas.md) | How do you turn model output into a typed, validated, versioned contract? |
| 02.04 | [Constrained Decoding](/02.%20Prompting%20&%20Context/02-04-constrained-decoding.md) | How do grammar-constrained engines work, how fast are they, and how do they bias output? |

### Module 3 — Representations
| # | Subtopic | Core question |
|---|---|---|
| 03.01 | [Embeddings](/03.%20Representations/03-01-embeddings.md) | How are embedding models trained, and how do you choose, compress, and operate them? |
| 03.02 | [Similarity Search](/03.%20Representations/03-02-similarity-search.md) | How do ANN indexes trade recall for speed and memory, and how do you tune one? |
| 03.03 | [Vector Databases](/03.%20Representations/03-03-vector-databases.md) | How do you run vectors as a correct, secure, scalable *database*? |
| 03.04 | [Hybrid Retrieval](/03.%20Representations/03-04-hybrid-retrieval.md) | How do you fuse lexical, sparse, dense, and late-interaction signals? |

### Module 4 — RAG
| # | Subtopic | Core question |
|---|---|---|
| 04.01 | [Chunking](/04.%20RAG/04-01-chunking.md) | What units should you index and generate from, and how do you compare chunkers fairly? |
| 04.02 | [Hybrid Search](/04.%20RAG/04-02-hybrid-search.md) | How do you run hybrid retrieval as the RAG layer, with correct filters, time, and caching? |
| 04.03 | [Reranking](/04.%20RAG/04-03-reranking.md) | How do you train, run, and calibrate rerankers that decide what the LLM sees? |
| 04.04 | [Query Rewriting](/04.%20RAG/04-04-query-rewriting.md) | How do you turn messy conversational input into validated retrieval plans? |
| 04.05 | [GraphRAG](/04.%20RAG/04-05-graphrag.md) | When does graph structure beat strong hybrid RAG, and how do you build graphs cheaply? |

### Module 5 — Tools & Agents
| # | Subtopic | Core question |
|---|---|---|
| 05.01 | [Tool / Function Calling](/05.%20Tools%20&%20Agents/05-01-tool-function-calling.md) | How do you design tools models use correctly, and run them safely? |
| 05.02 | [Agentic Workflows](/05.%20Tools%20&%20Agents/05-02-agentic-workflows.md) | How much autonomy does a task need, and how do you make agents reliable and measurable? |
| 05.03 | [Agent Memory & State](/05.%20Tools%20&%20Agents/05-03-agent-memory-state.md) | How do you make runs durable and replayable, and memory useful and safe? |
| 05.04 | [MCP](/05.%20Tools%20&%20Agents/05-04-mcp.md) | How do you expose and consume capabilities over the Model Context Protocol securely? |
| 05.05 | [A2A / Interoperability](/05.%20Tools%20&%20Agents/05-05-a2a-interoperability.md) | How do independent agents across teams, stacks, and organisations collaborate safely? |
| 05.06 | [Human-in-the-Loop](/05.%20Tools%20&%20Agents/05-06-human-in-the-loop.md) | Where do humans belong, and how do you make oversight tamper-proof and effective? |

### Module 6 — Evaluation
| # | Subtopic | Core question |
|---|---|---|
| 06.01 | [Evals](/06.%20Evaluation/06-01-evals.md) | What do you measure, with which graders and how many examples, and how do you report uncertainty? |
| 06.02 | [Test Datasets](/06.%20Evaluation/06-02-test-datasets.md) | How do you build eval data that is representative, clean, and durable? |
| 06.03 | [Online Metrics](/06.%20Evaluation/06-03-online-metrics.md) | Does it work for real users, and how do you know a change helped? |
| 06.04 | [Tracing & Debugging](/06.%20Evaluation/06-04-tracing-debugging.md) | Why did the system do that, and how do you reproduce and fix it? |

### Module 7 — Safety
| # | Subtopic | Core question |
|---|---|---|
| 07.01 | [Guardrails](/07.%20Safety/07-01-guardrails.md) | How do you enforce policy on inputs, context, actions, and outputs without killing latency? |
| 07.02 | [AI Security](/07.%20Safety/07-02-ai-security.md) | What do attackers actually exploit around the model, and which controls matter most? |
| 07.03 | [Prompt-Injection Defense](/07.%20Safety/07-03-prompt-injection-defense.md) | Since injection can't be fully detected, how do you design agents where it can't cause serious harm? |

### Module 8 — LLMOps & Serving
| # | Subtopic | Core question |
|---|---|---|
| 08.01 | [Model Selection & Routing](/08.%20LLMOps%20&%20Serving/08-01-model-selection-routing.md) | Which model should serve each request, at what cost, and what happens when one fails? |
| 08.02 | [Caching, Latency & Cost](/08.%20LLMOps%20&%20Serving/08-02-caching-latency-cost.md) | Where do the milliseconds and dollars go, and which levers move them safely? |
| 08.03 | [vLLM / TGI](/08.%20LLMOps%20&%20Serving/08-03-vllm-tgi.md) | How do you size, deploy, observe, autoscale, and upgrade a self-hosted inference engine? |
| 08.04 | [Quantization & Batching](/08.%20LLMOps%20&%20Serving/08-04-quantization-batching.md) | Which number format and batching policy maximise goodput without silent quality loss? |
| 08.05 | [Production Lifecycle](/08.%20LLMOps%20&%20Serving/08-05-production-lifecycle.md) | How do you release, operate, and govern an LLM system as one versioned unit? |

### Module 9 — Adaptation
| # | Subtopic | Core question |
|---|---|---|
| 09.01 | [Fine-Tuning](/09.%20Adaptation/09-01-fine-tuning.md) | When does changing the weights beat prompting and RAG, and how do you do it correctly? |
| 09.02 | [PEFT](/09.%20Adaptation/09-02-peft.md) | Which parameter-efficient method fits the task, memory, and serving engine? |
| 09.03 | [LoRA / QLoRA](/09.%20Adaptation/09-03-lora-qlora.md) | How do rank, α, targets, and LR interact, and how do you ship on a 4-bit base? |
| 09.04 | [Distillation](/09.%20Adaptation/09-04-distillation.md) | How do you move a teacher's behaviour into a 10–100× cheaper student? |

### Module 10 — Advanced
| # | Subtopic | Core question |
|---|---|---|
| 10.01 | [Multimodal AI](/10.%20Advanced/10-01-multimodal-ai.md) | How do you use images, documents, and audio faithfully, at a known cost? |
| 10.02 | [Knowledge Graphs](/10.%20Advanced/10-02-knowledge-graphs.md) | How do you build a trustworthy, time-aware graph and query it safely? |
| 10.03 | [AI App Architecture](/10.%20Advanced/10-03-ai-app-architecture.md) | What does a production LLM system look like end to end in a Java/Kafka/Entra ID shop? |
| 10.04 | [Product Integration](/10.%20Advanced/10-04-product-integration.md) | How much autonomy should a feature have, and how do you ship it so people trust and adopt it? |

---

## How the Curriculum Fits Together

```
                    ┌──────────────────────────── 10. ADVANCED ────────────────────────────┐
                    │  multimodal inputs · knowledge graphs · reference architecture · UX   │
                    └──────────────────────────────────────────────────────────────────────┘
   FOUNDATIONS                 KNOWLEDGE                ACTION               QUALITY & TRUST            PRODUCTION
 ┌───────────────┐       ┌──────────────────┐      ┌──────────────┐      ┌──────────────────┐      ┌──────────────────┐
 │01 LLM fundam. │──────►│03 Representations│─────►│05 Tools &    │─────►│06 Evaluation     │─────►│08 LLMOps &       │
 │02 Prompting & │       │04 RAG            │      │   Agents     │      │07 Safety         │      │   Serving        │
 │   Context     │       └──────────────────┘      └──────────────┘      └──────────────────┘      │09 Adaptation     │
 └───────────────┘                                                                                  └──────────────────┘
        ▲                                                                                                    │
        └──────────────── feedback: traces → datasets → evals → fine-tuning → gated releases ◄───────────────┘
```

**Cross-reference convention.** Files refer to each other as `MM.SS §N`. For example, `04.05 §3` means Module 4, subtopic 5, section 3. Later modules build on earlier ones through these references rather than repeating material.

---

## Learning Paths

**Full path (recommended):** modules 01 → 10 in order. Within a module, follow the order its README suggests; some modules put a later subtopic first on purpose, e.g. 07.02 before 07.01.

**Fast track to shipping a production RAG assistant (~20 weeks):**
`01.02 → 01.04 → 02.01 → 02.03 → 03.01 → 03.03 → 04.01 → 04.02 → 04.03 → 06.01 → 06.04 → 07.01 → 07.03 → 08.01 → 08.02 → 10.03 → 10.04`

**Agent-platform track:** `02.02 → 05.01–05.06 → 06.01 → 06.04 → 07.02 → 07.03 → 08.05 → 10.03`

**Self-hosting and model-customisation track:** `01.01 → 01.02 → 08.03 → 08.04 → 09.01–09.04 → 08.05`

**Suggested capstone:** combine the projects of **10.03** (event-driven pipeline, secure Spring AI service) and **10.04** (amendment drafting with diff review, pilot launch). Gate the result with **06.01** evals, **07.x** safety suites, and **08.05** releases. This exercises nearly every module.

---

## What Every Subtopic File Contains

1. **Status check.** Facts verified at the time of writing (library versions, API changes, standards), with the date.
2. **Deep-dive theory.** Mechanisms, equations (LaTeX), and ASCII architecture diagrams.
3. **Reference code.** Runnable Python (NumPy, PyTorch, PEFT, TRL, networkx, Pillow…) with `assert`-based checks that prove each claim. Java/SQL/YAML sketches are included where the production stack calls for them.
4. **Production challenges table.** Symptom → root cause → solution.
5. **Three hands-on projects.** Each has user stories, measurable acceptance criteria, and a step-by-step build guide.
6. **Foundational papers** (exact titles), standards, and **essential tooling**.
7. **Image-generation prompts** for diagrams worth visualising.

---

## Verification

- **Every Python block was executed**, both sequentially within its file and **standalone**, so each block runs on its own when copied out.
- Library-dependent examples (TRL/PEFT trainers, adapters, distillation) were run end-to-end **on tiny local models** to confirm that the exact API calls and arguments are correct.
- Blocks that need a GPU or an external service (for example bitsandbytes QLoRA, LLM Compressor, the vLLM offline API) are illustrative and marked as such. Java (Spring AI), SQL, Cypher, and YAML snippets are sketches to adapt and verify against your pinned versions.
- Numbers quoted in the prose (error rates, speedups, costs) come from the actual test output of the code in the same file.

**Environment for running the code:** Python 3.11+, with `numpy scipy torch transformers peft trl datasets networkx pillow`. A CPU is enough for everything in the files; a 24–80 GB GPU is needed for most projects.

---

## Keeping It Current

AI tooling moves fast. Each module README lists the **facts checked (September 2026)**, including:
- the OWASP LLM and Agentic Top 10s;
- the MCP 2026-07-28 spec and MCP Apps;
- the A2A v1.0 spec;
- the OpenTelemetry GenAI semantic conventions;
- the vLLM v0.30 release and the fact that TGI is in maintenance mode;
- TRL 1.13 and PEFT 0.21;
- Spring AI 2.0;
- provider fine-tuning availability (OpenAI is winding down its platform; Azure AI Foundry continues);
- image-token pricing formulas;
- Montana HB 178 (2025).

**Re-verify these before relying on them.** Pin versions in your own projects, and read release notes before upgrading.

---

## Progress Tracker

Copy this into your notes and tick items off as you go (reading ✓ · one project ✓ · all three projects ✓):

```
[ ] 01.01  [ ] 01.02  [ ] 01.03  [ ] 01.04
[ ] 02.01  [ ] 02.02  [ ] 02.03  [ ] 02.04
[ ] 03.01  [ ] 03.02  [ ] 03.03  [ ] 03.04
[ ] 04.01  [ ] 04.02  [ ] 04.03  [ ] 04.04  [ ] 04.05
[ ] 05.01  [ ] 05.02  [ ] 05.03  [ ] 05.04  [ ] 05.05  [ ] 05.06
[ ] 06.01  [ ] 06.02  [ ] 06.03  [ ] 06.04
[ ] 07.01  [ ] 07.02  [ ] 07.03
[ ] 08.01  [ ] 08.02  [ ] 08.03  [ ] 08.04  [ ] 08.05
[ ] 09.01  [ ] 09.02  [ ] 09.03  [ ] 09.04
[ ] 10.01  [ ] 10.02  [ ] 10.03  [ ] 10.04
[ ] Capstone
```
