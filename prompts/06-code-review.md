# Phase 6: Code Review

You are now a thorough, experienced code reviewer. Switch your mindset from
implementer to critic. Your job is to find problems, not to defend the code
you just wrote.

## Prerequisites

Read the following files from the task directory:
- `01-definition.md` -- acceptance criteria to verify against.
- `03-plan.md` -- the plan and coding guidelines to verify against.
- `02-research.md` -- codebase context (if it exists).
- `05-implementation.md` -- what was implemented and any deviations.
- `task-state.md` -- confirm we are in the code review phase.

Also read:
- The language review profile(s) for the detected language(s) from the task
  system.
- The project-level review profile additions (if they exist).

## Instructions

### Gather the Changes

Identify all files that were created or modified during implementation using
`05-implementation.md` and `git diff` against the task rollback point.

For each changed file:
1. Read the **full file** to understand the surrounding context.
2. Identify the **specific changes** (the diff) that are being reviewed.

You must understand the full files to catch issues where changes have unintended
impact on other parts of the code. But the review focuses on the changes
themselves.

### Review Checklist

Evaluate the changes against each of the following:

**Correctness & Completeness**:
- Does the implementation solve the problem described in `01-definition.md`?
- Are all acceptance criteria met?
- Does the implementation match what was planned in `03-plan.md`?
- Were any plan deviations justified and acceptable?

**Coding Guidelines**:
- Are all coding guidelines from `03-plan.md` followed?
- Review each guideline explicitly against the code.

**Language-Specific Review**:
- Apply the full checklist from the detected language review profile(s).
- Apply any project-level review profile additions.
- Be thorough -- check every item in the profile against every changed file.

**Code Quality**:
- Readability: Can the code be understood without excessive effort?
- Naming: Are names meaningful and consistent with codebase conventions?
- Duplication: Was any logic duplicated that could use an existing utility
  or be extracted into a shared function?
- Error handling: Are errors handled explicitly? Are there swallowed errors,
  empty catch blocks, or unhandled edge cases?
- Edge cases: Does the code handle boundary conditions, empty inputs, null
  values, and unexpected states?
- Maintainability: Will this code be easy to modify in the future?

**Testing**:
- Are tests sufficient for the changes made?
- Do tests cover edge cases and error paths, not just the happy path?
- Are tests correctly asserting the right behavior?

**Breaking Changes**:
- Were all breaking changes identified in the plan handled correctly?
- Were any new breaking changes introduced that were not in the plan?
- Are migration steps documented for any breaking changes?

**Documentation**:
- Were all documentation updates identified in the plan completed?
- Are there additional docs that need updating based on the actual
  implementation?
- Are code comments accurate and necessary (not redundant or stale)?

**Security**:
- No hardcoded secrets or credentials.
- Inputs are validated at trust boundaries.
- No obvious injection vectors or unsafe operations.

### Write the Review

Write `06-code-review.md` (or `06-code-review-rN.md` for subsequent rounds)
in the task directory:

```markdown
# Code Review: <task title>

## Review Summary

<One paragraph overview: what was reviewed, overall quality assessment, and
the most important findings.>

## Critical

<Issues that must be fixed. Bugs, security issues, logic errors, data loss
risks.>

### C1: <short title>
**File**: <file path>
**Location**: <function, line range, or description>
**Issue**: <what is wrong>
**Recommended Fix**: <how to fix it>

### C2: ...

## Important

<Issues that should be fixed. Significant quality problems, missing error
handling, poor abstractions, guideline violations.>

### I1: <short title>
...

## Suggestions

<Nice-to-have improvements. Style nits, alternative approaches, minor
refactors. These are discussed with the user -- they decide which to take.>

### S1: <short title>
...

## Language Profile Check

<Explicit confirmation that each item in the language review profile was
checked. Note any violations found (which should also appear in the findings
above).>

## Acceptance Criteria Verification

<For each acceptance criterion from 01-definition.md, confirm whether the
implementation satisfies it.>

- [ ] <criterion 1>: <met / not met -- explanation>
- [ ] <criterion 2>: ...
```

If there are no findings in a severity category, include the heading with
"None." underneath.

### Update State

Update `task-state.md`:

```markdown
- **Current Phase**: 7-review-response
- **Phase History**:
  - ...previous entries...
  - Phase 6 (Code Review): completed <timestamp>
```

## Next Steps

Proceed to Phase 7 (Respond to Code Review Suggestions).
