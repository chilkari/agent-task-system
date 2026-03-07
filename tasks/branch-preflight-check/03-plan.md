# Plan: Add Branch Preflight Check to Implementation Phase

## Summary

Add a new preflight check step to `prompts/05-implement.md` that detects when
the user is on `main` or `master`, offers to create a feature branch with a
smart name suggestion (based on issue identifier or task slug), handles
stashing uncommitted changes during branch creation, and logs the decision in
the implementation tracker.

## Coding Guidelines

### General Principles

- Prefer simple, readable code over clever code.
- No duplication — search for existing patterns before introducing new ones.
- Handle errors explicitly.
- Use meaningful, descriptive names.
- Follow existing codebase conventions.

### Comments

- Code should be self-documenting. No comments restating the obvious.
- Comments only for genuinely complex or non-obvious logic.
- No TODO/FIXME/HACK placeholders in shipped content.

### Unicode

- Unicode characters are allowed in this repo's markdown prompt templates
  where they improve readability (arrows →, em-dashes —, section signs §).

### Prompt Template Rules

- All changes must preserve cross-phase file references.
- Phase transition logic changes require updating skill.md routing table.
- Follow the self-documenting template pattern.

### Git Operations

- Never push to a remote. Pushing is always the user's responsibility.

### Commit Message Format

Plain descriptive summaries without type prefixes. Lowercase, imperative mood,
under 72 characters. No period at the end.

### Project Commands

No build, lint, test, or format commands — this is a markdown-only repo.

## Implementation Steps

### Step 1: Add branch check to preflight checks in `prompts/05-implement.md`

**Difficulty/Risk**: low

Insert a new numbered step after the existing step 1 (git working tree check)
and before step 2 (existing tests). The new step should:

1. **Detect current branch**: Run `git branch --show-current` to get the
   current branch name.

2. **Check if on main/master**: If the branch is `main` or `master`, proceed
   with the prompt. If on any other branch, skip silently — no output needed.

3. **Build a suggested branch name**:
   - Read `01-definition.md` from the task directory.
   - Check `.task-system/config.md` (the project config directory) for a
     `## Branch Prefix` section. If present, the value is used as a namespace
     prefix followed by `/` (e.g., `jdw/...`). If absent, no prefix is used.
   - If a "Source Issue" section exists, extract the issue identifier (the
     **ID** field, e.g., `ENG-123` or `chilkari/agent-task-system#1`).
     - For Gitea identifiers in `owner/repo#N` format, extract the issue
       number `N` and use it as the identifier portion.
     - For Linear identifiers like `ENG-123`, use the full identifier.
   - Build the branch name using this pattern:
     - With prefix + issue: `<prefix>/<identifier>-<task-slug>`
     - With prefix, no issue: `<prefix>/<task-slug>`
     - No prefix + issue: `<identifier>-<task-slug>`
     - No prefix, no issue: `<task-slug>`
   - Examples (with `branch-prefix: jdw`):
     - Gitea issue `chilkari/agent-task-system#1` → `jdw/1-branch-preflight-check`
     - Linear issue `ENG-123` → `jdw/ENG-123-branch-preflight-check`
     - No issue → `jdw/branch-preflight-check`
   - Examples (no branch prefix configured):
     - Gitea issue `#1` → `1-branch-preflight-check`
     - Linear issue `ENG-123` → `ENG-123-branch-preflight-check`
     - No issue → `branch-preflight-check`
   - The slug portion comes from the task directory name (already a short,
     lowercase, hyphenated name).
   - Never use `#` in branch names — always use the numeric portion only.

4. **Prompt the user**: Inform the user they are on `main`/`master` and
   present three options:
   - Accept the suggested branch name
   - Provide a different branch name
   - Decline and continue on the current branch

5. **Handle the user's choice**:
   - **Accept or provide a name**:
     - Check for uncommitted changes to project source code (using the same
       logic as step 1 — ignoring tasks directory and project config directory
       files).
     - If uncommitted changes exist, offer to stash them with
       `git stash --include-untracked`.
     - Create the branch with `git checkout -b <branch-name>`.
     - If changes were stashed, pop them with `git stash pop`.
   - **Decline**: Continue on the current branch. No git operations needed.

6. **Log the decision**: Record the branch decision in the implementation
   tracker (`05-implementation.md`) under a new "## Branch" section placed
   after the "## Rollback Point" section but before "## Steps". Format:

   ```markdown
   ## Branch
   **Working branch**: <branch name>
   **Created new branch**: yes | no (continued on <branch>)
   ```

**Rationale**: This is the core feature. It must come after the working tree
check (step 1) because we need to know about uncommitted changes, but before
tests and build checks (steps 2–3) so the user is working on the right branch
before any verification runs. The implementation tracker template also needs
updating to include the Branch section.

### Step 2: Document the `Branch Prefix` config option in `docs/configuration.md`

**Difficulty/Risk**: low

Add documentation for the new `## Branch Prefix` config option to
`docs/configuration.md` so users know how to configure it. This is an optional
field in `.task-system/config.md` that provides a namespace prefix for
suggested branch names (e.g., `jdw`, `feature`). When set, branch suggestions
use the format `<prefix>/<identifier>-<slug>`.

**Rationale**: Users need to know the option exists and how to set it. The
configuration docs are the canonical place for this.

### Step 3: Update the implementation tracker template in `prompts/05-implement.md`

**Difficulty/Risk**: low

Update the `05-implementation.md` template shown in the "Create Implementation
Tracker" section to include the Branch section:

```markdown
# Implementation: <task title>

## Rollback Point
**Task start**: <commit SHA>

## Branch
**Working branch**: <branch name>
**Created new branch**: <yes | no (continued on <branch>)>

## Steps
...
```

**Rationale**: The template should reflect the actual output format so agents
creating the tracker for the first time include the branch information.

## Testing Strategy

Since this is a markdown-only repo with no executable code, testing is manual
verification:

- **Verification 1**: Read the modified `prompts/05-implement.md` and confirm
  the branch check step is correctly positioned after the git working tree
  check and before existing tests.
- **Verification 2**: Confirm the branch suggestion logic covers all cases:
  with/without issue identifier, with/without branch prefix config, Gitea vs
  Linear identifier formats.
- **Verification 3**: Confirm the stash/pop flow is described for uncommitted
  changes during branch creation.
- **Verification 4**: Confirm the implementation tracker template includes the
  Branch section.
- **Verification 5**: Confirm all existing preflight check steps are preserved
  with correct numbering.
- **Verification 6**: Confirm no cross-phase file references are broken.
- **Verification 7**: Confirm `docs/configuration.md` documents the new
  `Branch Prefix` config option.
- **Verification 8**: Confirm `#` is never used in branch name examples or
  logic — only the numeric portion of Gitea identifiers is used.

## Documentation Impact

No separate documentation changes needed. The prompt file is self-documenting
and serves as its own specification. The existing `docs/phases.md` describes
Phase 5 at a high level and does not enumerate individual preflight steps, so
it does not need updating.

## Breaking Changes

No breaking changes. This adds a new step to the preflight checks without
modifying the behavior of existing steps. The implementation tracker template
gains a new section, but this is additive — existing trackers without a Branch
section will still work correctly when read by other phases.

## Risks & Open Questions

- **Stash conflicts**: If `git stash pop` encounters merge conflicts after
  creating the branch, the prompt should instruct the agent to inform the user
  and let them resolve manually. This is an edge case since the stash is
  popping onto a branch that was just created from the same commit, so
  conflicts should not occur in practice.
