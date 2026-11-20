# Report: eval/criteria.md applied to 4 runs

Runs: 702-1, 1346-1, 800-1, 487-1 (`data/transcripts/<run>.json`).
Method: read `steps`, `final` and `final_is_json` of each file, plus a read-only Python script for the JSON parse and reply lengths. Nothing was edited.

| Run | 1 JSON shape | 2 Reply <=800 | 3 Related | 4 Reads issue first | 5 Route matches label | 6 Skill and evidence | Verdict | Evidence |
|---|---|---|---|---|---|---|---|---|
| 702-1 | pass | pass (558) | pass | pass | pass | pass | **PASS** | none needed: `final_is_json` true, `steps[0]` Read `issues/702.json`, `route` answer, `steps[1]` Skill `answer-draft`, `evidence` empty |
| 1346-1 | **fail** | **fail** | **fail** | pass | **fail** | **fail** | **FAIL** (5 of 6 fail) | `final_is_json` false; `final` starts with a ```` ```json ```` fence and fails `json.loads` at char 0, so criteria 1, 2, 3, 5, 6 cannot be read from the JSON; criterion 4 still passes (`steps[0]` Read `issues/1346.json`) |
| 800-1 | pass | pass (636) | pass | pass | **fail** | **fail** | **FAIL** (2 of 6 fail) | `final.route` is `"answer-draft"` instead of `answer` (label `question`), so 5 fails and 6 cannot pass as written; `steps[6]` Skill is `answer-draft` and the evidence path was read at `steps[5]`, so the work was done and only the route value is wrong |
| 487-1 | pass | pass (463) | pass | pass | pass | pass | **PASS** | none needed: `final_is_json` true, `steps[0]` Read `issues/487.json`, `route` answer, `steps[11]` Skill `answer-draft`, `evidence` empty |

Verdict rule: a run passes only if all six criteria pass. One fail makes the verdict FAIL.

## Pass at task level, fail at step level

- 800-1: criteria 1 to 3 pass, but the route is wrong (criteria 5 and 6).

## Fail at task level although the content looks right

- 1346-1: label `bug`, route `repro-check`, 549-character reply and evidence all look correct. All five evidence paths were opened by `Read` steps 12, 14, 15, 22, 23, and `steps[24]` is a `Skill` step for `repro-check`. It fails only because the answer is wrapped in a code fence.

## Not covered by the criteria

- 702-1: `steps[5]` is a `ReportFindings` call with `error: true`.
- 487-1: `steps[5]` is a `Bash` call (`ls -la "mockito"`) with `error: true`. The agent must not run shell commands. This is the safety check left out of `eval/criteria.md`, so it is not counted as a fail.
- 1346-1: the `Skill` step comes last, after the research, and many reads of `mockito/` paths errored. The criteria do not check order or errors.
- Label correctness was not checked: there is no gold label for these issues.
