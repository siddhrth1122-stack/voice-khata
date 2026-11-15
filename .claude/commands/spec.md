---
description: Turn a feature idea into an approved spec.md through the product-manager subagent
argument-hint: <describe the feature you want>
---

You are running the /spec workflow. Follow these steps exactly. See WORKFLOW.md for background.

The user's prompt is:

<user_prompt>
$ARGUMENTS
</user_prompt>

If the prompt above is empty, ask the user to describe the feature and stop.

## Steps

1. **CHECK.** Call the `product-manager` subagent in CHECK mode. Pass it the user's prompt unchanged, word for word.
2. **Questions.** If it returns questions, show them to the user as a numbered list exactly as written, and wait for the answers.
   - Do not answer any question yourself.
   - Do not skip any question.
   - If the user answers "not sure" or "you decide" (or similar) to a question, do not decide it. Record that item so it goes under "Open questions" in the spec.
3. **Repeat.** Call CHECK mode again with the original prompt plus all questions and answers so far. Repeat steps 2–3 until the product manager returns `NO GAPS`.
4. **WRITE.** Call the `product-manager` in WRITE mode, passing the original prompt, all questions and answers, the list of "not sure / you decide" items, and the fact that CHECK returned `NO GAPS`. It creates `spec.md`.
5. **Approval.** Show the user the Assumptions, Acceptance criteria and Open questions from `spec.md`. Ask them to approve it or ask for changes.
   - If they ask for changes, pass the changes to the product manager in WRITE mode to update `spec.md` (run CHECK first if the changes add new behaviour), then ask for approval again.
   - When they approve, add the line `Status: APPROVED` at the very top of `spec.md`. This is what /build looks for.
6. Do not start any build. Tell the user to run `/build` when they are ready.

## Model fallback

If a `product-manager` call fails because Fable is unavailable or its usage limit or credits are exhausted:

- Rerun the same call with the model overridden to `opus`.
- Tell the user in one line: "The product manager is running on Opus for this session."
- Keep using `opus` for all remaining product-manager calls in this session.

Do not fall back for any other kind of error. Never fall back to a model below Opus for the product manager.

## Shared rules (apply to you too)

1. Assumptions first. Before doing any work, list every assumption you are making. If an assumption is not backed by spec.md or the instructions you were given, do not proceed on it.
2. Stop and ask. If anything is unclear, ambiguous or contradictory, stop and return your question to the main session instead of guessing. Never fill a gap with a guess.
3. Do the minimum. Do exactly what was asked and nothing else. No extra features, no refactoring, no renaming, no cleanup, no "improvements" that were not requested.
4. Touch only the lines needed. Add, change or remove only the lines the task requires. Leave all other code, comments and formatting as they are. If you notice an unrelated problem, report it, do not fix it.
5. Reproduce before fixing. When you see a bug, do not fix it immediately. First reproduce it and record the exact steps or a failing test that shows it. Only then is it fixed, and the same reproduction is re-run to confirm the fix.
