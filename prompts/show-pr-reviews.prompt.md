---
description: "Show all reviewer comments and pending reviews on the current branch's pull request. Use when checking PR feedback, review status, or outstanding threads."
argument-hint: "Optionally provide a PR number; defaults to the PR for the current branch"
agent: "agent"
---

You are helping inspect the review status of a pull request. Use `execute` to run all gh commands.

## Steps

1. If a PR number was supplied as an argument, use it. Otherwise run `git branch --show-current` to get the current branch, then run `gh pr list --head <branch> --state open --json number,url` to find the open PR. If no open PR is found, tell the user and stop.

2. Run `gh pr view <number> --json number,title,url,state,reviewDecision,reviews,comments` to get the full PR metadata.

3. Parse and display a **Review Summary** table with columns:
   - Reviewer (login)
   - State (`APPROVED` / `CHANGES_REQUESTED` / `COMMENTED` / `PENDING` / `DISMISSED`)
   - Submitted at (omit if PENDING)

4. Run `gh api repos/{owner}/{repo}/pulls/<number>/comments --paginate` to fetch all inline review thread comments. Parse `owner` and `repo` from `git remote get-url origin`.

5. Display each inline comment as a block:
   ```
   [FILE path/to/file.py  line <line>]
   Author: <login>  |  <created_at>
   <body>
   ```
   Group comments that share the same `pull_request_review_id` (same review thread) together.

6. Run `gh api repos/{owner}/{repo}/issues/<number>/comments --paginate` to fetch top-level PR conversation comments (non-inline).

7. Display each conversation comment:
   ```
   [COMMENT  <created_at>]
   Author: <login>
   <body>
   ```

8. Print a final **Pending Actions** summary:
   - List any reviewers whose state is `PENDING` or `CHANGES_REQUESTED`.
   - Count unresolved inline threads (threads where the last reply is not from the PR author).
   - If `reviewDecision` is `APPROVED`, state the PR is approved and ready to merge.
