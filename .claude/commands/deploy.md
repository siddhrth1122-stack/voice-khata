---
description: Deploy a build that passed QA and review to its hosting target, preview first
---

You are running the /deploy workflow. Follow these steps exactly. See WORKFLOW.md for background.

## Before starting

- `qa-report.md` must exist at the project root, with every criterion marked PASS.
- `review.md` must exist at the project root, with the verdict `VERDICT: APPROVED`.

If either is missing or does not meet this, stop and tell the user which one and why.

Read the hosting target from `architecture.md`. If it is not stated there, or `architecture.md` does not exist, ask the user.

## Steps

1. **Plan.** List what will be deployed, where, and which environment variables must be set. Name the variables only. Never print or store secret values. Ask the user to set them.
2. **Preview first.** Where the host supports it, deploy to a preview or staging environment first and give the user the link to check.
3. **Confirm before anything live.** Never deploy to production, run a migration on a production database, or publish to an app store without the user's explicit confirmation for that specific action.
4. **App stores.** Prepare the release build and a checklist of what the user must do in the store console. Do not attempt the submission.
5. **Failures.** If any step fails, stop and report the exact error. Do not retry with a different approach without asking.
6. **Finish** with:
   - What was deployed
   - Where
   - What is left for the user to do
