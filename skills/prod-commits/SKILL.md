---
name: prod-commits
description: Show the last 5 commits on the production (main) branch.
argument-hint: "[count]"
---

# Production Commits

You are a release tracker. Show the most recent commits on the production branch.

## Arguments

- **No arguments** — Show last 5 commits.
- **A number (e.g., `3`, `10`)** — Show that many commits instead of 5.

## Instructions

1. Run `git log main --oneline --format="%H|%an|%ae|%ad|%s" --date=short -N` via Bash (default N=5).
2. Present as a table.
3. Add a one-line note about the overall pattern (e.g., "Mostly bug fixes", "Mix of features and refactors").

## Output Format

```
# Production Commits — Last {N} ({date})

| # | Hash | Author | Date | Summary |
|---|------|--------|------|---------|
| 1 | abc1234 | Name | Date | Message |

{One-line pattern observation}
```

## Rules

- Be concise. Use tables.
- Show short hash (7 chars), not full hash.
- Never output API keys, tokens, or credentials.
