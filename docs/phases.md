# Phase Reference

Each phase has a prompt template in `prompts/` that the agent follows. This
document describes what each phase does, what it reads and writes, and tips
for getting the best results.

## Phase Flow

```
Phase 1 → Phase 2 → Phase 3 → Phase 4 → Phase 5 → Phase 6 → Phase 7 → Phase 8 → Phase 9
Define    Research   Plan       Review    Implement  Code       Review    Polish    Retro
          (skip if              (loop)     (auto→6)  Review     Response
           small)                                               (loop→6)
```

**Transitions:**
- After Phase 1: Small tasks skip to Phase 3. Medium/large go to Phase 2.
- After Phase 4: Loops until you approve the plan.
- After Phase 5: Automatically continues to Phase 6 (no user prompt).
- After Phase 7: Loops back to Phase 6 for re-review until clean.

## Phase 1: Definition

**Reads**: `issue-trackers.md` (resolved via file resolution), issue tracker
API (if an issue ticket is provided)
**Writes**: `01-definition.md`, `task-state.md`
**Interaction**: Interactive

The agent first asks if there is an issue ticket to work from (Linear or
Gitea). If provided, it fetches the issue details (title, description, labels,
status, comments) and uses them to seed the task definition. The agent then
drafts a definition with:
- Source issue (if fetched)
- Problem statement
- Acceptance criteria (with testing expectations)
- Scope (in/out)
- Complexity assessment (small/medium/large)

**Tips:**
- Be specific about what "done" looks like. Vague criteria lead to vague
  implementations.
- If unsure about complexity, err on the side of larger. It is easier to skip
  research than to discover you needed it mid-implementation.
- If you have a Linear or Gitea issue, provide it at the start to save time.
  The agent will pull in the description and comments so you do not have to
  re-describe the problem.

## Phase 2: Research

**Reads**: `01-definition.md`, `.codebase-knowledge.md` (if exists)
**Writes**: `02-research.md`, `.codebase-knowledge.md`, `task-state.md`
**Interaction**: Light — approve the research plan, review findings

The agent reads the codebase knowledge base (if it exists from previous tasks)
to understand what is already known, then proposes a focused research plan
targeting gaps. After you approve the findings, the agent proposes updates to
the knowledge base. Skipped for small tasks.

**Tips:**
- Point the agent to files you know are relevant. It saves time.
- If research reveals the task should be split, seriously consider it. Smaller
  tasks produce better results.
- Review knowledge base updates carefully — bad entries affect every future
  task.

## Phase 3: Plan

**Reads**: `01-definition.md`, `02-research.md` (if exists), defaults and
profiles
**Writes**: `03-plan.md`, `task-state.md`
**Interaction**: Light — review the initial draft

The agent creates a detailed plan with coding guidelines, implementation
steps, testing strategy, documentation impact, and breaking changes.

**Tips:**
- Pay attention to the coding guidelines section. This is your chance to add
  project-specific rules the agent will follow during implementation.
- Check the testing strategy. Does it actually verify the acceptance criteria?
- Review difficulty/risk ratings on each step.

## Phase 4: Review/Revise

**Reads**: `03-plan.md`, `04-review-notes.md` (if exists)
**Writes**: `03-plan.md` (revisions), `04-review-notes.md` (append-only),
`task-state.md`
**Interaction**: Interactive — the most collaborative phase

You and the agent refine the plan until you approve it. This may span multiple
sessions.

**Tips:**
- Do not rush this phase. A good plan is the single biggest predictor of a
  good implementation.
- Challenge the agent's assumptions. Ask "what about X?" for edge cases.
- Everything discussed is preserved in `04-review-notes.md` for cross-session
  continuity.

## Phase 5: Implement

**Reads**: `03-plan.md`, `02-research.md` (if exists), project commands,
`05-implementation.md` (if resuming)
**Writes**: `05-implementation.md`, `task-state.md`
**Interaction**: Minimal — only if blocked

The agent runs pre-flight checks (clean git, passing tests), then implements
each step with commits.

**Tips:**
- Make sure your project commands are correct before starting. Incorrect test
  or build commands cause thrashing.
- Progress is tracked incrementally. If the session crashes, the agent knows
  exactly where it left off.

## Phase 6: Code Review

**Reads**: `01-definition.md`, `03-plan.md`, `02-research.md` (if exists),
`05-implementation.md`, language review profiles
**Writes**: `06-code-review.md` (or `06-code-review-rN.md`), `task-state.md`
**Interaction**: Light — review the findings

