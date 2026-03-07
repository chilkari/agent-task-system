# Research: Issue Tracker Integration (Linear & Gitea)

## Relevant Files & Modules

### Core files to modify

| File | Role | Change Needed |
|------|------|---------------|
| `prompts/01-definition.md` | Phase 1 instructions | Add "Check for Issue Ticket" section early in the flow, before "Gather Information". Add `## Source Issue` to output template. |
| `skill.md` | Skill definition & file resolution | Add file resolution entry for `issue-trackers.md` config. |
| `defaults/issue-trackers.md` | *New file* | Default/template config for tracker settings: env var names, Gitea URL, default repo. |
| `docs/configuration.md` | Config documentation | Document the new `issue-trackers.md` file, env vars, and merge behavior. |
| `docs/phases.md` | Phase reference | Update Phase 1 description to mention issue ticket support. |

### Files that reference `01-definition.md` (downstream consumers)

These phases read `01-definition.md` but reference it generically for problem statement, acceptance criteria, and scope. Adding a `## Source Issue` section will not break any of them — the new section simply provides additional context they can use.

- `prompts/02-research.md` — reads definition for task scope
- `prompts/03-plan.md` — reads definition for acceptance criteria & scope
- `prompts/04-review.md` — reads definition for problem statement & criteria
- `prompts/06-code-review.md` — reads definition for acceptance criteria verification
- `prompts/08-final-polish.md` — reads definition for final summary criteria check
- `prompts/09-retrospective.md` — reads definition for original task context

### Configuration pattern files (existing examples to follow)

| File | Merge Strategy | Notes |
|------|---------------|-------|
| `defaults/coding-guidelines.md` | Append | Project adds rules to defaults |
| `defaults/commit-message-format.md` | Replace | Project fully overrides |
| `defaults/project-commands.md` | Replace | Project fully overrides |

The new `issue-trackers.md` should use **replace** semantics — if a project provides its own, it fully overrides the default template (since each project has its own trackers, URLs, and env var names).

### Setup prompt

`prompts/00-setup.md` — Currently handles initial project setup. Could mention issue tracker config as an optional step, but since credentials are env vars and the config file is optional, this is not strictly necessary. A brief mention (similar to how phase-steps are mentioned in Step 3b) would be sufficient.

## Architecture & Patterns

### File resolution pattern in `skill.md`

Each config file follows a consistent pattern:
1. A heading describing the file type
2. Priority order: project config → default
3. Merge strategy (append or replace)
4. The agent reads at the appropriate time

The new `issue-trackers.md` should follow this exact pattern. Since tracker config is entirely project-specific (URLs, env var names), **replace** semantics are appropriate.

### Phase prompt structure

Each prompt in `prompts/` follows a consistent structure:
1. Title and context
2. Prerequisites (files to read)
3. Instructions (step-by-step sections)
4. Project-specific steps injection point
5. Output template
6. Review with user
7. Update state
8. Next steps

The issue ticket check should be a new section in `01-definition.md` positioned **before** "Gather Information" so fetched issue data can seed the conversation.

### Default config file pattern

Default files in `defaults/` serve as templates with comments explaining usage. They are self-documenting. The new `issue-trackers.md` should follow this pattern — provide the default env var names, explain the format, and include commented examples.

### How the agent would fetch issue data

Since this system has no runtime code (everything is markdown interpreted by agents), the agent uses `curl` to call APIs. The prompt needs to include:
- Exact `curl` commands for both Linear (GraphQL) and Gitea (REST)
- How to parse the response (the agent reads JSON output)
- Error handling guidance (bad credentials, not found, network errors)

## Dependencies & Risks

1. **API availability** — If the tracker API is unreachable, the agent needs graceful degradation (proceed without issue data, inform the user).
2. **Credential security** — Env vars are the standard approach. The prompt should warn against putting secrets in committed config files.
3. **Response parsing** — Agents interpret `curl` JSON output directly. Large issues with many comments could produce verbose output. The prompt should guide truncation if needed.
4. **Linear GraphQL schema** — The prompt needs the correct query structure. Linear's API uses issue identifiers like `ENG-123` which require querying by `team.key` and `number`.
5. **Gitea API variations** — Different Gitea versions may have slightly different API responses. The REST API is stable for basic issue fields.

## Key Decisions & Constraints

1. **One tracker per task** — The user identifies an issue (which implicitly selects the tracker). No need for multi-tracker support within a single task.
2. **Replace semantics for config** — Project config fully overrides the default template since tracker settings are entirely project-specific.
3. **Env var names are configurable** — Defaults: `LINEAR_API_KEY`, `GITEA_URL`, `GITEA_API_TOKEN`. Overridable in `issue-trackers.md` to avoid conflicts with existing env vars.
4. **Issue data stored in `01-definition.md`** — The `## Source Issue` section captures all fetched data so downstream phases don't need to re-fetch.
5. **Comments included** — Issue comments are fetched and included as they often contain important context and decisions.
6. **No write-back** — The system does not update issue status or post comments to the tracker (explicitly out of scope).

## External References

- **Linear API docs**: https://developers.linear.app/docs/graphql/working-with-the-graphql-api — GraphQL endpoint at `https://api.linear.app/graphql`, authentication via `Authorization: <api-key>` header (no Bearer prefix).
- **Linear issue identifier format**: Team key + number (e.g., `ENG-123`). Query uses `issueFilterByTeamAndNumber` or `issue` with identifier.
- **Gitea API docs**: https://gitea.com/api/swagger — REST API at `{base_url}/api/v1/repos/{owner}/{repo}/issues/{number}`. Comments at `.../comments`. Auth via `Authorization: token <token>` header.

## Language(s) Detected

**Primary**: Markdown (prompt templates)

No programming language review profiles apply — this is a documentation/prompt-only change. The language field in `task-state.md` should remain `markdown`.
