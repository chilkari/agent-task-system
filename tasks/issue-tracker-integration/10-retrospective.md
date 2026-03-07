# Retrospective: Issue Tracker Integration (Linear & Gitea)

## Config Changes Made

### `.task-system/coding-guidelines.md`
- Added: Unicode override section — unicode characters (arrows, em-dashes, section signs) are explicitly allowed in this repo's prompt templates and documentation, overriding the default "No unicode special characters" guideline. (Prompted by: Phase 8 polish finding where unicode arrows were replaced unnecessarily, and user observation that they are natural and readable in prompt templates.)
- Added: Prompt template rules section — codifying conventions discovered during this task: preserve cross-phase file references, update skill.md routing table for transition changes, follow self-documenting template pattern for new default config files, specify merge strategy for new file resolution entries.

### `prompts/01-definition.md`
- Restored: Unicode arrows (→) in tracker type detection section that were replaced with ASCII during polish. Now consistent with codebase convention.

## Knowledge Base Updates
- Updated: `defaults/` module entry — added `issue-trackers.md` with replace semantics to the file list and noted its purpose (issue tracker integration configuration for Linear and Gitea).

## Config Changes Declined
- None — all suggestions accepted.

## Process Notes
- The polish phase flagged unicode characters as violations per the default coding guidelines, then the retrospective revealed the default doesn't fit this repo. For future tasks in this repo, the updated project coding guidelines will prevent this unnecessary churn.
