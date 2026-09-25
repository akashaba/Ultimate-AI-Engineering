# Module 7 — Safety

> Part of the **AI Engineer Master Curriculum**. Each subtopic file contains deep-dive theory with math, production failure modes and fixes, runnable reference code, ASCII diagrams, three graded projects (user stories, acceptance criteria, step-by-step builds), and a paper/standards and tooling list.

| # | File | Core question | Key artifacts you'll build |
|---|---|---|---|
| 07.01 | [Guardrails](07-01-guardrails.md) | How do you enforce deployment policy on inputs, context, actions, and outputs — measurably and without killing latency? | Policy-as-code guardrail service with streaming holdback · guardrail eval + cost-based thresholds · claim-level groundedness guard |
| 07.02 | [AI Security](07-02-ai-security.md) | What do attackers actually exploit around the model, and which controls matter most? | Threat model mapped to OWASP LLM 2025 + Agentic 2026 · output-handling hardening with fuzzing · model/dependency supply-chain pipeline with AI-BOM |
| 07.03 | [Prompt-Injection Defense](07-03-prompt-injection-defense.md) | Since injection can't be fully detected, how do you design agents where it can't cause serious harm? | Secure document/email agent (plan-then-execute, dual LLM, taint policies) vs baseline · adaptive red team vs detectors · org-wide Rule-of-Two audit |

## The safety stack this module builds

```
  CONTAINMENT (07.03)        architecture: Rule of Two, plan-then-execute, dual LLM, taint/sink policies, approvals (05.06)
  SYSTEM SECURITY (07.02)    least privilege, delegated identity, output handling, supply chain, budgets, isolation, IR
  GUARDRAILS (07.01)         policy-as-code rails: PII, scope, neutrality, grounding, streaming checks
  DETECTION & MONITORING     classifiers, anomaly detection, audit trails (06.03, 06.04)
  ─────────────────────────  measured with evals + adaptive red teaming (06.01, 07.03 §6)
```

**Consolidates and deepens:** 02.01 §6 (injection intro), 03.03 §6 (ACLs, RLS), 05.01 §5 (tool security), 05.03 §4 (memory poisoning), 05.04 §7 (MCP threats), 05.05 §5 (A2A trust), 05.06 (approvals).

## Suggested order and time budget

`07.02 (≈2 wk) → 07.03 (≈2 wk) → 07.01 (≈1.5 wk)`

The threat model comes first, then the containment architecture, and the behavioural guardrails go on top.

## Facts checked for this module (September 2026)

- **OWASP Top 10 for LLM Applications 2025:** LLM01 Prompt Injection · LLM02 Sensitive Information Disclosure · LLM03 Supply Chain · LLM04 Data and Model Poisoning · LLM05 Improper Output Handling · LLM06 Excessive Agency · LLM07 System Prompt Leakage · LLM08 Vector and Embedding Weaknesses · LLM09 Misinformation · LLM10 Unbounded Consumption.
- **OWASP Top 10 for Agentic Applications (2026 edition, Dec 2025):** ASI01 Agent Goal Hijack … ASI10 Rogue Agents (full list in 07.02 §1.3).
- **Meta's Agents Rule of Two** (Oct 2025): at most two of {untrusted input, sensitive access, state change/external communication} per session.
- **Nasr et al., *The Attacker Moves Second*** (Oct 2025): adaptive attacks bypassed 12 published defences, most at > 90% ASR; human red-teaming defeated all of them.
- **Beurer-Kellner et al., *Design Patterns for Securing LLM Agents against Prompt Injections*** (2025): action-selector, plan-then-execute, LLM map-reduce, dual LLM, code-then-execute, context minimisation.

## Verification

Every Python snippet was executed and tested, both together and **each block standalone**:
- **Guardrails:** the policy engine (redact vs block precedence; blocked text withheld; fail-closed vs fail-open on detector outages; timeouts); cascade math (99.2% joint recall vs 5.9% joint false blocks); the groundedness guard; the **streaming holdback guard (catches an SSN split across three chunks without emitting its digits)**; guard metrics.
- **Security:** static pickle scanning (flags disallowed globals in protocol 0 and in modern protocols without unpickling); Markdown sanitisation (removes exfiltration image URLs, external links, and script tags; keeps allowlisted hosts); **the SQL guard (blocks DELETE, stacked DROP, unlisted tables, and subquery access to `secrets`; enforces LIMIT; allows CTEs)**; the SSRF target check (blocks metadata, private, and loopback addresses, and non-HTTPS); hash verification; the cost bucket with settlement.
- **Injection defence:** the Rule-of-Two analyser; **plan-then-execute (an injected "send all drafts" instruction causes zero sends)**; dual-LLM quarantine with schema enforcement; taint sink policies (deny untrusted recipients and URLs; require approval for untrusted-derived bodies); known-answer detection; the ASR/utility suite.

Attack content is kept at the pattern level for defensive engineering. No working jailbreaks or exploit payloads are included.
