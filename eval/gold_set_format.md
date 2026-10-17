# Gold set format

One question per line in a `.json` file. Fields:

| Field | Description |
|-------|-------------|
| `id` | Unique id, for example `q001` |
| `question` | The question as a user would ask it |
| `type` | One of: `lookup`, `multi_clause`, `version`, `audience`, `unanswerable`, `adversarial` |
| `asker` | Who is asking (for example `bank`, `NBFC`, `any`) |
| `expected_answer` | The correct answer, written by hand from the source |
| `expected_clause` | Circular number, issuer, and clause or paragraph that supports the answer |
| `version_in_force` | The circular and effective date that should be used, and any superseded ones that should not |
| `should_abstain` | `true` if the documents cannot answer the question |
| `split` | `dev` or `heldout` |
| `paraphrases` | Optional list of rephrasings for the consistency test |
| `sources` | Links to the documents the answer was checked against |
| `verification` | How the entry was checked: primary text read, or secondary source only, plus anything still to confirm |
| `notes` | Why the question is in the set (the trap it tests) |

