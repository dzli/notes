---
tag: moe
parent: ml
children: []
---

# Mixture of Experts (MoE)

Sparse neural network architectures that activate only a subset of parameters per forward pass, enabling large total parameter counts with low compute cost. Used in models like Mixtral, Switch Transformer, and OpenAI Privacy Filter.

## Articles
- [[sources/openai-privacy-filter]] — Privacy Filter uses sparse MoE with 128 experts and top-4 routing (50M active of 1.5B total parameters)

## Related Tags
- [[tags/inference]]
- [[tags/ai]]