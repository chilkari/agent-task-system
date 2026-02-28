# Task System Design Plan

A portable, spec-driven task system for AI-assisted development. This system
guides an AI agent and user through a repeatable process of definition,
research, planning, implementation, and review for any development task.

## Directory Structure

```
task-system/
  README.md                     # Overview, setup instructions
  skill.md                      # Portable AI skill definition (entry point)
  task-system-design-plan.md    # This document
  prompts/
    00-setup.md                 # Prompt template for one-time project setup
    01-definition.md            # Prompt template for Phase 1
    02-research.md              # Prompt template for Phase 2
    03-plan.md                  # Prompt template for Phase 3
    04-review.md                # Prompt template for Phase 4
    05-implement.md             # Prompt template for Phase 5
    06-code-review.md           # Prompt template for Phase 6
    07-review-response.md       # Prompt template for Phase 7
    08-final-polish.md          # Prompt template for Phase 8
  defaults/
    coding-guidelines.md        # Default coding guidelines (always-on baseline)
    commit-message-format.md    # Default commit message conventions
    project-commands.md         # Template for common project commands
  review-profiles/
    typescript.md               # TypeScript-specific review checklist
    bash.md                     # Bash-specific review checklist
```

## Per-Task Output

Each task lives in the consuming project's repo at a user-configured location
(e.g., `docs/tasks/`, `.dev/tasks/`). Each task gets its own subdirectory named
with a slug:

```
<tasks-dir>/<task-slug>/
  01-definition.md
  02-research.md          # Omitted for small tasks
  03-plan.md
  04-review-notes.md      # Append-only log
  05-implementation.md
  06-code-review.md       # Subsequent reviews: 06-code-review-r2.md, r3, etc.
  08-final-polish.md
  09-summary.md
  task-state.md           # Tracks current phase for resume
```

## Project-Level Overrides

Each consuming repo can have a project config directory that provides
project-specific additions and overrides. The config directory path is
configurable (just like the tasks directory). It mirrors the task system
structure:

```
<project-config-dir>/
  config.md                    # Project-level settings
  coding-guidelines.md         # Additional guidelines merged with defaults
  commit-message-format.md     # Override the default format entirely
  project-commands.md          # Override with actual project commands
  review-profiles/
    typescript.md              # Additional checks merged with defaults
    bash.md
    python.md                  # Add a language the base system doesn't ship
```

**Merge behavior**:

- **Coding guidelines**: Start with `task-system/defaults/coding-guidelines.md`,
  then **append** project-level additions. The project file adds rules; it does
  not replace the defaults.
- **Commit message format**: If project-level file exists, it **replaces** the
  default entirely.
- **Project commands**: If project-level file exists, it **replaces** the
  default template entirely.
- **Review profiles**: Start with `task-system/review-profiles/<lang>.md`, then
  **append** project-level additions. If the project defines a language that
  does not exist in the base system, use it as-is.

## Portability

The task system is entirely markdown-based with no code dependencies. The
`skill.md` file is the single entry point that an AI agent configuration
references. It works with any AI coding tool that supports custom instructions
(pi skills, .cursorrules, CLAUDE.md, etc.).

A user wires it into their project by adding to their AI config:

```
Follow the task system defined in /path/to/task-system/skill.md
Project config: .task-system/
Tasks directory: docs/tasks/
```

Both the project config directory and tasks directory paths are fully
configurable per-repo. They can live wherever makes sense for the project.

---

## Setup (One-Time per Repo)

**Goal**: Guide the user through a one-time interactive setup that creates the
project config directory, populates starter files, and outputs the correct AI
config lines for their agent.

**Prompt file**: `prompts/00-setup.md`

**Flow**:

1. Agent asks the user:
   - Where should the project config directory live? (default: `.task-system/`)
   - Where should tasks be stored? (default: `tasks/`)
   - Which AI agent are they using? (pi, Claude/CLAUDE.md, Cursor, other)
