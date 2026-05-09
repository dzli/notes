---
tag: vector-search
parent: ml
children: []
---

# Vector Search

Similarity search over high-dimensional embedding vectors, typically using cosine distance or dot product. Enables semantic (meaning-based) retrieval rather than keyword matching.

## Articles
- [[sources/qmd]] — uses sqlite-vec for vector index; cosine distance normalized to `1/(1+distance)` range; combined with BM25 via RRF fusion
- [[sources/honcho]] — pgvector (default) with turbopuffer and lancedb as alternatives; cosine similarity over peer message/document embeddings

## Related Tags
- [[tags/embeddings]]
- [[tags/rag]]
- [[tags/search]]
- [[tags/ml]]
