# A pass-rate gate and flaky cases

Goal: decide a gate that would have stopped this change, without blocking a good one, and handle the cases whose result changes from run to run.

A gate is a rule such as "the pass rate must be at least X". Too strict and it blocks the baseline; too lax and it lets the regression through. A flaky case passes in one run and fails in another with nothing changed; it makes a gate unreliable unless you handle it on purpose.

## Prompt

```
Use the regression-check skill on the baseline data/transcripts/ and the candidate data/after/.
1. Find the flaky cases in each set: cases with more than one run whose runs do not all give the same result. List the runs and their results.
2. Compute the pass rate of each set over the non-flaky cases, using the first run of each case.
3. For gates of 95%, 85% and 50%, tell me whether the baseline passes and whether the candidate passes.
4. Recommend one gate and say why. Save the answer to eval/gate.md.
```

## Expected answer

Counted from the files, not by an agent.

Flaky cases:

| Case | Runs | Result |
|---|---|---|
| 1346 (candidate) | 1346-1, 1346-2 | 1346-1 fails (ended with no answer at all), 1346-2 passes: flaky |
| 1514 (candidate) | 1514-1, 1514-2, 1514-3 | all three pass: stable |
| 1514 (baseline) | 1514-1, 1514-2 | both pass: stable |
| 1346 (baseline) | only one run | cannot tell; its single run failed on format |

Take 1346 out of the gate and the sets have 11 cases each:

| Set | Pass rate (non-flaky) |
|---|---|
| Baseline | 10 of 11 (91%) |
| Candidate | 6 of 11 (55%) |

| Gate | Baseline passes | Candidate passes | Verdict |
|---|---|---|---|
| 95% | no (91%) | no | too strict: it blocks the known-good baseline |
| 85% | yes | no | catches the regression and allows the baseline |
| 50% | yes | yes (55%) | too lax: lets the regression through |

So 85% is the sensible gate here. A second rule that needs no threshold also catches this change: no case that passed in the baseline may fail in the candidate. Four cases break it (183, 236, 796, 878).

With 11 cases a gate of 85% allows exactly one failing case (10 of 11 is 91%, 9 of 11 is 82%). Say that in your answer: the gate is only as fine as the eval set.

## Check yourself

- The flaky case is reported and excluded from the rate, not dropped silently.
- Every gate was also tried on the baseline. A gate you did not test on a known-good version is a guess.
- Open `data/after/1346-1.json` and look at `final`: it is empty. The agent stopped without answering. Open `data/after/1346-2.json`: the same case answered correctly.