2. Agent inspects the repo and auto-detects what it can:
   - **Project commands**: Looks for `package.json`, `Makefile`,
     `pyproject.toml`, `Cargo.toml`, etc. and suggests pre-filled commands
     (e.g., `npm test`, `make lint`).
   - **Language profiles**: Detects primary languages in the repo and notes
     which review profiles will apply.
   - **Commit format**: Checks recent git log for existing commit message
     conventions and suggests a matching format.
3. Agent creates the project config directory with starter files:
   - `config.md` -- with the tasks directory path recorded.
   - `coding-guidelines.md` -- commented template showing where to add
     project-specific rules, with any auto-detected conventions noted.
   - `commit-message-format.md` -- pre-filled with the detected convention, or
     a few common formats commented out for the user to pick and customize.
   - `project-commands.md` -- pre-filled with auto-detected commands where
     possible, with remaining slots shown as commented placeholders.
4. Agent creates the tasks directory (empty, with a `.gitkeep`).
5. Agent outputs the exact lines to add to the user's AI config file, tailored
   to the agent they specified in step 1.
6. Agent walks the user through reviewing each generated file and making any
   adjustments.

**Design notes**:

- This is a one-time operation per repo. The setup prompt detects if setup has
  already been completed (config directory exists) and warns before overwriting.
- The auto-detection is best-effort. The user reviews and corrects everything
  before setup is finalized.
- The goal is to get from "I cloned the task system" to "I can start my first
  task" in a 2-minute interactive conversation.

---

## Phase 1: Task Definition

**Goal**: Capture a clear, scoped problem statement and acceptance criteria.

**Prompt file**: `prompts/01-definition.md`

**Flow**:

1. Agent asks the user to describe the task in their own words.
2. Agent asks clarifying questions -- scope, constraints, expected
   inputs/outputs, what "done" looks like.
