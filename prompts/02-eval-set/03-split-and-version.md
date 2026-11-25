# Dev slice, held-out slice and a version

Goal: split the 12 cases so you can tune on one part and check on another, and tie the eval set to a version of the data and the agent.

The dev slice is what you look at and fix against. The held-out slice is looked at once, at the end, to see whether your fixes only fit the dev cases. A case that moves from held-out to dev after you have seen it is no longer held out.

## Prompt

```
In eval/cases.jsonl, set "slice" for each case. Sort the cases by id ascending and put every 4th one (the 4th, 8th and 12th) in "heldout"; all others are "dev". Do not change anything else.
Show me the dev and held-out ids and the count of each gold label per slice.
Then write eval/README.md with: the eval set version "v1", the number of cases per slice, the first 12 characters of the SHA-256 of data/transcripts.sha256 (give the command you used), and one line saying that held-out cases are not used for tuning.
```

## Expected answer

| Slice | Cases | Gold labels |
|---|---|---|
| held-out (506, 796, 1514) | 3 | 2 bug, 1 enhancement |
| dev (183, 236, 379, 638, 702, 725, 878, 998, 1346) | 9 | 5 bug, 2 enhancement, 1 docs, 1 question |

The data fingerprint is `ed3a0a4a0764`, the first 12 characters of the SHA-256 of `data/transcripts.sha256`. Three held-out cases is small on purpose in this lab; real eval sets need more, so treat a held-out result here as a signal, not a proof.

## Version it

Commit the eval set and tag it, so a later run can say which eval set it used:

```bash
git add eval
git commit -m "Add eval set v1"
git tag eval-set-v1
```

```powershell
git add eval
git commit -m "Add eval set v1"
git tag eval-set-v1
```

Check:

```bash
git tag --list "eval-set-*"
```

```powershell
git tag --list "eval-set-*"
```

Expected: `eval-set-v1`.

## Check yourself

Reproduce the fingerprint:

```bash
shasum -a 256 data/transcripts.sha256 | cut -c1-12
```

```powershell
(Get-FileHash data/transcripts.sha256).Hash.Substring(0, 12).ToLower()
```

Expected: `ed3a0a4a0764`.

- The README and the tag name the same version. When you change a case, make `v2` and say why in one line; never edit `v1`.
- The held-out slice has no case you chose because it was interesting. The held-out ids come from the sort order, not from you.
