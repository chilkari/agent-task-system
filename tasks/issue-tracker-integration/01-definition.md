# Task: Issue Tracker Integration (Linear & Gitea)

## Problem Statement

Currently, when a user starts a new task, the task definition phase gathers
all context through interactive conversation. There is no mechanism to import
context from an existing issue ticket in an external tracker. This means users
with detailed Linear or Gitea issues must manually re-describe the problem,
acceptance criteria, and context — duplicating work and risking information
loss.

The task system should support fetching issue details (title, description,
labels, comments) from Linear and Gitea issue trackers and using that content
as seed material throughout the workflow. This includes the definition phase
(pre-populating the problem statement) and later phases where issue context
may be useful (e.g., research, planning, implementation).

## Acceptance Criteria

- [ ] Phase 1 (Definition) asks the user if there is an issue ticket to work from
- [ ] Linear issues can be fetched via API using a personal API key (issue identifier like `ENG-123`)
- [ ] Gitea issues can be fetched via API using a personal access token (full URL or `owner/repo#123` format)
- [ ] Fetched data includes title, description, labels, status, and comments
- [ ] Issue context is incorporated as seed material into the task definition (problem statement, acceptance criteria, scope)
- [ ] A `## Source Issue` section is added to the definition output for traceability
- [ ] Credentials use environment variables with configurable names (defaults provided, overridable in project config)
- [ ] A new `issue-trackers.md` config file is supported in project config directories for non-secret settings (tracker URL, default repo, env var name overrides)
- [ ] A default `issue-trackers.md` template is provided in `defaults/`
- [ ] `skill.md` is updated with file resolution rules for issue tracker config
- [ ] Issue context is stored in the definition artifact (`01-definition.md`) so later phases can reference it without re-fetching
- [ ] Documentation is updated (`docs/configuration.md` at minimum)
- [ ] Comments from the issue are included in the fetched context

## Scope

### In Scope
- Adding issue ticket prompt to Phase 1 (Definition)
- User identifies the issue (which determines the tracker for this task — one tracker per task)
- Linear API integration (GraphQL, personal API key)
- Gitea API integration (REST, personal access token)
- Environment variable configuration with overridable names
- New `issue-trackers.md` default config template
- File resolution rules in `skill.md` for issue tracker config
- `## Source Issue` section in definition output with full fetched context
- Storing fetched issue context in `01-definition.md` so later phases can reference it without re-fetching
- Documentation updates

### Out of Scope
- Updating issue status in the tracker from the task system
- Creating issues in the tracker
- Other trackers (GitHub Issues, Jira, etc.) — can be added later following the same pattern
- Re-fetching issue data in later phases (definition artifact serves as the source of truth)
- Updating or referencing the issue in later workflow phases (future enhancement)
- OAuth flows — only personal API keys/tokens
- Automated PR linking

## Complexity Assessment

**Level**: Medium

**Reasoning**: This touches multiple files across the repo (prompts, skill.md,
defaults, docs) and introduces a new cross-cutting concept (issue tracker
integration). It requires understanding how phases reference each other and
how the file resolution system works. However, the scope is well-defined, the
codebase is entirely markdown prompt templates, and the pattern for adding new
config files is established (see how coding-guidelines, commit-message-format,
and project-commands work). Research should focus on understanding which phases
and prompt files need modification and the exact API shapes for Linear and
Gitea.
