# Financial-Regulation
Measuring and Improving AI Agents for Financial Regulation

**Repository contents**

- `README.md`: the note (this file)
- `eval/metrics.md`: the five checks tracked per question and how each is scored
- `eval/question_types.md`: the question types the gold set should cover
- `eval/gold_set_format.md`: the fields for one gold-set question
- `examples/gold_set_v0.json`: 11 starter questions from real RBI and SEBI documents, each with the expected answer, clause, version in force and source link. This is a seed set, not the full 100 to 150 question set.
- `examples/SOURCES.md`: the documents used and how thoroughly each was checked


## 1. How I would measure and improve these agents

Regulation questions are harder than ordinary document questions for three reasons. Rules get amended or replaced, so the version in force matters. The answer often depends on who is asking, such as a bank or an NBFC. And a wrong answer that sounds confident is worse than saying "I do not know".

I would start with a gold set of around 100 to 150 questions built by hand from public RBI and SEBI circulars, including at least one circular that amends another. The questions would cover direct lookups, answers that need several clauses, "which version applies" questions, questions the documents cannot answer, and a few adversarial ones. Writing these by hand is slow, but it is the most useful part of the work.

For each question I would track:

- Whether the answer is correct or not?
- Whether the cited clause actually supports the claim?
- Whether the answer is grounded in the retrieved text?
- Whether the agent picked the version that is in force?
- Whether the agent abstained when it should have?

To verify an interpretation, every answer must point to a specific clause. I would run that check with an LLM judge and validate the judge itself by comparing its verdicts against my own labels on a sample. To test consistency, I would ask the same question in several phrasings and run it more than once, then flag any case where the conclusion changes. A difference in wording alone would not count. To reduce errors, I would read the failures, group them by where in the pipeline they started, fix one group at a time, and rerun the full set after every change so nothing quietly breaks. I would also hold out part of the gold set and never look at it while fixing failures, so the final score is not tuned to the questions I studied.

## 2. My work

I have done this kind of work on financial documents, although not yet on regulations. Here is the closest work I have done.

**[FinIntelAI](https://github.com/31groot/finintelai)** is a hybrid RAG agent for financial research. I ran it against 52 benchmarks, studied the failures, and grouped them into six classes. Fixing those classes raised accuracy from 77% to 89%. None of the fixes were prompt changes or a bigger model. They were in routing, filtering, and indexing:

- Questions asking for standalone figures were being pushed toward consolidated data, so I added routing for the statement type.
- The word "business" was too greedy as a classification keyword and sent questions to the wrong retrieval path. Fixing it moved the narrative category from 67% to 100%.
- Constant currency figures live in earnings call transcripts, not annual reports. Adding the right retrieval vocabulary moved that category from 33% to 100%.
- Superlative questions such as "which company had the highest revenue" now compare across all companies instead of collapsing to one.
- Filters now relax step by step instead of failing outright, so a strict filter no longer returns nothing.
- Table chunks made mostly of numbers were almost invisible to retrieval, so I added a short descriptive prefix to each one.

I also added verify and retry loops with a fixed limit, so the agent recovers from model failures instead of returning an unsupported answer.

The limits are clear. The FinIntelAI benchmark is small, and the per-category numbers come from small groups of questions, so treat them as indicative. I tuned the fixes while looking at the same questions I measured on. 

## 3. What I could do next

Everything below is a proposal, not a result.

- **Test on real regulations.** Build the gold set described above, run FinIntelAI on it, and report honestly what breaks.
- **Protect the version filters.** Relaxing filters helped in my earlier work, but in regulation it could silently return a superseded circular. I would relax everything except version filters, and abstain if the version in force cannot be confirmed.
- **Add context to every chunk.** Before indexing, I would attach the circular number, issuer, effective date, section title, and whether it has been superseded, so version filtering is exact and not guesswork.
- **Replace hardcoded routing with a decision model.** My query decomposer is rule based, so someone has to update it every time new documents are added. I would replace it with a decision model like [jev](https://typesafe.ai/blog/introducing-system-one-models-and-jev) for query routing, so new documents no longer mean new hand-written rules.
- **Fine tune embeddings only if needed.** Only if the failures show that regulatory vocabulary is the real problem, and only with enough labeled examples.
- **Keep a record and a human check.** Store the sources and the steps behind every answer, send low confidence answers to a person, and rerun the full evaluation whenever a regulation changes.

## 4. Closing note

With financial regulation, being right most of the time is not enough, because people act on these answers and the cost of a confident mistake falls on them. I think a dependable agent is one that shows its sources, admits when it is unsure, and can be checked by someone else. That is the kind of system I want to help build.
