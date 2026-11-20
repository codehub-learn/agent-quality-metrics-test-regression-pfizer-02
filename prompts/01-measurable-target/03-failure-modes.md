# Cataloguing failure modes from the logs

Goal: list what actually goes wrong in the recorded runs, with counts you can verify, using the `failure-modes` skill.

Paste the prompt below into the session. The `failure-modes` skill loads by itself from its description (not verified by the build).

## Prompt

```
Use the failure-modes skill on all runs in data/transcripts/.
Give me the catalogue as a table: failure mode, run ids, count, and one quoted line of evidence from a transcript. Show the command or method you used for each count.
Then list anything that fits no failure mode as "unclassified", and tell me which failures could come from the environment and not from the agent.
Save the catalogue to eval/failure-modes.md.
```

## Expected answer

Counted from the files with `grep` and a short script, not by an agent. 121 runs, 11 of them with a failure.

| Failure mode | Runs | Count |
|---|---|---|
| Final message is not one bare JSON object, wrapped in a code fence | 1346-1, 186-1, 755-1 | 3 |
| Final message is not JSON at all (prose) | 310-2, 583-1, 818-1 | 3 |
| Invalid route (`answer-draft` instead of `answer`) | 800-1 | 1 |
| A shell command was used | 487-1, 508-1, 714-1, 998-1 | 4 |

No run has an invalid label, a route that contradicts its label, a missing skill load, or a reply over 800 characters.

Likely "unclassified" findings, which are real but not failures of the six signals:

- 27 runs call a tool named `ReportFindings`, which the agent was never told about; ten of those calls ended in an error.
- 30 runs that are not `enhancement` have an empty `evidence` list. Some may be honest (the issue names no Mockito class), so this is not a failure without a rule that says when evidence is required.

Environment versus agent: the tool results show "No files found" or "No matches found" for the Mockito source in many runs. When the source is not there, empty `evidence` is not the agent's fault. Treat it as a question for the next section, not as a failure.

## Check yourself

Confirm two counts yourself:

```bash
grep -l '"final_is_json": false' data/transcripts/*.json | wc -l
grep -l '"tool": "Bash"' data/transcripts/*.json | wc -l
```

```powershell
(Select-String -Path data/transcripts/*.json -SimpleMatch '"final_is_json": false' -List).Count
(Select-String -Path data/transcripts/*.json -SimpleMatch '"tool": "Bash"' -List).Count
```

Expected: `6` and `4`. If the agent's counts differ, ask it to list the run ids and compare.
