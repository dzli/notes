---
title: Agent Memory
tags: [agent-memory, agent, rag, embeddings, vector-search]
---

# Agent Memory

Agent memory refers to mechanisms that give AI agents persistent, evolving knowledge about entities — users, other agents, topics — that survives across conversation sessions and improves over time. This is distinct from in-context memory (the current prompt window), which resets between sessions.

## Why It Matters

LLMs are stateless by default: every new conversation starts from scratch. For agents meant to assist specific users over time, this is a fundamental limitation. Agent memory systems address this by:

1. **Persisting interaction history** beyond the context window
2. **Deriving structured knowledge** (facts, preferences, behavioral patterns) from raw conversations
3. **Enabling personalization** — tailoring agent responses based on accumulated understanding of the user

## Key Approaches

### In-Context Stuffing
Simply prepend a summary or recent history to every prompt. Simple but expensive and limited by context window size.

### RAG over Conversation History
Store messages/summaries in a vector database; retrieve relevant segments at query time. Scales better than in-context stuffing; covered by libraries like Honcho's Collections/Search and qmd.

### Derived Representations
Asynchronously process conversations to extract structured facts and psychological/behavioral profiles about entities (the approach Honcho calls "derivation"). Results are stored as per-peer `Representations` — low-latency static documents suitable for prompt injection without needing a retrieval call.

### Local SQLite Memory
Store all agent memories, code indices, and trust scores in a single local SQLite database with FTS5 full-text search. No cloud dependency, no API keys, no separate server. LaPis exemplifies this approach: trigram-based deduplication (≥85% auto-merge), trust scoring linked to code stability, and a Dream Cycle that removes stale (not merely old) memories. Code and doc indexing via WASM tree-sitter provides semantic search without embeddings.

### Episodic + Semantic Memory
Episodic: specific past events/conversations. Semantic: general facts derived from episodes. Advanced agent memory systems maintain both.

## Retrieval Interfaces

Different use cases call for different retrieval strategies:

| Interface | Latency | Depth | Use case |
|-----------|---------|-------|----------|
| Representation | Very low | Medium | Inject static context into prompts |
| Context window | Low | Variable | Continue long conversations |
| Search | Medium | High | Find specific past interactions |
| Chat/Oracle API | High | Highest | Ask complex questions about a user |

## Tools & Libraries

- **[[sources/honcho]]** — open-source memory library; peer-centric model; async derivation; Chat API oracle; pgvector backend; managed service available
- **[[sources/lapis]]** — local SQLite persistent memory for the Pi coding agent; trust scoring, trigram dedup, Dream Cycle stale-memory cleanup, tree-sitter code/doc indexing, token-efficient wire format; zero cloud/API keys

## Related

- [[tags/agent-memory]]
- [[tags/agent]]
- [[tags/rag]]
- [[tags/embeddings]]
- [[tags/vector-search]]