3. Agent drafts `01-definition.md` in the task directory with the following
   sections:
   - **Problem Statement** -- what needs to change and why.
   - **Acceptance Criteria** -- concrete, testable conditions for "done".
     Includes general testing expectations (e.g., "unit tests for new
     functions", "manual verification of CLI output"). These remain high-level
     here and become specific in the planning phase.
   - **Scope** -- what is in scope and what is explicitly out of scope.
   - **Complexity Assessment** -- agent's recommendation of small, medium, or
     large, with reasoning. This determines the depth of subsequent phases:
     - **Small**: Research is skipped. Plan and testing are brief.
     - **Medium**: Targeted research. Moderate plan detail.
     - **Large**: Thorough research. Granular plan with detailed testing.
4. User reviews and approves or revises the definition.
5. Agent updates `task-state.md` to record completion of Phase 1.

**Design notes**:

- The acceptance criteria produced here are intentionally general. They serve as
  the "contract" that the code review phase (Phase 6) verifies against.
- The complexity assessment drives behavior in Phases 2 and 3 but can be
  overridden by the user.

---

## Phase 2: Research

**Goal**: Build sufficient codebase context for the agent to create a
high-quality implementation plan. Skipped entirely for small tasks.

**Prompt file**: `prompts/02-research.md`

**Flow**:

1. Agent reads `01-definition.md`.
2. Agent proposes a research plan to the user: "I plan to look at X, Y, Z --
   anything else I should explore?" User can guide, add, or adjust.
3. Agent explores the codebase (reading files, grepping for patterns,
   understanding structure).
4. Agent writes `02-research.md` in the task directory with:
   - **Relevant Files & Modules** -- what was found and where, with brief
     descriptions of purpose.
   - **Architecture & Patterns** -- how the relevant parts of the codebase are
     structured, conventions in use (naming, error handling, testing patterns).
   - **Dependencies & Risks** -- things that could complicate implementation
     (tight coupling, shared state, etc.).
   - **Key Decisions & Constraints** -- existing design decisions the
     implementation must respect.
   - **External References** -- links to relevant library docs, API references,
     or other external resources discovered during research. Brief notes on why
     each is relevant, but no verbose copies of external content.
   - **Language(s) Detected** -- the primary language(s) involved in this task.
     This determines which review profile(s) are activated for Phases 3, 6,
     and 8.
5. Agent assesses whether the task should be split. If during research the
   agent discovers the task is significantly larger or more complex than the
   definition anticipated, it recommends splitting into multiple smaller tasks.
   The agent proposes the split with suggested task boundaries. The user
   approves, adjusts, or declines the split. If approved, the agent creates
   stub `01-definition.md` files for each new task and the current task is
   either narrowed in scope or closed in favor of the sub-tasks.
6. User reviews the research -- can point the agent to areas it missed or
   correct misunderstandings.
7. Agent updates `task-state.md`.

**Scaling by complexity**:

- **Medium**: Targeted exploration of the specific files and modules that will be
  touched. Same document structure as large but shorter and more focused.
- **Large**: Broad exploration -- map out architecture, trace data flows,
  identify dependencies, read tests, understand conventions across the codebase.
  Thorough output.

---

## Phase 3: Plan

**Goal**: Produce a detailed, step-by-step implementation plan that the agent
can follow mechanically in Phase 5.

**Prompt file**: `prompts/03-plan.md`

**Flow**:

1. Agent reads `01-definition.md` and `02-research.md` (if it exists).
2. Agent drafts `03-plan.md` in the task directory with:

   **Summary** -- brief restatement of what is being built or changed.

   **Coding Guidelines** -- assembled from three sources:
   - The default baseline from `defaults/coding-guidelines.md` (always
     included).
   - The detected language review profile(s) from `review-profiles/`
     (auto-selected based on research or task context).
   - Any additions or modifications the agent proposes based on patterns
     observed during research.
   - Includes **commit message format** from `defaults/commit-message-format.md`,
     presented for user review/customization.
   - The agent surfaces all of these together for the user to review, add to,
     remove from, or modify.

   **Implementation Steps** -- ordered, numbered steps. Each step includes:
   - What file(s) to create or modify (natural language description).
   - What specifically to do -- concrete enough for the agent to act on without
     ambiguity.
   - Why -- brief rationale tying back to the definition.
   - **Estimated difficulty/risk** -- per step, to help the user understand where
     complexity lives.

   **Testing Strategy** -- concrete test plan derived from the general testing
   expectations in `01-definition.md`. Specific test cases, what to assert, how
   to run them. Should cover verification of each implementation step where
   applicable.

   **Documentation Impact** -- any documentation that needs to be created or
   updated as a result of this task. Includes READMEs, API docs, inline
   examples, changelogs, configuration references, or any other docs that
   consumers rely on. If no documentation changes are needed, this section
   explicitly states that.

   **Breaking Changes** -- any changes that affect public APIs, config formats,
   CLI interfaces, database schemas, or other contracts with consumers. For
   each breaking change, include what breaks, who is affected, and what
   migration steps are needed. If there are no breaking changes, this section
   explicitly states that.

   **Risks & Open Questions** -- anything the agent is uncertain about or that
   could go wrong.

3. If the plan reveals the task is significantly larger than expected (e.g.,
   research was skipped for a small task but planning reveals hidden
   complexity), the agent may recommend splitting the task -- same mechanism as
   the split decision point in Phase 2.
4. Plan is written and handed off to the user for review (Phase 4).
5. Agent updates `task-state.md`.

**Scaling by complexity**:

- **Small**: Fewer steps, possibly just a handful. Testing may be simple (e.g.,
  "run the script and verify output").
- **Medium/Large**: More granular steps. Testing strategy is more detailed with
  specific cases.

---

## Phase 4: Review/Revise with User

**Goal**: Interactive loop where the user and agent refine the plan until it is
ready for implementation.

**Prompt file**: `prompts/04-review.md`

**Flow**:

1. Agent presents `03-plan.md` to the user and asks for feedback.
2. User can:
   - Ask questions about specific steps.
   - Request changes (reorder, add, remove, modify steps).
   - Challenge the testing strategy.
   - Adjust coding guidelines.
   - Raise concerns about risks.
3. Agent revises `03-plan.md` in place to reflect the current agreed-upon state.
4. After each round of revisions, agent appends to `04-review-notes.md` with:
   - **Date/session marker** -- when this round occurred.
   - **What was discussed** -- brief summary of feedback points.
   - **What changed** -- concrete list of revisions made to the plan.
   - **Open items** -- anything still unresolved heading into the next round.
5. Loop continues until the user explicitly approves the plan.
6. On approval, agent appends a final "Plan Finalized" entry to
   `04-review-notes.md` summarizing the overall journey from initial draft to
   final plan.
7. Agent updates `task-state.md`.

**Design notes**:

- `03-plan.md` is the living document that always reflects the current state of
  the plan.
- `04-review-notes.md` is an append-only chronological log. It serves as an
  audit trail and provides cross-session continuity. If the user returns in a
  new session, the agent reads this file to understand where the review left
  off, what has been decided, and what is still open.
- The agent should be opinionated during review -- push back if a user's
  suggestion would create problems -- but ultimately defer to the user.
- This phase is expected to be the lengthiest and most interactive part of the
  process. The append-only log and incremental updates to the plan ensure that
  progress is never lost across sessions.

---

## Phase 5: Implement

**Goal**: Execute the finalized plan step by step. Should require minimal user
intervention.

**Prompt file**: `prompts/05-implement.md`

**Flow**:

1. Agent reads `03-plan.md` (finalized), `02-research.md` (if it exists), and
   the project commands file for context.
2. **Pre-flight checks** -- before any code changes, the agent verifies:
   - Git working tree is clean (no uncommitted changes). If dirty, the agent
     stops and asks the user to commit or stash.
   - Existing tests pass (using project commands). If tests fail, the agent
     stops and reports -- these are pre-existing failures that must be resolved
     before implementation begins.
   - Build succeeds (if a build command is configured). Same -- pre-existing
     build failures must be resolved first.
   - Records the current git commit SHA as the **rollback point** in
     `05-implementation.md`. If the entire task needs to be reverted, this is
     the commit to return to.
3. Agent creates `05-implementation.md` with all steps listed, initially marked
   as not started, and the pre-flight rollback point recorded at the top.
4. For each step:
   a. Agent marks the step as `in-progress` in `05-implementation.md` and
      records the current commit SHA as the step's rollback point (written to
      disk immediately, before starting work -- this enables crash recovery
      and per-step rollback).
   b. Agent implements the step, following the coding guidelines from the plan.
   c. Agent marks the step as `complete` in `05-implementation.md` with:
      - What was done -- files created/modified, brief summary.
      - Any deviations from the plan and why.
   d. Agent commits with a message following the commit message format from the
      coding guidelines.
