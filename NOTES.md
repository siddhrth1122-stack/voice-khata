# Concepts

- **Permission modes** (switch with Shift+Tab): Default (asks before edits/commands), Accept edits, Plan mode (read-only, plan first), Auto mode (acts without asking, still checks before risky steps).
- **/help** shows available commands. **/model** switches the AI model. **/clear** starts a fresh conversation (files are not touched).
- **Commit**: a saved snapshot of the project's changes in git. **Push**: uploading commits to GitHub.
- **Git hook**: a script git runs automatically at certain moments (e.g. right after a commit). Hooks live in `.git/hooks/` and are not uploaded to GitHub.

# Project setup

- Project folder: `voice khata`
- GitHub repo (private): https://github.com/siddhrth1122-stack/voice-khata, branch `main`
- Auto-push: `.git/hooks/post-commit` pushes every new commit to GitHub. If a push fails, the commit stays local; run `git push` later.

# Working agreements

- Claude adds every action it takes to the Log below, without waiting to be asked. This file is the context for new chat sessions.
- Claude only commits when asked.

# Log

- **2026-11-14** Explained Claude Code basics (modes, /help, /model, /clear). Created `NOTES.md`.
- **2026-11-14** Confirmed folder is linked to the existing private repo `voice-khata`. Added the `post-commit` hook to auto-push every commit.
- **2026-11-14** Filled in NOTES.md with concepts, project setup, and this log. Committed it (first test of auto-push).
