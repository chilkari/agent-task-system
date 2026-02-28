# Phase 2: Research

You are researching the codebase to build sufficient context for creating a
high-quality implementation plan. This phase is skipped for small tasks.

## Prerequisites

Read the following files from the task directory:
- `01-definition.md` -- understand what the task is and its scope.
- `task-state.md` -- confirm we are in the research phase.

## Instructions

### Propose a Research Plan

Before exploring, tell the user what you plan to investigate. Structure your
proposal as:

1. **Files and modules to examine** -- specific files, directories, or patterns
   you plan to look at and why.
2. **Questions to answer** -- what you need to understand to create a good plan
   (e.g., "How does the auth middleware chain work?", "What patterns do existing
   tests follow?").
3. **Scope of exploration** -- based on complexity:
   - **Medium**: Targeted. Focus on the specific files and modules that will be
     touched by this task.
   - **Large**: Broad. Map out architecture, trace data flows, examine testing
     patterns, and understand conventions across the codebase.

Ask the user: "Does this cover everything, or are there areas I should also
explore?" Wait for the user to confirm or adjust the plan before proceeding.

### Explore the Codebase

Systematically work through your research plan. For each area:
- Read the relevant files.
- Understand the patterns and conventions in use.
- Note dependencies, coupling, and potential risks.
- Identify any external documentation or references that would be useful.

### Detect Languages

Identify the primary programming language(s) involved in this task. This
determines which language review profiles will be activated for planning, code
review, and final polish phases.

### Assess Task Splitting

After completing research, evaluate whether the task should be split. Consider
splitting if:
- The task touches many unrelated parts of the codebase.
- The task has independent sub-goals that could be delivered separately.
- The estimated effort is significantly larger than the definition anticipated.
- Splitting would reduce risk by isolating complex changes.

If you recommend splitting:
1. Propose the split with clear boundaries for each sub-task.
2. Explain why splitting would produce better results.
3. Wait for the user to approve, adjust, or decline.
4. If approved, create stub `01-definition.md` files for each new task in
   separate task directories. Update the current task's scope or close it in
   favor of the sub-tasks.

### Write the Research Document

Write `02-research.md` in the task directory:

```markdown
# Research: <task title>

## Relevant Files & Modules

<List of files and modules examined, with brief descriptions of what each does
and why it is relevant to this task.>

## Architecture & Patterns

<How the relevant parts of the codebase are structured. Conventions in use:
naming, error handling, testing patterns, module organization, etc.>

## Dependencies & Risks

<Things that could complicate implementation. Tight coupling, shared state,
performance concerns, backward compatibility requirements, etc.>

## Key Decisions & Constraints

<Existing design decisions the implementation must respect. Patterns that must
be followed for consistency. Constraints from the architecture or dependencies.>

## External References

<Links to relevant library docs, API references, or other external resources.
For each link, a brief note on why it is relevant. Do not copy external content
verbosely -- just provide the link and a summary of what is useful there.>

## Language(s) Detected

<Primary language(s) for this task and which review profiles apply.>
```

### Review with User

Present the research findings to the user. Ask:
- Did I miss anything important?
- Are there any misunderstandings in my findings?
- Do they agree with the language detection?
- If a split was recommended, do they agree with the proposed boundaries?

Revise until the user is satisfied.

### Update State

Update `task-state.md`:

```markdown
- **Current Phase**: 3-plan
- **Language(s)**: <detected language(s)>
- **Phase History**:
  - ...previous entries...
  - Phase 2 (Research): completed <timestamp>
```

## Next Steps

Proceed to Phase 3 (Plan).
