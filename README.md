# Task System

A portable, spec-driven task system for AI-assisted development. Instead of
ad-hoc prompting, this system guides you and your AI agent through structured
phases -- definition, research, planning, review, implementation, code review,
and polish -- to produce high-quality, well-documented code changes.

## Why Spec-Driven Development?

AI coding agents produce better results when they follow a structured process:

- **Definition before code**: Clearly stating what "done" looks like prevents
  scope drift and ensures the agent solves the right problem.
- **Research before planning**: Understanding the codebase before writing a plan
  prevents architectural mistakes and ensures the plan respects existing
  conventions.
- **Planning before implementation**: A detailed plan reviewed by a human
  catches design issues before they become code. The agent implements
  mechanically rather than making judgment calls mid-stream.
- **Review after implementation**: A dedicated code review phase with a
  language-specific checklist catches issues that implementation alone misses.
- **Polish at the end**: A final pass catches AI-generated artifacts (verbose
  comments, unicode characters, duplication) that slip through review.

The result is a repeatable process that produces code you can be confident in,
with a full audit trail of decisions made along the way.

## The 8 Phases

| Phase | What Happens | User Involvement |
|-------|-------------|------------------|
| **1. Definition** | Capture the problem, acceptance criteria, scope, and complexity | Interactive: describe the task, review the definition |
| **2. Research** | Explore the codebase to build context (skipped for small tasks) | Light: approve the research plan, review findings |
| **3. Plan** | Create a detailed implementation plan with coding guidelines | Light: review the initial draft |
| **4. Review/Revise** | Iteratively refine the plan until ready | Interactive: provide feedback, approve the plan |
| **5. Implement** | Execute the plan step by step | Minimal: unblock if needed |
| **6. Code Review** | Critical review of all changes | Light: review findings |
| **7. Review Response** | Fix critical/important issues, discuss suggestions | Interactive: approve fixes, decide on suggestions |
| **8. Final Polish** | Lint, format, remove AI artifacts, final tests | None: fully automated |
| **9. Retrospective** | Review process, tune project config for future tasks | Interactive: accept/decline suggestions, share observations |

## Quick Start

### 1. Add the Task System to Your Project

Clone or add the task system repository somewhere accessible. Common
approaches:

**Git submodule** (lives inside your repo):
```bash
git submodule add <task-system-repo-url> vendor/task-system
```

**Separate clone** (lives outside your repo):
```bash
git clone <task-system-repo-url> ~/tools/task-system
```

**Direct copy** (copy the directory into your repo):
```bash
cp -r /path/to/task-system .task-system-core/
```

### 2. Add the Config Lines to Your AI Agent

Add the following to your AI agent's configuration file (see
[Setup Guides by Agent](#setup-guides-by-agent) for agent-specific
instructions):

```
Follow the task system defined in <path-to-task-system>/skill.md
Project config: .task-system/
Tasks directory: tasks/
```

Replace the paths with your actual locations.

### 3. Run the Interactive Setup

With the config lines in place, tell your AI agent:

> "Set up the task system"

The agent will walk you through an interactive setup:

1. **Confirm paths**: Where to put the project config directory and tasks
   directory. Defaults are `.task-system/` and `tasks/`.
2. **Auto-detect conventions**: The agent inspects your repo for build tools,
   package managers, languages, test frameworks, and commit message patterns.
3. **Create config files**: The agent creates starter files pre-filled with
   detected values:
   - `coding-guidelines.md` -- project-specific coding rules
   - `commit-message-format.md` -- your commit message convention
   - `project-commands.md` -- commands for build, test, lint, format, etc.
4. **Review together**: You walk through each file and adjust as needed.

After setup, the agent will tell you exactly what was created and confirm
everything is ready.

### 4. Start Your First Task

Tell your agent:

> "New task"

The agent guides you through defining the task -- it will ask what you want to
accomplish, clarify scope, and draft a definition document. From there, the
phases proceed automatically with your input at the right moments.

### Example: Full Task Lifecycle

Here is what a typical task looks like from start to finish:

