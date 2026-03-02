# Phase 2: Research

You are researching the codebase to build sufficient context for creating a
high-quality implementation plan. This phase is skipped for small tasks.

## Prerequisites

Read the following files from the task directory:
- `01-definition.md` -- understand what the task is and its scope.
- `task-state.md` -- confirm we are in the research phase.

Also read:
- `.codebase-knowledge.md` from the tasks directory (if it exists). This is a
  cumulative knowledge base built from previous tasks' research. Use it as a
  head start -- it tells you what is already known about the codebase's
  architecture, patterns, modules, and conventions.

## Instructions

### Review Existing Knowledge

If `.codebase-knowledge.md` exists, read it before proposing your research
plan. Identify:
- What is already documented that is relevant to this task -- you can rely on
  this instead of re-exploring it (but spot-check anything critical).
- What gaps exist -- areas relevant to this task that the knowledge base does
  not cover or covers only superficially.
- What might be stale -- entries that were last verified a long time ago or
  that seem inconsistent with what you can see in the file tree.

When presenting your research plan to the user, note what you already know
from the knowledge base and what you still need to investigate. This makes the
research scope transparent and shows the time savings.

### Propose a Research Plan

Before exploring, tell the user what you plan to investigate. Structure your
proposal as:

1. **What the knowledge base already covers** -- relevant sections from
   `.codebase-knowledge.md` that apply to this task. Note any entries you plan
   to spot-check because they are critical to the task or may be stale. If no
   knowledge base exists, state that and note that all areas will need fresh
   exploration.
2. **Files and modules to examine** -- specific files, directories, or patterns
   you plan to look at and why. Focus on areas **not** covered by the knowledge
   base.
3. **Questions to answer** -- what you need to understand to create a good plan
   (e.g., "How does the auth middleware chain work?", "What patterns do existing
   tests follow?").
4. **Scope of exploration** -- based on complexity:
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

### Update Codebase Knowledge Base

After the user approves the research, update the codebase knowledge base
(`.codebase-knowledge.md` in the tasks directory) with durable, reusable
findings from this research. This step builds cumulative knowledge that saves
time on future tasks.

**If the knowledge base does not exist yet**, create it with the findings from
this task:

```markdown
# Codebase Knowledge

Last updated: <date> (after task: <task-slug>)

## Architecture Overview
<High-level module map, major boundaries, data flow patterns discovered
during this research.>

## Key Modules
### <module-name>
- **Location**: <path>
- **Purpose**: <what it does>
- **Key patterns**: <how it works internally>
- **Public API**: <main exports/interfaces>
- **Last verified**: <date>

## Conventions & Patterns
<Naming conventions, error handling patterns, logging approach, dependency
injection style, etc. -- things discovered during research that are not
coding guidelines but are important context for understanding the codebase.>

## Testing Patterns
<How tests are structured, what frameworks are used, test file naming,
fixture patterns, mock strategies.>

## External Dependencies
<Key libraries, their versions, why they are used, links to relevant docs.>

## Infrastructure & Configuration
<Build pipeline, deployment, environment setup, config file formats.>
```

**If the knowledge base already exists**, propose updates:

1. **New entries** -- modules, patterns, or architectural knowledge discovered
   during this research that are not yet in the knowledge base.
2. **Corrections** -- entries that were found to be stale or inaccurate during
   spot-checking.
3. **Enrichments** -- existing entries that can be expanded with additional
   detail from this research.

Present the proposed updates to the user: "I'd like to update the codebase
knowledge base with the following findings from this research: [list]. This
will help future tasks start faster. OK to proceed?"

Apply approved updates. Update the "Last updated" line at the top of the file.
For module entries, update the "Last verified" date on any entries that were
spot-checked and confirmed accurate, even if no content changed.

**What to include**: Architecture, module descriptions, stable patterns,
conventions, key dependencies, testing approaches -- things that are properties
of the codebase itself and change slowly.

**What to exclude**: Task-specific risk assessments, task-specific file
relevance, anything that only matters in the context of the current task. These
belong in `02-research.md`, not the shared knowledge base.

### Backlog Awareness

During research, you may discover issues, technical debt, or improvement
opportunities that are clearly outside the scope of the current task (e.g.,
duplicated logic that predates this task, deprecated patterns, missing tests
for unrelated modules). When you encounter such items:

1. Note the item and ask the user: "This seems out of scope for the current
   task. Should I add it to the backlog?"
2. If the user agrees, append the item to `backlog.md` in the tasks directory
   (create the file if it does not exist). Use the format described in
   `skill.md` § "Backlog".
3. If the user says it is actually in scope, incorporate it into the research
   findings and it will feed into the plan.

Do not silently ignore out-of-scope discoveries -- surface them so nothing is
lost.

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
