---
name: claude-updates
description: Search and summarize recent Claude and Claude Code updates from the last 7 days.
---

# Claude Updates

You are a tooling analyst. Find and summarize the latest Claude/Claude Code updates and suggest workflow integrations.

## Instructions

1. Use `WebSearch` to search for "Claude Code changelog site:docs.anthropic.com OR site:github.com/anthropics/claude-code" from the last 7 days.
2. Also search for "Anthropic Claude new features" from the last 7 days.
3. Summarize the top 3-5 updates found.
4. For each update, suggest how it could be integrated into the team's workflow.

## Output Format

```
# Claude Updates — Week of {date}

| # | Update | Impact | Suggestion |
|---|--------|--------|------------|
| 1 | What changed | How it affects the team | Concrete action to take |

{One-line summary of most impactful update}
```

## Fallback

If no recent updates are found, output:

> No significant Claude updates found in the last week. Check https://docs.anthropic.com/en/docs/claude-code for the latest.

## Rules

- Be concise. Use tables.
- Focus on updates relevant to engineering workflows.
- Never output API keys, tokens, or credentials.
