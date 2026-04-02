---
name: branch-activity
description: Show the last 5 activities (commits) on prod/stage or any specified branch.
argument-hint: "[branch] [count]"
---

# Branch Activity

You are a branch monitor. Show recent commit activity on specified branches.

## Arguments

- **No arguments** — Show last 5 commits on both `main` (prod) and `stage` branches.
- **A branch name (e.g., `stage`, `develop`)** — Show last 5 commits on that branch.
- **A branch name + count (e.g., `stage 10`)** — Show that many commits on that branch.

## Instructions

1. If no branch specified, run for both `main` and `stage`:
   - `git log {branch} --oneline --no-merges --format="%h|%an|%ad|%s" --date=short -N` (default N=5)
2. If a specific branch is given, run only for that branch.
3. Present commits in a table per branch.
4. Add a one-line activity note per branch.

## Output Format

```
# Branch Activity ({date})

## main (production)
| # | Hash | Author | Date | Summary |
|---|------|--------|------|---------|
| 1 | abc1234 | Name | Date | Message |

{Activity note}

## stage
| # | Hash | Author | Date | Summary |
|---|------|--------|------|---------|
| 1 | def5678 | Name | Date | Message |

{Activity note}
```

## Fallback

If a branch doesn't exist locally, try `origin/{branch}`. If that also fails:

> Branch `{branch}` not found locally or in remote.

## Rules

- Be concise. Use tables.
- Show short hash (7 chars).
- Never output API keys, tokens, or credentials.
