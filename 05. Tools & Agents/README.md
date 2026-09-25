# Module 5 — Tools & Agents

> Part of the **AI Engineer Master Curriculum**. Each subtopic file contains deep-dive theory with math, production failure modes and fixes, runnable reference code, ASCII diagrams, three graded projects (user stories, acceptance criteria, step-by-step builds), and a paper/spec and tooling list.

| # | File | Core question | Key artifacts you'll build |
|---|---|---|---|
| 05.01 | [Tool / Function Calling](05-01-tool-function-calling.md) | How do you design tools models use correctly, and run them safely? | Provider-agnostic tool runtime + eval suite · 200-tool selection study · Spring Boot legislative tool service |
| 05.02 | [Agentic Workflows](05-02-agentic-workflows.md) | How much autonomy does a task need, and how do you make agents reliable and measurable? | Workflow vs agent vs multi-agent comparison · durable research agent · simulated-user eval harness with pass^k |
| 05.03 | [Agent Memory & State](05-03-agent-memory-state.md) | How do you make runs durable and replayable, and memory useful and safe? | Event-sourced runtime with time travel · skill-learning agent · memory-poisoning red team |
| 05.04 | [MCP](05-04-mcp.md) | How do you expose and consume capabilities over the (now stateless) Model Context Protocol securely? | Production legislative MCP server · MCP gateway with pinning & audit · MCP red team |
| 05.05 | [A2A / Interoperability](05-05-a2a-interoperability.md) | How do independent agents across teams, stacks, and orgs collaborate safely? | Python ↔ Java agents over A2A · agent registry & gateway · A2A conformance/chaos suite |
| 05.06 | [Human-in-the-Loop](05-06-human-in-the-loop.md) | Where do humans belong, and how do you make oversight tamper-proof, staffed, and effective? | Approval-gated amendment drafting agent · cost-optimal escalation + staffing · feedback flywheel with canaries |

## How the pieces fit

```
                         05.06 human oversight (approval tokens, escalation, audit)
                                          ▲
 05.01 tool runtime ──► 05.02 workflows/agents ──► 05.03 durable state & memory
        │                        │
        ▼                        ▼
 05.04 MCP (tools across      05.05 A2A (agents across
 process/network boundaries)  team/org boundaries)
```

Builds on: 02.03 (schemas and validation), 02.02 (context, memory basics), 04.03 (calibration → escalation thresholds), 03.03 (Postgres, CDC → event stores).

## Suggested order and time budget

`05.01 (≈1.5 wk) → 05.02 (≈2 wk) → 05.03 (≈2 wk) → 05.04 (≈1.5 wk) → 05.05 (≈1.5 wk) → 05.06 (≈1.5 wk)`

## Facts checked for this module (September 2026)

- **MCP 2026-07-28** is the latest stable spec (released July 28, 2026). It is **stateless**:
  - The `initialize` handshake and sessions are removed, `_meta` carries the version and capabilities, and `server/discover` is added.
  - Server-to-client requests are replaced by Multi Round-Trip Requests (`InputRequiredResult`, `requestState`).
  - `subscriptions/listen` is added, tasks move to an extension, and `ttlMs`/`cacheScope` are added to list and read results.
  - Sampling, Roots, and Logging are deprecated, and so is Dynamic Client Registration (in favour of Client ID Metadata Documents).
- The Python SDK on PyPI (`mcp` 1.27.0) still reports `LATEST_PROTOCOL_VERSION = "2025-11-25"`. Check SDK support before targeting 2026-07-28 features.
- **A2A v1.0** has been stable since March 2026, with multi-protocol bindings (JSON-RPC, gRPC, HTTP+JSON), multi-tenancy, and signed agent cards. Its operations are `SendMessage`, `SendStreamingMessage`, `GetTask`, `ListTasks`, `CancelTask`, `SubscribeToTask`, push-config CRUD, and `GetExtendedAgentCard`, and clients send an `A2A-Version` parameter. A2A joined the **Agentic AI Foundation** in August 2026, alongside MCP.
- **Claude API tool use:** `strict` tools, `tool_choice` (`auto`/`any`/`tool`/`none`, `disable_parallel_tool_use`), parallel tool use, the **tool search tool** (`defer_loading`), and **programmatic tool calling** (`allowed_callers` + code execution).

## Verification

Every Python snippet was executed and tested:
- **Tool runtime:** schema generation; validation, unknown-tool, authorisation, and timeout errors; idempotent replay of side effects; **no retries on deterministic errors**; result truncation with handles; the parallel agent loop; BFCL-style argument matching.
- **Workflow patterns:** loop detection and budgets; the verify-and-retry reliability formula (analytic 0.9778 vs simulated 0.9773); pass@k / pass^k; the graph runner with interrupts.
- **Event store:** OCC, snapshots, fork, and hash-chain tamper detection; Beta-ranked skill library with retirement; memory write gate.
- **MCP:** the requestState codec rejects tampering, the wrong principal, and a mismatched request; definition scanning and rug-pull diffing. The **FastMCP server was run through the official SDK's in-memory client** (typed `outputSchema`/`structuredContent`, actionable errors, resources, prompts).
- **A2A:** task state machine, `messageId` idempotency, `INPUT_REQUIRED` continuation, cancellation rules, card canonicalisation, webhook verification.
- **HITL:** cost-optimal thresholds; action-bound approval tokens (blocks changed args or versions, self-approval, replay, missing role); Erlang C staffing; edit analytics.

JSON protocol examples, Java/Spring snippets, and Cypher/SQL were reviewed but not executed.
