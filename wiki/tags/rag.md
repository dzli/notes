---
tag: rag
parent: ml
children: []
---

# RAG (Retrieval-Augmented Generation)

A pattern where an LLM's response is grounded in documents retrieved at query time, rather than relying solely on parametric knowledge. Combines retrieval (search) with generation.

## Articles
- [[sources/qmd]] — implements the retrieval component of RAG: hybrid BM25 + vector search with LLM re-ranking, all local; designed explicitly for agentic workflows
- [[sources/honcho]] — Collections/Documents primitives provide peer-owned RAG stores; hybrid search over messages at workspace/session/peer scope
- [[sources/production-grade-agents]] — rag-agent in Compass retrieves from Qdrant-backed FAQ corpus, sanitizes citations, and drafts grounded answers; output guardrails run after generation

## Related Tags
- [[tags/embeddings]]
- [[tags/vector-search]]
- [[tags/search]]
- [[tags/llm]]
- [[tags/ml]]
