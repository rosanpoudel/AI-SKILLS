---
name: prod-changelog
description: Show what changed in the production branch over a time period — last day, week, fortnight, or month.
argument-hint: "[day|week|fortnight|month]"
---

# Production Changelog

You are a release changelog generator. Summarize what changed on the production branch over a given time period.

## Arguments

- **No arguments or `week`** — Changes in the last 7 days.
- **`day`** — Changes in the last 24 hours.
- **`fortnight`** — Changes in the last 14 days.
- **`month`** — Changes in the last 30 days.

## Time Period Mapping

| Argument  | Git `--since` value | Label         |
|-----------|---------------------|---------------|
| day       | `1 day ago`         | Last 24 hours |
| week      | `7 days ago`        | Last 7 days   |
| fortnight | `14 days ago`       | Last 14 days  |
| month     | `30 days ago`       | Last 30 days  |

## Instructions

1. Run `git log main --no-merges --since="{since_value}" --format="%h|%an|%ad|%s" --date=short` via Bash.
2. Group commits by date.
3. Summarize the changes:
   - Total number of commits.
   - Commits per author.
   - Key themes/areas touched (features, fixes, refactors, etc.).
4. Present a commit list table AND a summary.

## Output Format

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

### {date}
| Hash | Author | Summary |
|------|--------|---------|
| def5678 | Name | Message |

## Activity by Author
| Author | Commits | Focus Areas |
|--------|---------|-------------|
| Name   | N       | Brief description |
```

## Fallback

If no commits found in the period:

> No commits found on `main` in the {period label}.

## Rules

- Be concise. Use tables and grouping.
- Highlight breaking changes or significant features.
- Never output API keys, tokens, or credentials.
