# Phase 1: Task Definition

You are starting a new task. Your goal is to capture a clear, scoped problem
statement and acceptance criteria through an interactive conversation with the
user.

## Instructions

### Check for Issue Ticket

Before gathering information, ask the user if there is an issue ticket for
this task. They can provide:

- A **Linear issue identifier** (e.g., `ENG-123`)
- A **Gitea issue URL** (e.g., `https://gitea.example.com/owner/repo/issues/42`)
- A **Gitea shorthand** (e.g., `owner/repo#42`, or just `#42` if a default
  repo is configured)
- **"no"** or similar to skip

If the user provides an issue reference, fetch the issue details:

#### Resolve Configuration

Read the issue tracker configuration using the file resolution rules defined
in `skill.md` § "Issue Tracker Configuration":
1. If `issue-trackers.md` exists in the project config directory, use it.
2. Otherwise, use `defaults/issue-trackers.md` from the task system.

From the configuration, determine the environment variable names for
credentials (the config may override the defaults).

#### Detect Tracker Type

Determine the tracker from the input format:
- Input matching `<LETTERS>-<NUMBER>` (e.g., `ENG-123`) -- **Linear**
- Input is a URL containing `linear.app` -- **Linear**
- Input containing a URL with `/issues/` in the path -- **Gitea**
- Input matching `<owner>/<repo>#<number>` or `#<number>` -- **Gitea**

#### Fetch from Linear

1. Read the API key from the environment variable specified in the config
   (default: `LINEAR_API_KEY`). If not set, inform the user and offer to
   proceed without issue data.
2. Parse the identifier into team key and issue number (e.g., `ENG-123` --
   team key `ENG`, number `123`).
3. Fetch the issue. Use the env var name from the resolved config (shown
   below as `$<api-key-variable>` -- substitute the actual variable name,
   which defaults to `LINEAR_API_KEY`):

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Content-Type: application/json" \
  -H "Authorization: $<api-key-variable>" \
  -d '{
    "query": "query($teamKey: String!, $issueNumber: Float!) { issues(filter: { team: { key: { eq: $teamKey } }, number: { eq: $issueNumber } }) { nodes { identifier url title description state { name } priority priorityLabel labels { nodes { name } } assignee { name } comments { nodes { body user { name } createdAt } } } } }",
    "variables": { "teamKey": "<TEAM>", "issueNumber": <NUMBER> }
  }'
```

4. Extract from the response: identifier, URL, title, description, state,
   priority, labels, assignee, and comments.

#### Fetch from Gitea

1. Read the base URL from the environment variable specified in the config
   (default: `GITEA_URL`). Read the API token from the env var specified in
   the config (default: `GITEA_API_TOKEN`). If either is not set, inform the
   user and offer to proceed without issue data.
2. Determine the owner, repo, and issue number:
   - From a full URL: parse the path segments.
   - From `owner/repo#N`: parse directly.
   - From `#N`: use the default repo from the config. If no default repo is
     configured, ask the user for the owner/repo.
3. Fetch the issue. Use the env var names from the resolved config (shown
   below as `$<url-variable>` and `$<token-variable>` -- substitute the
   actual variable names, which default to `GITEA_URL` and
   `GITEA_API_TOKEN`):

```bash
curl -s -H "Authorization: token $<token-variable>" \
  "$<url-variable>/api/v1/repos/<owner>/<repo>/issues/<number>"
```

4. Fetch comments:

```bash
curl -s -H "Authorization: token $<token-variable>" \
  "$<url-variable>/api/v1/repos/<owner>/<repo>/issues/<number>/comments"
```

5. Extract from the responses: title, body, state, labels, assignee, and
   comments (author, date, body).

#### Handle Errors

If the fetch fails for any reason (auth failure, issue not found, network
error):
- Report the error clearly to the user.
- Offer to retry, provide a different issue reference, or proceed without
  issue data.
- Do not block the task definition on a failed fetch.

#### Present Issue Data

Show the fetched issue data to the user in a readable format: title,
status/state, labels, priority (if Linear), description, and comments.

Include up to 20 of the most recent comments. If there are more than 20,
note that earlier comments were omitted and include only the 20 most recent.

