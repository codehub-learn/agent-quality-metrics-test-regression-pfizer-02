# Reading the results: pass rate, failure modes, noise

Goal: turn the numbers into a statement you can defend: how good is the agent, which failures are most common, and how much of any difference is noise.

You need `eval/results-v1.md` from the deterministic checks.

## Prompt

```
Using eval/results-v1.md, write eval/report-v1.md with: the overall pass rate and the pass rate on dev and held-out; the failure modes by case, not by check; and for every number the case ids behind it.
Then use the runs that were executed twice in data/transcripts/ (files <id>-1.json and <id>-2.json, issues 69, 310, 420 and 1514). For each of those four issues, apply the six checks to both runs, taking the agent's own label as the gold label, and say whether the two runs agree on label and on pass or fail.
Finish with one sentence on whether a 5 point change in pass rate on this eval set would mean anything.
```

## Expected answer

From the files, not by an agent:

| Statement | Value |
|---|---|
| Overall pass rate | 10 of 12 (83%) |
| Dev, held-out | 7 of 9, 3 of 3 |
| Failure modes by case | shell command used: 998. Not bare JSON (code fence): 1346 |

Run to run, same issue and same prompt (the agent's own label used as gold, all six checks):

| Issue | Run 1 | Run 2 | Agree |
|---|---|---|---|
| 69 | enhancement, pass | enhancement, pass | yes |
| 310 | bug, pass | no JSON (prose answer), fail | no |
| 420 | docs, pass | docs, pass | yes |
| 1514 | bug, pass | bug, pass | yes |

- Three of four repeated issues agree. The fourth (310) passes once and fails once on format, with nothing changed. So the agent is not fully repeatable, and a single run of an issue can flip.
- A 5 point change on 12 cases is less than half a case (one case is 8.3 points), so it cannot mean anything. A change of one case (8 points) can come from a flip like the one in issue 310, so it is not evidence either.

## Check yourself

- The report says "10 of 12" and lists the two failing ids, not only "83%".
- The report counts 1346 once as a format failure, not four times.
- Open `data/transcripts/310-2.json` and read how the final message starts. It is a checklist in prose, not JSON.
