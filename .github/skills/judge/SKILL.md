---
name: judge
description: Judge the reply of a recorded triage run against a two-item rubric (route fit and no promise) and answer with pass or fail per item. Use when asked to judge, grade or score the replies of triage runs with an LLM judge or the rubric.
version: 1.0.0
---

# Judge

Judge the `reply` text of triage runs against a fixed rubric. You are the judge: you see the reply and its route, not anyone's earlier verdict.

## Rubric

| Item | Pass when | Fail when |
|---|---|---|
| J1 route fit | bug (route `repro-check`): the reply says the report is reproducible as written, or asks for the specific missing items. question or docs (route `answer`): the reply says which API or documentation is concerned and what the answer or the problem is. enhancement (route `backlog`): the reply is a thanks sentence plus one sentence that restates the request, and nothing else. | the reply does something else, or for enhancement says more than thanks and one restating sentence (for example claims the feature already exists or is planned) |
| J2 no promise | the reply makes no commitment to a concrete change, release or date. "We will look into it" is not a commitment. | the reply commits to a concrete change ("we'll look at adding a note", "this will be fixed in 3.0") or to a date |

## Steps

1. For each run id the user gives (for example `958-1`), read only `data/transcripts/<run>.json` fields `final` (parse it for `route` and `reply`) and `issue.title`. Do not open any file under `eval/` other than the rubric the user names; in particular never read human labels.
2. For each item answer `pass` or `fail`, with one short quote from the reply that decided it.
3. Output one JSON object per run, one per line: `{"run": "958-1", "J1": "pass", "J2": "fail", "reason": "..."}`.
4. If the final message is not JSON, say so and judge the whole text as the reply with route unknown.

## Rules

- Judge the text against the rubric words, not whether you like the answer. Do not make up a third item.
- If an item is truly ambiguous, choose one verdict and put "ambiguous" in `reason`; do not refuse.
- Do not edit files in `data/`.
