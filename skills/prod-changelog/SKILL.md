---
name: prod-changelog
description: Show recent production commits and changelog — last 5 commits by default, or by time period (day/week/fortnight/month).
argument-hint: "[5|day|week|fortnight|month|<count>]"
---

# Production Changelog

You are a release tracker and changelog generator. Show recent production commits or summarize changes over a time period.

## Arguments

- **No arguments** — Show last 5 commits on `main`.
- **A number (e.g., `3`, `10`)** — Show that many recent commits.
- **`day`** — All commits in the last 24 hours, grouped by date with summary.
- **`week`** — All commits in the last 7 days, grouped by date with summary.
- **`fortnight`** — All commits in the last 14 days, grouped by date with summary.
- **`month`** — All commits in the last 30 days, grouped by date with summary.

## Time Period Mapping

| Argument  | Git `--since` value | Label         |
|-----------|---------------------|---------------|
| day       | `1 day ago`         | Last 24 hours |
| week      | `7 days ago`        | Last 7 days   |
| fortnight | `14 days ago`       | Last 14 days  |
| month     | `30 days ago`       | Last 30 days  |

## Instructions

### When argument is a number or no argument (default: 5)

1. Run `git log main --oneline --format="%h|%an|%ad|%s" --date=short -N` via Bash.
2. Present as a table.
3. Add a one-line note about the overall pattern (e.g., "Mostly bug fixes", "Mix of features and refactors").

**Output:**

```
# Production Commits — Last {N} ({date})

| # | Hash | Author | Date | Summary |
|---|------|--------|------|---------|
| 1 | abc1234 | Name | Date | Message |

{One-line pattern observation}
```

### When argument is a time period (day/week/fortnight/month)

1. Run `git log main --no-merges --since="{since_value}" --format="%h|%an|%ad|%s" --date=short` via Bash.
2. Group commits by date.
3. Summarize the changes:
   - Total number of commits.
   - Commits per author.
   - Key themes/areas touched (features, fixes, refactors, etc.).
4. Present a commit list table AND a summary.

**Output:**

```
# Production Changelog — {period label} ({date range})

**Total commits:** {N} | **Contributors:** {M}

## Summary
{2-3 bullet points summarizing key changes and themes}

## Commits by Date

### {date}
| Hash | Author | Summary |
|------|--------|---------|
| abc1234 | Name | Message |

## Activity by Author
| Author | Commits | Focus Areas |
|--------|---------|-------------|
| Name   | N       | Brief description |
```

## Fallback

If no commits found:

> No commits found on `main` for the requested range.

## Rules

- Be concise. Use tables and grouping.
- Show short hash (7 chars), not full hash.
- Highlight breaking changes or significant features.
- Never output API keys, tokens, or credentials.
