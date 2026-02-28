# Setup Prompt

You are helping the user set up the task system in their project for the first
time. This is a one-time interactive setup that creates the project config
directory, populates starter files, and provides the correct AI config lines.

## Pre-Check

Before starting, check if a project config directory already exists (look for
common locations: `.task-system/`, `.dev/task-system/`, or ask the user). If one
exists, warn the user and ask if they want to reconfigure or abort.

## Step 1: Gather Configuration

Ask the user the following questions. Provide the defaults shown and accept
short answers.

1. **Project config directory**: Where should the task system project config
   live?
   - Default: `.task-system/`
   - This is where project-specific overrides for coding guidelines, commit
     format, project commands, and review profiles will be stored.

2. **Tasks directory**: Where should task output be stored?
   - Default: `tasks/`
   - Each task will get its own subdirectory here with definition, plan,
     implementation tracking, and review documents.

3. **AI agent**: Which AI coding agent are you using?
   - Options: pi, Claude (CLAUDE.md), Cursor (.cursorrules), other
   - This determines the exact config lines to output at the end.

## Step 2: Auto-Detect Project Conventions

Inspect the repository and report findings to the user. Look for:

**Build system / package manager**:
- `package.json` -- Node.js project. Check for `scripts` entries (test, lint,
  build, format, typecheck). Check whether `pnpm-lock.yaml`, `yarn.lock`, or
  `package-lock.json` exists to determine the package manager.
- `Makefile` -- check for common targets (test, lint, build, format).
- `pyproject.toml` or `setup.py` -- Python project. Look for tool configs
  (pytest, ruff, black, mypy).
- `Cargo.toml` -- Rust project.
- `go.mod` -- Go project.
- Other build files as encountered.

**Languages**: Identify the primary language(s) by examining file extensions
and build configuration. Note which task system review profiles apply.

**Commit conventions**: Run `git log --oneline -20` and look for patterns:
- Conventional commits (`feat:`, `fix:`, etc.)
- Jira/ticket prefixes (`PROJ-123:`)
- Other consistent patterns
- No discernible pattern (suggest the default)

Present all findings to the user and ask for corrections before proceeding.

## Step 3: Create Project Config Directory

Create the following files in the project config directory:

### `config.md`

```markdown
# Task System Configuration

## Tasks Directory
<tasks-directory-path>
```

### `coding-guidelines.md`

Create with a header and any auto-detected conventions noted. Include comment
markers showing this is where the user adds project-specific rules:

```markdown
# Project Coding Guidelines

These guidelines are appended to the task system defaults. Add project-specific
rules below.

<!-- Add your project-specific coding guidelines here. These are merged with
     the default guidelines from the task system. Examples:

- All React components must use functional style with hooks.
- Use pnpm, not npm or yarn.
- All API responses must use the Result<T> wrapper type.
- Database queries must go through the repository layer.
-->
```

If conventions were detected during auto-detection, add them as starting
suggestions (commented or uncommented, based on confidence).

### `commit-message-format.md`

If a commit convention was detected, pre-fill with that format. Otherwise,
present a few common options as comments and let the user choose:

```markdown
# Commit Message Format

<!-- This file overrides the task system default commit format.
     Uncomment and customize the format that matches your project,
     or write your own. -->

<!-- Option 1: Conventional Commits (task system default)
<type>: <short summary>
Types: feat, fix, refactor, docs, test, chore, style
-->

<!-- Option 2: Jira-Prefixed
PROJ-<number>: <short summary>
-->

<!-- Option 3: Simple
<short summary of what changed>
-->
```

### `project-commands.md`

Pre-fill with auto-detected commands. Use comments for slots that were not
detected:

```markdown
# Project Commands

## Build
<detected or "# command to build the project">

## Lint
<detected or "# command to run linting">

## Format
<detected or "# command to run code formatting">

## Test (all)
<detected or "# command to run all tests">

## Test (single file)
<detected or "# command to run a single test file -- use FILE as placeholder">

## Type Check
<detected or "# command to run type checking">

## Dev Server
<detected or "# command to start the dev server">
```

## Step 4: Create Tasks Directory

Create the tasks directory with a `.gitkeep` file so it is tracked by git
even when empty.

## Step 5: Output AI Config Lines

Based on the agent the user specified, output the exact lines to add to their
config file:

**For pi** (skill file or `.pi/skills/`):
```
Follow the task system defined in <path-to-task-system>/skill.md
Project config: <project-config-dir>/
Tasks directory: <tasks-dir>/
```

**For Claude** (`CLAUDE.md`):
```
Follow the task system defined in <path-to-task-system>/skill.md
Project config: <project-config-dir>/
Tasks directory: <tasks-dir>/
```

**For Cursor** (`.cursorrules`):
```
Follow the task system defined in <path-to-task-system>/skill.md
Project config: <project-config-dir>/
Tasks directory: <tasks-dir>/
```

**For other agents**: Provide the generic three-line block above and explain
that it should be added to whatever custom instruction or system prompt
mechanism the agent supports.

## Step 6: Review

Walk the user through each generated file. For each file:
1. Show what was generated and why.
2. Ask if the user wants to make any changes.
3. Apply changes if requested.

Confirm that setup is complete and remind the user to add the config lines to
their AI agent configuration. Suggest they start their first task by saying
"new task" or similar.
