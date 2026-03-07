# Plan: Issue Tracker Integration (Linear & Gitea)

## Summary

Add support for importing issue context from Linear and Gitea issue trackers
during task definition. The agent asks if there is an issue ticket, fetches
its details (title, description, labels, comments) via API, and uses that
content to seed the task definition. Fetched data is stored in the definition
artifact for downstream phase reference. Configuration supports environment
variables with overridable names and a new `issue-trackers.md` config file.

## Coding Guidelines

### General Principles
- Prefer simple, readable prose over clever constructions.
- No duplication — reuse established patterns from existing prompts and config files.
- Follow existing codebase conventions for file structure, naming, and formatting.

### Prompt Template Conventions
- Preserve all cross-phase file references (phases reference each other's output by filename).
- Follow the established prompt structure: title → prerequisites → instructions → project-specific steps → output template → user review → state update → next steps.
- New sections in prompts should follow the tone and level of detail of existing sections.

### Config File Conventions
- New default config files follow the self-documenting template pattern (see existing files in `defaults/`).
- File resolution entries in `skill.md` follow the established format: heading, priority order, merge strategy.

### Documentation Conventions
- Documentation updates should follow the structure and style of existing docs.
- Cross-references between docs use relative markdown links.
- Configuration docs include examples and the merge behavior summary table.

### Text and Formatting
- No unicode special characters — use ASCII equivalents.
- Straight quotes, hyphens, three dots instead of typographic equivalents.

### Commit Messages
- Plain descriptive summaries, no type prefixes.
- Imperative mood, under 72 characters, no period at the end.

## Implementation Steps

### Step 1: Create `defaults/issue-trackers.md`
**Difficulty/Risk**: Low

Create the new default config file at `defaults/issue-trackers.md`. This is
the template that projects override with their own settings. It should:

- Document the supported trackers (Linear, Gitea)
- Define default environment variable names:
  - `LINEAR_API_KEY` for Linear API key
  - `GITEA_URL` for Gitea instance base URL
  - `GITEA_API_TOKEN` for Gitea personal access token
- Explain that env var names can be overridden in project config
- Show the config file format with examples
- Include commented examples for both trackers

Format:

```markdown
# Issue Tracker Configuration

This file configures issue tracker integration. The agent uses this to fetch
issue details when starting a new task.

Projects can override this file entirely by placing an `issue-trackers.md` in
their project config directory.

## Environment Variable Names

These are the default environment variable names. Override them in your
project's `issue-trackers.md` if they conflict with existing env vars.

### Linear
- **API Key Variable**: LINEAR_API_KEY

### Gitea
- **URL Variable**: GITEA_URL
- **API Token Variable**: GITEA_API_TOKEN

## Gitea Settings

<!-- If using Gitea, uncomment and configure: -->
<!-- - **Default Repo**: owner/repo -->
```

**Rationale**: Establishes the config file pattern needed before the prompt
and skill.md can reference it. Follows the self-documenting template pattern
of existing default files.

### Step 2: Add file resolution rules to `skill.md`
**Difficulty/Risk**: Low

Add a new "Issue Tracker Configuration" subsection under the "File Resolution"
section in `skill.md`, after the existing "Language Review Profiles" entry and
before the "Phase Steps" entry. Follow the established format:

```markdown
### Issue Tracker Configuration
1. If `issue-trackers.md` exists in the project config directory, use it
   (full replacement).
2. Otherwise, use `defaults/issue-trackers.md` from the task system.
```

**Rationale**: File resolution is defined in `skill.md`. The agent needs to
know how to find the issue tracker config. Replace semantics match the
project-commands pattern since tracker settings are entirely project-specific.

### Step 3: Modify `prompts/01-definition.md` — add issue ticket flow
**Difficulty/Risk**: Medium

This is the core change. Add a new section **"Check for Issue Ticket"** after
the phase title/intro but **before** "Gather Information". This section
instructs the agent to:

1. **Read the issue tracker config** — resolve `issue-trackers.md` using the
   file resolution rules from Step 2.

2. **Ask the user** if there is an issue ticket for this task. Accept:
   - A Linear issue identifier (e.g., `ENG-123`)
   - A Gitea issue URL (e.g., `https://gitea.example.com/owner/repo/issues/42`)
   - A Gitea shorthand (e.g., `owner/repo#42` or `#42` if default repo is configured)
   - "no" / skip

3. **Detect the tracker type** from the input format:
   - Letters followed by a hyphen and a number → Linear
   - URL containing `/issues/` → Gitea
   - `owner/repo#N` or `#N` → Gitea

4. **Check for credentials** — read the appropriate env var (using the name
   from the config, falling back to the default name). If the env var is not
   set, inform the user and offer to proceed without issue data.

5. **Fetch the issue** using `curl`:

   **Linear** (GraphQL):
   ```
   curl -s -X POST https://api.linear.app/graphql \
     -H "Content-Type: application/json" \
     -H "Authorization: $LINEAR_API_KEY" \
     -d '{"query": "{ issueVcsByTeamAndNumber(teamKey: \"<TEAM>\", issueNumber: <NUMBER>) { identifier title description state { name } priority priorityLabel labels { nodes { name } } assignee { name } comments { nodes { body user { name } createdAt } } } }"}'
   ```

   **Gitea** (REST):
   - Issue: `curl -s -H "Authorization: token $GITEA_API_TOKEN" "$GITEA_URL/api/v1/repos/<owner>/<repo>/issues/<number>"`
   - Comments: `curl -s -H "Authorization: token $GITEA_API_TOKEN" "$GITEA_URL/api/v1/repos/<owner>/<repo>/issues/<number>/comments"`

6. **Handle errors** gracefully:
   - HTTP errors or auth failures → inform user, offer to proceed without issue data
   - Issue not found → inform user, ask for correction or proceed without
   - Network errors → inform user, offer to retry or proceed without

7. **Extract and present** the fetched data to the user:
   - Title
   - Description/body
   - Status/state
   - Labels
   - Priority (Linear) 
   - Comments (author, date, body — in chronological order)

8. **Use as seed material** — the fetched issue content informs the
   "Gather Information" conversation. The agent should pre-populate its
   understanding of the problem from the issue description rather than asking
   the user to re-describe it. The agent should still ask clarifying questions
   for gaps not covered by the issue.

**Rationale**: This is the primary user-facing change. Positioning before
"Gather Information" ensures issue data is available to seed the conversation.

### Step 4: Modify `prompts/01-definition.md` — add Source Issue to output
**Difficulty/Risk**: Low

Add a `## Source Issue` section to the definition output template, positioned
after the title and before `## Problem Statement`. This section is only
included when an issue was fetched.

```markdown
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

<Each comment with author, date, and body. Chronological order.>
#### <author> (<date>)
<comment body>
```

Also add a note in the output template indicating this section is conditional:
only present when a source issue was fetched.

**Rationale**: Preserves full issue context in the task artifact for downstream
phases. The verbatim description and comments ensure no information is lost
during the translation to the task definition.

### Step 5: Update `docs/configuration.md`
**Difficulty/Risk**: Low

Add documentation for the new `issue-trackers.md` config file:

1. Add a new section after `project-commands.md` documentation (before
   `review-profiles/`), titled `## \`issue-trackers.md\``.
2. Explain its purpose, merge strategy (replace), and format.
3. Show examples for Linear-only, Gitea-only, and both-tracker configurations.
4. Document the environment variables (default names and how to override).
5. Include a security note about keeping API keys in env vars, not in committed files.
6. Update the "Merge Behavior Summary" table at the bottom to include the new file.

**Rationale**: All config files are documented in `configuration.md` with
examples. This maintains consistency.

### Step 6: Update `docs/phases.md`
**Difficulty/Risk**: Low

Update the Phase 1 (Definition) entry to mention issue ticket support:

- In the description, add that the agent can import context from Linear or
  Gitea issues.
- In the **Reads** line, add `issue-trackers.md` (resolved via file resolution).
- Add a tip about providing issue tickets to speed up definition.

**Rationale**: The phase reference doc should reflect the new capability so
users know it exists.

### Step 7: Update `prompts/00-setup.md` — mention issue tracker config
**Difficulty/Risk**: Low

Add a brief optional mention in setup, similar to how phase-steps are mentioned
in Step 3b. After the phase-steps mention, add a note like:

> "If you use Linear or Gitea for issue tracking, you can configure the
> integration by creating an `issue-trackers.md` file in your project config
> directory. See the default template at `defaults/issue-trackers.md` for the
> format. API credentials are read from environment variables."

Do not add a full interactive setup flow — this is informational only.

**Rationale**: Users should know the capability exists during setup, but it is
optional and doesn't require interactive configuration.

## Testing Strategy

Since this is a markdown-only repo with no automated tests, verification is
manual:

- **Template correctness**: Read through the modified `prompts/01-definition.md`
  and verify the issue ticket flow instructions are clear, complete, and
  unambiguous. An agent following them literally should be able to fetch from
  both Linear and Gitea.
- **Config file format**: Verify `defaults/issue-trackers.md` is self-documenting
  and follows the pattern of existing default files.
- **File resolution**: Verify the new entry in `skill.md` follows the exact
  pattern of existing entries and uses replace semantics.
- **Cross-phase references**: Verify no existing phase references are broken
  by checking all files that read `01-definition.md`.
- **Documentation consistency**: Verify `docs/configuration.md` merge behavior
  table includes the new file, and `docs/phases.md` accurately describes
  Phase 1.
- **curl commands**: Verify the Linear GraphQL query and Gitea REST endpoints
  are syntactically correct and use the right auth headers.
- **Manual walkthrough**: Mentally trace through the flow: user says "new task" →
  agent reads `01-definition.md` → asks about issue → user provides `ENG-123` →
  agent reads config for env var name → checks env var → fetches via curl →
  presents data → uses as seed → writes definition with Source Issue section.

## Documentation Impact

- `docs/configuration.md` — new section for `issue-trackers.md`, updated merge table
- `docs/phases.md` — updated Phase 1 description
- `defaults/issue-trackers.md` — new file (serves as both default and documentation)
- `prompts/00-setup.md` — brief mention of issue tracker config option

No README changes needed — the README lists repo structure but doesn't detail
individual features. The new `defaults/issue-trackers.md` file will be visible
in the structure naturally.

## Breaking Changes

None. This is purely additive:
- The new section in `01-definition.md` is conditional (only appears when an
  issue is provided).
- No existing output sections are modified or removed.
- No existing file resolution rules are changed.
- Downstream phases that read `01-definition.md` are unaffected — they read
  it for problem statement, acceptance criteria, and scope, which remain in
  the same format.

## Risks & Open Questions

1. **Linear GraphQL query accuracy**: The exact query field names need
   verification against the current Linear API. The query in Step 3 uses
   `issueVcsByTeamAndNumber` which may not be the correct field name. The
   actual field may be `issues` with a filter. This should be verified during
   implementation by consulting the Linear API docs or schema introspection.

2. **Large issues with many comments**: An issue with dozens of comments could
   produce a very large `## Source Issue` section. The prompt should include
   guidance on truncating or summarizing if the volume is excessive. A
   reasonable threshold might be including the 20 most recent comments and
   noting if earlier ones were omitted.

3. **Gitea URL parsing**: The agent needs to reliably parse different Gitea URL
   formats (with or without trailing slashes, with or without `/issues/` path).
   The prompt instructions should cover common variations.
