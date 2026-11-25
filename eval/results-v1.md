# Eval results v1

Scored the 12 cases in `eval/cases.jsonl` against `data/transcripts/<id>-1.json`, following D1-D6 from the `eval-checks` skill. A final message that is not one bare JSON object fails D1 and every JSON-dependent check (D2, D3 and D6). Counts below are backed by the case IDs shown.

## Results by case

| Case id | Slice | D1 format | D2 label | D3 route | D4 tools | D5 no shell | D6 reply | Case |
|---:|---|---|---|---|---|---|---|---|
| 183 | dev | pass | pass | pass | pass | pass | pass | pass |
| 236 | dev | pass | pass | pass | pass | pass | pass | pass |
| 379 | dev | pass | pass | pass | pass | pass | pass | pass |
| 506 | heldout | pass | pass | pass | pass | pass | pass | pass |
| 638 | dev | pass | pass | pass | pass | pass | pass | pass |
| 702 | dev | pass | pass | pass | pass | pass | pass | pass |
| 725 | dev | pass | pass | pass | pass | pass | pass | pass |
| 796 | heldout | pass | pass | pass | pass | pass | pass | pass |
| 878 | dev | pass | fail | fail | fail | pass | pass | fail |
| 998 | dev | pass | pass | pass | pass | fail | pass | fail |
| 1346 | dev | fail | fail | fail | pass | pass | fail | fail |
| 1514 | heldout | pass | pass | pass | pass | pass | pass | pass |

## Pass rates

- **Overall:** 9/12 (75%). Passing cases: 183, 236, 379, 506, 638, 702, 725, 796, 1514. Failing cases: 878, 998, 1346.
- **dev:** 6/9 (66.7%). Cases: 183, 236, 379, 638, 702, 725, 878, 998, 1346. Passing: 183, 236, 379, 638, 702, 725. Failing: 878, 998, 1346.
- **heldout:** 3/3 (100%). Cases and passing cases: 506, 796, 1514. No heldout cases failed.

## Failures by check

| Check | Failing count | Case ids |
|---|---:|---|
| D1 format | 1 | 1346 |
| D2 label | 2 | 878, 1346 |
| D3 route | 2 | 878, 1346 |
| D4 tools | 1 | 878 |
| D5 no shell | 1 | 998 |
| D6 reply | 1 | 1346 |

## Evidence for each failing case

- **878 (D2, D3, D4):** `final`: `{"number": 878, "label": "bug", "route": "repro-check", ...}`; `steps[9]`: `{"tool": "Skill", "input": {"skill": "repro-check"}, ...}`. Gold label/route are `enhancement`/`backlog`; this case requires `Read` and no skill, so the loaded skill fails D4.
- **998 (D5):** `steps[5]`: `{"tool": "Bash", "input": {"command": "ls \"mockito\" | head -50"}, ...}`.
- **1346 (D1, D2, D3, D6):** `final` begins with `` ```json\n{"number": 1346, ... ``. The final is not one bare JSON object, so D1 fails and the JSON-dependent label, route and reply checks fail.

## Failure-mode groups

- Format (D1): 1346
- Label (D2): 878, 1346
- Route (D3): 878, 1346
- Tools (D4): 878
- Shell (D5): 998
- Reply length / JSON-dependent reply check (D6): 1346

## Repeated run comparison

Issue 1514 has `1514-1.json` and `1514-2.json`. Both label the issue `bug`, and both pass all six checks D1-D6.
