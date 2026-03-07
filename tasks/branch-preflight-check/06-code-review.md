# Code Review: Add Branch Preflight Check to Implementation Phase

## Review Summary

Reviewed changes to `prompts/05-implement.md` (branch check step + tracker
template update) and `docs/configuration.md` (Branch Prefix documentation).
Overall quality is good — the new preflight step is well-structured, clearly
written, covers all the planned scenarios, and integrates naturally into the
existing preflight sequence. One important item and one suggestion identified.

## Critical

None.

## Important

### I1: Branch check should also detect detached HEAD state
**File**: `prompts/05-implement.md`
**Location**: Step 2 (Branch check)
**Issue**: `git branch --show-current` returns an empty string when in a
detached HEAD state. The current instructions only check for `main` or
`master` and silently skip otherwise — which means a detached HEAD would be
silently skipped too. This is technically correct behavior (the user isn't on
main/master), but the agent should be aware that an empty result means
detached HEAD rather than a named branch. An agent could potentially
misinterpret an empty result.
**Recommended Fix**: Add a note that if `git branch --show-current` returns
empty, the repository is in a detached HEAD state — skip the branch check
silently (same as being on a feature branch).
**Disposition**: Fixed — added detached HEAD note at the end of the branch check step.

## Suggestions

### S1: Config example in docs could show the field in the base template
**File**: `docs/configuration.md`
**Location**: The `config.md` section
**Issue**: The base `config.md` template example (lines 14–22) does not
include `Branch Prefix`, but there is a separate "Example with branch prefix"
below. This is fine, but adding an optional commented-out line in the base
template could make discoverability easier for users scanning the template
without reading the full description.
**Recommended Fix**: Optional — add a commented-out `## Branch Prefix` to the
base template, e.g.:
```markdown
## Branch Prefix
<!-- Optional: a namespace prefix for suggested branch names, e.g., jdw -->
```
**Disposition**: Accepted — added commented-out `## Branch Prefix` placeholder to the base config template in docs.

## Language Profile Check

No language-specific review profile applies — this is a markdown prompt
template repo with no executable code.

## Acceptance Criteria Verification

- [x] Phase 5 preflight checks detect when the current branch is `main` or `master`: Met — step 2 checks `git branch --show-current` against `main`/`master`.
- [x] User is prompted with the branch name and asked whether to create a feature branch: Met — three options presented (accept, provide different, decline).
- [x] If a source issue exists in `01-definition.md`, the suggested branch name starts with the issue identifier followed by a short task slug: Met — Gitea extracts numeric portion, Linear uses full identifier, both prepend to task slug.
- [x] If no source issue exists, the suggested branch name is based on the task slug: Met — pattern `<prefix>/<task-slug>` or just `<task-slug>`.
- [x] User can accept the suggestion, provide a different name, or decline (staying on main/master): Met — three options explicitly listed.
- [x] If the user declines, the decision is logged in `05-implementation.md`: Met — Branch section in tracker records the decision.
- [x] If uncommitted changes exist when creating a branch, the system offers to stash them, creates the branch, and pops the stash: Met — stash/pop flow described with conflict handling.
- [x] If the user is already on a non-main/master branch, the check passes silently: Met — "If the branch is not `main` or `master`, skip this check silently."
- [x] The new check integrates naturally into the existing preflight checks sequence: Met — positioned as step 2 between working tree check and tests, existing steps renumbered correctly.
