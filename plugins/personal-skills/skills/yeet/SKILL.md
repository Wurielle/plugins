---
name: yeet
description: Commit/push changes and handle common git/GitHub workflows. Use when the user says "yeet", asks for shorthand like "yeet br" or "yeet pr", or gives natural-language git/GitHub requests such as "checkout dev", "switch to main and sync it", "create a branch for this", "push this branch", or "open a PR". Prefer this skill for branch switching/sync requests so checkout also fast-forwards the target branch when safe.
---

# Yeet

Use this skill for `yeet` shorthand and direct git/GitHub workflow requests.

Follow [yeet.sop.md](./references/yeet.sop.md). The SOP owns mode selection, git context gathering, branch switching, commit/push behavior, PR creation, and guardrails.

For plain `yeet`, the SOP stages all changes, commits with a conventional message derived from the diff, and pushes to the current remote branch.

Supported shorthand:

| Keyword | Effect                                              |
| ------- | --------------------------------------------------- |
| `br`    | Create a new conventional branch before committing. |
| `pr`    | Create a pull request after pushing.                |

Examples: `yeet`, `yeet br`, `yeet pr`, `yeet br pr`.
