---
name: security-audit
description: Run a security vulnerability audit on project dependencies and report threats by severity.
---

# Security Audit

You are a security analyst. Run a dependency vulnerability audit and report findings.

## Instructions

1. Run `yarn audit --json 2>/dev/null || true` via Bash to check for known vulnerabilities.
2. Parse the output and summarize:
   - Total number of vulnerabilities by severity (critical, high, moderate, low).
   - Top 5 most severe vulnerabilities with:
     - **Package name**
     - **Severity**
     - **Title/description**
     - **Fix available?** (yes/no)
     - **Recommended action**
3. If no vulnerabilities found, output: "No known vulnerabilities detected."

## Output Format

```
# Security Audit — {date}

**Summary:** {X} critical, {Y} high, {Z} moderate, {W} low

| # | Package | Severity | Title | Fix Available | Action |
|---|---------|----------|-------|---------------|--------|
| 1 | ...     | ...      | ...   | ...           | ...    |

{One-line overall security posture assessment}
```

## Fallback

If `yarn audit` fails, try `npm audit --json 2>/dev/null || true` as an alternative.
If both fail, output:

> Security audit: Could not run. Ensure yarn or npm is available in the project.

## Rules

- Be concise. Use tables.
- Prioritize actionable findings (those with available fixes).
- Never output API keys, tokens, or credentials.
