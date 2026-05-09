---
title: "Pipelock: Open-source AI agent firewall for MCP security"
url: https://github.com/luckyPipewrench/pipelock
date: 2026-05-09
tags: [agent, mcp, ai-safety, agent-security, security, go]
---

# Pipelock: Open-Source AI Agent Firewall for MCP Security

## Summary

Pipelock is an open-source (Apache 2.0) AI agent firewall that acts as an egress proxy between AI agents and the network, scanning all outbound and inbound traffic to prevent secret exfiltration, prompt injection, and tool-poisoning attacks. It sits at the trust boundary using a capability-separation model: the agent process holds secrets but has no direct network access, while Pipelock has network access but no agent secrets. This ensures that even a prompt-injected agent cannot bypass firewall controls.

The tool provides an 11-layer URL scanner that validates schemes, detects CRLF injection, blocks path traversal, matches 48 DLP patterns (API keys, tokens, credentials, cryptocurrency seed phrases), analyzes path and subdomain entropy for obfuscation, enforces SSRF protections with DNS rebinding prevention, and applies per-domain rate limiting and data budgets. DLP scanning runs before DNS resolution to catch secrets before any network query leaves the proxy. A 6-pass text normalization pipeline handles zero-width characters, homoglyph substitution, leetspeak encoding, and base64-wrapped payloads across 25 built-in injection-detection patterns.

Pipelock also includes a process sandbox using OS-native kernel primitives (Landlock LSM, seccomp, network namespaces on Linux; sandbox-exec on macOS), bidirectional MCP proxy scanning across stdio and HTTP transports, tool-policy enforcement with 17 built-in rules, tool-call-chain detection with 10 attack-pattern signatures, cryptographically signed action receipts (Ed25519), a hash-chained flight recorder, canary-token injection, and a four-source emergency kill switch. It integrates with Claude Code, Cursor, VS Code, JetBrains, OpenAI Agents SDK, Google ADK, AutoGen, CrewAI, and LangGraph.

## Key Points

- **Capability separation architecture**: agent holds secrets but no network; Pipelock holds network but no agent secrets — a compromised agent cannot reach firewall controls
- **11-layer URL scanner** with 48 DLP patterns, entropy analysis, SSRF protection with DNS rebinding prevention; DLP runs before DNS resolution
- **Bidirectional MCP proxy** scans both client requests (DLP leaks) and server responses (injection, tool poisoning); supports stdio, Streamable HTTP, and HTTP reverse proxy transports
- **6-pass response normalization** catches zero-width characters, homoglyphs, leetspeak, and base64 payloads across 25 injection-detection patterns
- **Signed action receipts** (Ed25519) provide agent-external cryptographic attestation of every mediated action — receipts are verifiable independent of the agent
- **Process sandbox** (Landlock, seccomp, network namespaces on Linux; sandbox-exec on macOS) forces all traffic through the proxy at the OS level
- **Learn-and-lock** (v2.4) observes agent traffic, compiles behavioral contracts, shadows, ratifies, and enforces per-agent manifests
- **Three enforcement modes**: strict (allowlist-only), balanced (default, blocks naive + detects sophisticated), audit (logging only)
- **SSE streaming support**: scans LLM token streams per-event with fail-closed termination; compressed SSE rejected since compressed bytes evade regex matching
- **Mediation envelope** (v2.4): RFC 8941 sideband metadata carrying action type, verdict, actor identity, policy hash, taint context, and receipt correlation ID on forwarded requests

## Related

- [[concepts/agent-security]] — patterns and tools for securing AI agent egress, tool calls, and prompt injection defense
- [[tags/agent]] — AI agent systems
- [[tags/mcp]] — Model Context Protocol
- [[tags/ai-safety]] — AI safety concerns and mitigations
- [[tags/security]] — software security practices and tooling