# Task Summary: Add Branch Preflight Check to Implementation Phase

## Task
Add a branch detection step to Phase 5 (Implementation) preflight checks that
warns when the user is on `main` or `master` and offers to create a feature
branch with a smart name suggestion based on issue tracker identifiers and a
configurable namespace prefix.

## What Was Delivered

- [x] Phase 5 preflight checks detect `main`/`master` branch: Added as step 2 in the preflight checks sequence.
- [x] User prompted to create a feature branch: Three options presented (accept suggestion, provide different name, decline).
- [x] Issue-based branch naming for Gitea: Extracts numeric portion from `owner/repo#N`, never uses `#` in branch names.
- [x] Issue-based branch naming for Linear: Uses full identifier (e.g., `ENG-123`).
- [x] Task-slug-only fallback when no issue: Branch name based on task directory name.
- [x] Configurable `Branch Prefix` in `.task-system/config.md`: Namespace prefix separated by `/` (e.g., `jdw/1-my-task`).
- [x] Decision logged in `05-implementation.md`: New `## Branch` section in the implementation tracker.
- [x] Stash/pop handling for uncommitted changes: Offered before branch creation, popped after, conflict handling documented.
- [x] Silent pass-through for non-main branches and detached HEAD state.

## Files Changed
- `prompts/05-implement.md` — added branch check step (step 2), renumbered existing steps, added Branch section to implementation tracker template
- `docs/configuration.md` — documented Branch Prefix config option with description and examples

## Review Rounds
2 rounds. Round 1 found one important issue (detached HEAD handling) and one
suggestion (config template discoverability). Both addressed in round 2, which
came back clean.

## Notable Decisions
- Branch prefix is configured in `.task-system/config.md` rather than derived from issue tracker identifiers, giving users a consistent namespace across tasks.
- `#` characters are never used in branch names — Gitea issue numbers use only the numeric portion.
- `/` is used as the separator between prefix and branch name (e.g., `jdw/1-my-task`), creating a directory-like namespace in git.
- Detached HEAD state (empty result from `git branch --show-current`) silently skips the branch check.
