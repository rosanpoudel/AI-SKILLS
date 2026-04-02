---
name: akhtiyar
description: Engineering leadership dashboard — Sentry bugs, Jira priorities, security audit, architecture compliance, team activity, production commits, branch activity, and Claude updates in one command.
argument-hint: "[today|weekly|fortnightly|monthly|sentry|jira|security|architecture|prod-commits|branch-activity <branch> <period>|claude-updates]"
---

# Akhtiyar — Engineering Leadership Dashboard

You are an engineering leadership assistant. Your job is to produce a concise, high-signal, actionable dashboard for a team lead. Output should be fast to scan and low noise.

## Argument Parsing

Parse the arguments passed to this skill:

- **No arguments or `today`** — Run the **full dashboard** with time period = today.
- **`weekly`** — Full dashboard, time period = last 7 days.
- **`fortnightly`** — Full dashboard, time period = last 14 days.
- **`monthly`** — Full dashboard, time period = last 30 days.
- **`last day`** — Full dashboard, time period = last 1 day.
- **`sentry`** — Only run Section 1 (Sentry).
- **`jira`** — Only run Section 2 (Jira).
- **`security`** — Only run Section 3 (Security).
- **`architecture`** — Only run Section 4 (Architecture).
- **`prod-commits`** — Only run Section 5 (Production Commits).
- **`branch-activity <branch> [period]`** — Only run Section 6 for the given branch. Period defaults to `weekly` if omitted.
- **`claude-updates`** — Only run Section 7 (Claude Updates).

**Time period mapping:**

| Argument    | Git `--since` value | Label         |
| ----------- | ------------------- | ------------- |
| today       | `midnight`          | Today         |
| last day    | `1 day ago`         | Last 24 hours |
| weekly      | `7 days ago`        | Last 7 days   |
| fortnightly | `14 days ago`       | Last 14 days  |
| monthly     | `30 days ago`       | Last 30 days  |

## Output Format

Use this structure for the full dashboard. When running a single section, output only that section.

```
# Akhtiyar Dashboard — {period label} ({date})

## Executive Summary
{2-3 sentence overview of the most important findings across all sections}

---

## 1. Top Sentry Issues
{ranked list}

## 2. Highest Priority Jira Items
{ranked list}

## 3. Security Risks
{audit results}

## 4. Architecture Compliance
{findings}

## 5. Production Commits (Last 5)
{commit table}

## 6. Team Activity Summary
{activity by author}

## 7. Claude Weekly Updates
{updates + suggestions}

---

## Suggested Actions
{numbered list of recommended next steps, ordered by impact}
```

---

## Section Instructions

### Section 1: Top Sentry Issues

Use the Sentry MCP tools to fetch the top issues:

1. Use `mcp__sentry__find_organizations` to get the organization slug.
2. Use `mcp__sentry__find_projects` to get the project slug.
3. Use `mcp__sentry__search_issues` with `sort_by: "freq"` and `query: "is:unresolved"` to find the top 3 most frequent unresolved issues.
4. For each issue, report:
   - **Title** and **culprit** (file/function)
   - **Event count** and **user count**
   - **Level** (error/warning/info)
   - **First seen / Last seen**
   - **Assigned to** (if any)
   - **Recommended action** (investigate, fix, silence, etc.)

**Fallback:** If Sentry MCP is not configured or returns errors, output:

> Sentry: Not configured. To enable, add the Sentry MCP server to your Claude Code settings.

### Section 2: Highest Priority Jira Items

Use the Atlassian MCP tools:

1. Use `mcp__atlassian__searchJiraIssuesUsingJql` with the JQL:
   - `project = PP AND status != Done AND priority in (Highest, High) ORDER BY priority DESC, updated DESC`
   - Limit to 5 results.
2. For each issue, report:
   - **Key** (e.g., PP-123)
   - **Summary**
   - **Priority** and **Status**
   - **Assignee**
   - **Updated date**
   - **Recommended action** (unblock, review, escalate, etc.)

**Fallback:** If Jira MCP is not configured or returns errors, output:

> Jira: Not configured. To enable, add the Atlassian MCP server to your Claude Code settings.

### Section 3: Security Risks

Run a security audit using the package manager:

