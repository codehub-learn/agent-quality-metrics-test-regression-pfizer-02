# Deterministic checks

Goal: score the 12 eval cases with checks that need no judgement: the format, the label, the route, the tool calls and the reply length.

Use the `eval-checks` skill. It reads `eval/cases.jsonl` and the recorded runs. Nothing is sent to a model beyond the agent you are talking to.

Paste the prompt below into a session in the project root. The skill loads by itself from its description (not verified by the build).

If you did not finish the previous section, start from the reference cases:

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
Use the eval-checks skill on eval/cases.jsonl.
Give me the table of cases by checks (D1 to D6), the pass rate overall and per slice, the number of cases failing each check, and for each failing case one line of evidence. List the case ids behind every number.
Save the result to eval/results-v1.md.
```

## Expected answer

Counted from the files with a script that does not use an agent. 10 of 12 cases pass.

| Case | Slice | D1 | D2 | D3 | D4 | D5 | D6 | Result |
|---|---|---|---|---|---|---|---|---|
| 183, 236, 379, 506, 638, 702, 725, 796, 878, 1514 | dev or heldout | pass | pass | pass | pass | pass | pass | pass |
| 998 | dev | pass | pass | pass | pass | **fail** | pass | fail |
| 1346 | dev | **fail** | **fail** | **fail** | pass | pass | **fail** | fail |

- Pass rate: 10 of 12 overall, 7 of 9 on dev, 3 of 3 on held-out.
- Failing checks: D1 1 (1346), D2 1 (1346), D3 1 (1346), D4 0, D5 1 (998), D6 1 (1346).
- 998 has the correct label, route and skills, and ran a shell command (a `Bash` step). 1346 has the right content in a code fence, so D1 fails and the checks that need the JSON fail with it.

Things to notice:

- Score on the label alone, reading it even out of the fenced answer, and the agent gets 12 of 12. The deterministic checks find the two cases a label score misses.
- One format failure (1346) fails four checks. Count failure modes by case, not by check, or you will count it four times.
- With 12 cases a single case moves the rate by 8 points. Treat 83% as a rough number.

## Check yourself

- Every number the agent gives comes with the case ids it counts.
- Open `data/transcripts/998-1.json` and find the `Bash` step. Open `data/transcripts/1346-1.json` and look at the first characters of `final`.
- If the agent reports 11 of 12 because it accepted the fenced JSON, check D1 against the skill's definition: one bare JSON object, no fence.
