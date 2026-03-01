# Phase 5: Implement

You are implementing the finalized plan step by step. Follow the plan precisely.
Deviate only when necessary and document any deviations.

## Prerequisites

Read the following files from the task directory:
- `03-plan.md` -- the finalized implementation plan with coding guidelines.
- `02-research.md` -- codebase context (if it exists).
- `05-implementation.md` -- implementation progress (if it exists from a
  previous session).
- `task-state.md` -- confirm we are in the implementation phase.

Also read:
- The project commands file (project-level override or default template) to
  know the exact commands for building, testing, linting, etc.

If `05-implementation.md` already exists, read it to determine:
- Which steps are complete (resume after the last completed step).
- Whether any step was left `in-progress` (re-evaluate and restart that step).
- Whether any step is `blocked` (check if the user has provided guidance).

## Instructions

### Pre-Flight Checks

Before making any code changes, verify the codebase is in a clean state:

1. **Git working tree**: Run `git status`. If there are uncommitted changes
   to project source code, stop and ask the user to commit or stash them. Do
   not proceed with a dirty working tree. Ignore any untracked or modified
   files inside the tasks directory and project config directory -- these are
   task system artifacts and are expected to have uncommitted changes during
   task work.
2. **Existing tests**: Run the test command from project commands. If tests
   fail, stop and report. These are pre-existing failures that must be resolved
   before implementation begins -- do not attempt to fix them as part of this
   task.
3. **Build**: If a build command is configured, run it. If it fails, stop and
   report for the same reason.
4. **Record rollback point**: Run `git rev-parse HEAD` and record the commit
   SHA. This is the rollback point for the entire task.

If resuming from a previous session (05-implementation.md already exists and
has the rollback point recorded), skip pre-flight checks -- they were already
done.

### Create Implementation Tracker

If `05-implementation.md` does not already exist, create it:

```markdown
# Implementation: <task title>

## Rollback Point
**Task start**: <commit SHA>

## Steps

### Step 1: <description from plan>
**Status**: not started
**Rollback**: <to be recorded>

### Step 2: <description from plan>
**Status**: not started
**Rollback**: <to be recorded>

...
```

### Implement Each Step

For each step in order:

1. **Mark in-progress**: Update the step's status to `in-progress` and record
   the current commit SHA as the step's rollback point. Write this to disk
   immediately.

2. **Implement**: Make the code changes described in the plan. Follow the
   coding guidelines strictly. Key reminders:
   - Before creating a new utility function, search for existing ones.
   - Handle errors explicitly.
   - Use meaningful names.
   - No `any` types, no type assertions, no unicode special characters.
   - Follow existing codebase conventions.

3. **Verify**: If the project commands include relevant checks (build, type
   check, tests), run them to verify the step did not break anything.

4. **Mark complete**: Update the step's status in `05-implementation.md`:

```markdown
### Step N: <description>
**Status**: complete
**Rollback**: <commit SHA recorded at start of step>

**What was done**:
- <files created/modified>
- <brief summary of changes>

**Deviations from plan**:
- <any deviations and why, or "None">
```

5. **Commit**: Create a git commit with a message following the commit message
   format from the coding guidelines. The commit should cover the changes from
   this step only.

### Handling Blockers

If you encounter something that prevents progress on a step:

1. Mark the step as `blocked` with details about what is blocking it.
2. Write the block details to `05-implementation.md` immediately.
3. Ask the user for guidance. Do not attempt to work around blockers by
   deviating significantly from the plan.

### Running the Testing Strategy

After all implementation steps are complete:

1. Run the full testing strategy from the plan (not just the project test
   command -- include any manual verification steps).
2. Document results in `05-implementation.md`:

```markdown
## Test Results

**Test command**: <command run>
**Result**: <pass/fail>
**Details**: <summary of results, any failures>

**Manual verification**:
- <step>: <result>
```

3. If tests fail:
   - Analyze the failure and attempt to fix it.
   - Re-run tests after each fix attempt.
   - Track your attempts. If you notice thrashing (same failure recurring,
     oscillating between different failures, or no meaningful progress after
     3 attempts), stop. Document what you tried and ask the user for help.

### Update State

Update `task-state.md`:

```markdown
- **Current Phase**: 6-code-review
- **Phase History**:
  - ...previous entries...
  - Phase 5 (Implement): completed <timestamp>
```

## Next Steps

Proceed to Phase 6 (Code Review).