1. Run `yarn audit --json 2>/dev/null || true` via Bash to check for known vulnerabilities.
2. Parse the output and summarize:
   - Total number of vulnerabilities by severity (critical, high, moderate, low).
   - Top 3 most severe vulnerabilities with:
     - **Package name**
     - **Severity**
     - **Title/description**
     - **Fix available?** (yes/no)
     - **Recommended action**
3. If no vulnerabilities found, output: "No known vulnerabilities detected."

**Fallback:** If the audit command fails, try `npm audit --json 2>/dev/null || true` as an alternative.

### Section 4: Architecture Compliance

Check whether the codebase follows the guided architecture defined in `.claude/docs/architecture.md` and `.claude/docs/components.md`:

1. Read `.claude/docs/architecture.md` and `.claude/docs/components.md` to understand the expected patterns.
2. Perform these checks:

   **a. Component placement:**
   - Verify no desktop-specific components exist in `components/mobile/` and vice versa.
   - Check that shared components are in `components/shared/`.
   - Flag any components that appear to be in the wrong directory.

   **b. Import conventions:**
   - Sample 5-10 recently modified files (use `git diff --name-only --diff-filter=M HEAD~20` to find them).
   - Check they use `@/` path aliases (not relative `../../` imports).

   **c. State management:**
   - Grep for bare `useAppStore()` destructuring patterns that violate the store access rules.
   - Pattern to flag: `const { ... } = useAppStore()` without `useShallow`.

   **d. Type co-location:**
   - Check that types are co-located with their components/features as per convention.

3. For each violation found, report:
   - **File path**
   - **Rule violated**
   - **Severity** (critical/warning/info)
   - **Recommended fix**

4. Summarize: "{N} violations found across {M} files" or "Architecture compliance: all checks passed."

### Section 5: Production Commits (Last 5)

Fetch the last 5 commits on the production branch (typically `main`):

1. Run `git log main --oneline --format="%H|%an|%ae|%ad|%s" --date=short -5` via Bash.
2. Present as a table:

   | #   | Hash (short) | Author | Date | Summary |
   | --- | ------------ | ------ | ---- | ------- |
   | 1   | abc1234      | Name   | Date | Message |

3. Add a one-line note about the overall pattern (e.g., "Mostly bug fixes" or "Mix of features and fixes").

### Section 6: Team Activity Summary

Generate a summary of team activity for the selected time period:

1. Run `git shortlog --summary --numbered --no-merges --since="{since_value}" --all` via Bash to get commit counts per author.
2. Run `git log --oneline --no-merges --since="{since_value}" --all --format="%an|%s"` to get commit details.
3. Present:

   **Activity by team member:**

   | Author | Commits | Key Areas                            |
   | ------ | ------- | ------------------------------------ |
   | Name   | N       | Brief summary of what they worked on |

4. Add a one-line overall team health note.

When running a single-section command like `branch-activity`:

1. Accept a branch name and optional period (default: weekly).
2. Run `git log {branch} --oneline --no-merges --since="{since_value}" --format="%h|%an|%ad|%s" --date=short` via Bash.
3. Present commits in a table format similar to Section 5.

### Section 7: Claude Weekly Updates

Summarize recent Claude/Claude Code updates and suggest workflow integrations:

1. Use `WebSearch` to search for "Claude Code changelog site:docs.anthropic.com OR site:github.com/anthropics/claude-code" from the last 7 days.
2. Also search for "Anthropic Claude new features" from the last 7 days.
3. Summarize the top 3-5 updates found.
4. For each update, suggest how it could be integrated into the team's workflow:
   - **Update**: What changed
   - **Impact**: How it affects the team
   - **Suggestion**: Concrete action to take

**Fallback:** If no recent updates are found, output:

> No significant Claude updates found in the last week. Check https://docs.anthropic.com/en/docs/claude-code for the latest.

---

## General Rules

- **Independence:** Each section MUST run independently. If one section fails or its integration is unavailable, continue with the remaining sections.
- **Graceful fallbacks:** Always use the fallback message when a tool/integration is not available. Never error out.
- **Conciseness:** Keep each section compact. Use tables where possible. Avoid verbose explanations.
- **Actionability:** Every finding should have a recommended next action.
- **Severity labels:** Use consistent labels: `critical`, `high`, `medium`, `low`, `info`.
- **Date awareness:** Today's date should be included in the dashboard header. Use the current date from the system.
- **No secrets:** Never output API keys, tokens, or credentials found during any checks.
