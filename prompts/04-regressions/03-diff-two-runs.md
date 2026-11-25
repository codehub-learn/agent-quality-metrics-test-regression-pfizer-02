# Diffing two runs to find what broke

Goal: go from "the pass rate dropped" to "this specific behaviour broke, in these cases", using a case-by-case diff.

## Prompt

```
Use the regression-check skill on the baseline data/transcripts/ and the candidate data/after/.
Give me the case-by-case diff: for each of the 12 cases the failing checks in the baseline, the failing checks in the candidate, and a status (still pass, regressed, fixed, still fail, flaky).
Then group the regressed and failing cases by the check that fails, quote one piece of evidence from a candidate file for each group, and say whether it is a problem with the answers or with the steps.
Finally list anything in the candidate steps that the six checks do not cover.
Save the result to eval/diff.md.
```

## Expected answer

Counted from the files, not by an agent. First run of each case.

| Case | Baseline fails | Candidate fails | Status |
|---|---|---|---|
| 183 | none | D5 | regressed |
| 236 | none | D5 | regressed |
| 796 | none | D5 | regressed |
| 878 | none | D5 | regressed |
| 998 | D5 | D5 | still fail |
| 1346 | D1, D2, D3, D6 | all six | flaky (run 2 passes) |
| 379, 506, 638, 702, 725, 1514 | none | none | still pass |

No case was fixed.

Localised: the regression is in the steps, not the answers. Six candidate runs (183-1, 236-1, 796-1, 878-1, 998-1, 1346-1) tried a shell command, and in five of them it is the very first step (a PowerShell command that reads the issue file; in 878-1 it is the second step). All six calls ended in an error, so the shell was not usable, but the agent was told not to use it and tried anyway. The labels, routes and replies of the four regressed cases are still correct (D1 to D4 and D6 pass).

Not covered by the six checks:

- 998-1 wrote a file (`.triage-998-scratch.txt`) with a `Write` step. The agent's instructions say not to edit files. No check looks for it, so 998 failed D5 for the shell step and this went unseen.
- 1346-1 ended with an empty final message. D1 catches that, but it is a different failure from a code fence, so a failure-mode list should keep them apart.

A good next step is a new check (a D7: no file written), and a note that this change removed the restriction on tools. The data tells you where it broke; whether the cause is a missing tool restriction in the new runtime is a hypothesis to test by reading its agent configuration, which this lab does not include.

## Check yourself

Find the first step of a regressed run:

```bash
grep -m1 -A3 '"tool": "Bash"' data/after/183-1.json
```

```powershell
Select-String -Path data/after/183-1.json -SimpleMatch '"tool": "Bash"' -Context 0,3 | Select-Object -First 1
```

- Both show a `Bash` step with a `Get-Content` command reading `issues/183.json`.
- The status of 1346 is `flaky`, not `regressed`: its second run passes.
- You can name the check (D5), the level (steps) and the cases (183, 236, 796, 878), not only "quality dropped".
