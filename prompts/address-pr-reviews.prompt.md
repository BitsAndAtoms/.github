---
description: "Fetch all reviewer comments and requested changes on the current branch's PR, apply them to the codebase, commit, and push to update the PR. Use when acting on code review feedback."
argument-hint: "Optionally provide a PR number; defaults to the open PR for the current branch"
agent: "agent"
---

You are helping address reviewer feedback on a pull request. Use `execute` to run all git and gh commands. Use `read` and `edit` to inspect and modify source files.

## Steps

### 1 — Find the PR

If a PR number was supplied as an argument, use it. Otherwise run `git branch --show-current`, then run:
```
gh pr list --head <branch> --state open --json number,url
```
If no open PR is found, tell the user and stop.

### 2 — Fetch review comments

Run the following three commands and hold all results in memory:

```
gh pr view <number> --json number,title,url,reviewDecision,reviews
gh api repos/{owner}/{repo}/pulls/<number>/comments --paginate
gh api repos/{owner}/{repo}/issues/<number>/comments --paginate
```

Parse `owner` and `repo` from `git remote get-url origin`.

### 3 — Build a change list and make recommendations

From the combined output, extract every actionable item:
- Inline thread comments where a reviewer explicitly requests a code change (look for imperative language: "change", "rename", "remove", "add", "fix", "extract", etc.).
- Top-level conversation comments with similar imperative language.
- Any review with state `CHANGES_REQUESTED`.

Group them by file. For items that cannot be mapped to a specific file, treat them as general/architectural notes.

For each item, evaluate it and assign a recommendation:

- **ACCEPT** — Clear correctness bug, broken test, unreachable code, security/portability issue, or doc/instruction mismatch that will confuse contributors. Low risk to apply.
- **DISCUSS** — Stylistic preference, subjective refactor, architectural opinion, or change that could have unintended side-effects. Worth reviewing with the user before acting.
- **SKIP** — PR-process or meta feedback only (e.g. "split this PR"), no code change needed or appropriate.

Present the full annotated list before touching any files:

```
CHANGE LIST
──────────────────────────────────────────────────────
[1] FILE: path/to/file.py  line <n>  ✅ ACCEPT
    Reviewer: <login>
    Request: <verbatim comment body>
    Reason: <one sentence why this is recommended>

[2] FILE: path/to/other.py  line <n>  💬 DISCUSS
    Reviewer: <login>
    Request: <verbatim comment body>
    Reason: <one sentence why this needs discussion>

[GENERAL]
[3] Reviewer: <login>  ⏭ SKIP
    Request: <verbatim comment body>
    Reason: <one sentence why no code change is needed>
──────────────────────────────────────────────────────
Recommended: apply [<comma-separated ACCEPT numbers>]
```

Ask the user: "Apply which changes? [all / recommended / <numbers> / none]"

- **all** — apply every item regardless of recommendation
- **recommended** — apply only ACCEPT items
- **\<numbers\>** — apply a custom comma-separated subset (e.g. `1,3,5`)
- **none** — stop without making changes

### 4 — Apply changes

For each item the user wants applied:

1. Read the relevant file using `read`.
2. Check whether the relevant instruction file under `.github/instructions/` applies (e.g. if editing `event_analysis.py`, read `.github/instructions/event-analyzer.instructions.md` first). Follow its conventions strictly.
3. Apply the minimum change needed to satisfy the reviewer's request. Do not refactor beyond what was asked.
4. If a change is ambiguous, ask a single clarifying question before editing.
5. After all edits are done, run `git diff --stat` so the user can see what changed.

### 5 — Commit and push

1. Run `git status --short` to confirm staged/unstaged changes.
2. Suggest a commit message in the format:
   ```
   review: address PR #<number> feedback (<reviewer-login>[, <reviewer-login>])
   ```
   Ask the user to confirm or amend it.
3. Run:
   ```
   git add -A
   git commit -m "<confirmed message>"
   git push
   ```

### 6 — Post a reply comment (optional)

Ask the user: "Post a summary comment on the PR noting which items were addressed? [y/n]"

If yes, build a comment body listing each addressed item and run:
```
gh pr comment <number> --body "<summary>"
```

### 7 — Report

Print the PR URL and the final `reviewDecision` from `gh pr view <number> --json reviewDecision`.
