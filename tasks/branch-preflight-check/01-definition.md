# Task: Add Branch Preflight Check to Implementation Phase

## Source Issue

- **Tracker**: Gitea
- **ID**: chilkari/agent-task-system#1
- **URL**: https://code.chilkari.com/chilkari/agent-task-system/issues/1
- **Title**: Check and confirm branch in preflight checks
- **Status**: open
- **Labels**: none

### Description

As we're beginning the preflight checks, I'd like the task system to look at the current working git branch. If we're using a branch named `master` or `main` -- Prompt the user, that we are working on this branch, and inquire as to whether a feature branch should be created? If using an issue tracker from the 01-definition.md file, offer a suggested branch name starting with the issue identifier followed by a short slug for the task (i.e. ENG-1234-git-branch-handling). The user can also decline, allowing the workflow to proceed on the master/main branch or offer a different branch name. If the user would like a new branch and the name is agreed, go ahead and create that branch before proceeding.

## Problem Statement

The implementation phase (Phase 5) currently begins preflight checks without considering which git branch the user is on. If the user is on `main` or `master`, they may accidentally make all their task commits directly on the primary branch rather than a feature branch. The task system should detect this situation and offer to create a feature branch before proceeding with implementation.

When an issue tracker was used in Phase 1, the suggested branch name should incorporate the issue identifier (e.g., `ENG-1234-git-branch-handling`). When no issue tracker was used, the suggestion should be based on the task slug alone.

If the user declines a feature branch, the decision should be logged in the existing `05-implementation.md` artifact. If uncommitted changes exist (beyond task-system artifacts, which are already ignored), the system should offer to stash them before creating the branch and pop the stash afterward.

## Acceptance Criteria

- [ ] Phase 5 preflight checks detect when the current branch is `main` or `master`
- [ ] User is prompted with the branch name and asked whether to create a feature branch
- [ ] If a source issue exists in `01-definition.md`, the suggested branch name starts with the issue identifier followed by a short task slug (e.g., `ENG-1234-git-branch-handling`)
- [ ] If no source issue exists, the suggested branch name is based on the task slug (e.g., `add-branch-preflight-check`)
- [ ] User can accept the suggestion, provide a different name, or decline (staying on main/master)
- [ ] If the user declines, the decision is logged in `05-implementation.md`
- [ ] If uncommitted changes exist when creating a branch, the system offers to stash them, creates the branch, and pops the stash
- [ ] If the user is already on a non-main/master branch, the check passes silently
- [ ] The new check integrates naturally into the existing preflight checks sequence in `prompts/05-implement.md`

## Scope

### In Scope
- Adding a branch check step to the preflight checks in `prompts/05-implement.md`
- Branch name suggestion logic (with and without issue tracker)
- Stash/unstash handling for uncommitted changes during branch creation
- Logging the branch decision in `05-implementation.md`

### Out of Scope
- Changing any other phases
- Adding branch management at the end of a task (e.g., PR creation)
- Handling merge conflicts or rebasing
- Adding new artifact files for this feature

## Complexity Assessment

**Level**: small

**Reasoning**: This task modifies a single prompt file (`prompts/05-implement.md`) by adding a new preflight check step. The logic is straightforward — check the branch name, prompt the user, optionally create a branch with stash handling. No new files, no architectural changes, no cross-phase dependencies beyond reading `01-definition.md` (which is already a prerequisite for Phase 5).
