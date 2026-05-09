---
title: "plastic-labs/honcho: Memory library for building stateful agents"
url: https://github.com/plastic-labs/honcho
date: 2026-04-16
tags: [agent, agent-memory, embeddings, rag, vector-search, python, typescript, vector-db]
---

# plastic-labs/honcho: Memory library for building stateful agents

## Summary

Honcho is an open-source memory library and managed service by Plastic Labs for building stateful AI agents. Its core premise is that agents should develop and maintain persistent, evolving understanding of the entities they interact with — users, other agents, groups — rather than treating every session as a blank slate. The library claims to define the "Pareto Frontier of Agent Memory" based on published evals at evals.honcho.dev.

The central abstraction is the **Peer paradigm**: both human users and AI agents are modeled as `Peer` objects within a `Workspace`. This unified representation enables multi-participant sessions where any mix of humans and AI agents can interact, with configurable observation settings controlling which peers "see" which others. This contrasts with most memory systems that privilege only the human user as the entity worth modeling.

Honcho's data model layers from `Workspace` → `Peer` → `Session` / `Collection` → `Message` / `Document`. Sessions group interactions between peers; Collections hold vector-embedded documents for RAG; Messages are the atomic conversational unit. A background **deriver** worker processes messages asynchronously to continuously update peer representations, generate session summaries, and extract conclusions — meaning the system's model of a peer improves passively as conversations happen.

For retrieval, Honcho offers four interfaces at different latency/depth tradeoffs: (1) the **Chat API** (formerly Dialectic API) — a natural-language oracle endpoint (`/peers/{peer_id}/chat`) you query in plain English to get insights about a peer; (2) **Representations** — low-latency static documents summarizing a peer's current model, suitable for prompt injection; (3) **Search** — hybrid search over messages at workspace/session/peer scope; (4) **Context** — a token-budget-limited combination of messages, summaries, and conclusions for feeding into LLM context windows.

The server is implemented as a FastAPI application backed by PostgreSQL with pgvector for vector storage (with alternatives: turbopuffer, lancedb). It ships Python and TypeScript SDKs, supports multiple LLM backends (Gemini for derivation/summarization, Anthropic for high-reasoning dialectic, OpenAI for embeddings, Groq optionally), and can be self-hosted via Docker/Fly.io or used as a managed service at app.honcho.dev.

## Key Points

- **Peer paradigm**: users and agents are unified as `Peer` objects; enables multi-agent sessions with configurable who-observes-whom semantics
- **Asynchronous deriver**: background worker continuously updates representations, summaries, and conclusions without blocking the main API
- **Four retrieval modes**: Chat API (LLM oracle), Representations (fast static doc), Search (hybrid), Context (token-limited window)
- **Storage primitives**: Workspace → Peer → Session/Collection → Message/Document; Collections are peer-owned vector stores for RAG
- **Multi-provider LLM backend**: Gemini (derivation/summary), Anthropic (high-reasoning chat), OpenAI (embeddings), Groq (optional)
- **Self-hostable**: FastAPI + PostgreSQL/pgvector + deriver worker; Docker and Fly.io templates included; also available as managed service
- **SDKs**: Python (`honcho-ai` on PyPI) and TypeScript (`@honcho-ai/sdk` on npm)
- **AGPL-3.0 licensed**

## Related

- [[concepts/agent-memory]]
- [[tags/agent]]
- [[tags/agent-memory]]
- [[tags/rag]]
- [[tags/vector-search]]
- [[tags/embeddings]]
- [[tags/vector-db]]
- [[tags/python]]
- [[tags/typescript]]
