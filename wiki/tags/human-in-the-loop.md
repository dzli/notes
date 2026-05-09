---
tag: human-in-the-loop
parent: agent
children: []
---

# Human-in-the-Loop (HITL)

A pattern in agentic systems where an autonomous workflow pauses at a decision point and waits for a human to review, approve, edit, or reject before continuing. Genuine HITL requires durable state persistence so the paused execution survives restarts and can be resumed later.

## Articles

- [[sources/production-grade-agents]] — Compass models HITL as a native LangGraph workflow branch: the handoff node persists a Postgres checkpoint and creates a control-plane review task; a reviewer decision later resumes the exact workflow thread from the same checkpoint state

## Related Tags

- [[tags/agent]]
- [[tags/langgraph]]
