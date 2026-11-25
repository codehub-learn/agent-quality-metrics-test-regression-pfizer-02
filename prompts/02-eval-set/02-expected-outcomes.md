# Writing the expected outcome per case

Goal: for each of the 12 cases, write what a correct run must do: the gold label, the required tool calls, and what must and must not happen. You decide the gold label, not the agent under test.

Use the `eval-case` skill. It reads only the issue text, so a label copied from the triage agent's own answer cannot sneak in.

Paste the prompt below into the session. The `eval-case` skill loads by itself from its description (not verified by the build).

## Prompt

```
Use the eval-case skill for the 12 issues in eval/sample.md.
Show me each case as a proposal with the reason for the gold label, and mark any issue that could fit two labels. Do not save anything yet.
After I confirm or change the labels, save the 12 cases to eval/cases.jsonl, one JSON object per line.
```

Read each proposal against the issue before you confirm it. Change a label you disagree with; that is the point.

## Expected answer

Proposed from the issue text, to be confirmed by you. The reference labels:

| Issue | Gold label | Route | Why |
|---|---|---|---|
| 183 | enhancement | backlog | asks for a method to reset invocations but keep stubs |
| 236 | enhancement | backlog | asks for nicer printing of typed parameters |
| 379 | bug | repro-check | tests fail with `atLeast()` or `times()` |
| 506 | enhancement | backlog | asks for a better exception message |
| 638 | bug | repro-check | `NoClassDefFoundError` after upgrading |
| 702 | docs | answer | the website and README list versions without explanation |
| 725 | bug | repro-check | documented hint warnings do not appear (could also be docs) |
| 796 | bug | repro-check | package-private mocking breaks with another class loader |
| 878 | bug | repro-check | wrong error when spying an abstract class |
| 998 | question | answer | how to mock `URL` (the reporter says "I am not sure what is wrong") |
| 1346 | bug | repro-check | different mocks injected with `@Mock` and `@InjectMocks` |
| 1514 | bug | repro-check | Mockito stays in unfinished stubbing state |

Count: 7 bug (379, 638, 725, 796, 878, 1346, 1514), 3 enhancement, 1 question, 1 docs.

Required tool calls follow the label: `Read` of the issue file; `Skill:repro-check` for bug; `Skill:answer-draft` for question and docs; no skill for enhancement.

Things to notice:

- 725 is a real judgement call (bug or docs). A good case records it in `note` and still picks one label.
- The triage agent's own label matches these gold labels in all 12 cases. So label accuracy would score 12 of 12, and still two of these runs fail for other reasons: 998 ran a shell command and 1346 wrapped its answer in a code fence. That gap is the next section.

## Check yourself

- Every line of `eval/cases.jsonl` has `id`, `gold_label`, `gold_route`, `required_tools`, `must` and `must_not`.
- No gold label was copied from the agent. Ask the agent which file fields it read; it should say only `issue`.
- You changed at least one proposal or agreed with it for a reason you can say in one sentence.
