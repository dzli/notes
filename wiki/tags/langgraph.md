---
tag: langgraph
parent: agent
children: []
---

# LangGraph

A graph-based orchestration framework for building stateful, multi-step agentic workflows. Represents agent logic as a directed state graph with typed nodes, conditional edges, and pluggable checkpointers for durable persistence.

## Articles

- [[sources/production-grade-agents]] — LangGraph is the outer workflow envelope in Compass: a small, explicit node map (bootstrap → guardrails → supervisor → handoff → finalize) wraps specialist subagents; PostgresSaver provides durable checkpointing for HITL resume

## Related Tags

- [[tags/agent]]
- [[tags/human-in-the-loop]]
- [[tags/python]]
