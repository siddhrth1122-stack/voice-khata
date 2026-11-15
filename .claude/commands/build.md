---
description: Build an approved spec.md through the developer, qa-tester and code-reviewer subagents
---

You are running the /build workflow. Follow these steps exactly. See WORKFLOW.md for background.

## Before starting

Check that `spec.md` exists at the project root and that its first line is `Status: APPROVED`. If it is missing or not approved, stop and tell the user to run `/spec` first.

## Pipeline

1. **Decide on design agents.** Read `spec.md` and decide which design agents are needed:
   - `designer`: needed if the spec includes any user-facing screen or flow.
   - `architect`: needed if the spec involves a new project, stored data, schema changes, a new API, authentication, an external service or a hosting target.

   Tell the user in one line which will run and why. If neither is needed, go straight to step 5 (Plan).
2. **Design.** If needed, call the `designer` subagent, passing `spec.md`. It writes `design.md`.
3. **Architecture.** If needed, call the `architect` subagent, passing `spec.md` and `design.md` if it exists. It writes `architecture.md`. It runs after the designer so it can read `design.md`.
4. **Design approval.** Show the user `design.md` and `architecture.md` together, with every IRREVERSIBLE decision and every question listed first. Wait for approval or corrections. This is one pause, not two. If they correct either file, send the corrections to the agent that wrote it and show the updated files again.
5. **Plan.** Call the `developer` subagent in PLAN mode, passing `spec.md`, plus `design.md` and `architecture.md` where they exist. Tell it to follow `design.md` and `architecture.md` and not deviate from them; if it believes one of them is wrong, it must stop and return the problem instead of working around it. It writes `assumptions.md`. Show `assumptions.md` to the user and wait for approval or corrections. If they correct it, send the corrections to the developer in PLAN mode and show the updated file again. This is the only planned pause besides the design approval in step 4.
6. **Build.** Call the `developer` subagent in BUILD mode, passing `spec.md` and `assumptions.md`, plus `design.md` and `architecture.md` where they exist, with the same instruction to follow them and not deviate.
7. **QA.** Call the `qa-tester` subagent, passing `spec.md`. It writes `qa-report.md`.
   - If any criterion is FAIL, call the `developer` in BUILD mode with `spec.md`, `assumptions.md` and `qa-report.md`, plus `design.md` and `architecture.md` where they exist, with the same instruction, then run QA again.
   - Maximum 3 QA rounds.
8. **Review.** Only when QA fully passes (no FAIL), call the `code-reviewer` subagent, passing `spec.md`.
   - If the verdict is CHANGES REQUIRED, call the `developer` in BUILD mode with `spec.md`, `assumptions.md` and `review.md`, plus `design.md` and `architecture.md` where they exist, with the same instruction, then re-run QA and then the review.
   - Maximum 2 review rounds.
9. **Questions.** If any subagent stops with a question, pause and ask the user that exact question, then resume that subagent's step with the answer. Never answer on the user's behalf.
10. **Limits.** If a round limit is reached, stop and tell the user exactly what is still failing, instead of continuing.
11. **Summary.** Finish with a short summary:
   - What was built
   - QA result per criterion
   - Review verdict
   - Any NON-BLOCKING issues
   - UNVERIFIABLE criteria
   - Unrelated problems the agents noticed, for the user to check

Pass each subagent only the file paths it needs, never the developer's reasoning or summaries.

## Model fallback

If an `architect` call fails because Fable is unavailable or its usage limit or credits are exhausted:

- Rerun the same call with the model overridden to `opus`.
- Tell the user in one line: "The architect is running on Opus."

Do not fall back for any other kind of error. Never fall back to a model below Opus for the architect.

## Shared rules (apply to you too)

1. Assumptions first. Before doing any work, list every assumption you are making. If an assumption is not backed by spec.md or the instructions you were given, do not proceed on it.
2. Stop and ask. If anything is unclear, ambiguous or contradictory, stop and return your question to the main session instead of guessing. Never fill a gap with a guess.
3. Do the minimum. Do exactly what was asked and nothing else. No extra features, no refactoring, no renaming, no cleanup, no "improvements" that were not requested.
4. Touch only the lines needed. Add, change or remove only the lines the task requires. Leave all other code, comments and formatting as they are. If you notice an unrelated problem, report it, do not fix it.
5. Reproduce before fixing. When you see a bug, do not fix it immediately. First reproduce it and record the exact steps or a failing test that shows it. Only then is it fixed, and the same reproduction is re-run to confirm the fix.
