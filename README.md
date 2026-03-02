# Agent Task System

A structured task system for AI-assisted development. It guides you and your
AI coding agent through a repeatable workflow — define the problem, research
the codebase, plan the solution, review the plan, implement step by step,
code review, and polish — producing high-quality code with a full decision
trail.

This repo is not a standalone application. It is a set of markdown prompt
templates that AI coding agents (Claude, Cursor, Pi, etc.) read and follow.
You install it once and it works across all your projects.

## Some Notes on Current Status

As of this note, this repo is two days old, so... it's just a nascent idea. Ideas
welcomed, of course. A few things I want to pull out that may not be clear in
the denser README context:

1. To use, you clone this repo somewhere available, but _use it_ from inside
   other repos. After installation (see below), open your agent harness of
   choice _in your project repo_ and run a `/task set up the task system` to get
   it configured (one-time), then just use `/task new task` to start new tasks.
2. An important decision is whether you want to track the task artifacts in git
   or keep them excluded. On a large "work" repo — I'm just experimenting with
   this and don't want to impose it on everyone else — I exclude all the task
   artifacts. However, for personal projects, I quite like having the full
   markdown files of everything that was done, so I check them in on those
   projects. If you want to exclude, it just creates an excluded directory in
   git (via `.git/info/exclude`) and puts all the task artifacts in there. By
   default, it's named `.excluded`
3. It looks like a lot of phases. I agree -- but I have it in a bit of "safe"
   mode right now, and I like it to stop and ask me to continue. These will
   naturally get taken out in favor of more automation once I like what it's
   doing.
4. That being said, don't ignore the "Retrospective" phase. That's not a
   scrum/agile ceremony -- it's the point where you _add_ new coding guidelines
   and prompt enhancements to your project so that things you hit in this task
   don't happen again in future tasks. It's the part that makes this
   self-improving with each new task. It's agentic — the agent reviews its own
   code-review findings and suggests improvements to avoid those issues in
   future tasks.
5. There are also two shared files stored in the `<tasks>` directory. A shared
   "knowledge base" that is written to during research and read by future tasks,
   and a "backlog" for tasks discovered along the way that are out-of-scope, but
   shouldn't be lost.

## How It Works

When you say **"new task"**, the agent walks through 9 phases:

| # | Phase | What Happens |
|---|-------|-------------|
| 1 | **Define** | Capture the problem, acceptance criteria, and complexity |
| 2 | **Research** | Explore the codebase for context (skipped for small tasks) |
| 3 | **Plan** | Write a detailed implementation plan |
| 4 | **Review** | You and the agent refine the plan until you approve it |
| 5 | **Implement** | Execute the plan step by step with commits |
| 6 | **Code Review** | The agent critically reviews its own changes |
| 7 | **Review Response** | Fix issues, discuss suggestions with you |
| 8 | **Polish** | Lint, format, remove AI artifacts, final tests |
| 9 | **Retrospective** | Optionally tune project config for future tasks |

Tasks can span multiple sessions. All progress is saved to disk — just say
**"resume task"** to pick up where you left off.

## Installation

### 1. Clone this repo

Put it somewhere accessible from your projects. Your home directory works
well:

```bash
git clone <repo-url> ~/agent-task-system
```

### 2. Install the skill for your agent

Create the skill directory and copy the template:

**Claude:**
```bash
mkdir -p ~/.claude/skills/task
cp ~/agent-task-system/templates/SKILL.md ~/.claude/skills/task/SKILL.md
```

**Cursor:**
```bash
mkdir -p ~/.cursor/skills/task
cp ~/agent-task-system/templates/SKILL.md ~/.cursor/skills/task/SKILL.md
```

**Pi:**
```bash
mkdir -p ~/.pi/skills/task
cp ~/agent-task-system/templates/SKILL.md ~/.pi/skills/task/SKILL.md
```

### 3. Update the skill file

Open the copied `SKILL.md` and replace the placeholder path with the actual
location of your clone:

