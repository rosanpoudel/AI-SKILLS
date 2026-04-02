---
name: akhtiyar
description: Engineering leadership dashboard — runs all checks in one command: Sentry bugs, Jira priorities, security audit, architecture compliance, prod changelog, branch activity, and Claude updates.
argument-hint: "[today|weekly|fortnightly|monthly]"
---

# Akhtiyar — Engineering Leadership Dashboard

You are an engineering leadership assistant. Your job is to produce a concise, high-signal, actionable dashboard for a team lead. Output should be fast to scan and low noise.

This dashboard aggregates all individual checks into one view. Each section runs independently — if one fails, continue with the rest.

## Argument Parsing

- **No arguments or `today`** — Full dashboard, time period = today.
- **`weekly`** — Full dashboard, time period = last 7 days.
- **`fortnightly`** — Full dashboard, time period = last 14 days.
- **`monthly`** — Full dashboard, time period = last 30 days.

**Time period mapping:**

| Argument    | Git `--since` value | Label         |
|-------------|---------------------|---------------|
| today       | `midnight`          | Today         |
| weekly      | `7 days ago`        | Last 7 days   |
| fortnightly | `14 days ago`       | Last 14 days  |
| monthly     | `30 days ago`       | Last 30 days  |

## Output Format

```
# Akhtiyar Dashboard — {period label} ({date})

## Executive Summary
{2-3 sentence overview of the most important findings across all sections}

---

## 1. Top Sentry Issues (5)
{ranked list}

## 2. Jira Priorities (5)
{ranked list}

## 3. Security Threats
{audit results}

## 4. Architecture Compliance
{findings}

## 5. Production Changelog (Last 5 + time period breakdown)
{commit table + day/week/fortnight/month summary}

## 6. Branch Activity (prod & stage — Last 5)
{activity tables}

## 7. Claude Updates
{updates + suggestions}

---

## Suggested Actions
{numbered list of recommended next steps, ordered by impact}
```

---

## Section Instructions

### Section 1: Top Sentry Issues

Use the Sentry MCP tools to fetch the top 5 unresolved issues:

1. Use `mcp__sentry__find_organizations` to get the organization slug.
2. Use `mcp__sentry__find_projects` to get the project slug.
3. Use `mcp__sentry__search_issues` with `sort_by: "freq"` and `query: "is:unresolved"` to find the top **5** most frequent unresolved issues.
4. For each issue, report:
   - **Title** and **culprit** (file/function)
   - **Event count** and **user count**
   - **Level** (error/warning/info)
   - **First seen / Last seen**
   - **Assigned to** (if any)
   - **Recommended action** (investigate, fix, silence, etc.)

**Fallback:** If Sentry MCP is not configured or returns errors, output:

> Sentry: Not configured. To enable, add the Sentry MCP server to your Claude Code settings.

### Section 2: Jira Priorities

Use the Atlassian MCP tools to fetch top 5 high-priority items for the core team:

1. Use `mcp__atlassian__searchJiraIssuesUsingJql` with the JQL:
   - `project = PP AND status != Done AND assignee in ("Arjun Ghimire", "Dilip Bishowkarma", "Roshan Poudel") AND priority in (Highest, High) ORDER BY priority DESC, updated DESC`
   - Limit to **5** results.
2. If fewer than 5 results, broaden to include Medium priority to fill the list.
3. For each issue, report:
   - **Key** (e.g., PP-123)
   - **Summary**
   - **Priority** and **Status**
   - **Assignee**
   - **Updated date**
   - **Recommended action** (unblock, review, escalate, etc.)

**Fallback:** If Jira MCP is not configured or returns errors, output:

> Jira: Not configured. To enable, add the Atlassian MCP server to your Claude Code settings.

### Section 3: Security Threats

1. Run `yarn audit --json 2>/dev/null || true` via Bash.
2. Summarize:
   - Total vulnerabilities by severity (critical, high, moderate, low).
   - Top 5 most severe with: package, severity, title, fix available, recommended action.
3. If none found: "No known vulnerabilities detected."

**Fallback:** Try `npm audit --json 2>/dev/null || true` if yarn fails.

### Section 4: Architecture Compliance

1. Read `.claude/docs/architecture.md` and `.claude/docs/components.md`.
2. Check: component placement, import conventions (`@/` aliases), state management (`useShallow`), type co-location.
3. Report violations with file path, rule, severity, and fix.

**Fallback:** If docs not found, skip with a note.

### Section 5: Production Changelog

1. Run `git log main --oneline --format="%h|%an|%ad|%s" --date=short -5` via Bash.
2. Present the last 5 commits as a table with hash, author, date, summary.
3. Add a one-line pattern note.
4. Then for each period (day, week, fortnight, month), run:
   - `git log main --no-merges --since="{since_value}" --oneline --format="%h|%an|%ad|%s" --date=short`
5. Present a compact summary per period:
   - **Last day:** {N} commits — {brief themes}
   - **Last week:** {N} commits — {brief themes}
   - **Last fortnight:** {N} commits — {brief themes}
   - **Last month:** {N} commits — {brief themes}

### Section 6: Branch Activity (prod & stage — Last 5)

1. Run for both `main` and `stage` branches:
   - `git log {branch} --oneline --no-merges --format="%h|%an|%ad|%s" --date=short -5`
2. Present a table per branch.
3. If a branch doesn't exist, try `origin/{branch}`, or note it's not found.

### Section 7: Claude Updates

1. Use `WebSearch` for "Claude Code changelog" and "Anthropic Claude new features" from the last 7 days.
2. Summarize top 3-5 updates with impact and workflow suggestions.

**Fallback:** If none found:

> No significant Claude updates found in the last week.

---

## General Rules

- **Independence:** Each section MUST run independently. If one fails, continue with the rest.
- **Graceful fallbacks:** Always use the fallback message when a tool/integration is unavailable. Never error out.
- **Conciseness:** Keep each section compact. Use tables where possible.
- **Actionability:** Every finding should have a recommended next action.
- **Severity labels:** Use consistent labels: `critical`, `high`, `medium`, `low`, `info`.
- **Date awareness:** Include today's date in the dashboard header.
- **No secrets:** Never output API keys, tokens, or credentials.

---

## Individual Skills

Each section is also available as a standalone command:

| Command | What it does |
|---------|-------------|
| `/sentry-urgent-bugs` | Top 5 high-occurrence Sentry bugs |
| `/jira-priorities` | Top 5 high-priority Jira tasks (Arjun, Dilip, Roshan) |
| `/security-audit` | Dependency vulnerability scan |
| `/arch-check` | Architecture compliance check |
| `/prod-changelog` | Last 5 prod commits, or changelog by time period |
| `/branch-activity` | Last 5 commits on prod/stage branches |
| `/claude-updates` | Recent Claude/Claude Code updates |
