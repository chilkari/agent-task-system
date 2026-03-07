# Implementation: Issue Tracker Integration (Linear & Gitea)

## Rollback Point
**Task start**: 4276c7b6d3c5baeef144af493a8a4ee3f2401829

## Steps

### Step 1: Create `defaults/issue-trackers.md`
**Status**: complete
**Rollback**: 4276c7b6d3c5baeef144af493a8a4ee3f2401829

**What was done**:
- Created `defaults/issue-trackers.md`
- Documents Linear and Gitea env var defaults, override instructions, Gitea settings

**Deviations from plan**: None

### Step 2: Add file resolution rules to `skill.md`
**Status**: complete
**Rollback**: e7a91f1b2823c204e54fdf0db979ed4c42d1259e

**What was done**:
- Added "Issue Tracker Configuration" entry to File Resolution section in `skill.md`
- Placed after Language Review Profiles, before Phase Steps
- Uses replace semantics

**Deviations from plan**: None

### Step 3: Modify `prompts/01-definition.md` — add issue ticket flow
**Status**: complete
**Rollback**: 095c76993a8e27592d0137b3fa5eca506187a711

**What was done**:
- Added "Check for Issue Ticket" section before "Gather Information" in `prompts/01-definition.md`
- Covers: config resolution, tracker type detection, Linear GraphQL fetch, Gitea REST fetch + comments, error handling, data presentation (20 comment limit), seed material usage

**Deviations from plan**:
- Used Linear `issues` filter query with variables instead of the `issueVcsByTeamAndNumber` field mentioned in the plan, as the plan noted this needed verification. The `issues` query with `team.key` and `number` filters is the correct approach per Linear's GraphQL API.

### Step 4: Modify `prompts/01-definition.md` — add Source Issue to output
**Status**: complete
**Rollback**: 1be51f9ee299f79144eb27a166047a5818de9699

**What was done**:
- Added conditional `## Source Issue` section to definition output template
- Includes tracker, ID, URL, title, status, labels, description (verbatim), and comments
- Comments subsection notes 20-comment limit and omission of this subsection when no comments exist
- HTML comments clarify the section is conditional

**Deviations from plan**: None

### Step 5: Update `docs/configuration.md`
**Status**: complete
**Rollback**: c9330944c90da8773f7da2bdd9a438fd0e361b73

**What was done**:
- Added `issue-trackers.md` section between `project-commands.md` and `review-profiles/`
- Includes purpose, security note, default env var names, three examples (Linear-only, Gitea with custom env vars, both trackers)
- Added `issue-trackers.md` row to merge behavior summary table with Replace strategy

**Deviations from plan**: None

### Step 6: Update `docs/phases.md`
**Status**: complete
**Rollback**: 8a53900b4504905a4b34230c92012262742eda56

**What was done**:
- Updated Phase 1 description to mention issue ticket import from Linear/Gitea
- Updated Reads line to include `issue-trackers.md` and issue tracker API
- Updated Writes output list to include Source Issue section
- Added tip about providing issue tickets to save time

**Deviations from plan**: None

### Step 7: Update `prompts/00-setup.md` — mention issue tracker config
**Status**: complete
**Rollback**: c38a0e9e49c2718ecfca428e06ae25bb29b288dc

**What was done**:
- Added Step 3c to setup prompt mentioning issue tracker integration
- Lists default env var names and points to `defaults/issue-trackers.md` for format
- Informational only, does not create files during setup

**Deviations from plan**: None

## Test Results

**Manual verification**:
- Template correctness: PASS — `prompts/01-definition.md` issue ticket flow is clear, complete, covers both trackers with full fetch instructions, error handling, and seed material usage
- Config file format: PASS — `defaults/issue-trackers.md` follows existing default file pattern (self-documenting, commented examples)
- File resolution: PASS — new entry in `skill.md` follows exact pattern of existing entries, uses replace semantics
- Cross-phase references: PASS — all existing sections in `01-definition.md` output (Problem Statement, Acceptance Criteria, Scope, Complexity Assessment) are intact and unchanged
- Documentation consistency: PASS — `docs/configuration.md` merge table includes new file, `docs/phases.md` accurately describes Phase 1 with issue support
- curl commands: PASS — Linear GraphQL query validated as correct JSON, Gitea REST endpoints follow standard API pattern
- Mental walkthrough: PASS — traced flow: user says "new task" → agent reads 01-definition.md → asks about issue → user provides ENG-123 → agent reads config for env var name → checks env var → fetches via curl → presents data → uses as seed → writes definition with Source Issue section
