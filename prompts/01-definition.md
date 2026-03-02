# Phase 1: Task Definition

You are starting a new task. Your goal is to capture a clear, scoped problem
statement and acceptance criteria through an interactive conversation with the
user.

## Instructions

### Gather Information

Ask the user to describe the task in their own words. Listen for:
- What is the problem or need?
- What should change as a result of this task?
- What does "done" look like?

Ask clarifying questions to fill in gaps. Do not make assumptions about scope
or requirements -- ask. Good clarifying questions include:
- What is in scope and what is explicitly out of scope?
- Are there specific constraints (performance, compatibility, dependencies)?
- What are the expected inputs and outputs?
- How should this be tested or verified?
- Is there existing code or prior art related to this task?

### Assess Complexity

Based on the user's description and your understanding of the codebase,
recommend a complexity level:

- **Small**: The task is straightforward, touches a small number of files, and
  the necessary context is obvious or easily gathered during planning. Research
  phase will be skipped.
- **Medium**: The task requires understanding several modules or patterns in the
  codebase, but the scope is well-defined. Research will be targeted and
  focused.
- **Large**: The task is complex, touches many parts of the codebase, requires
  understanding architecture and data flows, or involves significant risk.
  Research will be thorough and broad.

Explain your reasoning for the complexity assessment. The user may override it.

### Write the Definition

Create the task directory at `<tasks-dir>/<task-slug>/` where `<task-slug>` is
a short, lowercase, hyphenated name derived from the task description.

Write `01-definition.md` in the task directory with the following sections:

```markdown
# Task: <descriptive title>

## Problem Statement

<What needs to change and why. Be specific about the current state and the
desired state.>

## Acceptance Criteria

<Concrete, testable conditions that must be true when the task is complete.
Each criterion should be verifiable. Include general testing expectations
(e.g., "unit tests for new functions", "manual verification of CLI output").
These remain high-level here and become specific in the planning phase.>

- [ ] <criterion 1>
- [ ] <criterion 2>
- [ ] ...

## Scope

### In Scope
- <what this task will do>

### Out of Scope
- <what this task will explicitly not do>

## Complexity Assessment

**Level**: <small | medium | large>

**Reasoning**: <why this complexity level was chosen>
```

### Project-Specific Steps

If `phase-steps/01-definition.md` exists in the project config directory, read
it and execute the additional steps it describes now.

### Review with User

Present the drafted definition to the user. Ask them to review each section:
- Is the problem statement accurate and complete?
- Are the acceptance criteria correct and sufficient?
- Is the scope right -- anything missing or anything that should be excluded?
- Do they agree with the complexity assessment?

Revise until the user approves the definition.

### Update State

Create or update `task-state.md` in the task directory:

```markdown
# Task State

- **Task**: <task slug>
- **Current Phase**: 2-research (or 3-plan if complexity is small)
- **Complexity**: <small | medium | large>
- **Language(s)**: <to be determined>
- **Phase History**:
  - Phase 1 (Definition): completed <timestamp>
```

## Next Steps

- If complexity is **small**: Proceed to Phase 3 (Plan). Skip research.
- If complexity is **medium** or **large**: Proceed to Phase 2 (Research).