```
You:    "New task"
Agent:  "What would you like to accomplish?"
You:    "Add a --verbose flag to the deploy script that shows each step"
Agent:  [Asks clarifying questions about scope, expected behavior, etc.]
Agent:  [Writes 01-definition.md, assesses complexity as "small"]
Agent:  [Skips research, moves to planning]
Agent:  [Writes 03-plan.md with steps, coding guidelines, testing strategy]
Agent:  "Here's the plan. Please review..."
You:    "Step 2 should also log to a file, not just stdout"
Agent:  [Revises plan, logs the change in 04-review-notes.md]
You:    "Looks good, proceed"
Agent:  [Runs pre-flight checks: clean git, tests pass]
Agent:  [Implements step by step, committing after each]
Agent:  [Switches to code reviewer role, writes 06-code-review.md]
Agent:  "I found 1 important issue and 2 suggestions..."
Agent:  [Fixes the important issue, you accept 1 suggestion, reject 1]
Agent:  [Re-reviews, comes back clean]
Agent:  [Runs final polish: lint, format, AI artifact check]
Agent:  [Writes 09-summary.md]
Agent:  "Would you like to do a quick retrospective?"
You:    "Sure"
Agent:  "During code review I flagged X twice. Want to add a coding guideline?"
You:    "Yes, add that"
Agent:  [Updates .task-system/coding-guidelines.md, writes 10-retrospective.md]
```

The task directory now contains the full record:

```
tasks/add-verbose-flag/
  01-definition.md      -- what we set out to do
  03-plan.md            -- how we planned to do it
  04-review-notes.md    -- decisions made during review
  05-implementation.md  -- what was actually done, step by step
  06-code-review.md     -- first review findings
  06-code-review-r2.md  -- second review (clean)
  08-final-polish.md    -- cleanup notes
  09-summary.md         -- final summary
  10-retrospective.md   -- config changes and process notes (if retrospective was done)
  task-state.md         -- phase tracking (complete)
```

---

## Setup Guides by Agent

### Pi

Add a _directory_ named for your skill (i.e. "task") inside of your
`.pi/skills/` directory and then either copy or symlink the skill.md file here
to a file named `SKILL.md` in that directory.

```
cd ~/.pi/skills/task/
ln -s /home/jonathon/dev/agent-task-system/skill.md SKILL.md
```

Alternatively, and for installation into a specific repo, add the same to
`[repo-root]/.pi/skills/task/SKILL.md` and modify it to something like this,
which allows you to specify where in the repo the project-specific config
directory and tasks directory should be.

```
Follow the task system defined in /home/jonathon/dev/agent-task-system/skill.md
Project config: <project-config-dir>/
Tasks directory: <tasks-dir>/
```

For example, if the task system is at `vendor/task-system` and you use the
defaults:

```
Follow the task system defined in vendor/task-system/skill.md
Project config: .task-system/
Tasks directory: tasks/
```

### Claude (CLAUDE.md)

Add to your `CLAUDE.md` file at the project root:

```
## Task System

Follow the task system defined in vendor/task-system/skill.md
Project config: .task-system/
Tasks directory: tasks/
```

### Cursor (.cursorrules)

Add to your `.cursorrules` file:

```
## Task System

Follow the task system defined in vendor/task-system/skill.md
Project config: .task-system/
Tasks directory: tasks/
```

### Other Agents

The task system works with any AI coding agent that supports custom
instructions or system prompts. Add the three configuration lines to whatever
file your agent reads for project-level instructions:

```
Follow the task system defined in <path-to-task-system>/skill.md
Project config: <project-config-dir>/
Tasks directory: <tasks-dir>/
```

The `skill.md` file contains all the behavioral instructions. The agent reads
it and follows the process. The prompts in `prompts/` are referenced by
`skill.md` and contain detailed instructions for each phase.

---

## Project Configuration

The project config directory contains project-specific overrides that customize
the task system for your codebase. It is created during setup and can be
modified at any time.

### `config.md`

Project-level settings:

```markdown
# Task System Configuration

## Tasks Directory
tasks/
```

### `coding-guidelines.md`

Project-specific coding rules that are **appended** to the task system defaults.
The defaults (in `defaults/coding-guidelines.md`) always apply. Your
project-level file adds additional rules on top.

**Example for a Node.js/React project**:
```markdown
# Project Coding Guidelines

- Use pnpm for all package management. Do not use npm or yarn.
- All React components must use functional style with hooks.
- All API responses must use the Result<T, E> wrapper type.
- Use zod for runtime validation of external inputs.
- Database queries must go through the repository layer -- no direct SQL in
  route handlers.
```