5. If the agent encounters a blocker (ambiguity, unexpected issue):
   - Marks the step as `blocked` in `05-implementation.md` with details.
   - Asks the user for guidance before continuing.
6. After all steps are complete, agent runs the testing strategy from the plan:
   - If tests pass, documents results in `05-implementation.md`.
   - If tests fail, agent attempts to fix autonomously and retries.
   - Agent monitors for thrashing (same failure repeating, oscillating between
     different failures, no meaningful progress). When detected, it stops,
     documents what it tried, and asks the user for help.
7. Agent updates `task-state.md`.

**Status markers in `05-implementation.md`**:

- Not started (no marker -- just the step description from the plan)
- `in-progress` -- agent is actively working on this step
- `complete` -- step is done, summary of what was done follows
- `blocked` -- agent needs user input, details follow

**Cross-session resilience**: Because the file is updated before and after each
step, a crash or new session allows the agent to read `05-implementation.md` and
understand exactly what has been completed, what was in-progress (needs
re-evaluation), and what has not been started.

---

## Phase 6: Code Review

**Goal**: Agent switches roles to become a critical, experienced code reviewer
examining all changes made during implementation.

**Prompt file**: `prompts/06-code-review.md`

**Flow**:

1. Agent reads the full diff of changes and the complete files that were
   modified. The review focuses on the diff, but the agent understands the
   surrounding code to catch issues where changes may have unintended impact on
   other parts of the codebase.
