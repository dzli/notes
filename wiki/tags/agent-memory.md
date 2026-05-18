---
tag: agent-memory
parent: agent
children: []
---

# Agent Memory

Systems and patterns for giving AI agents persistent, evolving memory about entities they interact with — users, other agents, topics — across sessions. Distinct from in-context memory (the current prompt window); agent memory survives across conversation boundaries and improves over time.

## Articles
- [[sources/honcho]] — defines a peer-centric memory architecture with async derivation of representations, summaries, and conclusions; exposes Chat API, Representations, Search, and Context retrieval interfaces
- [[sources/lapis]] — local SQLite-based persistent memory for the Pi coding agent; trust scoring, trigram dedup, Dream Cycle stale-memory cleanup, tree-sitter code indexing, token-efficient wire format

## Related Tags
- [[tags/agent]]
- [[tags/rag]]
- [[tags/embeddings]]
- [[tags/vector-search]]
- [[tags/sqlite]]
- [[tags/code-indexing]]
- [[tags/llm]]
