# Task Summary: Issue Tracker Integration (Linear & Gitea)

## Task
Add support for importing issue context from Linear and Gitea issue trackers
during task definition, storing the fetched data in the definition artifact
for downstream phase reference.

## What Was Delivered

- [x] Phase 1 asks if there is an issue ticket: "Check for Issue Ticket" section added before "Gather Information" in `prompts/01-definition.md`
- [x] Linear issues can be fetched via API: GraphQL query with team key + issue number filter, personal API key auth
- [x] Gitea issues can be fetched via API: REST endpoints for issue + comments, personal access token auth
- [x] Fetched data includes title, description, labels, status, and comments: both tracker flows extract all fields, 20-comment limit
- [x] Issue context incorporated as seed material: "Use as Seed Material" section instructs agent to pre-populate understanding
- [x] Source Issue section added to definition output: conditional section with tracker, ID, URL, title, status, labels, description, comments
- [x] Credentials use env vars with configurable names: defaults provided (LINEAR_API_KEY, GITEA_URL, GITEA_API_TOKEN), overridable in config
- [x] New `issue-trackers.md` config file supported: replace semantics, file resolution in skill.md
- [x] Default template provided: `defaults/issue-trackers.md` with self-documenting format
- [x] `skill.md` updated: file resolution entry for issue tracker config
- [x] Issue context stored in definition artifact: Source Issue section in `01-definition.md` for cross-phase reference
- [x] Documentation updated: `docs/configuration.md` (new section + merge table), `docs/phases.md` (Phase 1 description), `prompts/00-setup.md` (brief mention)
- [x] Comments included: both tracker flows fetch comments, 20 most recent with omission note

## Files Changed

| File | Change |
|------|--------|
| `defaults/issue-trackers.md` | **Created** -- default config template for tracker settings |
| `prompts/01-definition.md` | **Modified** -- added issue ticket flow + Source Issue output section |
| `skill.md` | **Modified** -- added Issue Tracker Configuration file resolution entry |
| `docs/configuration.md` | **Modified** -- new section documenting `issue-trackers.md`, updated merge table |
| `docs/phases.md` | **Modified** -- updated Phase 1 description, reads line, and tips |
| `prompts/00-setup.md` | **Modified** -- added Step 3c mentioning issue tracker integration |

## Review Rounds
2 rounds. Round 1 found 2 important issues (hardcoded env var names in curl
examples, URL detection ambiguity) and 1 suggestion (backtick inconsistency
in config values). All addressed. Round 2 was clean.

## Notable Decisions
- **Replace semantics** for `issue-trackers.md` — project config fully overrides defaults since tracker settings are entirely project-specific.
- **Env var name placeholders** in curl examples — uses `$<api-key-variable>` style instead of hardcoded defaults, with notes to substitute from config.
- **Linear URL detection** added to tracker type rules so `linear.app` URLs aren't misidentified as Gitea.
- **20-comment limit** — includes the 20 most recent comments, notes if earlier ones were omitted.
- **No setup flow for trackers** — informational mention only in setup prompt, since config is optional and simple to create manually.
