# Flaky cases and pass-rate gates

Scored the 12 cases in `eval/cases.jsonl` with D1-D6. A run passes only if all six checks pass. Flakiness is determined by whether multiple runs of the same eval case all have the same pass/fail result. Duplicate issues outside the 12 eval cases are not included.

## Flaky cases

| Set | Case id | Runs and results |
|---|---:|---|
| Baseline (`data/transcripts/`) | none | No eval case is flaky. Repeated case 1514 is stable: 1514-1 pass, 1514-2 pass. |
| Candidate (`data/after/`) | 1346 | 1346-1 fail, 1346-2 pass. |
| Candidate (`data/after/`) | none | Repeated case 1514 is stable: 1514-1 pass, 1514-2 pass, 1514-3 pass. |

Thus the baseline has 0 flaky cases; the candidate has 1 flaky case (1346).

## Pass rates over non-flaky cases

Use the first run for each case. The candidate's flaky case 1346 is excluded from its denominator.

| Set | Rate | Passing case ids | Non-passing non-flaky case ids | Excluded flaky case ids |
|---|---:|---|---|---|
| Baseline | 9/12 (75%) | 183-1, 236-1, 379-1, 506-1, 638-1, 702-1, 725-1, 796-1, 1514-1 | 878-1, 998-1, 1346-1 | none |
| Candidate | 6/11 (54.5%) | 379-1, 506-1, 638-1, 702-1, 725-1, 1514-1 | 183-1, 236-1, 796-1, 878-1, 998-1 | 1346-1 (first run was fail; case is excluded as flaky) |

The baseline denominator is all 12 eval cases: 183, 236, 379, 506, 638, 702, 725, 796, 878, 998, 1346, 1514. The candidate denominator is 11 non-flaky cases: 183, 236, 379, 506, 638, 702, 725, 796, 878, 998, 1514.

## Gate outcomes

| Threshold | Baseline | Candidate |
|---:|---|---|
| 95% | fail: 9/12 (75%) | fail: 6/11 (54.5%) |
| 85% | fail: 9/12 (75%) | fail: 6/11 (54.5%) |
| 50% | pass: 9/12 (75%) | pass: 6/11 (54.5%) |

The case IDs behind these rates are listed in the pass-rate table above.

## Recommendation

I recommend 85% as a quality target: 50% is permissive enough that both current sets pass, while 95% is also above the current baseline. However, the baseline itself is only 9/12 (75%), so 85% is not yet a usable regression gate for this baseline; neither is there an offered threshold that the baseline passes while the candidate fails.
