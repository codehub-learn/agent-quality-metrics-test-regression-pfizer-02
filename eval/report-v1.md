# Eval report v1

## Pass rates

- **Overall:** 9/12 (75%). Passing cases: 183, 236, 379, 506, 638, 702, 725, 796, 1514. Failing cases: 878, 998, 1346. The 12 cases are 183, 236, 379, 506, 638, 702, 725, 796, 878, 998, 1346, 1514.
- **dev:** 6/9 (66.7%). Cases: 183, 236, 379, 638, 702, 725, 878, 998, 1346. Passing: 183, 236, 379, 638, 702, 725. Failing: 878, 998, 1346.
- **heldout:** 3/3 (100%). All three cases passed: 506, 796, 1514. No heldout cases failed.

## Failure modes by case

| Case id | Failure mode | Evidence |
|---:|---|---|
| 878 | Wrong label and route, and a skill was loaded when the enhancement route requires none. | `final`: `{"number": 878, "label": "bug", "route": "repro-check", ...}`; `steps[9]` loads `repro-check`. Gold label/route: `enhancement` / `backlog`. |
| 998 | Shell command attempted. | `steps[5]`: `{"tool": "Bash", "input": {"command": "ls \"mockito\" | head -50"}, ...}` |
| 1346 | Final is fenced rather than one bare JSON object, so the JSON-dependent checks fail. | `final` starts with `` ```json\n{"number": 1346, ... `` |

These are the only failing cases: 878, 998, 1346.

## Repeated-run comparison

Checks are listed as D1-D6; `P` means pass and `F` means fail. For each run, its own label is used to derive the expected route and required skill. The 310-2 final is not bare JSON; its embedded object says `bug` / `repro-check`, used only to compare its label and determine D4.

| Issue | Run 1 label | Run 1 D1-D6 | Run 2 label | Run 2 D1-D6 | Labels agree? | Overall pass/fail agrees? |
|---:|---|---|---|---|---|---|
| 69 | enhancement | P P P P P P | enhancement | P P P P P P | yes | yes (pass) |
| 310 | bug | P P P P P P | bug* | F F F P P F | yes* | no |
| 420 | docs | P P P P P P | docs | P P P P P P | yes | yes (pass) |
| 1514 | bug | P P P P P P | bug | P P P P P P | yes | yes (pass) |

For issue 310, the two runs agree on the embedded label, but not on whether the run passes all checks: 310-1 passes and 310-2 fails D1, D2, D3 and D6. Runs 69, 420 and 1514 agree on both label and all-check outcome.

A 5-point pass-rate change would not be meaningful on this 12-case set: one case changes the rate by 1/12, or about 8.3 percentage points.
