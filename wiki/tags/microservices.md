---
tag: microservices
parent: architecture
children: []
---

# Microservices

An architectural style where a system is decomposed into independently deployable services with narrow responsibilities, communicating over well-defined APIs or event buses. Each service owns its own data and can be scaled, deployed, and replaced independently.

## Articles

- [[sources/production-grade-agents]] — Compass uses distinct services for the agent runtime (cx-agent-runtime), control plane API, CRM MCP server, customer chat web app, and reviewer console, routed through Kong and fronted by Caddy; service health is polled and surfaced in the ops dashboard

## Related Tags

- [[tags/architecture]]
- [[tags/distributed-systems]]
- [[tags/api-design]]
