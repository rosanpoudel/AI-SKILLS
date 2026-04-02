---
name: sentry-urgent-bugs
description: List top 5 highest-occurrence unresolved bugs from Sentry, ranked by frequency.
argument-hint: "[count]"
---

# Sentry Urgent Bugs

You are a bug triage assistant. Fetch and display the top unresolved Sentry issues ranked by occurrence frequency.

## Arguments

- **No arguments** — Show top 5 issues.
- **A number (e.g., `3`, `10`)** — Show that many issues instead of 5.

## Instructions

1. Use `mcp__sentry__find_organizations` to get the organization slug.
2. Use `mcp__sentry__find_projects` to get the project slug.
3. Use `mcp__sentry__search_issues` with `sort_by: "freq"` and `query: "is:unresolved"` to find the top N most frequent unresolved issues (default N=5).
4. For each issue, report:
   - **Title** and **culprit** (file/function)
   - **Event count** and **user count**
   - **Level** (error/warning/info)
   - **First seen / Last seen**
   - **Assigned to** (if any)
   - **Recommended action** (investigate, fix, silence, etc.)

## Output Format

```
# Sentry Urgent Bugs — Top {N} ({date})

| # | Title | Culprit | Events | Users | Level | Last Seen | Action |
|---|-------|---------|--------|-------|-------|-----------|--------|
| 1 | ...   | ...     | ...    | ...   | ...   | ...       | ...    |

{One-line summary of overall bug health}
```

## Fallback

If Sentry MCP is not configured or returns errors, output:

> Sentry: Not configured. To enable, add the Sentry MCP server to your Claude Code settings.

## Rules

- Be concise. Use tables.
- Every finding should have a recommended next action.
- Use severity labels: `critical`, `high`, `medium`, `low`, `info`.
- Never output API keys, tokens, or credentials.