**Example for a CLI tool**:
```markdown
# Project Coding Guidelines

- All user-facing output must go through the logger module.
- Exit codes must follow the conventions in docs/exit-codes.md.
- All flags must have both short (-v) and long (--verbose) forms.
```

### `commit-message-format.md`

Your commit message convention. This **replaces** the task system default
entirely.

**Example: Conventional Commits** (this is also the default):
```markdown
# Commit Message Format

<type>: <short summary>

Types: feat, fix, refactor, docs, test, chore, style
Summary: lowercase, imperative mood, under 72 characters, no period.
```

**Example: Jira-Prefixed**:
```markdown
# Commit Message Format

<PROJ-number>: <short summary>

Always include the Jira ticket number. Summary in imperative mood.

Example: PROJ-1234: add retry logic to webhook delivery
```

**Example: Simple**:
```markdown
# Commit Message Format

<short summary of what changed>

Imperative mood, under 72 characters. No type prefix needed.

Example: add retry logic to webhook delivery
```

### `project-commands.md`

The actual commands for building, testing, linting, and formatting your
project. This **replaces** the task system default template entirely.

The agent uses these commands during implementation (running tests after each
step), code review (verifying tests pass), and final polish (running linters
and formatters). Providing accurate commands prevents the agent from guessing.

**Example: Node.js project with pnpm**:
```markdown
# Project Commands

## Build
pnpm build

## Lint
pnpm lint

## Format
pnpm format

## Test (all)
pnpm test

## Test (single file)
pnpm vitest run FILE

## Type Check
pnpm tsc --noEmit
```

**Example: Python project**:
```markdown
# Project Commands

## Lint
ruff check .

## Format
ruff format .

## Test (all)
pytest

## Test (single file)
pytest FILE

## Type Check
mypy .
```

Only include commands that apply to your project. Omit sections that are not
relevant.

### `review-profiles/`

Language-specific review checklist additions. Files here are **appended** to
the matching task system profile. If you create a profile for a language the
task system does not ship (e.g., `python.md`), it is used as-is.

**Example: Adding project-specific TypeScript checks**:
```markdown
# Project TypeScript Review Additions

- All React components must handle loading and error states explicitly.
- Use the project's ApiClient type for all HTTP requests -- do not use fetch
  directly.
- Prefer the project's date utility functions over raw Date objects.
```

### Merge Behavior Summary

| File | Merge Strategy | Effect |
|------|---------------|--------|
| `coding-guidelines.md` | Append | Project rules added to defaults |
| `commit-message-format.md` | Replace | Project format used instead of default |
| `project-commands.md` | Replace | Project commands used instead of template |
| `review-profiles/<lang>.md` | Append | Project checks added to default profile |

---

## Phase Reference

### Phase 1: Definition

**Reads**: nothing (starting fresh)
**Writes**: `01-definition.md`, `task-state.md`
**User interaction**: Interactive -- describe the task, answer questions, review

The agent asks you to describe the task, then drafts a definition with:
- Problem statement
- Acceptance criteria (with general testing expectations)
- Scope (in/out)
- Complexity assessment (small/medium/large)

**Tips**:
- Be specific about what "done" looks like. Vague acceptance criteria lead to
  vague implementations.
- If you are unsure about complexity, err on the side of larger. It is easier
  to skip research than to discover you needed it during implementation.

### Phase 2: Research

**Reads**: `01-definition.md`
**Writes**: `02-research.md`, `task-state.md`
**User interaction**: Light -- approve the research plan, review findings

The agent proposes what it will explore, you confirm, it explores and reports.
Skipped for small tasks.

**Tips**:
- Point the agent to files you know are relevant. It saves time and ensures
  nothing is missed.
- If the agent's research reveals the task should be split, seriously consider
  it. Smaller tasks produce better results.

### Phase 3: Plan

**Reads**: `01-definition.md`, `02-research.md` (if exists), defaults and
profiles
**Writes**: `03-plan.md`, `task-state.md`
**User interaction**: Light -- review the initial draft

The agent creates a detailed plan with coding guidelines, implementation steps,
testing strategy, documentation impact, and breaking changes.

**Tips**:
- Pay attention to the coding guidelines. This is your chance to add
  project-specific rules that the agent will follow during implementation.
