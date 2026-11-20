# Criteria for "the new version feels smarter"

Each criterion is checked on one run file `data/transcripts/<issue>-<run>.json`. Result is pass or fail. `steps` is the list of tool calls (`tool`, `input`, `result`, `error`); `final` is the agent's last message as a string; `final_is_json` says whether it parsed. "Parsed final" means `final` parsed as JSON.

Basis: rules in `data/triage-agent/triage-agent.md`, `repro-check.md` and `answer-draft.md`. These are my reading of "smarter"; the owner has not confirmed them. Only run 1086-1 was read as a sample to learn the file format.

| # | Type | Criterion | Where to look | Pass when |
|---|------|-----------|---------------|-----------|
| 1 | task | The answer is exactly one well-formed JSON object | `final_is_json`, parsed `final` keys, `label`, `number`, top-level `number` | `final_is_json` is `true`; keys are exactly `number`, `label`, `route`, `related`, `evidence`, `reply`; `label` is one of `bug`, `enhancement`, `question`, `docs`; parsed `number` equals top-level `number` |
| 2 | task | The reply fits the length limit | parsed `final.reply` | Length is 800 characters or fewer (and not empty) |
| 3 | task | Related issues follow the rules | parsed `final.related`, top-level `number` | `related` is a list of at most 3 integers and does not contain the run's own `number` |
| 4 | step | The agent reads the issue file first | `steps[0].tool`, `steps[0].input.file_path` | `steps[0].tool` is `Read` and `file_path` is `issues/<number>.json` |
| 5 | step | The route matches the label | parsed `final.label`, `final.route` | `bug` gives `repro-check`; `question` or `docs` gives `answer`; `enhancement` gives `backlog` |
| 6 | step | The skill loaded matches the route, and every evidence path was opened | `steps[*]` where `tool` is `Skill` (`input.skill`); `steps[*]` where `tool` is `Read` (`input.file_path`); parsed `final.evidence` | Route `repro-check` has a `Skill` step with `skill` = `repro-check`; route `answer` has one with `answer-draft`; route `backlog` has no `Skill` step. And every path in `evidence` equals the `file_path` of some `Read` step |

Note on 6: it is two checks in one row. Score it as fail if either half fails, but record both halves so a regression can be traced.

## Not checkable from a transcript

| Idea | Why it can not be checked |
|------|---------------------------|
| The label is the right one | Needs a gold label from a human. The transcript has no ground truth, only the agent's choice. |
| The related issues are about the same problem | Needs a human to judge. A transcript only shows the numbers; checking them against `issue-index.tsv` titles is a judgement call. |
| Every class or method named in the reply exists in `mockito/` | The Mockito source is not in the transcript. Tool results are truncated (`[cut]`), and in run 1086-1 the search returned "No files found", so existence can not be confirmed. |
| The reply is helpful, polite or accurate | A quality judgement with no objective rule. Needs a rubric and a judge (human or LLM), not a deterministic check. |
| The reply is honest about what it could not verify | Needs reading for meaning. Only a rubric or judge can say it, not a field comparison. |
| The agent did not edit files or run shell commands | Checkable as "no `Edit`, `Write` or `Bash` tool in `steps`". I left it out because it is a safety rule, not part of "smarter". It can be added as a seventh criterion if wanted. |
| The new version is better than the old one | A single run can not show it. Needs the same issues run on both versions and a pass rate compared. |
