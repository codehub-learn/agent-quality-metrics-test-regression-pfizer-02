# First-run pass rates

Rates are for the first run (`<id>-1.json`) of each of the 12 cases, requiring all D1-D6 checks to pass.

| Set | Slice | Pass rate | Passing case ids | Non-passing case ids |
|---|---|---:|---|---|
| Baseline (`data/transcripts/`) | Overall | 9/12 (75%) | 183-1, 236-1, 379-1, 506-1, 638-1, 702-1, 725-1, 796-1, 1514-1 | 878-1, 998-1, 1346-1 |
| Baseline (`data/transcripts/`) | dev | 6/9 (66.7%) | 183-1, 236-1, 379-1, 638-1, 702-1, 725-1 | 878-1, 998-1, 1346-1 |
| Baseline (`data/transcripts/`) | heldout | 3/3 (100%) | 506-1, 796-1, 1514-1 | none |
| Candidate (`data/after/`) | Overall | 6/12 (50%) | 379-1, 506-1, 638-1, 702-1, 725-1, 1514-1 | 183-1, 236-1, 796-1, 878-1, 998-1, 1346-1 |
| Candidate (`data/after/`) | dev | 4/9 (44.4%) | 379-1, 638-1, 702-1, 725-1 | 183-1, 236-1, 878-1, 998-1, 1346-1 |
| Candidate (`data/after/`) | heldout | 2/3 (66.7%) | 506-1, 1514-1 | 796-1 |
