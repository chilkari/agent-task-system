# Agent Task System

A structured task system for AI-assisted development. It guides you and your
AI coding agent through a repeatable workflow — define the problem, research
the codebase, plan the solution, review the plan, implement step by step,
code review, polish and retrospective.

This repo is not a standalone application. It is a set of markdown prompt
templates that AI coding agents (Claude, Cursor, Pi, etc.) read and follow.

**NOTE**: Not currently syncing with underlying changes. I'm modifying this quickly and in a personal way that won't necessarily be useful to others given my personalized workflow. Leaving around in this early form, but if you're interested, reach out. I have modified this heavily since.

## How It Works

When you say **"new task"**, the agent walks through 9 phases:

| # | Phase | What Happens |
|---|-------|-------------|
| 1 | **Define** | Capture the problem, acceptance criteria, and complexity. Optionally pull context from a Linear or Gitea issue. |
| 2 | **Research** | Explore the codebase for context (skipped for small tasks) |
| 3 | **Plan** | Write a detailed implementation plan |
| 4 | **Review** | You and the agent refine the plan until you approve it |
| 5 | **Implement** | Branch preflight check (suggests a feature branch if on main/master), then execute the plan step by step with commits |
| 6 | **Code Review** | The agent critically reviews its own changes |
| 7 | **Review Response** | Fix issues, discuss suggestions with you |
| 8 | **Polish** | Lint, format, remove AI artifacts, final tests |
| 9 | **Retrospective** | Tune project config for future tasks |

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
| `config.md` | Tasks directory path, artifact tracking mode, and optional branch prefix |
| `coding-guidelines.md` | Project-specific coding rules (appended to defaults) |
| `commit-message-format.md` | Your commit message convention |
| `project-commands.md` | Commands for build, test, lint, format |
| `issue-trackers.md` | Issue tracker integration (Linear, Gitea) — env var names and settings |
| `phase-steps/<phase>.md` | Additional steps injected into specific phases (optional) |

It also creates a **tasks directory** (default: `tasks/`) where each task gets
its own subdirectory with definition, plan, implementation tracking, review
documents, and a final summary.

## Issue Tracker Integration

When starting a new task, the agent asks if there is an issue ticket. You can
provide a **Linear issue identifier** (e.g., `ENG-123`) or a **Gitea issue
URL** (e.g., `https://gitea.example.com/owner/repo/issues/42`). The agent
fetches the issue title, description, labels, and comments via the tracker's
API, then uses that context to pre-fill the task definition.

If you don't have an issue, just say "no" and the agent proceeds as usual.

API credentials are stored in environment variables (not in config files).
The `issue-trackers.md` config file only controls which env var names to use
and non-secret settings like the Gitea base URL. See
[docs/configuration.md](docs/configuration.md) for details.

## Branch Preflight Check

At the start of the implementation phase, the agent checks if you're on
`main` or `master`. If so, it suggests creating a feature branch before
making changes. The suggested branch name is built from the task name and,
optionally, the issue identifier (e.g., `ENG-123-add-verbose-flag` or
`1-add-verbose-flag`).

You can configure an optional **branch prefix** in `config.md` (e.g., your
initials) so branch names are namespaced (e.g., `jdw/ENG-123-add-verbose-flag`).

You can accept the suggestion, provide your own branch name, or decline and
stay on the current branch. If you're already on a feature branch, the check
is skipped silently.

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
Agent:  "Is there an issue ticket for this task?"
You:    "ENG-456"
Agent:  [Fetches issue from Linear, pre-fills context]
Agent:  [Asks clarifying questions, writes definition, assesses as "small"]
Agent:  [Skips research, writes implementation plan]
Agent:  "Here's the plan. Please review..."
You:    "Looks good, proceed"
Agent:  "You're on main. I suggest creating branch ENG-456-add-verbose-flag."
You:    "Go ahead"
Agent:  [Creates branch, implements step by step, committing after each]
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
  issue-trackers.md         # Default issue tracker config template
review-profiles/            # Language-specific code review checklists
  typescript.md
  bash.md
docs/                       # Detailed documentation
```
