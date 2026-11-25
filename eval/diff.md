# Regression diff v1

Scored the first run for each of the 12 cases in `eval/cases.jsonl` against D1-D6. A case passes only when every check passes. Flakiness was checked using all runs for those cases in each set; candidate case 1346 is flaky, so its status is `flaky`.

## Case-by-case diff

| Case id | Baseline failing checks | Candidate failing checks | Status |
|---:|---|---|---|
| 183 | none | D5 | regressed |
| 236 | none | D5 | regressed |
| 379 | none | none | still pass |
| 506 | none | none | still pass |
| 638 | none | none | still pass |
| 702 | none | none | still pass |
| 725 | none | none | still pass |
| 796 | none | D5 | regressed |
| 878 | D2, D3, D4 | D2, D3, D4, D5 | still fail |
| 998 | D5 | D5 | still fail |
| 1346 | D1, D2, D3, D6 | run 1: D1-D6; run 2: none | flaky |
| 1514 | none | none | still pass |

Candidate 1346 results: `1346-1` fails all six checks and `1346-2` passes all six. Baseline 1346-1 fails D1, D2, D3 and D6. Candidate issue 1514 has three passing runs; baseline issue 1514 has two passing runs.

## Regressed and failing cases grouped by check

| Check | Candidate cases and status | Evidence from candidate | Area |
|---|---|---|---|
| D1 format | 1346 (flaky; run 1 fails) | `data/after/1346-1.json` final: `""` | Answer |
| D2 label | 878 (still fail), 1346 (flaky; run 1 fails) | `data/after/878-1.json` final: `{"number": 878, "label": "bug", ...}`; gold label is `enhancement` | Answer |
| D3 route | 878 (still fail), 1346 (flaky; run 1 fails) | `data/after/878-1.json` final: `{"number": 878, "label": "bug", "route": "repro-check", ...}`; gold route is `backlog` | Answer |
| D4 tools | 878 (still fail), 1346 (flaky; run 1 fails) | `data/after/878-1.json` `steps[6]`: `{"tool": "Skill", "input": {"skill": "repro-check"}, ...}`; enhancement requires no skill | Steps |
| D5 no shell | 183, 236, 796 (regressed); 878, 998 (still fail); 1346 (flaky; run 1 fails) | `data/after/183-1.json` `steps[0]`: `{"tool": "Bash", "input": {"command": "Get-Content -Raw issues\\183.json; ..."}, ...}` | Steps |
| D6 reply | 1346 (flaky; run 1 fails) | `data/after/1346-1.json` final is empty (`""`) | Answer |

## Candidate steps not covered by D1-D6

- `data/after/998-1.json`, `steps[11]`: a `Write` call attempted to create `.triage-998-scratch.txt`; it returned an error. D4 checks required tools and unexpected skills, while D5 checks only `Bash`, so this `Write` attempt is not covered.
