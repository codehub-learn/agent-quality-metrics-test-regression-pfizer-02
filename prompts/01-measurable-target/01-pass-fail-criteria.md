# From "feels smarter" to pass/fail criteria

Goal: turn one vague quality goal into a few criteria that can be checked on a recorded run without any opinion.

The triage agent's own instructions are in `data/triage-agent/`. The recorded runs are in `data/transcripts/`. Run everything from the project root.

Start the agent in the project root and paste the prompt below (not verified by the build):

```bash
copilot
```

```powershell
copilot
```

## Prompt

```
The owner of the triage agent in data/triage-agent/ says: "the new version feels smarter".
Turn that into 5 or 6 criteria. Each one must be checkable on one recorded run in data/transcripts/ by reading the file, give a pass or fail with no judgement call, and be marked "task" (about the final answer) or "step" (about the tool calls and routing). For each criterion name the exact field or step of the transcript you would look at. Do not write a criterion you cannot check from a transcript; list those separately with the reason.
Save the result to eval/criteria.md.
```

## Expected answer

Your wording will differ. A good answer has these properties, and usually these six criteria:

| # | Level | Criterion | Where to look |
|---|---|---|---|
| C1 | task | The final message is exactly one JSON object, with no code fence or prose | `final`, `final_is_json` |
| C2 | task | `label` is one of bug, enhancement, question, docs | `final` |
| C3 | step | `route` follows the policy for the label (bug to repro-check, question and docs to answer, enhancement to backlog) | `final`, `data/triage-agent/triage-agent.md` step 5 |
| C4 | step | No shell command was run | `steps[].tool` is never `Bash` |
| C5 | step | The skill the route requires was loaded (repro-check, answer-draft), and none for backlog | `steps[]` with tool `Skill` |
| C6 | task | `reply` is at most 800 characters | `final` |

Things that should land in the "cannot check" list: "the label is correct" (a transcript has no gold label until you write one, see the next section), "the reply is helpful", "it found the right related issues".

## Check yourself

- Every criterion names a field or a step. "The answer is good" does not.
- At least one criterion is about steps, not only the answer.
- No criterion uses words such as good, helpful, accurate or smart.
- If your list needs a gold label, it belongs in the "cannot check yet" list.