2. Agent reviews against:
   - The acceptance criteria from `01-definition.md` -- does the implementation
     actually solve the problem?
   - The implementation plan from `03-plan.md` -- does the implementation match
     what was planned?
   - The coding guidelines from `03-plan.md` -- are all guidelines followed?
   - The detected language review profile(s) from `review-profiles/`.
   - General code quality: readability, maintainability, error handling, edge
     cases, naming, duplication.
   - Testing completeness -- are the tests sufficient?
   - Breaking changes -- were all breaking changes identified in the plan
     actually handled? Were any new breaking changes introduced that were not
     anticipated?
   - Documentation impact -- were all documentation updates identified in the
     plan completed? Are there any additional docs that need updating based on
     the actual implementation?
3. Agent writes `06-code-review.md` with findings organized by severity:
   - **Critical** -- bugs, security issues, logic errors. Must fix.
   - **Important** -- significant quality issues, missing error handling, poor
     abstractions. Should fix.
   - **Suggestions** -- style nits, alternative approaches, minor improvements.
     Discuss with user.
4. Each finding includes:
   - File and location.
   - What the issue is.
   - Recommended fix.
5. Agent updates `task-state.md`.

**Language-specific review**: The review profile for the detected language(s) is
injected into the review process. These profiles contain curated checklists of
language-specific anti-patterns and best practices (see `review-profiles/`).

---

## Phase 7: Respond to Code Review Suggestions

**Goal**: Address code review findings, with user input on suggestions.

**Prompt file**: `prompts/07-review-response.md`

**Flow**:

1. Agent presents `06-code-review.md` to the user.
2. **Critical and Important items**: Agent presents its fix plan confidently
   ("I plan to fix these critical/important issues -- proceed?"). User approves
   before the agent begins fixing.
3. **Suggestions**: Agent walks through each suggestion with the user, who
   decides to accept, reject, or modify. Rejections require a reason, which is
   documented.
4. Agent implements the accepted changes.
5. Agent commits after each logical group of fixes, following the commit message
   format.
6. Agent updates `06-code-review.md` with the disposition of each finding:
   - Fixed
   - Accepted (for suggestions taken)
   - Rejected -- with the user's stated reason
7. Agent loops back to Phase 6 for a fresh code review. The new review is
   written to a numbered file: `06-code-review-r2.md`, `06-code-review-r3.md`,
   etc. This preserves history and shows how many review rounds were needed.
8. Loop continues until the user is satisfied that the review is clean or
   remaining items are acceptable.
9. Agent updates `task-state.md`.

---

## Phase 8: Final Polish

**Goal**: Last pass to clean up rough edges before the task is complete.

**Prompt file**: `prompts/08-final-polish.md`

**Flow**:

1. Agent runs linters and formatters using the project commands and fixes issues.
2. Agent scans for common AI-generated code indicators:
   - Overly verbose comments (e.g., "This function does X" above a function
     clearly named X).
   - Placeholder or TODO comments that should not ship.
   - Inconsistent naming that does not match codebase conventions.
   - Unnecessary comments that explain obvious code.
   - Generic variable names that snuck through.
   - Comments on non-exported functions (remove unless they clarify
     difficult-to-understand code).
3. Agent checks for common AI text artifacts:
   - No unicode special characters -- no smart/curly quotes, no em-dashes, no
     other non-ASCII punctuation. Favor ASCII equivalents throughout.
4. Agent checks for duplication:
   - Before any helper function exists, search for an existing function that
     can be used or expanded to support the use case.
   - Flag any duplicated logic introduced during implementation.
5. Agent does a final check against the coding guidelines and language review
   profile for anything missed.
6. Agent verifies all documentation updates from the plan's Documentation
   Impact section have been completed and are accurate.
7. Agent verifies all tests still pass after polish changes.
8. Agent writes `08-final-polish.md` with:
   - What was cleaned up.
   - Final test results.
