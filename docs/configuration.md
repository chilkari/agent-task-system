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

## Branch Prefix
<!-- Optional: a namespace prefix for suggested branch names, e.g., jdw -->
```

- **Tasks Directory**: Where task output is stored.
- **Artifact Tracking**: Controls whether the task system commits its own
  files to git. See [git-excluded artifacts](git-excluded-artifacts.md).
- **Branch Prefix** (optional): A namespace prefix for suggested branch names
  during the implementation phase's branch preflight check. When the agent
  detects that the user is on `main` or `master`, it suggests a feature branch
  name. If a branch prefix is configured, it is prepended with a `/` separator
  (e.g., `jdw/1-my-task`). If omitted, no prefix is used.

**Example with branch prefix:**
```markdown
# Task System Configuration

## Tasks Directory
tasks/

## Artifact Tracking
committed

## Branch Prefix
jdw
```

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

## `issue-trackers.md`

Issue tracker integration configuration. This **replaces** the task system
default template entirely. When starting a new task, the agent asks if there
is an issue ticket (Linear or Gitea) and uses this file to determine
credential environment variable names and default settings.

API credentials (API keys, tokens) should be stored in environment variables,
not in this file. This file only configures which environment variable names
to use and non-secret settings like the Gitea base URL and default repository.

**Default environment variable names** (used if not overridden):
- `LINEAR_API_KEY` -- Linear personal API key
- `GITEA_URL` -- Gitea instance base URL
- `GITEA_API_TOKEN` -- Gitea personal access token

**Example — Linear only:**
```markdown
# Issue Tracker Configuration

## Environment Variable Names

### Linear
- **API Key Variable**: LINEAR_API_KEY
```

**Example — Gitea with custom env var names:**
```markdown
# Issue Tracker Configuration

## Environment Variable Names

### Gitea
- **URL Variable**: MY_GITEA_URL
- **API Token Variable**: MY_GITEA_TOKEN

## Gitea Settings
- **Default Repo**: myorg/myproject
```

**Example — Both trackers with overrides:**
```markdown
# Issue Tracker Configuration

## Environment Variable Names

### Linear
- **API Key Variable**: ACME_LINEAR_KEY

### Gitea
- **URL Variable**: ACME_GITEA_URL
- **API Token Variable**: ACME_GITEA_TOKEN

## Gitea Settings
- **Default Repo**: acme/backend
```

The **Default Repo** setting allows users to reference Gitea issues with just
`#42` instead of `owner/repo#42`.

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

## `phase-steps/`

Project-specific additional steps that are injected into individual phases.
Each file corresponds to a phase prompt by name and contains extra instructions
that the agent executes at a designated point within that phase. These are
**appended** — they add steps to the phase without replacing any default
behavior.

File names must match the prompt file names in the task system's `prompts/`
directory:

| File | Injection Point |
|------|-----------------|
| `phase-steps/01-definition.md` | After writing the definition, before user review |
| `phase-steps/02-research.md` | After exploring the codebase, before writing the research document |
| `phase-steps/03-plan.md` | After writing the plan, before user review |
| `phase-steps/04-review.md` | During the review loop, before finalizing the plan |
| `phase-steps/05-implement.md` | After all steps are implemented, before the final testing strategy |
| `phase-steps/06-code-review.md` | After the review checklist, before writing the review document |
| `phase-steps/07-review-response.md` | After implementing fixes, before the re-review loop |
| `phase-steps/08-final-polish.md` | After standard polish checks, before writing the polish document |
| `phase-steps/09-retrospective.md` | After gathering user observations, before applying changes |

Only create files for phases where you need additional steps. Missing files
are silently skipped.

**Example — extra type-check step in polish** (`phase-steps/08-final-polish.md`):
```markdown
# Additional Polish Steps

## Run Full Type Check

Run the following command to verify type safety across the full monorepo:

\`\`\`
pnpm tsc --noEmit --project tsconfig.build.json --pretty 2>&1 | head -100
\`\`\`

This project uses composite TypeScript projects with path aliases. The
standard `tsc --noEmit` will fail — you must use `tsconfig.build.json`.
If there are type errors, fix them before proceeding. Do not suppress errors
with `@ts-ignore` or `@ts-expect-error`.
```

**Example — require dependency review during research** (`phase-steps/02-research.md`):
```markdown
# Additional Research Steps

## Check for Circular Dependencies

Run `madge --circular --extensions ts src/` and report any circular
dependency chains that involve files relevant to this task. Note these as
risks in the research document.
```

**Example — extra validation during implementation** (`phase-steps/05-implement.md`):
```markdown
# Additional Implementation Steps

## Verify Bundle Size

After all implementation steps, run `pnpm build && pnpm size-limit`.
If any package exceeds its size budget, investigate and fix before proceeding.
```

## Merge Behavior Summary

| File | Strategy | Effect |
|------|----------|--------|
| `coding-guidelines.md` | Append | Project rules added to defaults |
| `commit-message-format.md` | Replace | Project format overrides default |
| `project-commands.md` | Replace | Project commands override template |
| `issue-trackers.md` | Replace | Project tracker config overrides default |
| `review-profiles/<lang>.md` | Append | Project checks added to defaults |
| `phase-steps/<phase>.md` | Append | Project steps added to phase |
