---
title: "How Spam Filters Shaped the Field of Adversarial ML"
source: "https://hackernoon.com/how-spam-filters-shaped-the-field-of-adversarial-ml"
author:
  - "[[Gautham Koorma]]"
published: 2026-04-28
created: 2026-05-09
description: "Evasion attacks and data poisoning let spammers bypass filters, turning the early-2000s inbox into a lab that shaped adversarial machine learning."
tags:
  - "clippings"
---
![featured image - How Spam Filters Shaped the Field of Adversarial ML](https://hackernoon.imgix.net/images/YYi4iUIAm6S2XaRnih4BXNpG7dt1-pl03a3f.png?auto=format&fit=max&w=3840)

featured image - How Spam Filters Shaped the Field of Adversarial ML

If you weren’t using email in the early 2000s, it’s hard to grasp how severe the spam problem became. Every day brought with it a relentless flood of dubious pharmaceutical offers, get-rich-quick schemes, and thinly veiled attempts to steal your banking credentials. By 2007, as botnets matured, [spam accounted for the majority of global email traffic](https://www.emailtray.com/blog/email-spam-trends-2001-2012/?ref=hackernoon.com), with some estimates putting it as high as 90%. Legitimate emails were buried underneath piles of junk, undermining the trust and reliability of email as a communication medium.

The response to this crisis was the emergence of increasingly sophisticated [spam filters and anti-spam techniques](https://en.wikipedia.org/wiki/Anti-spam_techniques?ref=hackernoon.com). Over time, spam filters became remarkably effective, pushing most spam out of the inbox through a continuous cycle of adaptation between filters and attackers. That cycle first took the form of evasion, as attackers learned to bypass detection, and later of poisoning, as they began to manipulate the data these systems learned from.

The spam arms race became one of the first real stress tests for machine learning systems that were personalized on user data and deployed in an adversarial environment. This is the story of that arms race, and how it shaped the field of adversarial machine learning and AI security.

The lessons it revealed still underpin modern AI and ML systems:

- Attackers can shape outputs by manipulating inputs without direct visibility into the model.
- Feedback loops turn blind attacks into controlled experiments.
- Systems that learn from data can be poisoned through malicious examples.
- A small fraction of malicious data in the training set can corrupt a model’s behavior.

## From Blunt Instruments to Bayesian Learning

As Karl Krueger noted in a [2002 SANS Institute tactical update on spam](https://www.giac.org/paper/gsec/2218/spam-battle-2002-tactical-update/103775?ref=hackernoon.com), early email systems were never designed for the kind of systematic abuse spammers unleashed.

The first wave of spam filters were blunt instruments. IP blacklists blocked known spam servers. Keyword filters blocked suspicious phrases. At the time, Justin Mason's [SpamAssassin](https://en.wikipedia.org/wiki/Apache_SpamAssassin?ref=hackernoon.com) was the most ambitious attempt to build a rules-based spam filter. It ran each incoming email against hundreds of hand-written Perl regular expression rules. Each rule that matched added weight to the email’s overall spam score.

Spammers defeated these hand-written rules within days. They rotated IP addresses, misspelled words, and invented new vocabulary faster than any human-maintained ruleset could keep up. Filter developers consequently learned that the handcrafted, rules-based approach had a structural limit.

Paul Graham’s August 2002 essay " [A Plan for Spam](https://paulgraham.com/spam.html?ref=hackernoon.com) " reframed spam filtering as a statistical learning problem. The essay proposed training a [Naive Bayes](https://en.wikipedia.org/wiki/Naive_Bayes_classifier?ref=hackernoon.com) classifier on each user's personalized email history, weighting words by how often they appeared in the spam class versus the legitimate or ham class. The key insight was personalization. A model trained on your inbox could outperform most generic rulesets because it adapted to your patterns of communication.

An easy way to picture a Bayesian spam filter is as a tally sheet. Each word in an email labeled as spam gets a mark on the spam side of the ledger, while each word in a legitimate email gets a mark on the ham side. When a new email arrives, the filter consults the tally built from a user’s inbox and decides which side the email more closely resembles.

Graham prototyped his Bayesian spam filter by training on a corpus of roughly 4,000 spam and 4,000 ham emails and reported striking results: fewer than 5 in 1,000 spam emails made it through, with no false positives.

The open source community responded immediately. The [SpamBayes](https://spambayes.sourceforge.io/?ref=hackernoon.com) project, which grew out of Graham’s essay, refined the approach in an important way. Instead of issuing a binary ham-or-spam verdict, SpamBayes introduced a third "unsure" category. Email that fell between the ham and spam thresholds went into a separate folder for human review instead of being silently misfiled. SpamAssassin, already in wide deployment, incorporated Bayesian techniques into its existing mix of handcrafted rules and blocklists.

Within two years of Graham's essay, [filters were reporting accuracy scores above 99 percent](https://merl.com/publications/docs/TR2004-091.pdf?ref=hackernoon.com). The problem looked solved. It was not!

## Passive Evasion Attacks

A Bayesian filter estimates the likelihood that an email is spam by combining the individual contributions of each word towards that likelihood. Because the classifier treats each word in isolation (the ["naive" conditional independence assumption](https://scikit-learn.org/stable/modules/naive_bayes.html?ref=hackernoon.com)), it can be manipulated by sheer volume of words.

A spam email selling diet pills could be padded with hundreds of invisible benign words like "conference," "meeting," or "earnings." Those legitimate words diluted the overall spam score and helped the email slip into the inbox. This tactic became known as the [Good Word Attack](https://www.ceas.cc/papers-2005/125.pdf?ref=hackernoon.com).

Spammers also attacked at the [character level](https://www.levelblue.com/blogs/spiderlabs-blog/stupid-spammer-tricks-multi-character-set-text?ref=hackernoon.com) using [homoglyphs](https://en.wikipedia.org/wiki/Homoglyph?ref=hackernoon.com) and by crafting [homograph-style obfuscation](https://unit42.paloaltonetworks.com/homograph-attacks/?ref=hackernoon.com). "Mortgage" became "M0rtgag3," or was written with a Cyrillic character that looked identical on screen but registered as unfamiliar words to the filter. When classifiers caught up, spammers used HTML to split words across invisible formatting tags. Each move produced a countermove, often within days.

These strategies shared a common structure: the attacker modified the input to evade detection without observing the filter's response. This is passive evasion. The attack is crafted offline, based on general assumptions about the model rather than real-time feedback.

Researchers at the University of Washington formalized this dynamic, arguing that [classification in an adversarial setting](https://homes.cs.washington.edu/~pedrod/papers/kdd04.pdf?ref=hackernoon.com) is better understood as a two-player game rather than as a static optimization problem. The filter with the Bayesian classifier is deployed first, and the attacker adapts in response.

## Active Evasion with Feedback

In theory, an attacker may have complete knowledge of a classifier, including its weights and decision boundary. That is a white-box threat model, and it rarely holds in practice. Real spammers operated in a black-box setting, where the model was opaque and only its input-output behavior could be observed.

Early attacks on spam filters were noisy, iterative, and largely blind. Emails disappeared into a void, leaving spammers with no reliable signal of which tactics were working. At the [2004 MIT Spam Conference](https://blog.jgc.org/2023/07/how-to-beat-adaptivebayesian-spam.html?ref=hackernoon.com), the creator of the [POPFile](https://getpopfile.org/docs/welcome?ref=hackernoon.com) spam filter showed that without feedback, [passive evasion succeeded just 0.04% of the time](https://www.virusbulletin.com/virusbulletin/2006/02/does-bayesian-poisoning-exist?ref=hackernoon.com).

What changed was the introduction of feedback. Spammers engineered a feedback loop using a [tracking pixel](https://www.bbc.com/news/technology-56071437?ref=hackernoon.com), an invisible pixel embedded in an email’s HTML that acted as a web beacon. When the recipient opened the message, the email client fetched that pixel from the sender’s server, signaling that the email had reached the inbox.

With that signal, guesswork became experimentation. By sending thousands of variants, each tied to a unique pixel, spammers could test methods directly against a user’s filter. This is active evasion, where feedback is used to iteratively refine attacks.

This shift altered the economics of evasion. Passive attacks often required hundreds of words and still offered no reliable guarantee of landing. With feedback, that cost dropped to [fewer than thirty](https://slidetodoc.com/adversarial-machine-learning-daniel-lowd-university-of-oregon/?ref=hackernoon.com). Researchers Daniel Lowd and Christopher Meek later formalized the underlying intuition. Even with only black-box access, an attacker can [map a classifier's decision boundary using a manageable number of queries](https://ix.cs.uoregon.edu/~lowd/kdd05lowd.pdf?ref=hackernoon.com) rather than an exponentially large search.

The countermeasure was simple—block automatic image loading and break the feedback loop. That is why email clients still ask whether you want to load remote images.

## Poisoning the Data

Spammers then started asking a more dangerous question—what if, instead of dodging the model, you could shape how it learns? [Evasion attacks](https://www.nightfall.ai/ai-security-101/evasion-attacks?ref=hackernoon.com) fool a model at decision time. [Poisoning attacks](https://en.wikipedia.org/wiki/Bayesian_poisoning?ref=hackernoon.com) target a model’s training process with the intent to corrupt it. It helps to return to the tally-sheet analogy. In an evasion attack, the goal is to slip past the tally. In a poisoning attack, the goal is to corrupt the tally itself.

Against evasion, a filter can collect new variants of spam, retrain the Bayesian model, and improve performance. Against poisoning, that recovery mechanism becomes the attack surface. Retraining on poisoned samples is precisely what the attacker relies on.

Think of it this way. Evasion is a burglar wearing a disguise to get past a guard dog. Poisoning is the same burglar spending weeks training the dog to associate that disguise with treats. By the time the break-in happens, the dog has learned the wrong lesson.

The attack surface was hiding in plain sight. The same mechanism that made Bayesian filters adaptive also made them exploitable. SpamBayes, the leading open source Bayesian filter of the era, periodically retrained on user-labeled email, using both messages marked as spam and those rescued from the junk folder.

## The Asymmetry of Poisoning

A [UC Berkeley research team](https://people.eecs.berkeley.edu/~tygar/papers/SML/Spam_filter.pdf?ref=hackernoon.com) demonstrated in 2008 just how fragile SpamBayes was under poisoning attacks, using two distinct methods.

The first was an indiscriminate attack designed to trigger enough false positives to make the filter unusable. Attackers sent spam email padded with ordinary ham vocabulary. Those messages were meant to be caught as spam, but they carried words like “meeting,” “agenda,” and “invoice” so that retraining would gradually drag those legitimate terms onto the spam side of the ledger. Over time, the filter learned to associate those words with spam. When real emails containing those terms arrived, they were pushed into junk, forcing users to sift through the junk folder or disable the filter entirely.

The second attack was more targeted. Instead of degrading the filter overall, the attacker aimed to bury a specific class of email before it arrived. An accountant’s filter, for example, could be poisoned by sending spam laced with words like “audit,” “reconciliation,” and “quarterly.” The filter would catch those emails, the user would confirm them as spam, and the model would retrain on that data. Each cycle pushed those words further toward the spam side of the tally. When a legitimate email with a financial report eventually arrived, the filter buried it.

Both attacks relied on the same lie: spam was deliberately written to carry legitimate vocabulary. Over time, the training data was poisoned, dragging useful words onto the wrong side of the ledger. And it did not take much. Like a few drops of toxin contaminating a well, [as little as 1% poisoned training data could break a spam filter](https://people.eecs.berkeley.edu/~tygar/papers/SML/Spam_filter.pdf?ref=hackernoon.com). In a Bayesian model trained on 10,000 emails, roughly 100 adversarially crafted examples, quietly injected into the training mix over time, were enough to render it useless.

A countermeasure for these attacks existed on paper. The Reject On Negative Impact, or RONI, defense, evaluated whether each candidate training example degraded performance on a held-out validation set. If it did, the example could be discarded. In practice, this required training a shadow model for each incoming email. At scale, the cost and computation required to do that were prohibitive.

These attacks reinforced a principle as old as security itself. Attacks are cheap. Defense is not. An attacker only needs to corrupt a small fraction of the data. A defender has to validate all of it. That asymmetry is a fundamental rule of security, and it has not changed.

## The Lessons That Still Hold

The inbox eventually stabilized. Filters improved, and spam stopped feeling like a systemic crisis. But the adversarial dynamics did not disappear. They resurfaced in more sophisticated forms.

Nearly every major attack class from the spam-filter era has a modern descendant. Good word attacks, which manipulated inputs to evade detection, became adversarial examples in image classifiers. Tracking pixels, which closed the feedback loop between attacker and model, became black-box query attacks against production APIs. Bayesian poisoning, which corrupted training data with a handful of bad examples, became training-data poisoning in large language models, where a small fraction of corrupted web pages can shift the behavior of a model trained on billions of documents.

The attack surface is broader now, but the underlying dynamic remains unchanged. When an adversary can manipulate model inputs and training data, accuracy alone is not a sufficient measure of performance. Systems must remain accurate while being robust to adversarial manipulation. That constraint, exposed in the spam arms race, still shapes the security of modern machine learning and AI systems.

[![Yubico](https://hackernoon.imgix.net/images/img-4p13m2j.png?auto=format&fit=max&w=3840)](https://www.yubico.com/resource/how-to-get-started-with-passwordless-using-device-bound-passkeys/?utm_source=hackernoon&utm_medium=referral&utm_campaign=Brand_Hackernoon-How-to-Get-Started-ToFu_Story-Page_701Wy00000WeXKq_NAM)