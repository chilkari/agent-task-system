# Advanced Installation

The [main README](../README.md) covers the recommended installation: a global
skill in your home directory that works across all your repos. This document
covers alternative approaches.

## Repo-Specific Installation

If you want the task system config committed to a specific repo (so
collaborators can use it too), install the skill at the project level instead
of globally.

### Adding the Task System to Your Repo

**Git submodule:**
```bash
git submodule add <repo-url> vendor/task-system
```

**Direct copy:**
```bash
cp -r ~/agent-task-system vendor/task-system
```

### Configuring the Agent

Add the skill reference and paths to your project's agent config file:

**Claude** — add to `CLAUDE.md` at the project root:
```
## Task System

Follow the task system defined in vendor/task-system/skill.md
Project config: .task-system/
Tasks directory: tasks/
```

**Cursor** — add to `.cursorrules`:
```
## Task System

Follow the task system defined in vendor/task-system/skill.md
Project config: .task-system/
Tasks directory: tasks/
```

**Pi** — create `.pi/skills/task/SKILL.md`:
```
Follow the task system defined in vendor/task-system/skill.md
Project config: .task-system/
Tasks directory: tasks/
```

The `Project config` and `Tasks directory` lines are optional if setup has
already been run — they will be discovered from `.git/info/task-system`.
Including them explicitly gives them priority over auto-discovered paths.

## Path Resolution

The agent resolves the project config and tasks directory paths in this order:

1. **Agent config file** — paths specified in `CLAUDE.md`, `.cursorrules`,
   `SKILL.md`, etc.
2. **Repo-local pointer** — `.git/info/task-system`, written during setup.
3. **Neither found** — the agent asks you to run setup.

This means:
- A global skill (no paths) works because setup writes `.git/info/task-system`.
- A repo-specific config with explicit paths works and takes priority.
- Both can coexist — the explicit paths win.

## Mixed Mode

You can install the skill globally _and_ add repo-specific overrides. The
global skill provides the baseline, and repo-local config files can override
paths for specific projects. This is useful if most repos use the defaults but
one or two need custom locations.

## Without an AI Agent

The prompt templates in `prompts/` and the phase structure can be followed
manually. The output documents serve as useful artifacts regardless of whether
an AI agent or a human creates them.
