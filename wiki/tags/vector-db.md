---
tag: vector-db
parent: databases
children: []
---

# Vector Database

Databases optimized for storing and querying high-dimensional embedding vectors via similarity search (cosine distance, dot product, etc.). Often used as the retrieval backbone for RAG and semantic search.

## Articles
- [[sources/honcho]] — uses PostgreSQL + pgvector as default vector store; also supports turbopuffer and lancedb as alternatives
- [[sources/production-grade-agents]] — Compass uses Qdrant as the dedicated vector store backing the RAG agent's approved knowledge retrieval

## Related Tags
- [[tags/vector-search]]
- [[tags/embeddings]]
- [[tags/rag]]
- [[tags/databases]]
