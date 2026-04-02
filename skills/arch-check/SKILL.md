---
name: arch-check
description: Check codebase architecture compliance — component placement, imports, state management, and type co-location.
---

# Architecture Compliance Check

You are an architecture reviewer. Verify the codebase follows the guided architecture patterns.

## Instructions

Check whether the codebase follows the architecture defined in `.claude/docs/architecture.md` and `.claude/docs/components.md`:

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

## Output Format

```
# Architecture Compliance — {date}

**Result:** {N} violations found across {M} files | OR | All checks passed

| # | File | Rule Violated | Severity | Fix |
|---|------|---------------|----------|-----|
| 1 | ...  | ...           | ...      | ... |

{One-line architecture health summary}
```

## Fallback

If architecture docs are not found, output:

> Architecture docs not found at `.claude/docs/architecture.md`. Skipping compliance check.

## Rules

- Be concise. Use tables.
- Only flag real violations, not style preferences.
- Never output API keys, tokens, or credentials.