- Check the testing strategy. Does it actually verify the acceptance criteria?
- Review the difficulty/risk ratings on each step. High-risk steps may need
  extra attention during review.

### Phase 4: Review/Revise

**Reads**: `03-plan.md`, `04-review-notes.md` (if exists)
**Writes**: `03-plan.md` (revisions), `04-review-notes.md` (append-only log),
`task-state.md`
**User interaction**: Interactive -- this is the most collaborative phase

You and the agent refine the plan until you approve it. This may span multiple
sessions.

**Tips**:
- Do not rush this phase. A good plan is the single biggest predictor of a
  good implementation.
- Challenge the agent's assumptions. Ask "what about X?" for edge cases you
  can think of.
- If you pause and come back later, the agent reads `04-review-notes.md` to
  catch up. Everything discussed is preserved.

### Phase 5: Implement

**Reads**: `03-plan.md`, `02-research.md` (if exists), project commands,
`05-implementation.md` (if resuming)
**Writes**: `05-implementation.md`, `task-state.md`
**User interaction**: Minimal -- only if the agent encounters a blocker

The agent runs pre-flight checks (clean git, passing tests), then implements
each step with commits.

**Tips**:
- Make sure your project commands are correct before starting. Incorrect test
  or build commands cause thrashing.
- If the agent gets blocked, provide clear guidance. The more specific you are,
  the faster it gets unblocked.
- Implementation progress is tracked incrementally. If the session crashes,
  the agent knows exactly where it left off.

### Phase 6: Code Review

**Reads**: `01-definition.md`, `03-plan.md`, `02-research.md` (if exists),
`05-implementation.md`, language review profiles
**Writes**: `06-code-review.md` (or `06-code-review-rN.md`), `task-state.md`
**User interaction**: Light -- review the findings

The agent switches to a reviewer role and critically examines all changes
against the acceptance criteria, coding guidelines, and language-specific
checklist.

**Tips**:
- Treat the code review findings seriously. The agent is deliberately looking
  for problems from a different perspective than when it wrote the code.
- Pay attention to critical and important findings -- these should almost
  always be fixed.

### Phase 7: Review Response

**Reads**: `06-code-review.md` (latest), `03-plan.md`
**Writes**: `06-code-review.md` (dispositions), `task-state.md`
**User interaction**: Interactive -- approve fixes, decide on suggestions

The agent presents its fix plan for critical/important items (approve before it
proceeds) and walks through suggestions one by one for your decision.

**Tips**:
- When rejecting a suggestion, give a reason. It is documented for the record
  and helps the agent understand your preferences.
- After fixes, the agent re-reviews. This loop continues until clean. Usually
  2-3 rounds is enough.

### Phase 8: Final Polish

**Reads**: `01-definition.md`, `03-plan.md`, project commands, language
review profiles
**Writes**: `08-final-polish.md`, `09-summary.md`, `task-state.md`
**User interaction**: None -- fully automated

The agent runs linters/formatters, checks for AI artifacts (unicode
characters, verbose comments, duplication), verifies documentation, runs tests,
and writes the final summary.

**Tips**:
- Review `09-summary.md` after completion. It gives a clean overview of what
  was delivered and the decisions made along the way.

### Phase 9: Retrospective

**Reads**: All task artifacts, current project config files
**Writes**: `10-retrospective.md`, project config files (if changes accepted),
`task-state.md`
**User interaction**: Interactive -- accept/decline suggestions, share
observations. Optional -- user can skip entirely.

The agent mines the task artifacts for signals about what could be improved in
the project configuration: recurring code review findings that should become
coding guidelines, missing review profile checks, incorrect project commands,
or conventions the agent had to learn mid-task. It presents concrete
suggestions, the user accepts or declines each one, and approved changes are
applied directly to the `.task-system/` config files. The user can also share
process-level observations about the workflow itself.

**Tips**:
- This phase is optional. If a task went smoothly and nothing needs tuning,
  decline and move on.
- Pay attention to agent suggestions drawn from code review findings --
  these are patterns that will recur and are worth codifying.
- Process-level observations (about prompts or phase flow) are captured as
  notes. If you maintain a fork of the task system, review these periodically
  to inform prompt changes.

---

## Cross-Session Workflow

Tasks can span multiple sessions. The system is designed for this:

### Pausing
Just stop. All progress is saved to disk incrementally. The conversation can
end at any point without losing work.

