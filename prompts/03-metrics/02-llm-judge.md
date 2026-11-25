# An LLM judge, calibrated against your labels

Goal: use a judge for the one thing the checks cannot see, the quality of the reply text, and find out how far you can trust it by comparing it with your own labels.

The rubric has two items. J1 route fit: the reply does what its route asks. J2 no promise: the reply commits to no concrete change, release or date. The full wording is in the `judge` skill.

You label first, then the judge, so the judge cannot copy you.

## Step 1: you label six replies

```
Show me the reply, the route and the title of these six runs in data/transcripts/: 183-1, 236-1, 379-1, 958-1, 895-1 and 1088-1. Do not give a verdict.
```

Read the rubric in `.github/skills/judge/SKILL.md`, then write your own pass or fail for J1 and J2 for each run into `eval/human-labels.jsonl`, one line per run: `{"run": "958-1", "J1": "pass", "J2": "fail"}`.

## Step 2: the judge labels the same six, in a new session

Start a new session so the judge has not seen your labels (not verified by the build):

```bash
copilot
```

```powershell
copilot
```

```
Use the judge skill on 183-1, 236-1, 379-1, 958-1, 895-1 and 1088-1. Save the output to eval/judge-labels.jsonl.
```

## Step 3: compare

```
Compare eval/human-labels.jsonl and eval/judge-labels.jsonl. Give the number of agreeing item labels out of 12, list every disagreement with both verdicts and the judge's reason, and compute Cohen's kappa for J1 and for J2 separately. Show the formula and the counts you used.
```

## Expected answer

The reference human labels (proposed by the build from the rubric; yours may differ where the rubric is open):

| Run | J1 | J2 | Why |
|---|---|---|---|
| 183-1 | fail | pass | enhancement reply says more than thanks and one restating sentence: it claims `clearInvocations` already does this |
| 236-1 | pass | pass | thanks and one restating sentence |
| 379-1 | pass | pass | asks for the specific missing items |
| 958-1 | pass | fail | "we'll look at adding a note" commits to a concrete change |
| 895-1 | pass | pass | "we will look into it" is not a commitment |
| 1088-1 | pass | pass | says the example no longer throws, asks for the version |

10 of the 12 item labels are pass. The judge's output is not predicted: it is a model and varies from run to run. What to look for:

- High agreement (10 or more of 12) with disagreements on the borderline items (958-1 J2, 183-1 J1) means the rubric is usable and the borderline wording needs sharpening.
- Cohen's kappa on 6 runs is very noisy, and with only 2 failing labels in 12 it can be low even when the agreement is high. Report the agreement count first and kappa as a rough second number.
- If the judge and you disagree, do not change your label to match. Read the rubric words and decide whether the rubric is unclear. Change the rubric, relabel both sides, run again.

## Check yourself

- You wrote your labels before the judge ran, and the judge session did not read `eval/human-labels.jsonl`.
- Kappa is `(observed agreement - chance agreement) / (1 - chance agreement)`. With 2 fails in 12 labels a judge that says pass for everything agrees 10 of 12 and has kappa 0.
- A judge you have not compared with human labels gives you a number and no way to know whether to trust it.
