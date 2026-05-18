---
title: "LaPis: Persistent Memory for the Pi Coding Agent"
url: https://github.com/GeneGulanesJr/LaPis
date: 2025-05-17
tags: [agent-memory, cli, open-source, sqlite, code-indexing]
---

# LaPis: Persistent Memory for the Pi Coding Agent

## Summary

LaPis is an open-source persistent memory system designed specifically for the [Pi coding agent](https://github.com/earendil-works/pi-coding-agent). Its core proposition is radical simplicity: one SQLite database, zero cloud dependencies, and zero API keys. Rather than relying on vector databases or cloud services, LaPis stores all agent memories in a local SQLite file and exposes them through a Node.js CLI that Pi calls on demand. This makes it completely self-contained and operable without network access.

The system implements a **modular monolith** architecture with strict internal boundaries between extension adapters, CLI routing, feature services, and shared platform/storage infrastructure. Key feature domains include declarative memory observations (save, search, context, dedup, compaction), procedural workflow memory (steps, outcomes, scoring), code indexing via web-tree-sitter (WASM) across six languages, and code analysis queries (import graphs, call hierarchies, blast radius, dead code, complexity, coupling). A **Dream Cycle** runs every 10 sessions to clean stale memories—not simply old ones, but superseded entries, zero-recall auto-progress, low-trust auto-detected decisions, stale corrections, and replaced configurations.

LaPis introduces a **trust scoring** model where memories linked to changed source code lose trust, while stable code boosts it. Deduplication uses trigram overlap: ≥85% similarity auto-merges, 60–84% issues a warning. A compact wire format (columnar CSV with path interning and hoisting) achieves ~36–37% token savings on analysis results, verified lossless across 217 tests. Workspaces provide formal project isolation. The system supports in-place updates and soft deletes rather than spawning correction entries.

## Key Points

- One SQLite DB, zero cloud, zero API keys — fully local, no Python dependency, code parsing via WASM tree-sitter
- Modular monolith with strict module boundaries: extension adapters, CLI routing, feature services, platform/storage
- Trust scoring: memories linked to changed code lose trust; stable code reinforces trust
- Trigram deduplication: ≥85% auto-merge, 60–84% warning threshold
- Dream Cycle every 10 sessions cleans stale memories (superseded, zero-recall, low-trust) — age alone is not a signal
- Code indexing for JS/TS/Go/Python/Rust/SQL via web-tree-sitter (WASM); code analysis includes import graphs, call hierarchies, blast radius, dead code, complexity, coupling, hotspots, cycles, importance (PageRank), signal chains, layer violations
- Doc indexing: markdown sections, backlinks, broken links, glossary, tutorial path reconstruction, code example search, doc coverage, orphan detection, duplicate detection
- Token-efficient wire format: ~36–37% savings via columnar CSV, path interning, uniform-column hoisting, internal-ID stripping
- One-command install via `pi install git:github.com/GeneGulanesJr/LaPis`; auto-wires on session start
- MIT licensed

## Related

- [[concepts/agent-memory]] — broader landscape of agent memory approaches
- [[tags/agent-memory]] — persistent memory for AI agents
- [[tags/sqlite]] — embedded relational database
- [[tags/code-indexing]] — code search and analysis systems