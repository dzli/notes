---
title: "tobi/qmd: mini CLI search engine for docs, knowledge bases, meeting notes"
url: https://github.com/tobi/qmd
date: 2026-04-14
tags: [cli, mcp, embeddings, rag, vector-search, typescript, search]
---

# tobi/qmd

## Summary

QMD is an on-device hybrid search engine for markdown files — notes, docs, meeting transcripts, knowledge bases. It runs entirely locally using GGUF models via node-llama-cpp, with no external API calls required.

The search pipeline combines three approaches: BM25 full-text search (FTS5), vector semantic search, and LLM re-ranking. The `query` command (highest quality) uses query expansion to generate variants, runs all variants against both BM25 and vector indexes in parallel, fuses results with Reciprocal Rank Fusion (RRF), then re-ranks the top 30 with a local LLM. A position-aware blend ensures high-confidence retrieval results aren't destroyed by the reranker.

QMD also exposes an MCP (Model Context Protocol) server, making it directly usable as a native tool by Claude Code, Claude Desktop, or any MCP client. This makes it a natural fit for agentic workflows where an LLM needs to search a local knowledge base.

The tool is also available as a Node.js/Bun library (`@tobilu/qmd`), with a full SDK for embedding search into applications. Collections, context, and embeddings are all manageable programmatically.

Smart chunking breaks documents at natural markdown boundaries (headings, code fences, paragraph breaks) using a scored algorithm rather than hard token cutoffs. An optional AST-aware mode uses tree-sitter for code files (TS, JS, Python, Go, Rust).

## Key Points

- Three search modes: `qmd search` (BM25 only), `qmd vsearch` (vector only), `qmd query` (hybrid + reranking — best quality)
- Three local GGUF models: embeddinggemma-300M (embeddings, ~300MB), qwen3-reranker-0.6b (reranking, ~640MB), qmd-query-expansion-1.7B (query expansion, fine-tuned, ~1.1GB)
- MCP server via `qmd mcp` — stdio or HTTP transport; HTTP keeps models loaded in VRAM across requests
- RRF fusion with position-aware blending: top 1-3 results weight retrieval 75%, deeper results trust reranker more
- Context system: attach descriptions to collections/paths to improve relevance; returned alongside results
- AST-aware chunking (opt-in) for `.ts`, `.js`, `.py`, `.go`, `.rs` files
- Output formats: CLI (colorized, clickable terminal links), JSON, markdown, CSV, XML
- Custom embedding model via `QMD_EMBED_MODEL` env var — supports Qwen3-Embedding for multilingual (CJK)
- Index stored at `~/.cache/qmd/index.sqlite` (SQLite + sqlite-vec)

## Related

- [[tags/cli]] — command-line tool
- [[tags/mcp]] — exposes MCP server for agent integration
- [[tags/embeddings]] — uses local embedding models
- [[tags/rag]] — hybrid BM25 + vector search is foundational RAG infrastructure
- [[tags/vector-search]] — vector similarity search component
- [[tags/search]] — search engine tooling
- [[tags/typescript]] — built in TypeScript, runs on Node.js/Bun
