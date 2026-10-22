# Metrics

Each gold-set question is scored on five checks. Every check is recorded separately so failures can be grouped by where they started in the pipeline.

| # | Check | Question it answers | How it is scored |
|---|-------|---------------------|------------------|
| 1 | Correctness | Is the final answer right? | Compared with the expected answer. Human label first; LLM judge once validated. |
| 2 | Citation support | Does the cited clause actually say what the answer claims? | LLM judge reads the clause and the claim. Judge is validated against human labels on a sample. |
| 3 | Grounding | Is every claim in the answer supported by the retrieved text? | Claim-by-claim check against the retrieved passages. |
| 4 | Version in force | Did the agent use the circular that is currently in force, not a superseded one? | Compare the cited circular and date with `version_in_force` in the gold set. |
| 5 | Abstention | Did the agent decline when the documents cannot answer, and answer when they can? | Compare behavior with `should_abstain`. Track both wrong answers and wrong refusals. |

## Consistency

For a subset of questions, ask the same question in several phrasings and run each more than once.

- **Flag:** the conclusion changes (for example "permitted" vs "not permitted", or a different threshold).
- **Not a flag:** different wording with the same conclusion.

## Judge validation

The LLM judge is only trusted after its verdicts are compared with my own labels on a sample of answers. Report the agreement rate and review the disagreements before using the judge at scale.

## Held-out split

Part of the gold set is held out and not looked at while fixing failures. The final score is reported on the held-out part only, so it is not tuned to the questions studied during debugging.

## Error analysis loop

1. Run the full set.
2. Read the failures and group them by where in the pipeline they started (routing, retrieval, filtering, indexing, generation, citation).
3. Fix one group at a time.
4. Rerun the full set after every change so nothing quietly breaks.
