---
name: failure-modes
description: Catalogue the failure modes of the triage agent from its recorded runs in data/transcripts/. Use when asked to find, list, count or group what goes wrong in the transcripts, or to turn a quality goal into failure modes.
version: 1.0.0
---

# Failure modes

Build a failure-mode catalogue from recorded runs. Work only from the files in `data/transcripts/`; the agent's own rules are in `data/triage-agent/`.

## Steps

1. Read `data/triage-agent/triage-agent.md` first, so you know what the agent was told to do.
2. Take the runs the user names, or all of `data/transcripts/` if none are named. Each file has `steps` (tool, input, result) and `final` (the last message).
3. For each run, look for these observable signals only:
   - the final message is not one bare JSON object (code fence, prose, empty)
   - `label` is not one of bug, enhancement, question, docs
   - `route` does not follow the policy for the label, or is not backlog, answer or repro-check
   - a tool was used that the agent was told not to use (a shell command)
   - the skill the route requires was not loaded
   - `reply` is longer than 800 characters
4. Count by listing run ids. Do not state a count you did not get from listing those ids. If you used a command to count, show it.
5. Report a table: failure mode, run ids, count, one quoted line of evidence from a transcript.
6. A run with none of the signals is reported as "no failure found". Anything odd that fits no signal goes in a separate "unclassified" list with the run id and why; do not force it into a mode.

## Rules

- A failure mode is a pattern with a visible signal in the file, not a guess about why the agent did it.
- Say which part of a failure is the agent and which could be the environment (for example a tool returning "No files found").
- Do not edit files in `data/`.
