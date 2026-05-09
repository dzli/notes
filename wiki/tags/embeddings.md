---
tag: embeddings
parent: ml
children: []
---

# Embeddings

Vector representations of text (or other data) produced by a neural model, where semantic similarity corresponds to geometric proximity. Foundation of modern semantic search and RAG.

## Articles
- [[sources/qmd]] — uses embeddinggemma-300M (default) or Qwen3-Embedding for multilingual support; chunks documents at ~900 tokens with 15% overlap before embedding
- [[sources/honcho]] — uses OpenAI embeddings (when EMBED_MESSAGES=true) to index peer messages in Collections for cosine-similarity retrieval

## Related Tags
- [[tags/vector-search]]
- [[tags/rag]]
- [[tags/ml]]
