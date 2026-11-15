# Concepts

- **Permission modes** (switch with Shift+Tab): Default (asks before edits/commands), Accept edits, Plan mode (read-only, plan first), Auto mode (acts without asking, still checks before risky steps).
- **/help** shows available commands. **/model** switches the AI model. **/clear** starts a fresh conversation (files are not touched).
- **Commit**: a saved snapshot of the project's changes in git. **Push**: uploading commits to GitHub.
- **Git hook**: a script git runs automatically at certain moments (e.g. right after a commit). Hooks live in `.git/hooks/` and are not uploaded to GitHub.

# Project setup

- Project folder: `voice khata`
- GitHub repo (private): https://github.com/siddhrth1122-stack/voice-khata, branch `main`
- Auto-push: `.git/hooks/post-commit` pushes every new commit to GitHub. If a push fails, the commit stays local; run `git push` later.

# The app

- `index.html`: one-page web app. Big mic button; speech becomes text in the same tile. English (`en-IN`) / हिंदी (`hi-IN`) toggle; Copy and Clear buttons.
- Uses the browser's Web Speech API (`SpeechRecognition`), so it works in Chrome and Edge only (not Firefox). It needs HTTPS and internet, because Chrome sends the audio to Google to turn it into text.
- Hosted on GitHub Pages from `main` (root). Repo made **public** for this (free plan). Live link: https://siddhrth1122-stack.github.io/voice-khata/
- `.gitignore` keeps `.claude/settings.local.json` out of git.

# Working agreements

- Claude logs only necessary info (decisions, setup, files/features added, open to-dos) without being asked. Skip trivial actions. Keep entries short. This file is the context for new chat sessions.
- Claude only commits when asked.
- Use subagents for parallel help, not agent teams (teams are turned off here).

# Log

- **2026-11-14** Explained Claude Code basics (modes, /help, /model, /clear). Created `NOTES.md`.
- **2026-11-14** Confirmed folder is linked to the existing private repo `voice-khata`. Added the `post-commit` hook to auto-push every commit.
- **2026-11-14** Filled in NOTES.md with concepts, project setup, and this log. Committed it (first test of auto-push).
- **2026-11-14** Ran /doctor. Disabled 12 account plugins for this folder only in `.claude/settings.local.json` (they're used in other chat windows; keep this file out of commits). Set auto mode as default in `~/.claude/settings.json`. To-do: disable unused connectors here via `/mcp`.
- **2026-11-14** Built the Voice Khata mic app (`index.html`), added `.gitignore`, made the repo public and turned on GitHub Pages.
- **2026-11-15** Decided to use subagents, not agent teams. Removed the agent teams setting from `.claude/settings.local.json` and deleted the `docs/` folder about teams.
- **2026-11-15** Set up the /spec → /build workflow: 4 subagents in `.claude/agents/` (product-manager on Fable with Opus fallback, developer, qa-tester, code-reviewer), commands in `.claude/commands/`, `WORKFLOW.md`, `CLAUDE.md`. /spec marks an approved spec with `Status: APPROVED` on line 1; /build requires it.
- **2026-11-15** Installed Node.js v26.13.1 (npm 11.20.0) with Homebrew at `/opt/homebrew/bin`. That folder is not on the terminal PATH yet, so `node` only runs by full path. User connected the Vercel connector; its tools did not appear in this session (needs a Claude Code restart).
- **2026-11-15** Checked readiness for a full app. Vercel connector tools now show up in this session. Still open: workflow files not committed, `node` not on PATH, no app idea/spec yet.
- **2026-11-15** Added designer (Opus) and architect (Fable → Opus fallback) agents, design step + approval pause in /build, new /deploy command. Updated developer, qa-tester, code-reviewer, WORKFLOW.md, CLAUDE.md.
