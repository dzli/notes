---
title: Agent Security
created: 2026-05-09
tags: [agent-security, ai-safety, mcp, security]
---

# Agent Security

Agent security addresses the unique attack surfaces exposed when AI agents have access to credentials, network requests, filesystem operations, and tool calls. Unlike traditional software security, agents can be manipulated through their inputs (prompt injection) and their tool interfaces (tool poisoning), creating a dual trust boundary that requires both ingress and egress controls.

## The Core Problem

AI agents with shell access, API keys, and tool-calling capability present a concentrated risk surface. A single crafted instruction can cause an agent to exfiltrate secrets through URL parameters, DNS queries, or file writes. The attack surface is bidirectional: agents can be manipulated via their inputs (prompt injection in fetched content, poisoned MCP tool descriptions) and their outputs (secret exfiltration via outbound requests, file operations, or tool-call chains).

## Key Threat Model

### Egress Threats (Agent → Network)
- **Secret exfiltration**: embedding `API_KEY` values in URL parameters, headers, or request bodies
- **DNS tunneling**: encoding data in subdomain queries
- **Chunked exfiltration**: splitting secrets across multiple requests
- **Cryptographic exfiltration**: encrypting data with a public key before sending
- **File-system staging**: writing secrets to disk for later retrieval

### Ingress Threats (Network → Agent)
- **Prompt injection via fetched content**: jailbreak phrases, instruction manipulation, credential solicitation embedded in web pages
- **MCP tool poisoning**: malicious tool descriptions or injected responses from compromised MCP servers
- **Tool-call chain attacks**: multi-step sequences (recon → credential access → staging → exfiltration) obscured by innocent interleaved calls
- **State/context poisoning**: memory persistence attacks, preference manipulation

### Supply-Chain Threats
- **MCP server rug-pulls**: tool descriptions changed mid-session after initial approval
- **Compromised agent runtimes**: tampered prompts or tool configs

## Defense Patterns

### Capability Separation
The agent process holds secrets and credentials but has no direct network access. A separate mediator process holds network access but no agent secrets. Even if the agent is fully compromised, it cannot reach the firewall's controls. This is the architecture used by [[sources/pipelock]].

### Egress Proxy
All outbound traffic (HTTP, WebSocket, MCP) is routed through a proxy that applies multi-layer scanning before DNS resolution: URL scheme validation, DLP pattern matching, entropy analysis, rate limiting, and domain allowlisting. DLP must run before DNS resolution to prevent data from leaving in DNS queries.

### Bidirectional MCP Scanning
MCP traffic is scanned in both directions: client requests for DLP leaks, server responses for injection and tool poisoning. Tool-list responses are checked for modified descriptions and mid-session changes.

### Process Containment
OS-native sandboxing (Landlock LSM, seccomp, network namespaces on Linux; sandbox-exec on macOS) forces all traffic through the proxy at the kernel level, preventing bypass even if the agent process is compromised.

### Signed Action Receipts
Each mediated action produces a cryptographically signed receipt (Ed25519) that records what happened, the verdict, and the active policy — verifiable by third parties without trusting the agent or the mediator's operator.

### Tool-Call Chain Detection
Subsequence matching across sequences of tool calls to detect multi-step attack patterns (recon → credential theft → staging → exfiltration) even when interspersed with innocent operations.

### Response Normalization
Multi-pass text normalization before injection scanning to defeat evasion via zero-width characters, homoglyphs, leetspeak, and base64 wrapping.

## Enforcement Modes

| Mode | Behavior | Use Case |
|------|----------|----------|
| **Strict** | Allowlist-only; no unsanctioned outbound traffic | Regulated environments, high-security |
| **Balanced** | Blocks naive exfiltration; detects sophisticated attacks | Most developers (default) |
| **Audit** | Logging only; no blocking | Evaluation and visibility before enforcement |

## Operational Concerns

- **SSE streaming**: LLM chat UX requires token-by-token streaming; the proxy must scan each SSE event individually and fail-closed on detection while preserving clean-event passthrough
- **TLS interception**: for full payload scanning of HTTPS traffic, the proxy needs to MITM CONNECT tunnels with a trusted CA
- **False positive management**: DLP patterns (especially entropy-based) need per-domain tuning and suppression rules to avoid blocking legitimate traffic
- **Agent cooperation**: agents that receive structured block-reason headers (`X-Pipelock-Block-Reason`) can react intelligently to policy enforcement rather than silently failing

## Related
- [[concepts/adversarial-ml]] — evasion, poisoning, and extraction attacks on ML systems; spam-filter origins of adversarial ML
- [[concepts/pii-masking]] — detecting and masking PII before data reaches LLM APIs

- [[sources/pipelock]] — the open-source reference implementation of these patterns
- [[tags/agent-security]] — all articles on agent security
- [[tags/ai-safety]] — broader AI safety concerns
- [[tags/mcp]] — Model Context Protocol