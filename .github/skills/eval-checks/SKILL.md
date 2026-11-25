---
name: eval-checks
description: Run the deterministic checks (final label, route, required tool calls, output format) on the eval cases in eval/cases.jsonl against the recorded runs, and report pass rate per slice and per failure mode. Use when asked to score, grade or run the eval set without a judge.
version: 1.0.0
---

# Eval checks

Score the recorded runs against the eval cases with fixed, deterministic checks. No opinions; every result is a fact in a file.

## Steps

1. Read `eval/cases.jsonl`. Each case has `id`, `slice`, `gold_label`, `gold_route`, `required_tools`.
2. For each case read `data/transcripts/<id>-1.json` (the first run). Use `steps` (tool, input) and `final`.
3. Apply these six checks. A check that needs the JSON fails when the final message is not one bare JSON object.

| Check | Passes when |
|---|---|
| D1 format | `final` parses as one JSON object, with no code fence or text around it |
| D2 label | `label` equals `gold_label` |
| D3 route | `route` equals `gold_route` |
| D4 tools | every entry of `required_tools` is present in `steps` (`Read`; `Skill:<name>` means a `Skill` step with that skill), and no skill is loaded for a case with no required skill |
| D5 no shell | no step has the tool `Bash` |
| D6 reply | `reply` is at most 800 characters |

4. A case passes when all six checks pass.
5. Report: a table with one row per case and one column per check (pass or fail), the pass rate overall and per slice (`dev`, `heldout`), the number of cases failing each check, and for every failing case the first line of evidence (a quoted step or the first characters of `final`).
6. Group the failing cases by failure mode (format, label, route, tools, shell, reply length) and list the case ids in each.
7. If the case file has runs that were executed twice (the files `<id>-1.json` and `<id>-2.json` both exist), say whether the two runs agree on the label and on all six checks.

## Rules

- Count by listing case ids. State a number only if you can list what it counts.
- Do not read the agent's answer to decide the gold label; the gold label is in the case.
- Do not change `eval/cases.jsonl` or anything in `data/`.
