---
title: Production Agent Architecture
tags: [agent, langgraph, human-in-the-loop, observability, microservices, rag]
---

# Production Agent Architecture

Moving an agent from prototype to production requires solving problems that don't appear in demos: durable state, governed routing, auditable decisions, and operational visibility. The Compass blueprint (see [[sources/production-grade-agents]]) provides a concrete reference implementation.

## The Three-Decision Runtime

The most important architectural pattern in production support agents is constraining the runtime to exactly three decision branches:

1. **Answer from customer context** — route to a CRM-backed agent with access to order, subscription, and profile data
2. **Answer from approved knowledge** — route to a RAG agent grounded in a curated knowledge base (Qdrant + FAQ corpus)
3. **Pause for human review** — route to a handoff node that creates durable review state

This constraint keeps the agent graph legible, limits tool access by route, and ensures every response has a clear evidence trail.

## Separating Workflow Control from Generation

LangGraph owns the outer workflow envelope (bootstrap → guardrails → supervisor → specialist → finalize). Specialist agents (general-answer, crm, rag) handle token generation inside that envelope. This separation means prompt changes, model route swaps, and connector updates don't destabilize the orchestration graph.

## What Makes HITL Real

Human-in-the-loop is only meaningful if the paused execution survives restarts. The requirements:

- **Postgres-backed checkpointing** (LangGraph `PostgresSaver`) so paused turns persist across service restarts
- **Durable review task** in a control-plane store with full audit activity log
- **Resume path** that resumes the exact workflow thread from the checkpoint, not a new conversation

Without durable checkpointing, HITL is a UI banner. With it, it's a stateful workflow primitive.

## Config as the Adaptation Surface

Production blueprints need a clear seam between the reusable engine and the deployment-specific configuration. In Compass, `blueprint.config.yaml` owns:

- enabled workflow packs and their routing
- allowed actions per pack
- connector provider bindings
- knowledge source manifests
- escalation reason code policy

This means teams can rebrand, reroute, and reconfigure without touching orchestration code.

## Observability Stack for LLM Systems

Production agent observability is layered:

| Layer | Tool | What it covers |
|---|---|---|
| LLM tracing | Langfuse | Per-turn traces with prompt versions, citations, workflow steps, eval scores |
| Infrastructure metrics | Prometheus + Grafana | Latency, error rates, queue depths |
| Logs | Loki + Promtail | Structured JSON logs with request IDs |
| Health | `/healthz`, `/*/status` endpoints | Per-subsystem readiness surfaced in ops dashboard |

Request IDs are threaded from browser through BFF, gateway, runtime, and control plane, making cross-service correlation possible.

## The Rich Response Contract

A production agent response should carry more than the answer text. Useful fields:

- `citations` — evidence with scores and section titles
- `escalation_required` + `review_id` — HITL state
- `generation_mode` — grounded / heuristic / hitl-pending
- `agent_route` — which specialist was used
- `trace_id` — Langfuse correlation
- `guardrail` + `policy_decisions` — safety audit trail
- `workflow_steps` — human-readable step trace

This makes the UI and reviewer console reflect runtime truth rather than displaying decorative status text.

## Related

- [[sources/production-grade-agents]]
- [[tags/langgraph]]
- [[tags/human-in-the-loop]]
- [[tags/rag]]
- [[tags/observability]]
- [[tags/microservices]]
- [[tags/agent]]
