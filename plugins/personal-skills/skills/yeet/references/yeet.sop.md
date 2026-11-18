# Yeet Git Workflow

## Overview

This SOP handles `yeet` shorthand and natural-language git/GitHub requests by selecting the requested mode, gathering git context, safely switching or creating branches, committing and pushing authorized work, and optionally opening a pull request.

## Parameters

- **user_request** (required): The current user message that triggered the git/GitHub workflow.
- **workspace_path** (optional, default: "current working directory"): Repository path where git commands should run.
- **requested_branch** (optional): Branch name requested for checkout, sync, or branch creation.
- **pr_requested** (optional, default: "false"): Whether the current request includes PR creation.

**Constraints for parameter acquisition:**

- If all required parameters are already provided, You MUST proceed to the Steps.
- If any required parameters are missing, You MUST ask for them before proceeding.
- When asking for parameters, You MUST request all parameters in a single prompt.
- When asking for parameters, You MUST use the exact parameter names as defined.

## Steps

### 1. Decide The Mode

Map the current request to one or more actions:

- `checkout-sync`: switch to a named branch and fast-forward it.
- `branch-create`: create a new conventional branch.
- `commit-push`: stage, commit, and push current work.
- `pull-request`: open a PR for the current branch.

**Constraints:**

- You MUST treat plain `yeet` as `commit-push`.
- You MUST treat `yeet br` as `branch-create` plus `commit-push`.
- You MUST treat `yeet pr` as `commit-push` plus `pull-request`.
- You MUST treat `yeet br pr` as `branch-create` plus `commit-push` plus `pull-request`.
- You MUST map direct requests such as `checkout dev`, `switch to main`, or `sync release/next` to `checkout-sync`.
- You MUST only map to `commit-push` when the current user message explicitly requests commit or push behavior.
- You MUST NOT infer commit or push authorization from prior turns because authorization is per request.

### 2. Load Git Conventions

Load the bundled Git Conventions skill before deriving branch names, commit messages, PR titles, or labels.

**Constraints:**

- You MUST read the bundled [Git Conventions skill](../../git-conventions/SKILL.md).
- You MUST follow its commit message, branch naming, PR title, base branch, and label rules.

### 3. Gather Git Context

Collect repository context before changing branch state, committing, pushing, or opening a PR.

**Constraints:**

- You MUST inspect `git status --short`.
- You MUST inspect `git diff --stat`.
- You MUST inspect `git log --oneline -5`.
- You MUST inspect `git branch --show-current`.
- You SHOULD inspect `git rev-parse --abbrev-ref --symbolic-full-name '@{u}'` when an upstream is needed.
- You MUST quote `@{u}` in PowerShell because unquoted `@{u}` is parsed as a hash literal.
- For checkout or sync requests, You MUST inspect the working tree but MUST NOT block checkout preemptively; let Git report overwrite safety errors.

### 4. Checkout And Fast-Forward When Requested

Switch branches and fast-forward only when the selected mode includes `checkout-sync`.

**Constraints:**

- You MUST run `git checkout <branch>` for the requested branch.
- If checkout fails, You MUST report the Git error and stop.
- After checkout, You MUST fast-forward the branch from its upstream when possible.
- You MUST use `git pull --ff-only`.
- If no upstream is configured, You SHOULD use `git pull --ff-only origin <branch>`.
- You MUST NOT use plain `git pull` because it may create a merge commit.
- If fast-forward fails, You MUST report the blocker and stop instead of creating a merge commit.

### 5. Create A Branch When Requested

Create a new conventional branch only when the selected mode includes `branch-create`.

**Constraints:**

- You MUST derive the branch name from the change using Git Conventions.
- You MUST use the pattern `<type>/<kebab-case-name>`.
- You MUST run `git checkout -b <branch>`.
- You MUST tell the user which branch was created and from which previous branch.
- If the user asked for a new branch and current work should continue there, You MUST stay on the new branch.

### 6. Commit And Push When Authorized

Stage, commit, and push only when the selected mode includes `commit-push`.

**Constraints:**

- You MUST stage all changes with `git add -A`.
- You MUST derive a conventional commit message from the staged diff.
- You MUST run `git commit -m "<message>"`.
- You MUST run `git push -u origin HEAD`.
- If there is nothing to commit, You MUST say so plainly and skip commit creation.
- If push is rejected, You MUST fetch and inspect the remote branch before integrating remote changes.
- You MUST NOT force-push unless the user explicitly requests it because force-push can overwrite remote work.

### 7. Create A Pull Request When Requested

Open a PR only when the selected mode includes `pull-request`.

**Constraints:**

- You MUST resolve the remote default branch instead of hard-coding `main`, `master`, or `dev`.
- You MUST create the PR against the remote default branch unless the user explicitly requests another base.
- You MUST use a short changelog-style title without a Conventional Commit prefix.
- You SHOULD select labels from existing repo labels based on the change.
- You MUST return the PR URL.

### 8. Preserve Guardrails

Apply safety rules throughout the workflow.

**Constraints:**

- You MUST NOT commit or push PR review fixes unless the current user message explicitly asks for commit or push because PR review fixes stay local by default.
- You MUST NOT stash, reset, force checkout, or force push unless the user explicitly asks for that operation because those operations can hide, discard, or overwrite work.
- You MUST treat short imperatives like `checkout dev`, `switch to dev`, `go to main`, `sync this branch`, or `open a PR` as direct git/GitHub instructions.

## Examples

### Plain Yeet

**Input:**

- user_request: `yeet`

**Expected Behavior:**

The agent loads Git Conventions, gathers context, stages all changes, commits with a conventional message, pushes `HEAD`, and reports the commit.

### Pull Request

**Input:**

- user_request: `yeet pr`

**Expected Behavior:**

The agent commits and pushes current work, resolves the remote default branch, opens a PR, and returns the PR URL.

## Troubleshooting

- If there is nothing to commit, You MUST say so plainly and skip commit creation.
- If push is rejected because the remote branch advanced, You MUST fetch and inspect the divergence before rebasing, merging, or asking the user.
- If a PR already exists for the branch, You SHOULD report the existing PR instead of creating a duplicate.
