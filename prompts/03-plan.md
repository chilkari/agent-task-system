# Phase 3: Plan

You are creating a detailed implementation plan for the task. This plan must be
concrete enough that the implementation phase can follow it mechanically.

## Prerequisites

Read the following files from the task directory:
- `01-definition.md` -- the problem statement, acceptance criteria, and scope.
- `02-research.md` -- codebase context and findings (if it exists; it will not
  exist for small tasks).
- `task-state.md` -- confirm we are in the planning phase.

Also read:
- The default coding guidelines from the task system (`defaults/coding-guidelines.md`).
- The project-level coding guidelines override (if it exists in the project
  config directory).
- The default commit message format (`defaults/commit-message-format.md`) or
  the project-level override.
- The project commands file (project-level override or default template).
- The language review profile(s) for the detected language(s).
- The project-level review profile additions (if they exist).

## Instructions

### Assemble Coding Guidelines

Build the coding guidelines for this task by combining:

1. **Default baseline** -- from `defaults/coding-guidelines.md` (always
   included).
2. **Project-level additions** -- appended from the project config directory's
   `coding-guidelines.md` (if it exists).
3. **Language review profile(s)** -- the relevant profile(s) from
   `review-profiles/` based on the language(s) detected in research (or
   inferred from the codebase for small tasks that skipped research).
4. **Project-level review profile additions** -- appended from the project
   config directory's `review-profiles/` (if they exist).
5. **Research-informed additions** -- any conventions, patterns, or rules you
   observed during research that are not already covered by the above.
6. **Commit message format** -- from the project-level override or the default.

Present the assembled guidelines to the user for review. They may add, remove,
or modify guidelines before they are finalized into the plan.

### Assess Task Splitting (if research was skipped)

For small tasks that skipped research, the planning phase may reveal that the
task is more complex than anticipated. If this happens:
1. Recommend upgrading the complexity assessment.
2. Optionally recommend splitting the task using the same mechanism described
   in Phase 2.
3. Wait for user approval before proceeding.

### Write the Plan

Write `03-plan.md` in the task directory:

```markdown
# Plan: <task title>

## Summary

<Brief restatement of what is being built or changed. One to three sentences.>

## Coding Guidelines

<The assembled coding guidelines, including the commit message format. This is
the complete set of rules that implementation and review will follow.>

## Implementation Steps

### Step 1: <short description>
**Difficulty/Risk**: <low | medium | high>

<What to do. Be specific -- name the files to create or modify, describe the
exact changes, explain the logic. This should be concrete enough to act on
without ambiguity.>

**Rationale**: <Why this step is needed, tying back to the definition.>

### Step 2: <short description>
...

## Testing Strategy

<Concrete test plan derived from the acceptance criteria in 01-definition.md.
For each relevant implementation step, describe how it will be verified.>

- **Test case 1**: <what to test, what to assert, how to run it>
- **Test case 2**: ...
- **Manual verification**: <any manual steps needed>

## Documentation Impact

<Documentation that needs to be created or updated as a result of this task.
READMEs, API docs, inline examples, changelogs, configuration references, etc.
If no documentation changes are needed, state that explicitly.>

## Breaking Changes

<Changes that affect public APIs, config formats, CLI interfaces, database
schemas, or other contracts with consumers. For each breaking change: what
breaks, who is affected, and what migration steps are needed. If there are no
breaking changes, state that explicitly.>

## Risks & Open Questions

<Anything uncertain or potentially problematic. Known unknowns, areas where
the plan might need to adapt, dependencies on external factors, etc.>
```

### Backlog Awareness

While creating the plan, you may identify related work that falls outside the
task's defined scope (e.g., prerequisite refactors that are not strictly
necessary, improvements to adjacent code, additional features that would be
nice but were not requested). When this happens:

1. Note the item and ask the user: "This seems out of scope for the current
   task. Should I add it to the backlog?"
2. If the user agrees, append the item to `backlog.md` in the tasks directory
   (create the file if it does not exist). Use the format described in
   `skill.md` § "Backlog".
3. If the user says it is actually in scope, incorporate it into the plan.

### Project-Specific Steps

If `phase-steps/03-plan.md` exists in the project config directory, read it
and execute the additional steps it describes now.

### Review with User

Present the plan and ask the user to review it. Point out:
- The assembled coding guidelines and any additions you proposed.
- Steps with high difficulty/risk ratings.
- The testing strategy and whether it sufficiently covers the acceptance
  criteria.
- Any breaking changes identified.
- Open questions that need the user's input.

This is a draft. The user will have the opportunity to revise it thoroughly in
Phase 4.

### Update State

Update `task-state.md`:

```markdown
- **Current Phase**: 4-review
- **Phase History**:
  - ...previous entries...
  - Phase 3 (Plan): completed <timestamp>
```

## Next Steps

Proceed to Phase 4 (Review/Revise with User).
