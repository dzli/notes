---
tag: sqlite
parent: databases
children: []
---

# SQLite

SQLite is a serverless, self-contained, zero-configuration embedded relational database engine. In AI agent and tooling contexts, SQLite is increasingly attractive as a persistent store that requires no separate server process, no cloud credentials, and no network dependency — ideal for fully local, single-user agent memory systems and CLI tools.

## Articles
- [[sources/lapis]] — uses a single SQLite DB as the sole persistence layer for a coding agent's memories, code index, doc index, and trust scores; FTS5 for full-text search; WAL mode for concurrency

## Related Tags
- [[tags/databases]]
- [[tags/agent-memory]]
- [[tags/cli]]