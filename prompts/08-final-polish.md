# Phase 8: Final Polish

You are doing the final cleanup pass before the task is complete. The code
should already be correct and reviewed -- this phase is about polish, not
substance.

## Prerequisites

Read the following files from the task directory:
- `01-definition.md` -- acceptance criteria for the final summary.
- `03-plan.md` -- coding guidelines, documentation impact, and breaking changes.
- `task-state.md` -- confirm we are in the final polish phase.

Also read:
- The project commands file for lint and format commands.
- The language review profile(s) for the detected language(s).

## Instructions

### Step 1: Run Linters and Formatters

Using the project commands, run:
1. The format command (if configured). Fix any formatting issues.
2. The lint command (if configured). Fix any lint errors or warnings.
3. The type check command (if configured). Ensure no type errors.

If project commands are not configured for these, skip them but note it in the
polish document.

### Step 2: Scan for AI-Generated Code Indicators

Review all changed files and look for:

**Unnecessary comments**:
- Comments that restate what the code obviously does (e.g., "// increment the
  counter" above `counter++`).
- Comments on non-exported functions that do not clarify complex logic. Remove
  these -- internal functions should be self-documenting.
- Placeholder or TODO comments that should not ship.
- Stale comments that no longer match the code.

**Generic or vague names**:
- Variable names like `data`, `result`, `temp`, `item`, `value` that could be
  more descriptive.
- Function names that do not clearly convey what the function does.

**Inconsistent style**:
- Naming that does not match the codebase conventions discovered during
  research.
- Formatting inconsistencies with the surrounding code.

### Step 3: Check for AI Text Artifacts

Scan all changed files (including documentation, comments, and strings) for
unicode special characters that should be ASCII:

- Curly/smart quotes (" " ' ') -- replace with straight quotes (" ')
- Em-dashes (--) -- replace with double hyphens (--)
- En-dashes (-) -- replace with single hyphens (-)
- Ellipsis characters (...) -- replace with three dots (...)
- Any other non-ASCII punctuation that is not intentional

### Step 4: Check for Duplication

Review the changes for duplicated logic:
- Search the codebase for existing functions that do similar things to any new
  helper functions introduced.
- Look for copy-pasted logic between the changed files and the rest of the
  codebase.
- If duplication is found, refactor to use shared utilities.

### Step 5: Final Guidelines and Profile Check

Do one final pass through:
- Each item in the coding guidelines from `03-plan.md`.
- Each item in the language review profile(s).

Confirm that all guidelines are satisfied. Fix any remaining violations.

### Step 6: Verify Documentation

Check that all documentation updates from the plan's Documentation Impact
section have been completed:
- Are READMEs updated?
- Are API docs accurate?
- Are examples correct?
- Are changelogs updated?
- If the plan stated no documentation changes were needed, verify this is
  still true.

### Step 7: Run Tests

Run the full test suite using the project commands. All tests must pass.

If tests fail after polish changes, fix the issues. If the failures are not
caused by polish changes, investigate and resolve.

### Step 8: Write Polish Document

Write `08-final-polish.md` in the task directory:

```markdown
# Final Polish: <task title>

## Changes Made
- <list of cleanup changes: removed comments, fixed formatting, etc.>

## Lint/Format Results
- **Format**: <clean / N issues fixed>
- **Lint**: <clean / N issues fixed>
- **Type check**: <clean / N issues fixed / not configured>

## AI Artifact Check
- <any unicode replacements made, or "No AI artifacts found">

## Duplication Check
- <any duplication found and resolved, or "No duplication found">

## Final Test Results
- **Test command**: <command run>
- **Result**: <pass>
- **Details**: <all N tests passing, or summary>
```

### Step 9: Write Task Summary

Write `09-summary.md` in the task directory:

```markdown
# Task Summary: <task title>

## Task
<Brief description from the problem statement.>

## What Was Delivered
<Summary of what was implemented, mapped to the acceptance criteria from
01-definition.md. For each criterion, confirm it was met.>

- [x] <criterion 1>: <how it was met>
- [x] <criterion 2>: <how it was met>

## Files Changed
<List of all files created, modified, or deleted during this task.>

## Review Rounds
<How many code review cycles were needed and a brief note on what the main
review findings were.>

## Notable Decisions
<Key choices made during planning and review, with rationale. Include any
suggestions that were rejected and why.>
```

### Step 10: Final Commit

Commit all polish changes with an appropriate commit message (e.g.,
`chore: final polish for <task-slug>`).

### Update State

Update `task-state.md`:

```markdown
- **Current Phase**: complete
- **Phase History**:
  - ...previous entries...
  - Phase 8 (Final Polish): completed <timestamp>
  - Task complete: <timestamp>
```

## Task Complete

The task is finished. The task directory contains the full record of the
process: definition, research, plan, review notes, implementation tracking,
code reviews, polish notes, and the final summary.
