# Phase 9: Retrospective & Tuning

You are offering the user an optional retrospective to tune the project's task
system configuration for future tasks. This phase is about improving the
process, not the code.

## Prerequisites

Read the following files from the task directory:
- `01-definition.md` -- the original task definition.
- `03-plan.md` -- the plan, including any task-specific coding guidelines.
- `04-review-notes.md` -- plan review discussion log (if exists).
- `05-implementation.md` -- implementation progress and any blockers.
- `06-code-review.md` (and all `06-code-review-rN.md` files) -- all review
  rounds and dispositions.
- `08-final-polish.md` -- polish results.
- `09-summary.md` -- task summary.
- `task-state.md` -- confirm we are in the retrospective phase.

Also read:
- `.codebase-knowledge.md` from the tasks directory (if it exists) -- the
  cumulative codebase knowledge base.

Also read the current project config files:
- `coding-guidelines.md` from the project config directory.
- `commit-message-format.md` from the project config directory.
- `project-commands.md` from the project config directory.
- Any `review-profiles/<language>.md` files from the project config directory
  that match languages used in this task.

## Instructions

### Step 1: Offer the Retrospective

Ask the user:

"The task is complete. Would you like to do a quick retrospective? I'll review
what happened during this task and suggest adjustments to your project
configuration so future tasks go more smoothly. You can also share anything you
noticed during the process.

If not, we're done -- the task is complete."

If the user declines, skip to **Declined -- Update State**.

### Step 2: Mine Task Artifacts for Suggestions

Review the task artifacts and identify concrete, actionable suggestions. Look
for these specific signals:

**From code review findings (`06-code-review.md` and all rounds)**:
- Findings that recurred across review rounds -- these are patterns that should
  be codified as coding guidelines or review profile entries so they are caught
  during planning, not review.
- Language-specific findings that are not already in the review profile -- these
  are candidates for review profile additions.
- Findings about patterns or conventions specific to this project that the agent
  had to discover during review -- these belong in project coding guidelines.

**From final polish (`08-final-polish.md`)**:
- Lint or format issues that were found late -- may indicate missing or
  incorrect project commands.
- AI artifact issues that recurred -- may indicate coding guidelines should
  explicitly address the pattern.

**From plan review notes (`04-review-notes.md`)**:
- Conventions or patterns the user had to explain during review -- these are
  project knowledge that should be in coding guidelines so the agent knows
  them upfront.
- Corrections to the agent's assumptions about architecture or patterns --
  these may belong in coding guidelines.

**From implementation (`05-implementation.md`)**:
- Blocker entries or failed commands -- may indicate incorrect project commands.
- Steps where the agent had to deviate from the plan -- may reveal missing
  context that coding guidelines could provide.

**From the plan (`03-plan.md`)**:
- Task-specific coding guidelines that were added during planning -- evaluate
  whether these should be permanent project-level rules.

**From research and implementation (for codebase knowledge base)**:
- Architectural knowledge or module understanding gained during this task that
  is not yet in `.codebase-knowledge.md` -- especially if research was skipped
  (small task) but the agent still learned things about the codebase during
  planning or implementation.
- Corrections to existing knowledge base entries discovered during
  implementation (e.g., a module's API changed, a pattern is no longer used).

### Step 3: Present Suggestions

Organize your suggestions by config file. For each suggestion:
- State what you observed (with a reference to which artifact/phase it came
  from).
- Propose the specific text to add or change.
- Explain why this would help future tasks.

Present them grouped:

**Coding guidelines** (`coding-guidelines.md`):
- "During code review (round 1, finding I2), I flagged [pattern] twice.
  Adding a guideline about this would catch it during planning. Suggested
  addition: '[rule text]'"

**Review profiles** (`review-profiles/<language>.md`):
- "Code review found [language-specific issue] that isn't in the review
  profile. Suggested addition: '[check text]'"

**Project commands** (`project-commands.md`):
- "The [command type] command [failed / was missing / needed adjustment].
  Suggested update: '[new command]'"

**Commit message format** (`commit-message-format.md`):
- "The commits during this task used [observed pattern] which differs from
  the configured format. Suggested update: '[new format]'"

**Codebase knowledge base** (`.codebase-knowledge.md` in tasks directory):
- "During this task I learned about [module/pattern/convention] that isn't in
  the knowledge base. Adding it would give future tasks a head start."
- "The knowledge base entry for [module] appears to be outdated -- [what
  changed]. Suggested update: '[correction]'"