The agent switches to a reviewer role and critically examines all changes
against acceptance criteria, coding guidelines, and language-specific
checklists.

**Tips:**
- Treat findings seriously. The agent is deliberately looking for problems
  from a different perspective than when it wrote the code.
- Critical and important findings should almost always be fixed.

## Phase 7: Review Response

**Reads**: `06-code-review.md` (latest), `03-plan.md`
**Writes**: `06-code-review.md` (dispositions), `task-state.md`
**Interaction**: Interactive — approve fixes, decide on suggestions

The agent presents its fix plan for critical/important items (approve before
it proceeds) and walks through suggestions one by one for your decision.

**Tips:**
- When rejecting a suggestion, give a reason. It helps the agent understand
  your preferences and is documented for the record.
- After fixes, the agent re-reviews. Usually 2-3 rounds is enough.

## Phase 8: Final Polish

**Reads**: `01-definition.md`, `03-plan.md`, project commands, review profiles
**Writes**: `08-final-polish.md`, `09-summary.md`, `task-state.md`
**Interaction**: None — fully automated

The agent runs linters/formatters, checks for AI artifacts (unicode
characters, verbose comments, duplication), verifies documentation, runs
tests, and writes the final summary.

**Tips:**
- Review `09-summary.md` after completion for a clean overview of what was
  delivered.

## Phase 9: Retrospective

**Reads**: All task artifacts, current project config files
**Writes**: `10-retrospective.md`, project config files (if changes accepted),
`task-state.md`
**Interaction**: Interactive — agent presents suggestions, user approves or declines each

The agent mines the task for signals about what could improve: recurring code
review findings that should become coding guidelines, missing review profile
checks, incorrect project commands, or conventions it had to learn mid-task.
You accept or decline each suggestion.

**Tips:**
- Pay attention to suggestions drawn from code review findings — these are
  patterns that will recur and are worth codifying.
- Process-level observations about the workflow are captured as notes for your
  reference.

## Backlog

During research, planning, implementation, and code review, the agent may
discover work that is outside the current task's scope — technical debt,
improvement opportunities, or related features. Rather than silently ignoring
these or scope-creeping the current task, the agent surfaces them and asks
whether to add them to `backlog.md` in the tasks directory.

The backlog is a shared file across all tasks. It is a simple append-only list
that the user manages and prioritizes outside the task system. See `skill.md`
§ "Backlog" for the file format.

## Project-Specific Phase Steps

Any phase can be extended with project-specific steps by creating files in the
project config directory under `phase-steps/`. For example,
`phase-steps/08-final-polish.md` adds extra steps to the polish phase.

This is the recommended way to customize individual phases per-project without
modifying the task system. See
[configuration.md](configuration.md) § `phase-steps/` for injection points
and examples.

## Cross-Session Workflow

Tasks can span multiple sessions. All progress is saved to disk incrementally.

**Pausing**: Just stop. The conversation can end at any point.

**Resuming**: Say "resume task" or "continue task". The agent reads
`task-state.md`, determines the current phase, reads the relevant artifacts,
and summarizes where things stand before continuing.

**Multiple tasks**: If you have several tasks in progress, the agent asks
which one to resume. Use "task list" to see all tasks and their phases.

## Task Directory Structure

A completed task directory contains:

| File | Phase | Purpose |
|------|-------|---------|
| `01-definition.md` | 1 | Problem statement, acceptance criteria, scope |
| `02-research.md` | 2 | Codebase findings (omitted for small tasks) |
| `03-plan.md` | 3 | Implementation plan, coding guidelines, testing strategy |
| `04-review-notes.md` | 4 | Append-only log of plan review discussions |
| `05-implementation.md` | 5 | Step-by-step progress tracker |
| `06-code-review.md` | 6 | First code review findings |
| `06-code-review-rN.md` | 7 | Subsequent review rounds |
| `08-final-polish.md` | 8 | Cleanup notes, lint/format/test results |
| `09-summary.md` | 8 | Final summary of what was delivered |
| `10-retrospective.md` | 9 | Config changes and process notes |
| `task-state.md` | 1+ | Phase tracking, timestamps, complexity |

The tasks directory (parent of task subdirectories) may also contain:

| File | Purpose |
|------|---------|
| `.codebase-knowledge.md` | Cumulative codebase knowledge base |
| `backlog.md` | Out-of-scope items discovered during tasks |
