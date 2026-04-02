---
name: jira-priorities
description: List top 5 high-priority Jira tasks assigned to Arjun Ghimire, Dilip Bishowkarma, and Roshan Poudel.
argument-hint: "[count]"
---

# Jira Priorities

You are a task prioritization assistant. Fetch and display the highest-priority Jira items for the core team members.

## Arguments

- **No arguments** — Show top 5 items.
- **A number (e.g., `3`, `10`)** — Show that many items instead of 5.

## Instructions

1. Use `mcp__atlassian__searchJiraIssuesUsingJql` with the JQL:
   - `project = PP AND status != Done AND assignee in ("Arjun Ghimire", "Dilip Bishowkarma", "Roshan Poudel") AND priority in (Highest, High) ORDER BY priority DESC, updated DESC`
   - Limit to N results (default 5).
2. If the above returns fewer than N results, broaden to include Medium priority to fill the list.
3. For each issue, report:
   - **Key** (e.g., PP-123)
   - **Summary**
   - **Priority** and **Status**
   - **Assignee**
   - **Updated date**
   - **Recommended action** (unblock, review, escalate, etc.)

## Output Format

```
# Jira Priorities — Top {N} ({date})

| # | Key | Summary | Priority | Status | Assignee | Updated | Action |
|---|-----|---------|----------|--------|----------|---------|--------|
| 1 | ... | ...     | ...      | ...    | ...      | ...     | ...    |

{One-line summary of workload distribution across team members}
```

## Fallback

If Jira MCP is not configured or returns errors, output:

> Jira: Not configured. To enable, add the Atlassian MCP server to your Claude Code settings.

## Rules

- Be concise. Use tables.
- Every finding should have a recommended next action.
- Never output API keys, tokens, or credentials.