- This is especially valuable for small tasks that skipped the research phase
  (which normally updates the knowledge base) but where the agent still
  learned things about the codebase.

If you have no suggestions for a category, skip it rather than saying "no
suggestions." Only present categories where you have something concrete.

If you have no suggestions at all, say so briefly: "I didn't identify any
configuration changes from this task. Everything worked as expected."

### Step 4: User-Driven Observations

After presenting your suggestions (or lack thereof), ask:

"Is there anything you noticed during this task that should work differently
next time? This could be about the coding guidelines, review checks, commands,
or the process itself."

Listen to the user's observations. For each one:
- If it maps to a config file change, propose the specific edit and confirm.
- If it is about the task system's process or prompts (e.g., "research should
  have looked at X", "code review was too aggressive about Y"), acknowledge it
  and note it as a process observation -- these cannot be fixed by editing
  project config but are valuable to record.

### Step 5: Apply Approved Changes

For each suggestion the user accepts (whether agent-suggested or user-raised):

1. Edit the actual config file in the project config directory (or the
   knowledge base in the tasks directory).
2. Show the user what was changed.

For review profile additions where the project config directory does not yet
have a `review-profiles/` directory or the specific language file, create it
with an appropriate header:

```markdown
# Project <Language> Review Additions

These checks are appended to the task system's default <language> review
profile.

- <new check>
```

For codebase knowledge base updates, follow the same format and conventions
described in `prompts/02-research.md` § "Update Codebase Knowledge Base". If
the knowledge base does not exist yet, create it. Update the "Last updated"
line at the top of the file.

### Step 6: Write Retrospective Document

Write `10-retrospective.md` in the task directory:

```markdown
# Retrospective: <task title>

## Config Changes Made

### <config file path>
- Added: "<rule or check text>" (prompted by <source reference>)

### <config file path>
- Updated: "<what changed>" (prompted by <source reference>)

<!-- Repeat for each config file that was changed. Omit files with no changes. -->

## Knowledge Base Updates
- Added: "<module/pattern/convention>" (learned during <phase>)
- Updated: "<entry>" -- <what changed>

<!-- Omit this section if no knowledge base changes were made. -->

## Config Changes Declined
- <suggestion>: <user's reason for declining>

<!-- Omit this section if all suggestions were accepted or if there were none. -->

## Process Notes
- <user observation about process, prompts, or workflow>

<!-- Omit this section if the user had no process-level observations. -->
```

If no changes were made and no process notes were captured, the document should
still be written with a brief note:

```markdown
# Retrospective: <task title>

No configuration changes or process notes from this task.
```

### Step 7: Final Commit

Check the `artifact-tracking` setting in the project config's `config.md`.

- If `committed`: If any config files were modified, commit them with an
  appropriate message (e.g.,
  `chore: update task system config from <task-slug> retrospective`).
- If `excluded`: Skip the commit. Config files are in the excluded location
  and are never committed to git. The changes are already written to disk.

### Update State

Update `task-state.md`:

```markdown
- **Current Phase**: complete
- **Phase History**:
  - ...previous entries...
  - Phase 9 (Retrospective): completed <timestamp>
  - Task complete: <timestamp>
```

### Declined -- Update State

If the user declined the retrospective, update `task-state.md`:

```markdown
- **Current Phase**: complete
- **Phase History**:
  - ...previous entries...
  - Phase 9 (Retrospective): skipped
  - Task complete: <timestamp>
```

## Task Complete

The task is finished. The task directory contains the full record of the
process from definition through retrospective.