### Resuming
Tell the agent:

> "Resume task" or "Continue task"

The agent reads `task-state.md` to determine the current phase, then reads the
relevant artifacts:
- **Phase 4 (Review)**: Reads `04-review-notes.md` for discussion history and
  open items.
- **Phase 5 (Implement)**: Reads `05-implementation.md` for completed and
  in-progress steps.
- **Any phase**: Reads the relevant phase documents for full context.

The agent summarizes where things stand before continuing.

### Multiple Tasks
If you have several tasks in progress, the agent will ask which one to resume.
Use "task list" to see all tasks and their current phase.

---

## Customization

### Customizing Defaults

Fork or copy the task system and modify:

- `defaults/coding-guidelines.md` -- change the baseline rules for all your
  projects.
- `defaults/commit-message-format.md` -- change the default commit format.
- `defaults/project-commands.md` -- change the command template.

### Adding Language Review Profiles

Create a new file in `review-profiles/`:

```markdown
# <Language> Review Profile

This checklist is used during code review (Phase 6) and final polish (Phase 8)
for tasks involving <Language>.

## <Category>

- <check 1>
- <check 2>
...
```

The task system auto-detects languages during research and applies matching
profiles.

### Modifying Prompt Templates

The files in `prompts/` contain the full instructions for each phase. You can
modify them to adjust the workflow:

**Safe to modify**:
- Adding extra checks or steps within a phase.
- Adjusting the format of output documents.
- Adding new sections to phase output files.
- Changing the wording of user-facing messages.

**Modify with care**:
- Changing the phase transition logic (which phase follows which).
- Removing sections from output documents that other phases depend on.
- Changing the names of output files (other phases reference them by name).

---

## Task Directory Structure

A completed task directory contains:

| File | Created In | Purpose |
|------|-----------|---------|
| `01-definition.md` | Phase 1 | Problem statement, acceptance criteria, scope |
| `02-research.md` | Phase 2 | Codebase findings, architecture, risks (omitted for small tasks) |
| `03-plan.md` | Phase 3 | Implementation plan, coding guidelines, testing strategy |
| `04-review-notes.md` | Phase 4 | Append-only log of plan review discussions |
| `05-implementation.md` | Phase 5 | Step-by-step progress tracker with rollback points |
| `06-code-review.md` | Phase 6 | First code review findings |
| `06-code-review-rN.md` | Phase 7 | Subsequent review rounds (r2, r3, etc.) |
| `08-final-polish.md` | Phase 8 | Cleanup notes, lint/format results, test results |
| `09-summary.md` | Phase 8 | Final summary of what was delivered |
| `10-retrospective.md` | Phase 9 | Config changes made and process notes (optional) |
| `task-state.md` | Phase 1 | Phase tracking, timestamps, complexity, languages |

---

## FAQ / Troubleshooting

### The agent is skipping phases

Check `task-state.md` in the task directory. It tracks the current phase. If
the state is wrong, edit it to the correct phase and tell the agent to resume.

### The agent cannot find the config files

Verify the paths in your AI agent config match the actual directory locations.
The paths should be relative to the project root.

### Tests are failing before implementation starts

This is the pre-flight check working as intended. The codebase must have
passing tests before the agent begins. Fix the pre-existing failures first,
then resume the task.

### The agent is thrashing on a test failure

The agent should detect this and stop after a few attempts. If it does not,
tell it to stop and explain the issue. Check that the project commands file has
the correct test command.

### I want to reset a task to an earlier phase

Edit `task-state.md` and set the current phase to the one you want to return
to. The agent will re-read the relevant artifacts and continue from there.
Previous phase artifacts are preserved -- the agent will overwrite them as it
re-executes.

### I want to abandon a task

Delete the task directory, or leave it as-is for historical reference. There
is no special cleanup needed.

### The review loop will not end

In Phase 7, after the agent fixes findings and re-reviews, you must explicitly
confirm that the review cycle is complete. Tell the agent "review is clean,
proceed" or similar.

### I want to use different paths in different repos

Both the project config directory and tasks directory paths are configured
per-repo in your AI agent config. Each repo can have its own paths.

### Can I use this without an AI agent?

Yes. The prompt templates in `prompts/` and the phase structure described in
this README can be followed manually. The output documents serve as useful
artifacts regardless of whether an AI agent or a human creates them.
