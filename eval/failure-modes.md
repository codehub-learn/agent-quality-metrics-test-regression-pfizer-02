# Failure modes of the triage agent

Source: all 121 files in `data/transcripts/` (`Get-ChildItem data\transcripts` shows 121; `data/transcripts.sha256` has 121 lines). Rules come from `data/triage-agent/triage-agent.md`. Nothing in `data/` was edited.

Method: a read-only Python script, run from the repo root, loaded every `*.json` file and tested each skill signal. Each count below is `len()` of the list of run ids shown, not an estimate. The "bare JSON" test is `json.loads(final)`, which is also what `final_is_json` records; the two agreed on every run. For the six non-JSON runs I pulled the JSON object out of the text with a regex, so the other signals could still be checked on them.

## Catalogue

| Failure mode | Run ids | Count | Evidence (quoted from a transcript) | How counted |
|---|---|---|---|---|
| Final message is a JSON object inside a code fence | 186-1, 755-1, 1346-1 | 3 | 1346-1 `final`: `` ```json\n{"number": 1346, "label": "bug", ... `` | `json.loads(final)` fails and `final` starts with three backticks |
| Final message is prose followed by the JSON | 310-2, 583-1, 818-1 | 3 | 818-1 `final`: `Reproducible as written. The report has the snippet, the Mockito version (2.2.28), the stack trace...` then the JSON | `json.loads(final)` fails and `final` does not start with three backticks |
| `route` is not a valid value | 800-1 | 1 | 800-1 `final`: `"label": "question", "route": "answer-draft"` (policy value is `answer`) | `route` not in `backlog`, `answer`, `repro-check` |
| Shell command attempted | 487-1, 508-1, 714-1, 998-1 | 4 | 487-1 `steps[5]`: `{"command": "ls -la \"mockito\" \| head -30"}` (tool `Bash`) | any step with `tool` = `Bash` |
| `label` not one of the four | none | 0 | no failure found | `label` not in bug, enhancement, question, docs |
| `route` does not follow the label | none | 0 | no failure found | label to route map: bug to repro-check, question and docs to answer, enhancement to backlog (800-1 is counted above, not here) |
| Required skill not loaded (or a skill loaded on `backlog`) | none | 0 | no failure found | compared `Skill` steps with the skill the route needs |
| `reply` longer than 800 characters | none | 0 | no failure found | `len(reply) > 800` on every run (27 `final` strings are over 800 characters, but only because of the JSON wrapper, fence or prose; no `reply` is) |

Runs with none of these signals: 121 minus the 11 distinct runs above = 110 (the four lists do not overlap). "No failure found" here means none of the six signals fired, not that the answer is correct.

## Unclassified

These fit no failure mode in the skill, so I did not force them in.

| Observation | Run ids | Count | Evidence | Why it is not a mode |
|---|---|---|---|---|
| `ReportFindings` called with no `findings` argument and rejected | 10 of the 27 runs that call it (all 27 listed below) | 27 runs call it, 10 calls errored | 702-1 `steps[5]` result: `InputValidationError: ReportFindings failed due to the following issue: The required parameter findings is missing` | The agent was not told about this tool and no rule forbids it |
| `Output` tool called and unavailable | 930-1 | 1 | `<tool_use_error>Error: No such tool available: Output</tool_use_error>` | Same reason |
| `related` contains issue numbers larger than the run's own number, although the rules say "earlier issues" | 95 runs, for example 1086-1 (`[1097, 1769]`), 1107-1 (`[1637]`) | 95 | 1086-1 `final`: `"related": [1097, 649, 1769]` | I cannot check this: `issue-index.tsv` is not in the repo, and 1086's own issue text cites #1097. It may be a real rule break or a sign the index is not ordered by time. |
| `evidence` lists a source file with no successful `Read` step for that exact path | 1266-1, 337-1, 379-1, 718-1, 749-1, 755-1, 804-1 | 7 | 337-1 `evidence` has `mockito/mockito-core/src/main/java/org/mockito/stubbing/Stubber.java`, no matching `Read` | The rules say evidence is files "you opened". A file may have been seen only through `Grep` output. Needs a person to decide. |

`ReportFindings` runs: 1107-1, 1243-1, 1514-1, 197-1, 236-1, 243-1, 312-1, 316-1, 321-1, 379-1, 408-1, 478-1, 508-1, 552-1, 644-1, 658-1, 667-1, 702-1, 718-1, 730-1, 739-1, 765-1, 804-1, 862-1, 895-1, 945-1, 952-1.

## Agent or environment

- **Shell attempts (4 runs):** the agent's part is trying `Bash` when told not to run shell commands. The environment's part is that it was blocked every time: `Bash is disabled for this session, in subagents as well as here.` No harm resulted. In all four runs the `Bash` call is `steps[5]` and the steps just before it are `No files found` or `No matches found` (checked for 714-1 and 998-1 as well as the other two).
- **Empty `mockito/` (13 runs):** 1086-1, 1668-1, 200-1, 482-1, 508-1, 608-1, 666-1, 667-1, 688-1, 692-1, 707-1, 739-1, 765-1. Every search of `mockito/` returned `No files found` or `No matches found` and no source file was read (1086-1 `steps[2]`: `No files found`). This is most likely the environment, since the source folder looks empty or unreadable. The agent still replied sensibly and said it could not open the source, so the empty `evidence` is expected there. This also explains the 4 shell attempts: in all four runs the searches of `mockito/` just before the `Bash` call returned nothing (487-1 and 508-1 are in this list; 714-1 and 998-1 found nothing early on but later read source files, so they are not).
- **Unavailable tools (`ReportFindings`, `Output`):** the error is an `InputValidationError` or "No such tool"; the agent called a tool that was not in its instructions. Likely the agent, but the harness exposing these tools is a possible environment cause.
- **Code fence and prose-before-JSON (6 runs) and the `answer-draft` route (1 run):** these are in the agent's own output, and nothing in the transcripts points to the environment. Which of them come from the skill text is a guess I can not check; `answer-draft` is the skill name, which may have leaked into `route`.

## Not verified

- Whether labels are correct (no gold label).
- Whether the related issues and reply facts are right.
- Why the 6 non-JSON runs added fences or prose; only the observable output is recorded.
- 117 issue numbers are covered by the 121 runs; 1514, 310, 420 and 69 have two runs each. I did not compare runs of the same issue.
