# Task level versus step level

Goal: see that a run can give the right answer and still fail at step level, and that a good-looking run can fail at task level for a reason unrelated to the content.

Use the criteria from the previous prompt. The expected answer below uses the reference criteria C1 to C6 from that file. If your own list differs, compare the pattern, not every cell.

## Prompt

```
Apply eval/criteria.md to these four recorded runs in data/transcripts/: 702-1, 1346-1, 800-1 and 487-1.
Give a table with one row per run and one column per criterion: pass or fail, and for every fail the exact line or step that shows it.
Then say which runs pass at task level but fail at step level, and which fail at task level even though the content looks right.
If an answer is not valid JSON, mark every criterion that needs the JSON as fail and say so.
```

## Expected answer

Checked by reading the four files and counting, not by an agent.

| Run | C1 JSON | C2 label | C3 route | C4 no shell | C5 skill | C6 reply | All six |
|---|---|---|---|---|---|---|---|
| 702-1 | pass | pass | pass | pass | pass | pass | pass |
| 1346-1 | fail | fail | fail | pass | fail | fail | fail |
| 800-1 | pass | pass | fail | pass | fail | pass | fail |
| 487-1 | pass | pass | pass | fail | pass | pass | fail |

- 487-1 has a correct final answer and fails only at step level: it ran a shell command (`Bash`) although the agent was told not to.
- 1346-1 wraps an otherwise well-formed answer in a code fence (` ```json `). It fails at task level for format, not for content. Criteria that need the JSON fail with it, which is why one format problem fails five criteria.
- 800-1 gives route `answer-draft`, a skill name, instead of the route `answer`. Label and reply are fine.
- Over all 121 runs, 110 pass all six criteria (91%).

## Check yourself

- Open `data/transcripts/487-1.json` and find the step with tool `Bash`.
- Open `data/transcripts/1346-1.json` and look at the first characters of `final`.
- If your table marks 1346-1 as pass on C2, you judged the content. The criterion says the final message is one bare JSON object, so a fence fails it. Decide which behaviour you want, and write that into the criterion.
