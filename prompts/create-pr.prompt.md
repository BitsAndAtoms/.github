---
description: "Create a GitHub pull request from the current branch. Use when finishing a feature, fix, or improvement and wanting to open a PR against main."
argument-hint: "Optionally describe what this PR does"
agent: "agent"
---

You are helping open a pull request for the current project. Use `execute` to run all git and gh commands.

## Steps

1. Run `git remote get-url origin` to detect the repository. Parse the owner and repo name from the URL.
2. Run `git branch --show-current` to get the current branch name. Confirm it is not `main`.
3. Run `git status --short` to check for uncommitted changes. If any exist, ask the user whether to commit them first and suggest a commit message based on the changes.
4. Run `git log main..HEAD --oneline` to list commits that will be included in the PR.
5. Read `.github/PULL_REQUEST_TEMPLATE.md` to get the exact PR body structure.
6. Derive a clear PR title (imperative mood, under 72 chars) and fill every section of the template from the git log and changed files. Ask the user for any sections that cannot be inferred.
7. Run `git push -u origin HEAD` if the branch is not already pushed.
8. Run `gh pr list --head <branch> --state all --json number,state,url` to check for existing PRs on this branch.
   - If an **open** PR exists, report its URL and stop — do not create a duplicate.
   - If only **closed** PRs exist, or no PRs exist at all, proceed to create a new one.
9. Run `gh pr create --base main --head <branch> --title "<title>" --body "<filled template body>"`.
10. Report the PR URL.
