# Project Configuration

The project config directory contains project-specific overrides that
customize the task system for your codebase. It is created during setup and
can be modified at any time.

Default location: `.task-system/` (or inside your excluded directory if using
[git-excluded artifacts](git-excluded-artifacts.md)).

## `config.md`

Project-level settings:

```markdown
# Task System Configuration

## Tasks Directory
tasks/

## Artifact Tracking
<!-- "committed" or "excluded" -->
committed
```

- **Tasks Directory**: Where task output is stored.
- **Artifact Tracking**: Controls whether the task system commits its own
  files to git. See [git-excluded artifacts](git-excluded-artifacts.md).

## `coding-guidelines.md`

Project-specific coding rules that are **appended** to the task system
defaults. The defaults (in `defaults/coding-guidelines.md`) always apply.
Your project-level file adds additional rules on top.

**Example — Node.js/React project:**
```markdown
# Project Coding Guidelines

- Use pnpm for all package management. Do not use npm or yarn.
- All React components must use functional style with hooks.
- All API responses must use the Result<T, E> wrapper type.
- Use zod for runtime validation of external inputs.
- Database queries must go through the repository layer.
```

**Example — CLI tool:**
```markdown
# Project Coding Guidelines

- All user-facing output must go through the logger module.
- Exit codes must follow the conventions in docs/exit-codes.md.
- All flags must have both short (-v) and long (--verbose) forms.
```

## `commit-message-format.md`

Your commit message convention. This **replaces** the task system default
entirely.

**Example — Conventional Commits** (also the default):
```markdown
# Commit Message Format

<type>: <short summary>

Types: feat, fix, refactor, docs, test, chore, style
Summary: lowercase, imperative mood, under 72 characters, no period.
```

**Example — Jira-Prefixed:**
```markdown
# Commit Message Format

<PROJ-number>: <short summary>

Always include the Jira ticket number. Summary in imperative mood.
Example: PROJ-1234: add retry logic to webhook delivery
```

## `project-commands.md`

The actual commands for building, testing, linting, and formatting your
project. This **replaces** the task system default template entirely.

The agent uses these during implementation (running tests after each step),
code review (verifying tests pass), and final polish (running linters and
formatters).

**Example — Node.js with pnpm:**
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

**Example — Python:**
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

Only include commands that apply to your project.

## `review-profiles/`

Language-specific review checklist additions. Files here are **appended** to
the matching task system profile. If you create a profile for a language the
task system does not ship (e.g., `python.md`), it is used as-is.

**Example — project-specific TypeScript checks:**
```markdown
# Project TypeScript Review Additions

- All React components must handle loading and error states explicitly.
- Use the project's ApiClient type for HTTP requests — do not use fetch.
- Prefer the project's date utility functions over raw Date objects.
```

## Merge Behavior Summary

| File | Strategy | Effect |
|------|----------|--------|
| `coding-guidelines.md` | Append | Project rules added to defaults |
| `commit-message-format.md` | Replace | Project format overrides default |
| `project-commands.md` | Replace | Project commands override template |
| `review-profiles/<lang>.md` | Append | Project checks added to defaults |
