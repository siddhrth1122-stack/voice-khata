# Development Workflow

Every new feature in this folder goes through two commands: **/spec** (decide exactly what to build) and then **/build** (design it, build it, test it, review it). **/deploy** puts a finished build online. Six helper agents (subagents) do the work. Each one has one job and hands its result to the next through a file.

## The six agents

| Agent | Model | Job | Writes only |
| :- | :- | :- | :- |
| product-manager | Fable (falls back to Opus) | Asks you questions until nothing is unclear, then writes the spec | `spec.md` |
| developer | Opus | Plans the change, then builds it and fixes reported issues | `assumptions.md` and the code |
| qa-tester | Sonnet | Runs the tests and checks each acceptance criterion: PASS, FAIL or UNVERIFIABLE | `qa-report.md` |
| code-reviewer | Opus | Independent review of the changes against the spec | `review.md` |
| designer | Opus | Describes the user flow, screens and each screen state, reusing existing styles. Runs only when the spec has something a user sees or interacts with | `design.md` |
| architect | Fable (falls back to Opus) | Describes the tech stack, data, API, login and hosting, and marks hard-to-reverse choices IRREVERSIBLE. Runs only when the spec involves a new project, stored data, schema changes, a new API, authentication, an external service or a hosting target | `architecture.md` |

The agent files live in `.claude/agents/`.

## The five shared rules

Every agent, and the main chat, follows these:

1. **Assumptions first.** List every assumption before working. Don't act on one that isn't backed by the spec or the instructions.
2. **Stop and ask.** If anything is unclear, ask instead of guessing.
3. **Do the minimum.** Only what was asked. No extra features, renaming, cleanup or "improvements".
4. **Touch only the lines needed.** Leave everything else as it is. Report unrelated problems instead of fixing them.
5. **Reproduce before fixing.** For a bug, first show it happening (exact steps or a failing test), then fix it, then re-run the same check.

## The three commands

### /spec your feature idea

1. The product manager reads your idea (CHECK mode) and returns numbered questions about anything missing.
2. You answer. It checks again. This repeats until it says **NO GAPS**.
   - If you answer "not sure" or "you decide", it isn't guessed. It's listed under **Open questions** in the spec.
3. It writes `spec.md` (WRITE mode): problem, user, scope, non-goals, assumptions, open questions, and numbered acceptance criteria (AC-1, AC-2, ...). Each criterion is a clear pass/fail check.
4. You see the assumptions, criteria and open questions, then approve or ask for changes. On approval, `Status: APPROVED` is added to the top of `spec.md`.

Nothing gets built during /spec.

### /build

1. **Design agents:** the chat reads the spec and tells you in one line whether the designer, the architect, both or neither will run, and why.
2. **Design:** if needed, the designer writes `design.md`. Then, if needed, the architect writes `architecture.md` (after the designer, so it can read `design.md`).
3. **Design approval:** you see both files together, with every IRREVERSIBLE decision and every question listed first. **You approve or correct them.** This is one stop, skipped if neither agent ran.
4. **Plan:** the developer writes `assumptions.md` (its assumptions, which files it will change, any questions). **You approve or correct it.**
5. **Build:** the developer makes the change and writes tests for the acceptance criteria. It follows `design.md` and `architecture.md` exactly. If it thinks one is wrong, it stops and tells you instead of working around it.
6. **QA:** the tester checks every criterion, plus the screens and states in `design.md` and the data and API in `architecture.md`. If anything FAILs, it goes back to the developer. Up to 3 rounds.
7. **Review:** once QA passes, the reviewer checks the changes. Any deviation from the design files, any secret in the code, a missing login or permission check, unrestricted database access, or a migration that can't be undone is BLOCKING. If it says CHANGES REQUIRED, the developer fixes them, then QA and review run again. Up to 2 rounds.
8. If any agent has a question, the chat stops and asks you. Nobody answers for you.
9. If a round limit is hit, it stops and tells you exactly what's still failing.
10. At the end you get a summary: what was built, the QA result for each criterion, the review verdict, minor (non-blocking) issues, criteria that couldn't be checked, and unrelated problems to look at.

/build won't start without an approved `spec.md`.

### /deploy

1. Only runs when `qa-report.md` has every criterion PASS and `review.md` says APPROVED. Otherwise it stops.
2. It reads where to host from `architecture.md`, or asks you.
3. It lists what will be deployed, where, and which environment variables (settings like keys or passwords) you need to set. It names them only and never shows or saves their values.
4. It deploys to a preview first where the host supports it and gives you the link.
5. Going live, running a migration on the live database, or publishing to an app store each needs your explicit yes for that specific step.
6. For app stores it prepares the release build and a checklist for you. You do the submission yourself.
7. If a step fails, it stops and shows the exact error. It won't try another way without asking.
8. It ends with what was deployed, where, and what's left for you to do.

## Product manager fallback to Opus

The product manager normally runs on Fable. If Fable is unavailable or its usage limit or credits run out, the same step reruns on Opus. You get a one-line notice, and Opus is used for the rest of that session. This only happens for those reasons, and it never drops below Opus.

## Architect fallback to Opus

The architect also normally runs on Fable. If a call fails because Fable is unavailable or its usage limit or credits run out, the same call reruns on Opus and you get a one-line notice. This only happens for those reasons, and it never drops below Opus.

## Handoff files

All at the project root:

| File | Written by | Read by |
| :- | :- | :- |
| `spec.md` | product-manager | everyone |
| `design.md` | designer | you, architect, developer, qa-tester, code-reviewer |
| `architecture.md` | architect | you, developer, qa-tester, code-reviewer, /deploy |
| `assumptions.md` | developer (PLAN) | you, developer (BUILD) |
| `qa-report.md` | qa-tester | developer, you |
| `review.md` | code-reviewer | developer, you |

Each agent gets only the file paths it needs, never the developer's reasoning. That keeps QA and review independent.

## Worked example

Say you want Voice Khata to remember the last thing you said after the page reloads.

1. You type:
   `/spec Keep the transcribed text after the page is reloaded, so I don't lose it`
2. The product manager asks, for example:
   1. Should the text be kept per language (English and Hindi separately) or as one text?
   2. Should the Clear button also delete the saved text?
   3. Is there a limit on how much text to keep?
3. You answer: "1. One text. 2. Yes. 3. You decide." It checks again and says NO GAPS. Question 3 goes under Open questions.
4. `spec.md` is written with criteria such as:
   - AC-1: After speaking, reloading the page shows the same text in the tile.
   - AC-2: After pressing Clear and reloading, the tile is empty.
5. You reply "approved".
6. You type `/build`. The developer's plan says it will change only `index.html`. You approve.
7. The developer builds it, QA checks AC-1 and AC-2, the reviewer reviews, and you get the summary.

## Quick start

1. Start a new Claude Code session in this folder (so the agents and commands load).
2. Type `/spec` followed by your feature idea in plain words.
3. Answer the questions, then approve the spec.
4. Type `/build`, approve `design.md` and `architecture.md` if they are shown, approve the plan, and wait for the summary.
