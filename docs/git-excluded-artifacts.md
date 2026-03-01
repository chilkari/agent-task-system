# Git-Excluded Artifacts

By default, the task system commits its artifacts (task files, config files)
to git alongside your project code. If you prefer to keep task system
artifacts out of git entirely — they are personal workflow files, not project
deliverables — you can use excluded mode.

## Setup

During setup, when asked "Do you intend to commit task system artifacts to
git?", answer **no**. The agent will:

1. Create a `.excluded/` directory at the project root (or use one you
   already have).
2. Add `.excluded/` to `.git/info/exclude` so git ignores it locally. This
   uses `.git/info/exclude` rather than `.gitignore` so the exclusion does
   not affect other collaborators or appear in git history.
3. Place the project config directory and tasks directory inside `.excluded/`
   (e.g., `.excluded/.task-system/` and `.excluded/tasks/`).
4. Set `artifact-tracking` to `excluded` in `config.md`.

## What Changes in Excluded Mode

- **Project code commits proceed normally.** Implementation steps, review
  fixes, and polish changes to your source code are committed as usual.
- **Task artifacts are never committed.** Definition files, plans, review
  documents, and all other task system markdown files are written to disk but
  never staged or committed.
- **Config file changes are never committed.** Updates to coding guidelines,
  project commands, etc. during retrospective are written to disk but not
  committed.

## How the Agent Finds Per-Repo Paths

A key challenge with excluded mode: you typically install the skill globally
(in your home directory), but each repo needs its own paths. A global skill
file cannot hardcode per-repo paths.

The solution is `.git/info/task-system` — a repo-local pointer file written
during setup:

```
project-config: .excluded/.task-system/
tasks-directory: .excluded/tasks/
```

This file lives inside `.git/` so it is inherently per-repo and never
committed. When the agent starts in a repo, it checks this file to find where
the config and tasks live. This means:

- Your global skill file only needs the skill reference line — no paths.
- A single global skill works across all your repos, each with its own paths.
- No trace of the task system appears in the repository.

This pointer file is written for **all** setups (both committed and excluded),
but it is especially important for excluded mode.

### Path Resolution Order

1. Paths in the agent config file (if present, they take priority).
2. Paths in `.git/info/task-system` (auto-discovered).
3. If neither is found, the agent asks the user to run setup.

## Skill Installation for Excluded Mode

Since excluded mode keeps everything out of git, install the skill globally
rather than in the project:

```bash
# Claude
mkdir -p ~/.claude/skills/task
cp ~/agent-task-system/templates/SKILL.md ~/.claude/skills/task/SKILL.md

# Cursor
mkdir -p ~/.cursor/skills/task
cp ~/agent-task-system/templates/SKILL.md ~/.cursor/skills/task/SKILL.md

# Pi
mkdir -p ~/.pi/skills/task
cp ~/agent-task-system/templates/SKILL.md ~/.pi/skills/task/SKILL.md
```

Update the `SKILL.md` to point to your clone, and you're done. The per-repo
paths are handled automatically via `.git/info/task-system`.

If you prefer using a project-local config file instead (like `CLAUDE.md` or
`.cursorrules`), that works too — just be aware the config lines will be
visible in git.
