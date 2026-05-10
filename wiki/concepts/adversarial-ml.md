# Adversarial ML

Adversarial machine learning examines how ML systems fail when deployed in environments where an adversary can manipulate inputs, observe outputs, or corrupt training data. The field was forged in the early-2000s spam wars, where every defensive innovation was met by an offensive countermove within days, revealing principles that still govern modern AI security.

## Attack Taxonomy

**Evasion attacks** manipulate a model's inputs at inference time to produce a misclassification. These split into:

- **Passive evasion** — crafted offline with no feedback from the target model (e.g., the Good Word Attack padding spam with benign vocabulary).
- **Active evasion** — iterative, using an output signal (e.g., tracking pixels confirming inbox delivery) to map the decision boundary. Lowd & Meek showed that black-box query access lets attackers find adversarial inputs with a manageable number of queries rather than an exponential search.

**Poisoning attacks** corrupt the model's training data. Instead of dodging the classifier, the attacker shapes what it learns:

- **Indiscriminate poisoning** degrades overall accuracy, e.g., injecting spam padded with common ham words so the filter associates legitimate vocabulary with the spam label, causing mass false positives.
- **Targeted poisoning** corrupts classification of a specific class, e.g., poisoning words like "audit" and "quarterly" so a financial analyst's filter buries legitimate reports.

A UC Berkeley study demonstrated that **as little as 1% poisoned training data** can break a Bayesian spam filter — roughly 100 adversarial examples in a 10,000-email training set.

**Model extraction / stealing** is a third category less prominent in the spam era but now critical: an adversary queries an API to reconstruct a functionally equivalent model, which then enables more efficient evasion.

## Fundamental Asymmetries

The spam wars exposed two enduring asymmetries:

1. **Attack–defense cost asymmetry**: An attacker needs to corrupt only a tiny fraction of data; a defender must validate all of it. The RONI (Reject On Negative Impact) defense — retraining a shadow model per candidate training example — illustrated that principled defenses can be computationally prohibitive at scale.
2. **Feedback advantage**: An attacker with even a noisy feedback signal (e.g., a tracking pixel) can turn blind evasion into a controlled experiment, reducing the cost of successful evasion from hundreds of padding words to fewer than thirty.

## From Spam Filters to Modern AI

Every major spam-era attack class has a modern descendant:

| Spam-era attack | Modern descendant |
|---|---|
| Good Word Attack (input padding) | Adversarial examples on image classifiers |
| Tracking-pixel feedback loops | Black-box query attacks on production ML APIs |
| Bayesian / training-data poisoning | LLM training-data poisoning (corrupted web pages shift model behavior) |

The attack surface has broadened — LLMs ingest billions of documents, agents expose tool-use APIs — but the underlying dynamic is unchanged. Accuracy alone is not a sufficient measure of performance when an adversary can manipulate inputs and training data; systems must remain accurate *while being robust* to adversarial manipulation.

## Key References

- Paul Graham, "A Plan for Spam" (2002) — reframed spam filtering as a statistical learning problem
- Dalvi et al., "Adversarial Classification" (KDD 2004) — formalized classification as a two-player game
- Lowd & Meek, "Adversarial Learning" (KDD 2005) — black-box decision-boundary mapping with manageable queries
- UC Berkeley (Nelson et al., 2008) — demonstrated poisoning attacks on SpamBayes with ≥1% poisoned data

## Related
- [[sources/how-spam-filters-shaped-adversarial-ml]]
- [[tags/adversarial-ml]]
- [[tags/ai-safety]]
- [[tags/security]]
- [[concepts/agent-security]]