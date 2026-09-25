# Module 9 — Adaptation

> Part of the **AI Engineer Master Curriculum**. Each subtopic file contains:
> - deep-dive theory with math;
> - production failure modes and fixes;
> - runnable reference code (NumPy/PyTorch/PEFT/TRL, tested);
> - ASCII diagrams;
> - three graded projects (user stories, acceptance criteria, step-by-step builds);
> - a list of papers and tooling.

| # | File | Core question | Key artifacts you'll build |
|---|---|---|---|
| 09.01 | [Fine-Tuning](09-01-fine-tuning.md) | When does changing the weights beat prompting and RAG, and how do you do it correctly? | House-style summarizer (SFT) vs prompted baselines · DPO from staff edits · RFT/GRPO for statute-citation extraction with a verifiable reward |
| 09.02 | [PEFT](09-02-peft.md) | Which parameter-efficient method fits the task, memory, and serving engine? | PEFT bake-off on amendment explanations · multi-adapter serving for department tasks · LoRA/(IA)³/prefix from scratch |
| 09.03 | [LoRA / QLoRA](09-03-lora-qlora.md) | How do rank, α, targets, and LR interact, and how do you fine-tune and ship on a 4-bit base? | LoRA-vs-full-FT study on your data · QLoRA 70B on one GPU → evaluated serving artefact · LoRA + NF4 from scratch |
| 09.04 | [Distillation](09-04-distillation.md) | How do you move a teacher's behaviour into a 10–100× cheaper student without losing the long tail? | Frontier → 8B bill-summary student behind a cascade · logit vs on-policy vs SFT-only study · distilled reranker for RAG |

## How the pieces fit

```
 gap analysis (09.01 §1) ─► prompt/RAG enough? ─yes─► stop
            │ no
            ▼
   data (09.01 §4) ─► SFT ─► [DPO/KTO | GRPO/RFT] ─► distil to a smaller student (09.04) ─► quantize (08.04)
                      │  method: full FT | PEFT (09.02) → LoRA/QLoRA (09.03)
                      ▼
   evals + regression/safety gates (06.x, 07.x) ─► bundle + serve (08.03/08.05): merged weights or multi-LoRA
```

**Builds on:** 01.01 (transformer internals), 01.03 §7 (chat templates), 04.x (RAG, rerankers), 06.01–06.02 (evals, datasets), 08.01 (cascades, TCO), 08.03 (multi-LoRA serving), 08.04 (quantization and slice gates), 08.05 (bundles, governance).

## Suggested order and time budget

`09.01 (≈2.5 wk) → 09.02 (≈1 wk) → 09.03 (≈1.5 wk) → 09.04 (≈2 wk)`

A GPU box (1× 24–80 GB) is needed for the projects. All the code in the files runs on CPU.

## Facts checked for this module (September 2026)

- **OpenAI** is *"winding down the fine-tuning platform"*. It is closed to new users, existing users can create jobs "for the coming months", and fine-tuned models serve until their base models are deprecated.
- **Azure AI Foundry** fine-tuning:
  - SFT on gpt-4o-mini, gpt-4o, and gpt-4.1 / mini / nano;
  - DPO on gpt-4o and the gpt-4.1 family;
  - RFT on o4-mini (gpt-5 RFT is invitation-only);
  - open models (Llama-3.3-70B-Instruct, Qwen-32B, gpt-oss-20b, Ministral-3B);
  - Standard, Global, and Developer training tiers.
- **Hugging Face TRL 1.13.0** (Sep 10, 2026) has SFT/DPO/GRPO/KTO/RLOO/Reward/**Distillation** trainers. Verified defaults:
  - `SFTConfig`: `loss_type="chunked_nll"`, `packing_strategy="bfd"`, `assistant_only_loss` option;
  - `GRPOConfig`: `loss_type="dapo"`, `beta=0.0`, `scale_rewards="group"`, `num_generations=8`;
  - `DPOConfig`: `beta=0.1`;
  - `DistillationConfig`: generalised JSD `beta=1.0` (reverse KL).
- **PEFT 0.21** implements 40+ methods, including DoRA, PiSSA, OLoRA, CorDA, EVA, LoftQ, VeRA, and the 2026 additions (HiRA, GLoRA, BEFT, Uni-LoRA, …).
- **LoRA Without Regret** (Thinking Machines, 2025; summarised in the TRL docs) recommends:
  - all-linear targets;
  - an LR about 10× that of full FT;
  - a modest batch size;
  - rank ≈ 256 for post-training-scale SFT and 1–32 for RL.

## Verification

Every Python block was executed both sequentially per file and **standalone** (40 runs, 0 failures). The one exception is the CUDA/bitsandbytes QLoRA script, which was not run. The TRL and PEFT blocks were run **end-to-end on tiny local Llama models** (a student and a teacher with a custom BPE tokenizer and a chat template with `{% generation %}` markers). This checks the exact API calls and arguments shown against TRL 1.13 / PEFT 0.21.

- **09.01:**
  - the loss-normalisation bug (mean-of-means overweights a 3-token example 11×);
  - memory model: 8B full FT ≈134 GB, LoRA ≈22.6 GB, QLoRA ≈10.7 GB (6.5 GB with chunked logits); ZeRO-3 on 8 GPUs gives 16.1 GB static per GPU;
  - dataset validator (duplicates, role order, empty targets, length, n-gram eval contamination);
  - **DPO converges exactly to π_ref·e^{r/β}/Z** (max error < 1e-4), and KL shrinks as β grows;
  - the GRPO group advantage and verifiable citation reward;
  - SFT, DPO, and GRPO training on the tiny model (the GRPO run shows the zero-variance-group failure mode).
- **09.02:**
  - closed-form trainable counts for LoRA, (IA)³, prompt, and prefix **match PEFT exactly**; LoRA r=16 all-linear on 8B is 41.9M parameters (0.52%, 80 MiB);
  - controlled experiment: **LoRA r=2 beats a bottleneck adapter with 4× fewer parameters**;
  - multi-adapter switching; merge matches unmerged to 3e-7.
- **09.03:**
  - under α/r the first-step update shrinks as 1/√r and high rank learns no faster, while rsLoRA keeps it constant (the claims from the literature are reconciled in §2);
  - LoRA, rsLoRA, DoRA, PiSSA, and OLoRA all start at the base model's function;
  - **the NF4 codebook matches bitsandbytes' table to 1e-4**; NF4 has 27% lower MSE than INT4; double quantization gives 4.127 bits/param;
  - **QLoRA serving artefacts differ:** merged-into-BF16 has 0.0013 error vs 0.0059 unmerged vs 0.0146 re-quantized.
- **09.04:**
  - **forward KL puts 42% of mass where the teacher has ~0, while reverse KL puts 0.4% there but drops a mode**;
  - the T² factor keeps KD gradients stable;
  - **top-64 sparse logits recover 98% of the full-KL gradient** (77 GB vs 12.8 TB);
  - the generalised JSD matches TRL's convention;
  - the verified, self-consistent distillation-set builder;
  - TRL `DistillationTrainer` runs end-to-end.