9. Agent writes `09-summary.md` with:
   - **Task**: brief description (from the problem statement).
   - **What was delivered**: summary of changes against the acceptance criteria
     from `01-definition.md`.
   - **Files changed**: list of files created, modified, or deleted.
   - **Review rounds**: how many code review cycles were needed.
   - **Notable decisions**: key choices made during planning and review, with
     rationale.
10. Final commit.
11. Agent updates `task-state.md` to mark the task as complete.

---

## Default Coding Guidelines

Stored in `defaults/coding-guidelines.md`. These are always included as the
starting point for every task's coding guidelines. The agent augments them with
research-informed additions, and the user reviews/customizes during Phase 4.

The default guidelines should cover:

- Prefer simple, readable code over clever code.
- No duplication -- search for existing utilities before creating new ones.
- Handle errors explicitly -- no swallowed errors or empty catch blocks.
- Use meaningful, descriptive names for variables, functions, and files.
- No unnecessary comments -- code should be self-documenting. Comments are only
  for clarifying genuinely complex logic.
- Non-exported/internal functions do not need comments unless the code is
  difficult to understand.
- No unicode special characters in code or strings -- use ASCII equivalents
  (straight quotes, hyphens, etc.).
- No hardcoded secrets, credentials, or environment-specific values.
- Follow existing codebase conventions for file structure, naming, and patterns.
- Commit message format (references `defaults/commit-message-format.md`).

---

## Project Commands

Stored in `defaults/project-commands.md` as a template. The project-level
override (in the project config directory) fills in the actual commands for the
specific repo. If a project override exists, it replaces the template entirely.

The template covers common operations an agent needs to perform:

- **Build** -- command to build the project.
- **Lint** -- command to run linting.
- **Format** -- command to run formatting.
- **Test** -- command to run all tests.
- **Test (single file)** -- command to run a single test file (with placeholder
  for the file path).
- **Type Check** -- command to run type checking (if applicable).
- **Dev Server** -- command to start the dev server (if applicable).

Not all commands will apply to every project. The project override includes only
the ones that are relevant and omits the rest.

These commands are referenced throughout the task phases:

- **Phase 5 (Implement)**: Agent uses the build, test, and type check commands
  after completing each step.
- **Phase 6 (Code Review)**: Agent uses the test command to verify tests pass.
- **Phase 8 (Final Polish)**: Agent uses the lint and format commands explicitly.

This eliminates guesswork and prevents the agent from thrashing on basic
operations like running tests or building the project.

---

## Language Review Profiles

Stored in `review-profiles/`. Auto-selected based on the language(s) detected
during the research phase (or inferred for small tasks that skip research).

### TypeScript (`review-profiles/typescript.md`)

Starting checklist:

- No `any` types -- use `unknown` with type narrowing, or define proper types.
- No type assertions (`as`) -- prefer type guards and discriminated unions.
- No non-null assertions (`!`) -- handle nullability explicitly.
- Prefer `readonly` for data that should not mutate.
- Check for unhandled promise rejections.
- Prefer discriminated unions over boolean/string type flags.
- Use strict TypeScript configuration.
- Prefer named exports over default exports.
- Avoid enums -- prefer const objects or union types.
- No barrel imports/exports -- favor importing directly from files where exports
  are defined.

### Bash (`review-profiles/bash.md`)

Starting checklist:

- Always use `set -euo pipefail`.
- Quote all variable expansions.
- Use `[[ ]]` over `[ ]` for conditionals.
- Use `$(command)` over backticks.
- Check command exit codes explicitly where needed.
- Use `local` for function variables.
- Use `readonly` for constants.
- Avoid parsing `ls` output -- use globs or `find`.
- Use `mktemp` for temporary files.
- Scripts must be compatible with Bash 3.2 (macOS default) unless the script
  explicitly declares a newer bash requirement. Bash 3.2 restrictions:
  - No associative arrays (`declare -A`) -- use paired regular arrays or other
    workarounds.
  - No `readarray` / `mapfile` -- use `while read` loops instead.
  - No `${var,,}` or `${var^^}` for case conversion -- use `tr` instead.
  - No `|&` to pipe stderr -- use `2>&1 |` instead.
  - No `&>>` to append stdout+stderr -- use `>> file 2>&1` instead.
  - No negative array indexing (`${arr[-1]}`) -- compute the index explicitly.
  - No namerefs (`declare -n`) -- use `eval` cautiously or restructure.

