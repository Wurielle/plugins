---
name: git-conventions
description: Apply consistent branch names, Conventional Commit messages, and pull request titles, bases, and labels. Use when naming branches, writing commits, creating pull requests, or reviewing Git and GitHub metadata.
---

# Git Conventions

## Purpose

Git conventions keep branch names, commits, and pull requests predictable, reviewable, and aligned with the repository's release and changelog workflows.

## Scope

These rules apply to:

- branch names
- commit messages
- pull request titles, bases, and labels

These rules do NOT apply to:

- temporary local throwaway branches that are never pushed
- merge commits created by the hosting platform
- external contributor history before it is normalized for the repository

## Ownership

This standard owns:

- branch naming and PR branch base expectations
- commit message format and atomicity expectations
- pull request titles, bases, and label selection

This standard does NOT own:

- code quality inside a commit, which is owned by topic standards and project tests
- CI workflow behavior, except for git metadata naming
- release tagging policy beyond metadata conventions stated here

## Rules

### Commit Messages

Commit messages MUST use Conventional Commits format:

```text
<type>(<scope>): <description>
```

The scope MAY be omitted when the change spans multiple areas or the scope is obvious from context.

Descriptions MUST use imperative mood, start lowercase, and omit trailing periods.

Each commit SHOULD be atomic and independently buildable.

The first line SHOULD stay under 72 characters.

Allowed commit types:

| Type       | When to use                                                |
| ---------- | ---------------------------------------------------------- |
| `feat`     | A new feature or capability                                |
| `fix`      | A bug fix                                                  |
| `refactor` | Code restructuring that does not change behavior           |
| `docs`     | Documentation only                                         |
| `test`     | Adding or updating tests                                   |
| `chore`    | Maintenance tasks such as dependencies, config, or tooling |
| `ci`       | CI or workflow changes                                     |

### Branch Naming

Branches MUST follow the pattern `<type>/<kebab-case-name>`.

Branch names MUST NOT contain `codex/`, including temporary local branches.

Branch names SHOULD be short and descriptive, with 2-4 words after the prefix as the default target length.

Branch names SHOULD summarize the change rather than describe implementation details.

Branches for PR work MUST branch off the repository's default branch.

Use the remote default branch instead of hard-coding `main`, `master`, or `dev`.

Branch prefixes:

| Change pattern                                   | Branch prefix |
| ------------------------------------------------ | ------------- |
| Bug fix (`fix-*`, `bug-*`)                       | `fix/`        |
| CI or workflow (`ci-*`, `pipeline-*`)            | `ci/`         |
| Docs only (`docs-*`, `doc-*`)                    | `docs/`       |
| Dependencies (`deps-*`, `update-*`, `upgrade-*`) | `deps/`       |
| Anything else                                    | `feat/`       |

### Pull Requests

Pull requests MUST target the repository's default branch unless the user explicitly requests another base.

Pull request titles MUST be short, changelog-style sentences without Conventional Commit prefixes.

Pull request titles MUST start with a capitalized imperative verb.

Pull request titles SHOULD focus on the primary purpose of the change.

Labels SHOULD be selected from existing repo labels based on the change.

| Change type or content                     | Label           |
| ------------------------------------------ | --------------- |
| `fix:`, bug fixes                          | `bug`           |
| `feat:`, enhancements                      | `enhancement`   |
| `docs:`, documentation                     | `documentation` |
| `deps:`, dependency changes                | `dependencies`  |
| `.ts`, `.js`, `.tsx`, `.jsx` files changed | `javascript`    |

## Examples

### Commit Message Shape

This example shows the expected Conventional Commit format, description casing, and single-purpose summary.

Bad:

```text
Fixed config crash.
feature: Added dark mode
feat(react-ui): add select component and fix tests and update docs and cleanup lint
docs(contributing): Add contributing guide.
```

Better:

```text
feat(auth): implement JWT-based authentication
fix: prevent crash when config file is missing
refactor(cli): simplify dependency resolver
docs: add contributing guide
test(react-ui): add snapshot tests for button
chore: upgrade vitest to v3
ci: add caching to build workflow
```

Reason: conventional commits are machine-readable while still summarizing one logical change for humans.

### Branch Base And Name

This example shows that PR branches should use the repo default branch and a short conventional branch name.

Bad:

```bash
git switch -c feature/select main
```

Better:

```bash
git symbolic-ref --short refs/remotes/origin/HEAD
git switch -c feat/add-select-component origin/dev
```

Reason: branch names use the project convention and branch from the actual remote default branch instead of assuming a fixed branch name.

### Pull Request Title

This example shows that PR titles should read like changelog entries, not commit messages or catch-all summaries.

Bad:

```text
feat: add select component
Add select component, fix lint, update stories, and refactor recipes
added select stuff
```

Better:

```text
Add dark mode toggle to settings page
Fix crash when config file is missing
Upgrade Vitest to v3 and update snapshots
Refactor CLI dependency resolver for clarity
Add select input component
```

Reason: PR titles read like changelog entries and do not duplicate commit syntax.

## Verification Requirements

Verification MUST use observable evidence from branch names, commit history, pull request metadata, and repo labels.

Acceptable evidence includes:

- current branch name and upstream base
- `git log` output for commit messages and atomicity
- pull request title, base branch, and labels
- existing repository label list when assigning labels

Tool-backed evidence includes `git branch`, `git log`, `git status`, remote default branch lookup, pull request metadata, and existing repository labels.

Verification modes:

- Automated: Git and GitHub metadata commands prove branch names, commit messages, bases, and labels.
- Manual: commit atomicity and message usefulness require reviewer judgment from visible history and PR metadata.

## Review Criteria

- Branch names use `<type>/<kebab-case-name>` and branch from the repository default branch for PR work.
- Branch names never contain `codex/`, including temporary local branches.
- Commit messages use `<type>(<scope>): <description>` with an imperative, lowercase description and no trailing period.
- Commits are scoped to one logical change unless the visible history shows a deliberate squash.
- Pull requests target the repository default branch unless another base was explicitly requested.
- Pull request titles are short, changelog-style sentences without Conventional Commit prefixes.
- Labels come from existing repo labels and match observable changed files or change type.