#### Use as Seed Material

With issue data available, do not ask the user to re-describe the problem
from scratch. Instead, use the issue content to pre-populate your
understanding of:
- What the problem is (from the issue description)
- What "done" looks like (from acceptance criteria or checklists in the issue)
- Relevant context (from comments and labels)

Then ask targeted clarifying questions to fill in gaps not covered by the
issue, rather than starting from a blank slate.

### Gather Information

Ask the user to describe the task in their own words. Listen for:
- What is the problem or need?
- What should change as a result of this task?
- What does "done" look like?

Ask clarifying questions to fill in gaps. Do not make assumptions about scope
or requirements -- ask. Good clarifying questions include:
- What is in scope and what is explicitly out of scope?
- Are there specific constraints (performance, compatibility, dependencies)?
- What are the expected inputs and outputs?
- How should this be tested or verified?
- Is there existing code or prior art related to this task?

### Assess Complexity

Based on the user's description and your understanding of the codebase,
recommend a complexity level:

- **Small**: The task is straightforward, touches a small number of files, and
  the necessary context is obvious or easily gathered during planning. Research
  phase will be skipped.
- **Medium**: The task requires understanding several modules or patterns in the
  codebase, but the scope is well-defined. Research will be targeted and
  focused.
- **Large**: The task is complex, touches many parts of the codebase, requires
  understanding architecture and data flows, or involves significant risk.
  Research will be thorough and broad.

Explain your reasoning for the complexity assessment. The user may override it.

### Write the Definition

Create the task directory at `<tasks-dir>/<task-slug>/` where `<task-slug>` is
a short, lowercase, hyphenated name derived from the task description.

Write `01-definition.md` in the task directory with the following sections:

```markdown
# Task: <descriptive title>

<!-- Include the Source Issue section only if an issue was fetched. Omit it
     entirely if the user did not provide an issue ticket. -->

## Source Issue

- **Tracker**: <Linear | Gitea>
- **ID**: <identifier, e.g., ENG-123 or owner/repo#42>
- **URL**: <direct link to the issue>
- **Title**: <original issue title>
- **Status**: <issue status/state>
- **Labels**: <comma-separated labels, or "none">

### Description

<Original issue description/body, preserved verbatim>

### Comments

<!-- Include up to 20 most recent comments. If more than 20 exist, note that
     earlier comments were omitted. Omit this subsection if there are no
     comments. -->

#### <author> (<date>)
<comment body>

<!-- End of Source Issue section -->

## Problem Statement

<What needs to change and why. Be specific about the current state and the
desired state.>

## Acceptance Criteria

<Concrete, testable conditions that must be true when the task is complete.
Each criterion should be verifiable. Include general testing expectations
(e.g., "unit tests for new functions", "manual verification of CLI output").
These remain high-level here and become specific in the planning phase.>

- [ ] <criterion 1>
- [ ] <criterion 2>
- [ ] ...

## Scope

### In Scope
- <what this task will do>

### Out of Scope
- <what this task will explicitly not do>

## Complexity Assessment

**Level**: <small | medium | large>

**Reasoning**: <why this complexity level was chosen>
```

### Project-Specific Steps

If `phase-steps/01-definition.md` exists in the project config directory, read
it and execute the additional steps it describes now.

### Review with User

Present the drafted definition to the user. Ask them to review each section:
- Is the problem statement accurate and complete?
- Are the acceptance criteria correct and sufficient?
- Is the scope right -- anything missing or anything that should be excluded?
- Do they agree with the complexity assessment?

Revise until the user approves the definition.

### Update State

Create or update `task-state.md` in the task directory:

```markdown
# Task State

- **Task**: <task slug>
- **Current Phase**: 2-research (or 3-plan if complexity is small)
- **Complexity**: <small | medium | large>
- **Language(s)**: <to be determined>
- **Phase History**:
  - Phase 1 (Definition): completed <timestamp>
```

## Next Steps

Once the user approves the definition and state is updated, immediately
proceed to the next phase without waiting for additional user input:

- If complexity is **small**: Load and follow `prompts/03-plan.md` now.
- If complexity is **medium** or **large**: Load and follow `prompts/02-research.md` now.
