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

3. **Artifact tracking**: Do you intend to commit task system artifacts
   (task files, config) to git?
   - Default: yes
   - If **yes**: The tasks directory and project config directory will be
     tracked by git normally.
   - If **no**: Artifacts will be stored in a git-excluded location and the
     task system will never commit them. See the "Set Up Git-Excluded
     Location" sub-flow below.

4. **AI agent**: Which AI coding agent are you using?
   - Options: pi, Claude (CLAUDE.md), Cursor (.cursorrules), other
   - This determines the exact config lines to output at the end.

### Set Up Git-Excluded Location

Only run this sub-flow if the user answered **no** to Question 3.

1. Ask the user if they already have a git-excluded directory they would like
   to use for task system artifacts.

2. **If they do not have one**, suggest creating `.excluded/` at the project
   root:
   - Create the `.excluded/` directory.
   - Append `.excluded/` as a new line to `.git/info/exclude`. (Use
     `.git/info/exclude` rather than `.gitignore` so the exclusion stays
     local to this clone and does not affect other collaborators or appear in
     git history.) Create the file if it does not exist.
   - Set the project config directory to `.excluded/.task-system/` and the
     tasks directory to `.excluded/tasks/` (overriding whatever the user
     answered for Questions 1 and 2, with their confirmation).

3. **If they already have one**, ask for the path and set the project config
   directory and tasks directory to live inside it (e.g.,
   `<their-path>/.task-system/` and `<their-path>/tasks/`). Verify that the
   directory is actually excluded from git (check `.git/info/exclude` and
   `.gitignore`). If it is not excluded, warn the user and offer to add it to
   `.git/info/exclude`.

The paths chosen here will be used for all subsequent steps.

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

## Artifact Tracking
<!-- "committed" or "excluded" -->
<committed or excluded>
```

Set the `Artifact Tracking` value based on the user's answer to Question 3:
- `committed` if they chose to commit artifacts (the default).
- `excluded` if they chose not to commit artifacts.

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

If artifact tracking is `committed`, create the tasks directory with a
`.gitkeep` file so it is tracked by git even when empty.

If artifact tracking is `excluded`, create the tasks directory without
`.gitkeep` -- the directory is not tracked by git.

## Step 5: Output AI Config Lines

Based on the agent the user specified, output the exact lines to add to their
config file. The config lines are the same regardless of agent:

```
Follow the task system defined in <path-to-task-system>/skill.md
Project config: <project-config-dir>/
Tasks directory: <tasks-dir>/
```

### When artifact tracking is `committed`

Tell the user to add the config lines to their project-local config file:

- **Pi**: `.pi/skills/task/SKILL.md` (or a symlink to `skill.md`)
- **Claude**: `CLAUDE.md` at the project root
- **Cursor**: `.cursorrules` at the project root
- **Other**: Whatever file the agent reads for project-level instructions

### When artifact tracking is `excluded`

Warn the user:

> Since you chose not to commit task system artifacts, adding these config
> lines to a project-local file (like `CLAUDE.md` or `.cursorrules`) would
> leave task-system references visible in git. Consider adding them to your
> **home directory** config file instead so no trace is left in the repo:
>
> - **Pi**: `~/.pi/skills/task/SKILL.md` (global skill)
> - **Claude**: `~/.claude/CLAUDE.md` (global config)
> - **Cursor**: Global settings or `~/.cursorrules`
> - **Other**: Your agent's global/user-level config file
>
> If you prefer to use the project-local file anyway, that is fine -- just be
> aware those config lines will be visible in git.

Then output the config lines with the actual paths (which will point into the
excluded directory, e.g., `.excluded/.task-system/` and `.excluded/tasks/`).

Note: when placing config lines in a home-directory file, the paths must be
either absolute or relative to where the agent resolves them. Confirm with
the user that the paths will resolve correctly from their chosen config
location.

## Step 6: Review

Walk the user through each generated file. For each file:
1. Show what was generated and why.
2. Ask if the user wants to make any changes.
3. Apply changes if requested.

Confirm that setup is complete and remind the user to add the config lines to
their AI agent configuration. Suggest they start their first task by saying
"new task" or similar.