---

## Task State Tracking

Each task has a `task-state.md` file that tracks:

- **Task slug**: the directory name.
- **Current phase**: which phase is active.
- **Complexity**: small, medium, or large.
- **Language(s)**: detected language(s) for review profile selection.
- **Phase history**: timestamp of when each phase was entered/completed.

This enables the agent to resume a task in a new session by reading the state
file and the relevant phase artifacts.

---

## README Documentation Plan

The `README.md` should be thorough and serve as the primary reference for anyone
setting up and using the task system. It should cover:

### Overview
- What the task system is and why it exists.
- The spec-driven development philosophy -- why structured phases produce better
  output than ad-hoc prompting.
- The 8 phases at a glance -- a brief summary table or list so users understand
  the full workflow before diving in.

### Quick Start
- Minimal steps to get running:
  1. Clone or add the task-system repo.
  2. Add the task system reference to your AI agent config (exact lines
     provided per agent type).
  3. Tell the agent "set up the task system" -- the guided setup prompt
     auto-detects project conventions, creates the config directory with
     starter files, and creates the tasks directory.
  4. Review and adjust the generated config files.
  5. Start your first task.
- A concrete end-to-end example walking through setup and then a complete task
  lifecycle showing what the agent does at each phase.
- Emphasis that the setup is interactive and does the heavy lifting -- the user
  should not need to manually create directories or config files.

### Setup Guides by Agent
- **Pi (agentskill)**: How to wire `skill.md` into a pi skill configuration.
- **Claude (CLAUDE.md)**: How to reference the task system from a CLAUDE.md
  file.
- **Cursor (.cursorrules)**: How to reference the task system from .cursorrules.
- **Other agents**: General instructions for any agent that supports custom
  system prompts or instruction files.
- Each guide should include the exact text to add to the agent's config file,
  with the configurable paths called out.

### Project Configuration
- How to create and populate the project config directory.
- Detailed explanation of each override file:
  - `coding-guidelines.md` -- what to put here, examples.
  - `commit-message-format.md` -- what to put here, examples of common formats
    (conventional commits, Jira-prefixed, etc.).
  - `project-commands.md` -- what to put here, with a filled-in example for a
    typical Node.js project and a typical Python project.
  - `review-profiles/` -- how to add checks to existing profiles or add new
    language profiles.
  - `config.md` -- available settings.
- Merge behavior explained clearly with examples.

### Phase Reference
- Detailed documentation of each phase:
  - What it does.
  - What files it reads and writes.
  - What user interaction is expected.
  - Tips for getting the most out of that phase.
- Cross-session workflow: how to pause and resume a task, what the agent reads
  to recover context.

### Customization
- How to fork and customize the defaults for your team or organization.
- How to add new language review profiles.
- How to modify prompt templates for different workflows.
- What is safe to change vs what could break the phase flow.

### Task Directory Structure
- Full reference of all files a task produces, what each contains, and when it
  is created.
- Example of a completed task directory with realistic content snippets.

### FAQ / Troubleshooting
- Common issues: agent skipping phases, agent not finding config, etc.
- How to reset a task to a previous phase.
- How to abandon a task cleanly.

---

## Implementation Order

To build this system, implement in this order:

1. Write `defaults/coding-guidelines.md`, `defaults/commit-message-format.md`,
   and `defaults/project-commands.md`.
2. Write `review-profiles/typescript.md` and `review-profiles/bash.md`.
3. Write each prompt template in `prompts/` (00 through 08).
4. Write `skill.md` -- the portable skill definition that ties everything
   together with behavioral instructions for the agent.
5. Write `README.md` -- comprehensive documentation following the README
   Documentation Plan above.
