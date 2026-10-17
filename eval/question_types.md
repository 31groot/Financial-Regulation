# Question types

The gold set (target: around 100 to 150 questions) is built by hand from public RBI and SEBI circulars, including at least one circular that amends another.

| Type | What it tests | Example pattern |
|------|---------------|-----------------|
| Direct lookup | Finding a single stated rule | "What does the circular say about X?" |
| Multi-clause | Combining several clauses into one answer | "Under what conditions can an entity do X, considering both clause A and clause B?" |
| Version | Picking the circular that is in force after an amendment | "What is the current limit for X, given that the circular was amended?" |
| Audience-dependent | The answer differs by who is asking | "Does this apply to a bank, an NBFC, or both?" |
| Unanswerable | The documents do not contain the answer | A reasonable-sounding question the corpus cannot answer. The agent should abstain. |
| Adversarial | Wrong premises, leading wording, or pressure to guess | "Since the rule was removed, what replaced it?" when it was not removed. |

Each type should be represented in both the working split and the held-out split.
