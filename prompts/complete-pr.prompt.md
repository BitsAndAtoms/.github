---
description: "Check PR readiness and merge it into main, then clean up the local branch. Use when a PR has been reviewed and is ready to land."
argument-hint: "Optionally provide a PR number; defaults to the open PR for the current branch"
agent: "agent"
---

You are helping merge a pull request and clean up the local branch. Use `execute` to run all git and gh commands.

## Steps

### 1 — Find the PR

If a PR number was supplied as an argument, use it. Otherwise run `git branch --show-current`, then run:
```
gh pr list --head <branch> --state open --json number,url,headRefName
```
If no open PR is found, tell the user and stop.

### 2 — Check readiness

Run:
```
gh pr view <number> --json number,title,url,state,reviewDecision,mergeable,statusCheckRollup,reviews
```

Evaluate and report the following:

| Check | Status |
|-------|--------|
| Review decision | `APPROVED` / `REVIEW_REQUIRED` / `CHANGES_REQUESTED` / none |
| Merge conflicts | `MERGEABLE` / `CONFLICTING` / `UNKNOWN` |
| CI checks | Pass / Fail / Pending / None |

If any blockers exist (not approved, merge conflicts, failing CI), list them clearly and ask:
> "Blockers found (listed above). Merge anyway, or stop? [merge / stop]"

If everything is green, skip the blocker question and proceed.

### 3 — Choose merge strategy

Ask:
> "Merge strategy? [squash (default) / merge / rebase]"

- **squash** — squashes all commits into one, keeps history clean (recommended for feature branches)
- **merge** — creates a merge commit, preserves full branch history
- **rebase** — replays commits onto main, linear history

### 4 — Merge

Run the appropriate command based on the chosen strategy:

```
# squash (default)
gh pr merge <number> --squash --delete-branch

# merge commit
gh pr merge <number> --merge --delete-branch

# rebase
gh pr merge <number> --rebase --delete-branch
```

`--delete-branch` removes the remote branch automatically after merge.

### 5 — Confirm merge succeeded before any cleanup

Run:
```
gh pr view <number> --json state,mergedAt
```

- If `state` is **`MERGED`**, proceed to step 6.
- If `state` is anything else (e.g. still `OPEN`), stop immediately. Do NOT touch the local branch. Tell the user the merge did not complete and report the current PR state.

### 6 — Clean up local branch (only on confirmed MERGED)

Run:
```
git checkout main
git pull origin main
git branch -d <branch>
```

If `git branch -d` fails because the branch is not fully merged according to git's local tracking (common after squash merge), run `git branch -D <branch>` instead and note why.

### 7 — Report

Print:
- The merge commit SHA from `git log main --oneline -1`
- Confirmation that the remote branch was deleted
- Confirmation that the local branch was deleted
- The PR URL with its `MERGED` state
