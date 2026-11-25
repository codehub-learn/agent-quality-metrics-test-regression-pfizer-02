# Sampling the recorded runs

Goal: pick a small set of issues that looks like real usage, and add a few cases on purpose that the sample would miss.

There are 117 distinct issues in `data/transcripts/` (four ran twice; use the first run, `-1`). A sample you can repeat beats one you chose by eye.

Paste the prompt below into a session in the project root (not verified by the build).

## Prompt

```
List the distinct issue numbers in data/transcripts/ in ascending order. Take every 11th one (the 11th, 22nd, 33rd and so on). Show the list and the command you used.
Then compare that sample with all runs: for the sample and for all 121 runs, how many runs did the triage agent label bug, enhancement, question and docs (read the label in each run's final message; skip runs whose final message is not JSON)?
Tell me which label the sample under-represents. Add the two runs 702 and 1346 as extra cases and say why each is worth adding. Save the 12 ids, marked "sample" or "added", to eval/sample.md.
```

## Expected answer

The sample is 10 issues: 183, 236, 379, 506, 638, 725, 796, 878, 998, 1514. Counted from the files, not by an agent.

| Agent label | Sample (10 first runs) | All valid runs (115 of 121) |
|---|---|---|
| bug | 6 | 41 |
| enhancement | 3 | 46 |
| question | 1 | 13 |
| docs | 0 | 15 |

- The sample has no docs case, though docs is about 13% of all valid runs. A sample of 10 is small, so a missing label is expected. This is why a case is added on purpose.
- 702 (docs: the version list on the website) fills the docs gap. 1346 is a run whose answer is wrapped in a code fence, a known format failure.
- 998 is already in the sample and is a run that used a shell command. Sampling without looking found a failure; do not remove it.

## Check yourself

Reproduce the sample list yourself:

```bash
ls data/transcripts | sed 's/-.*//' | sort -un | awk 'NR%11==0'
```

```powershell
Get-ChildItem data/transcripts | ForEach-Object { [int]($_.Name -split '-')[0] } | Sort-Object -Unique | ForEach-Object -Begin { $i = 0 } -Process { $i++; if ($i % 11 -eq 0) { $_ } }
```

Expected: `183 236 379 506 638 725 796 878 998 1514`, one per line.
