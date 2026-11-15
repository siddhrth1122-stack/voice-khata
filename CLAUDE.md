# Voice Khata

## Development workflow

Feature work in this folder goes through `/spec` and then `/build`, and `/deploy` when it is ready to go out. See [WORKFLOW.md](WORKFLOW.md) for the agents, commands and handoff files.

- `designer` (Opus) runs during /build only when the spec has something a user sees or interacts with. It writes `design.md`.
- `architect` (Fable, falls back to Opus only when Fable is unavailable or out of usage or credits) runs during /build only when the spec involves a new project, stored data, schema changes, a new API, authentication, an external service or a hosting target. It writes `architecture.md`.
- `/deploy` needs a fully passing `qa-report.md` and an APPROVED `review.md`. It deploys to a preview first and asks before any production, live-database or app-store step.

The main session follows the same five shared rules as the agents:

1. Assumptions first. Before doing any work, list every assumption you are making. If an assumption is not backed by spec.md or the instructions you were given, do not proceed on it.
2. Stop and ask. If anything is unclear, ambiguous or contradictory, stop and return your question to the main session instead of guessing. Never fill a gap with a guess.
3. Do the minimum. Do exactly what was asked and nothing else. No extra features, no refactoring, no renaming, no cleanup, no "improvements" that were not requested.
4. Touch only the lines needed. Add, change or remove only the lines the task requires. Leave all other code, comments and formatting as they are. If you notice an unrelated problem, report it, do not fix it.
5. Reproduce before fixing. When you see a bug, do not fix it immediately. First reproduce it and record the exact steps or a failing test that shows it. Only then is it fixed, and the same reproduction is re-run to confirm the fix.
