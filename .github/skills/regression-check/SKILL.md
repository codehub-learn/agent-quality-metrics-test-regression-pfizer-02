---
name: regression-check
description: Compare two sets of recorded triage runs on the eval cases, apply a pass-rate gate, find flaky cases and diff the two sets case by case to localise what a change broke. Use when asked to check for a regression, gate a change, find flaky cases or diff two runs.
version: 1.0.0
---

# Regression check

Compare a baseline run set with the run set after a change. Work only from the files named; do not run the agent.

## Inputs

- `eval/cases.jsonl`: the eval cases (gold label, route, required tools).
- A baseline folder and a candidate folder of compact transcripts (`<id>-<run>.json`). In this lab the baseline is `data/transcripts/` and the candidate is `data/after/`.
- The six checks D1 to D6 defined in the `eval-checks` skill. Load that skill, read its table, and use the definitions exactly as written.

## Steps

1. For every case, apply D1 to D6 to run 1 of the baseline and run 1 of the candidate. A case passes when all six pass.
2. Flaky cases: a case is flaky in a set when it has more than one run in that folder and the runs do not all have the same result. List the case id and the result of each run. Flaky cases are reported, excluded from the gate, and never hidden.
3. Gate: the user gives a pass-rate threshold. Compute the pass rate of each set over the non-flaky cases, and say whether each set passes the gate. Also report the cases that passed in the baseline and fail in the candidate (regressions).
4. Diff: one row per case with the failing checks in the baseline, the failing checks in the candidate, and a status: `still pass`, `regressed`, `fixed`, `still fail`, or `flaky`.
5. Localise: group the regressed and still failing cases by the check that fails, and for each group quote one step or the start of `final` from a candidate file as evidence. Say whether the group is about the answer (D1 to D3, D6) or about the steps (D4, D5).
6. Report anything in the candidate steps the checks do not cover (for example a tool the baseline never used) as a separate "not covered" list, with the file and step.

## Rules

- Count by listing case ids. State a number only if you can list what it counts.
- Do not change the baseline, the candidate or the cases.
- The gate threshold is the user's decision. If asked, show what the pass rate would be at other thresholds, and say whether the baseline itself would pass each one.
