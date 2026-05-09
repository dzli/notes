---
title: "Production-Grade Agents with LangGraph, Qdrant, Langfuse, Redis, Postgres, Prometheus and Grafana"
url: https://agentnativedev.medium.com/production-grade-agents-with-langgraph-qdrant-langfuse-redis-postgres-prometheus-and-grafana-94df1f03c652
date: 2026-04-18
tags: [langgraph, agent, human-in-the-loop, rag, observability, microservices, vector-db, python, typescript]
---

# Production-Grade Agents with LangGraph, Qdrant, Langfuse, Redis, Postgres, Prometheus and Grafana

## Summary

Compass is a free, open blueprint of a production-grade customer support agent released by Agent Native to demonstrate how real agent systems are engineered and operated. It goes far beyond typical demo projects by shipping a complete application topology: Kong and Caddy for edge routing, Postgres and Redis for durable state, Qdrant for vector retrieval, Redpanda for event streaming, MinIO for artifact storage, and a full observability stack (Prometheus, Loki, Grafana, Langfuse). Multiple UI surfaces are included — a customer chat interface, a reviewer workspace, and a platform ops dashboard.

The central architectural decision is that the runtime makes exactly three categories of decisions: answer from customer context (CRM agent), answer from approved knowledge (RAG agent), or pause and hand off to a human reviewer. This constraint, expressed through LangGraph's outer workflow envelope and a supervisor with specialist subagents, keeps the agent graph legible, limits tool access by route, and makes every decision auditable. Workflow packs in `blueprint.config.yaml` tie routing to config rather than hardcoded domain logic, so the system can be rebranded, rerouted, and re-configured without touching orchestration code.

Human-in-the-loop (HITL) is treated as a first-class runtime path, not a UI afterthought. When the supervisor routes to the handoff path, a durable checkpoint is written to Postgres and a review task is created in the control plane. A reviewer can later approve, edit, or reject the proposed response, at which point the paused workflow thread resumes from the exact same checkpoint. Without Postgres-backed checkpointing, "human in the loop" is effectively just a banner in the UI — durable state is what makes it a real stateful workflow.

Observability is built in from the first request. Every turn opens a Langfuse trace keyed on customer ID and conversation ID, with the request ID threaded through BFF, gateway, runtime, and control plane. Prompt versions, policy decisions, workflow steps, and citations are attached to the same trace, enabling regression review and eval correlation via `make eval-langfuse`. Prometheus and Grafana cover infrastructure metrics; Loki and Promtail cover log aggregation.

The repo is designed to be forked in a predictable order: rebrand UI, update `blueprint.config.yaml`, replace the FAQ corpus with a real knowledge base, swap fixture-backed connector adapters for real provider implementations (Shopify, Zendesk, ShipBob stubs are included), and run golden evals before promoting state-changing actions out of human approval.

## Key Points

- **One design principle drives everything**: the runtime chooses between CRM context, RAG knowledge, or human handoff — no more, no less.
- **LangGraph controls the outer workflow envelope**; specialist agents (general-answer, crm, rag) handle generation inside it, keeping the graph stable when prompts or model routes change.
- **Durable checkpointing via PostgresSaver** is what separates genuine HITL from a UI trick — paused turns survive restarts and can be resumed by reviewers against the exact conversation state.
- **`blueprint.config.yaml` is the adaptation surface**: workflow packs, connector bindings, allowed actions, model routes, and escalation reason codes all live there.
- **The response contract is rich by design**: `ChatResponse` carries citations, escalation flags, review IDs, generation mode, agent route, trace ID, guardrail results, policy decisions, and workflow steps — enabling the UI and reviewer console to reflect runtime truth.
- **SSE streaming uses three typed events** (`meta`, `chunk`, `done`): the client receives structured metadata (route, citations, workflow steps) before the first token, not after.
- **Connectors are real seams**: fixture-backed mock adapters run locally on day one; provider-shaped stubs (Shopify, Zendesk, ShipBob) show exactly where real integrations belong.
- **Observability is layered**: Langfuse for LLM tracing and eval, Prometheus/Grafana for infrastructure metrics, Loki for logs, all wired into the reviewer ops dashboard.

## Related

- [[concepts/production-agent-architecture]]
- [[tags/langgraph]]
- [[tags/human-in-the-loop]]
- [[tags/agent]]
- [[tags/rag]]
- [[tags/observability]]
- [[tags/microservices]]
- [[tags/vector-db]]
