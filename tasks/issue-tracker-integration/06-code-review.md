# Code Review: Issue Tracker Integration (Linear & Gitea)

## Review Summary

Reviewed 6 files changed (265 lines added, 2 removed) across `defaults/`,
`docs/`, `prompts/`, and `skill.md`. The implementation is clean, well-structured,
and follows established patterns throughout the codebase. All changes are
additive with no disruption to existing functionality. Two important items
found and one suggestion.

## Critical

None.

## Important

### I1: Linear curl command uses hardcoded env var name in example
**File**: `prompts/01-definition.md`
**Location**: Fetch from Linear, step 3 curl example
**Issue**: The curl example hardcodes `$LINEAR_API_KEY` in the Authorization
header, but the instructions in step 1 correctly say to read the env var name
from the config. An agent following the example literally might use the
hardcoded name instead of the configured one. The Gitea examples have the
same issue with `$GITEA_API_TOKEN` and `$GITEA_URL`.
**Recommended Fix**: Add a note to the curl examples clarifying they show the
default variable names and the agent should substitute the actual variable
name from the resolved config. Alternatively, use a placeholder like
`$<api-key-variable>` in the curl commands.
**Disposition**: Fixed -- replaced hardcoded env var names with `$<api-key-variable>`,
`$<url-variable>`, and `$<token-variable>` placeholders in curl examples. Added
notes above each curl block explaining the agent should substitute the actual
variable name from the resolved config.

### I2: Tracker type detection may be ambiguous for URLs with `/issues/`
**File**: `prompts/01-definition.md`
**Location**: Detect Tracker Type section
**Issue**: The detection rule "URL containing `/issues/` in the path" assumes
any URL with `/issues/` is Gitea. A Linear issue URL (e.g.,
`https://linear.app/team/issue/ENG-123`) does not contain `/issues/` so this
works in practice, but if Linear ever changes their URL format, or if a user
pastes a different tracker's URL containing `/issues/`, the detection could
misfire. This is low probability but worth noting.
**Recommended Fix**: Add a note that URL-based detection assumes Gitea. If the
URL contains `linear.app`, treat it as Linear instead. This makes the
detection more robust.
**Disposition**: Fixed -- added `linear.app` URL detection rule before the
Gitea `/issues/` URL rule in the tracker type detection section.

## Suggestions

### S1: Config file examples inconsistency — backticks vs. no backticks
**File**: `defaults/issue-trackers.md` vs `docs/configuration.md`
**Issue**: In `defaults/issue-trackers.md`, env var names are wrapped in
backticks (e.g., `` `LINEAR_API_KEY` ``). In the `docs/configuration.md`
examples, the env var names in the markdown list items under "Default
environment variable names" use backticks, but inside the example markdown
code blocks they do not (which is correct since they are inside code blocks).
In the example config files within `docs/configuration.md`, the env var
override values are shown without backticks (e.g., `LINEAR_API_KEY` not
`` `LINEAR_API_KEY` ``). The `defaults/issue-trackers.md` uses backticks
around the values (`` `LINEAR_API_KEY` ``). These should be consistent — since
the config file is parsed as markdown by the agent, backticks around values
in the list items would be included as part of the value.
**Recommended Fix**: Remove backticks from the values in
`defaults/issue-trackers.md` list items so they match the examples in
`docs/configuration.md`. The values should be plain text:
`- **API Key Variable**: LINEAR_API_KEY` (not `` `LINEAR_API_KEY` ``).
**Disposition**: Accepted -- removed backticks from all env var name values
in `defaults/issue-trackers.md`.

## Language Profile Check

No language review profiles apply — this is a markdown-only change. Confirmed
no programming language code was introduced.

## Acceptance Criteria Verification

- [x] Phase 1 (Definition) asks the user if there is an issue ticket to work from — met, "Check for Issue Ticket" section added before "Gather Information"
- [x] Linear issues can be fetched via API using a personal API key — met, GraphQL query with team key + issue number filter
- [x] Gitea issues can be fetched via API using a personal access token — met, REST endpoints for issue + comments
- [x] Fetched data includes title, description, labels, status, and comments — met, all fields extracted in both tracker flows
- [x] Issue context is incorporated as seed material into the task definition — met, "Use as Seed Material" section instructs agent
- [x] A `## Source Issue` section is added to the definition output for traceability — met, conditional section in output template
- [x] Credentials use environment variables with configurable names — met, config file specifies env var names with defaults
- [x] A new `issue-trackers.md` config file is supported in project config directories — met, file resolution in skill.md with replace semantics
- [x] A default `issue-trackers.md` template is provided in `defaults/` — met, self-documenting template created
- [x] `skill.md` is updated with file resolution rules for issue tracker config — met, new entry added
- [x] Issue context is stored in the definition artifact for cross-phase reference — met, Source Issue section in 01-definition.md
- [x] Documentation is updated — met, docs/configuration.md and docs/phases.md updated, setup prompt mentions integration
- [x] Comments from the issue are included in the fetched context — met, both Linear and Gitea fetch flows include comments, 20-comment limit documented
