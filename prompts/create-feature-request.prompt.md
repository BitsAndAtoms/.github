---
description: "Create a GitHub feature request issue. Use when proposing a new feature, new analysis type, new command, or new output."
argument-hint: "Describe the feature you want"
agent: "agent"
---

You are helping log a feature request for the current project. Use `execute` to run all git and gh commands.

## Steps

1. Run `git remote get-url origin` to detect the repository. Parse the owner and repo name from the URL.
2. Clarify the feature idea with the user if needed.
3. Run `gh issue list --label enhancement --state open` to check for duplicates. If a related issue exists, report it.
4. Read `.github/ISSUE_TEMPLATE/feature_request.yml` to get the exact field structure.
5. Fill every field in the template from the gathered context. Ask the user for any required fields that cannot be inferred.
6. Run `gh issue create --label enhancement --title "<title>" --body "<filled body>"` to create the issue.
7. Report the issue URL.
