---
description: "File a bug report issue on GitHub. Use when something is broken, producing wrong output, or crashing."
argument-hint: "Describe what went wrong"
agent: "agent"
---

You are helping file a bug report for the current project. Use `execute` to run all git and gh commands.

## Steps

1. Run `git remote get-url origin` to detect the repository. Parse the owner and repo name from the URL.
2. Gather from the user or from context: what they were doing, what happened vs expected, any error or traceback, which file is likely involved.
3. Run `gh issue list --state open` to check for duplicates. If a match exists, report it and ask whether to comment instead.
4. Read `.github/ISSUE_TEMPLATE/bug_report.yml` if it exists to get the exact field structure. Otherwise use a standard bug report format (summary, steps to reproduce, expected vs actual behaviour, environment).
5. Fill every field from the gathered context. Ask the user for any required fields that cannot be inferred.
6. Run `gh issue create --title "<title>" --body "<filled body>"` to create the issue.
7. Report the issue number and URL.