```
Follow the task system defined in ~/agent-task-system/skill.md
```

### 4. Run setup in your project

Open your project in your AI agent and say:

> "/task Set up the task system"

The agent will:
- Ask whether to commit task artifacts to git (or keep them excluded)
- Auto-detect your build tools, languages, and commit conventions
- Create a project config directory with starter files
- Store the per-repo paths in `.git/info/task-system` for automatic discovery

Then start your first task:

> "New task"

## What Setup Creates

In each project, setup creates a **project config directory** (default:
`.task-system/`) with files the agent uses to understand your project:

| File | Purpose |
|------|---------|
| `config.md` | Tasks directory path and artifact tracking mode |
| `coding-guidelines.md` | Project-specific coding rules (appended to defaults) |
| `commit-message-format.md` | Your commit message convention |
| `project-commands.md` | Commands for build, test, lint, format |
| `phase-steps/<phase>.md` | Additional steps injected into specific phases (optional) |

It also creates a **tasks directory** (default: `tasks/`) where each task gets
its own subdirectory with definition, plan, implementation tracking, review
documents, and a final summary.

## Commands

| Command | What It Does |
|---------|-------------|
| `new task` | Start a new task from Phase 1 |
| `resume task` | Continue an in-progress task |
| `task status` | Report current phase and progress |
| `task list` | List all tasks and their phases |
| `set up the task system` | One-time project setup |

While it can work without it, prefixing these with `/task` (or your agent's
command symbol) can help ensure the agent recognizes them as task system
commands from a skill.

## Example Session

```
You:    "/task New task"
Agent:  "What would you like to accomplish?"
You:    "Add a --verbose flag to the deploy script"
Agent:  [Asks clarifying questions, writes definition, assesses as "small"]
Agent:  [Skips research, writes implementation plan]
Agent:  "Here's the plan. Please review..."
You:    "Looks good, proceed"
Agent:  [Implements step by step, committing after each]
Agent:  [Reviews its own code, finds 1 issue and 2 suggestions]
Agent:  [Fixes the issue, you accept 1 suggestion, reject 1]
Agent:  [Runs lint, format, final tests]
Agent:  "Task complete. Want to do a retrospective?"
```

## Documentation

| Topic | Link |
|-------|------|
| Phase details and tips | [docs/phases.md](docs/phases.md) |
| Project configuration files | [docs/configuration.md](docs/configuration.md) |
| Keeping artifacts out of git | [docs/git-excluded-artifacts.md](docs/git-excluded-artifacts.md) |
| Customizing the task system | [docs/customization.md](docs/customization.md) |
| Alternative installation methods | [docs/advanced-installation.md](docs/advanced-installation.md) |
| Codebase knowledge base | [docs/knowledge-base.md](docs/knowledge-base.md) |
| Troubleshooting / FAQ | [docs/troubleshooting.md](docs/troubleshooting.md) |

## Repo Structure

```
skill.md                    # Core skill definition (the agent reads this)
templates/SKILL.md          # Template to copy into your agent's skill dir
prompts/                    # Phase-specific prompt templates
  00-setup.md               # One-time project setup
  01-definition.md          # Phase 1: Task definition
  02-research.md            # Phase 2: Codebase research
  03-plan.md                # Phase 3: Implementation planning
  04-review.md              # Phase 4: Plan review loop
  05-implement.md           # Phase 5: Step-by-step implementation
  06-code-review.md         # Phase 6: Code review
  07-review-response.md     # Phase 7: Address review findings
  08-final-polish.md        # Phase 8: Lint, format, cleanup
  09-retrospective.md       # Phase 9: Retrospective & config tuning
defaults/                   # Baseline config (applied to all projects)
  coding-guidelines.md      # Default coding rules
  commit-message-format.md  # Default commit format
  project-commands.md       # Command template with placeholders
review-profiles/            # Language-specific code review checklists
  typescript.md
  bash.md
docs/                       # Detailed documentation
```
