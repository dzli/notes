---
tag: observability
parent: devops
children: []
---

# Observability

Practices and tooling for making distributed systems inspectable at runtime: metrics (Prometheus, Grafana), structured logs (Loki, Promtail), distributed tracing (Langfuse, OpenTelemetry), and health/status endpoints. For LLM systems, observability extends to prompt versioning, eval correlation, and per-turn trace metadata.

## Articles

- [[sources/production-grade-agents]] — Compass ships a full observability stack: Langfuse for LLM tracing and eval (every turn gets a trace keyed on customer ID + conversation ID with prompt versions and workflow steps attached), Prometheus + Grafana for infrastructure metrics, Loki + Promtail for logs; all surfaces are queryable from the reviewer ops dashboard

## Related Tags

- [[tags/agent]]
- [[tags/devops]]
