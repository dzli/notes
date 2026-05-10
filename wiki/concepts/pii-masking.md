---
related:
  - tags/privacy
  - tags/ai-safety
  - tags/agent-security
  - sources/openai-privacy-filter
---

# PII Masking

Personally Identifiable Information (PII) masking is the process of detecting and redacting or replacing personal data in unstructured text before it is processed, stored, or transmitted to third-party systems. It is a critical compliance and security layer for any pipeline that handles user data, especially when sending text to hosted LLM APIs.

## Why PII Masking Matters

Sending raw user data to third-party LLM APIs (OpenAI, Anthropic, Google) creates regulatory exposure under GDPR, HIPAA, and similar frameworks. In the EU, forwarding personal data to a US-based API without explicit consent or a legal basis constitutes a data transfer violation. Internal data leaks (e.g., Samsung's ChatGPT ban after engineers pasted source code) demonstrate that the risk is real, not hypothetical.

Regex-based PII scrubbing fails on contextual cases: "Maya from accounting" and "Maya Chen who manages the Oslo route" both refer to a real person, but regex cannot distinguish private individuals from fictional characters or public figures. Effective PII masking requires understanding context, which is exactly where small specialized models excel.

## Approaches

| Approach | Strengths | Weaknesses |
|----------|-----------|------------|
| Regex / rule-based | Fast, deterministic, no GPU needed | No context awareness, high false-negative rate on indirect references |
| Named Entity Recognition (NER) | Some context, mature libraries (spaCy) | Not designed for PII specifically; limited categories |
| LLM-based classification | Strong context understanding | Slow, expensive, requires sending data to the model |
| **Specialized local PII models** | Context-aware, runs locally, configurable | Requires model deployment; may need domain fine-tuning |

The emerging pattern — exemplified by OpenAI Privacy Filter — is small, specialized, locally-runnable models that solve PII masking as a token classification task with bidirectional context.

## Key Considerations

- **PII masking ≠ anonymization.** Masking explicit identifiers raises the cost of re-identification but does not eliminate it. Context clues can still enable re-identification.
- **Configurable precision-recall.** Training data pipelines want high recall (catch everything that might be PII); user-facing redaction wants high precision (don't over-redact).
- **Domain adaptation.** General-purpose models may miss domain-specific PII (medical codes, legal citations, internal product names). Fine-tuning on a few hundred labeled examples is often sufficient.
- **Compliance is more than a model.** DPIA, DPA agreements, retention policies, and audit logs are still required. PII masking is a tool in the compliance stack, not the whole stack.

## Notable Tools

- **OpenAI Privacy Filter** — 1.5B/50M active sparse MoE bidirectional token classifier, Apache 2.0, 8 PII categories, 128k context, ~97% F1. See [[sources/openai-privacy-filter]]

## Related

- [[tags/privacy]] — data privacy and regulatory compliance
- [[tags/ai-safety]] — broader AI safety concerns
- [[concepts/agent-security]] — securing agent pipelines including data loss prevention