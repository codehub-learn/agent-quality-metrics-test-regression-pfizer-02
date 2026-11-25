# Running the eval set after a change

Goal: score the same 12 cases on a new version of the agent and compare with the baseline, before anyone ships it.

`data/after/` holds a second set of recorded runs of the same triage agent: same instructions, same model, recorded after the agent was moved to a different runtime. It has the runs for the 12 eval issues (15 runs, because 1346 and 1514 ran more than once). Nothing is run for you to wait for; the runs are already recorded.

If you did not finish the earlier sections, start from the reference cases:

```bash
mkdir -p eval
cp data/reference/cases.jsonl eval/cases.jsonl
```

```powershell
New-Item -ItemType Directory -Force eval | Out-Null
Copy-Item data/reference/cases.jsonl eval/cases.jsonl
```

## Prompt

```
Use the regression-check skill. The baseline is data/transcripts/ and the candidate is data/after/.
First give me only the pass rate of each set on the 12 cases (first run of each case), overall and for dev and held-out, and the case ids behind each number. Do not draw conclusions yet.
Save it to eval/results-after.md.
```

## Expected answer

Counted from the files with a script that uses no agent.

| Set | Pass all six | Dev | Held-out |
|---|---|---|---|
| Baseline (`data/transcripts/`) | 10 of 12 (83%) | 7 of 9 | 3 of 3 |
| Candidate (`data/after/`) | 6 of 12 (50%) | 4 of 9 | 2 of 3 |

Passing in the candidate: 379, 506, 638, 702, 725, 1514. Failing: 183, 236, 796, 878, 998, 1346.

The drop is 33 points, much more than one case (8 points). Do not look for a cause yet; the next prompts do that.

## Check yourself

Count the candidate runs that tried a shell command:

```bash
grep -l '"tool": "Bash"' data/after/*.json | wc -l
```

```powershell
(Select-String -Path data/after/*.json -SimpleMatch '"tool": "Bash"' -List).Count
```

Expected: `6`. Count the runs in the baseline with the same command on `data/transcripts/`: `4` of 121.
