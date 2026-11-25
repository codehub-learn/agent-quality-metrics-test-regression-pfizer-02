# Transcript sample

## Distinct issue numbers

Ascending distinct issue numbers from `data/transcripts/` (117 numbers across 121 run files):

69, 86, 90, 99, 112, 114, 128, 134, 137, 179, 183, 186, 191, 197, 200, 203, 210, 212, 224, 226, 233, 236, 243, 304, 307, 310, 312, 316, 321, 329, 337, 340, 379, 383, 405, 408, 420, 438, 445, 457, 478, 482, 487, 506, 508, 541, 552, 554, 565, 567, 583, 585, 593, 608, 638, 644, 658, 666, 667, 688, 692, 702, 707, 714, 718, 725, 726, 730, 732, 739, 749, 750, 755, 757, 765, 787, 796, 800, 804, 806, 818, 820, 838, 840, 862, 866, 877, 878, 895, 919, 930, 945, 952, 958, 963, 976, 982, 984, 998, 1086, 1088, 1107, 1127, 1212, 1225, 1243, 1262, 1266, 1346, 1514, 1593, 1623, 1668, 1691, 3503, 3528, 3659

Every 11th number (positions 11, 22, ..., 110) gives 10 sampled issue numbers: 183, 236, 379, 506, 638, 725, 796, 878, 998, 1514.

Issue 1514 has two transcript runs. To keep the sample at one run per distinct issue, I selected the first run in ascending filename order, `1514-1`.

## Sample runs

| Run id | Mark |
|---|---|
| 183-1 | sample |
| 236-1 | sample |
| 379-1 | sample |
| 506-1 | sample |
| 638-1 | sample |
| 725-1 | sample |
| 796-1 | sample |
| 878-1 | sample |
| 998-1 | sample |
| 1514-1 | sample |
| 702-1 | added |
| 1346-1 | added |

702-1 is added because it is a docs issue, a label absent from the sample, and its transcript also records a failed `ReportFindings` tool call (`steps[5]`). 1346-1 is added to include an output-format edge case: `final` is fenced JSON and `final_is_json` is false. Its label is not included in the label counts below because the final message is not valid JSON.

## Label counts

For the sample, the 10 selected run files all have valid JSON finals. For all runs, 121 transcript files were considered; six whose `final` did not parse as JSON were skipped, leaving 115 counted runs. The label is taken from each successfully parsed final message.

| Label | Sample (10 runs) | All valid-JSON runs (115) |
|---|---:|---:|
| bug | 6 | 41 |
| enhancement | 3 | 46 |
| question | 1 | 13 |
| docs | 0 | 15 |

The sample most clearly under-represents `docs`: it has no docs run, while docs account for 15 of the 115 valid-JSON runs. Enhancement is also proportionally lower in the sample (3/10 vs. 46/115); question is slightly lower (1/10 vs. 13/115).

## Command and counting method

Run from the repository root in PowerShell. This lists the distinct sorted issue numbers, samples each 11th, selects one run per sampled issue (first filename in ascending order), and counts labels only when `json.loads(final)` succeeds:

```powershell
@'
import glob, json, os
from collections import Counter, defaultdict

by_issue = defaultdict(list)
all_runs = []
for path in sorted(glob.glob("data/transcripts/*.json")):
    with open(path, encoding="utf-8") as f:
        data = json.load(f)
    entry = (os.path.basename(path), data)
    by_issue[int(data["number"])].append(entry)
    all_runs.append(entry)

issues = sorted(by_issue)
sample_issues = [n for i, n in enumerate(issues, 1) if i % 11 == 0]
sample_runs = [sorted(by_issue[n], key=lambda x: x[0])[0] for n in sample_issues]
labels = ("bug", "enhancement", "question", "docs")

def count(runs):
    counts = Counter()
    skipped = []
    for run_id, data in runs:
        try:
            final = json.loads(data["final"])
        except (json.JSONDecodeError, TypeError):
            skipped.append(run_id)
            continue
        if final.get("label") in labels:
            counts[final["label"]] += 1
    return {label: counts[label] for label in labels}, skipped

print("distinct issue numbers:", issues)
print("sample issue numbers:", sample_issues)
print("sample runs:", [run_id.removesuffix(".json") for run_id, _ in sample_runs])
print("sample counts:", count(sample_runs))
print("all-run counts:", count(all_runs))
'@ | python -
```

The two added cases are not included in the 10-run sample counts.
