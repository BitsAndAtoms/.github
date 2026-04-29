---
description: "Start a new feature branch. Use when beginning work on a new feature, fix, or improvement — creates the branch and optionally links a GitHub issue."
argument-hint: "Describe what you're building"
agent: "agent"
---

You are helping start a new feature branch for the current project. Use `execute` to run all git and gh commands.

## Steps

1. Run `git remote get-url origin` to detect the repository. Parse the owner and repo name from the URL.
2. Ask (or infer) what is being built. Suggest a branch name following common naming patterns:
   - Features: `feature/<short-description>` (e.g. `feature/export-to-csv`)
   - Fixes: `fix/<short-description>` (e.g. `fix/crash-on-empty-input`)
   - Chores: `chore/<short-description>` (e.g. `chore/update-deps`)
3. Confirm the user wants to branch off `main` (default) or another base.
4. Run `git checkout main && git pull origin main && git checkout -b <branch-name>`.
5. If the work relates to an existing GitHub issue, note the issue number so commit messages can reference it (e.g. `fix crash on empty input (#12)`).
6. If the project has a `.github/instructions/` folder, read the file list and mention any instruction files relevant to this work so the user is aware of coding conventions to follow.
