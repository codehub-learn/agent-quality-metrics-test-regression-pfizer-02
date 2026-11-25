---
name: eval-case
description: Draft eval cases for the triage agent from issue numbers, with a proposed gold label, required tool calls and must and must-not conditions, for a human to review. Use when asked to write, draft or add eval cases or expected outcomes for the triage agent.
version: 1.0.0
---

# Eval case

Draft one eval case per issue number the user gives. A human confirms every gold label.

## Steps

1. Read `data/triage-agent/triage-agent.md` (labels and routing policy) and `eval/criteria.md` if it exists.
2. For each issue number `N`, read only the `issue` field of `data/transcripts/N-1.json` (title and body). Do not read `final`, the label or the route the agent gave: a gold label copied from the agent measures nothing.
3. Propose `gold_label` from the issue text with one of bug, enhancement, question, docs. Add a one-line `reason` quoting the issue. If the issue could fit two labels, say which two in `note` and still pick one.
4. Derive from the label, using the policy: `gold_route` (bug: repro-check; question and docs: answer; enhancement: backlog) and `required_tools`: `Read` of the issue file, plus `Skill:repro-check` for bug, `Skill:answer-draft` for question and docs, and no `Skill` for enhancement.
5. Write `must` and `must_not` as short observable conditions, taken from `eval/criteria.md` when it exists (final message is one bare JSON object, reply at most 800 characters, no shell command).
6. Output one JSON object per line, in this form, and nothing else per case:

```
{"id": 379, "slice": "", "source": "sample", "gold_label": "bug", "gold_route": "repro-check", "required_tools": ["Read", "Skill:repro-check"], "must": ["..."], "must_not": ["..."], "reason": "...", "note": ""}
```

Leave `slice` empty; the split is a separate step. Set `source` to `sample` unless the user says the case was added on purpose.

## Rules

- Mark every gold label as a proposal when you show it, and ask the user to confirm or change each one before the file is saved as final.
- Never fill in a case from memory of Mockito; use the issue text.
- Do not edit files in `data/`.
