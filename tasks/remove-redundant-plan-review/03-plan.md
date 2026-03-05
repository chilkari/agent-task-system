# Plan: Remove Redundant Plan Review from Phase 3

## Summary

Remove the "Review with User" section from `prompts/03-plan.md` and update the
"Next Steps" section to auto-transition into Phase 4, matching the pattern used
by Phase 5 → Phase 6.

## Coding Guidelines

### General
- Preserve cross-phase file references (Phase 4 still reads `03-plan.md`).
- No unicode special characters — use ASCII only.
- Follow existing wording patterns in the repo (match Phase 5's auto-transition language).

### Commit Message Format
Plain descriptive summary, no prefix.

## Implementation Steps

### Step 1: Remove "Review with User" section from `prompts/03-plan.md`
**Difficulty/Risk**: Low

Remove the entire "### Review with User" section (lines 163–175) which asks the
user to review the plan before Phase 4. This is redundant with Phase 4's
purpose.

**Rationale**: Phase 4 already presents the plan and invites structured feedback.
The Phase 3 review step creates a redundant pause.

### Step 2: Update "Next Steps" section to auto-transition
**Difficulty/Risk**: Low

Replace the current "## Next Steps" text:
```
Proceed to Phase 4 (Review/Revise with User).
```

With auto-transition language matching Phase 5's pattern:
```
Once the plan is written, state is updated, and any project-specific steps are
complete, immediately proceed to Phase 4 (Review/Revise with User). Do not wait
for user input -- load and follow `prompts/04-review.md` now.
```

**Rationale**: Makes the auto-transition explicit and unambiguous, consistent
with how Phase 5 → Phase 6 works.

## Testing Strategy

- **Manual verification**: Read the modified `prompts/03-plan.md` and confirm:
  1. The "Review with User" section is gone.
  2. The "Next Steps" section uses auto-transition language.
  3. All other sections are unchanged.
  4. The flow from Phase 3 → Phase 4 is seamless (Phase 4 handles all user review).

## Documentation Impact

No documentation changes needed. The `skill.md` phase routing table already
shows Phase 3 → Phase 4, and `docs/phases.md` does not describe the intra-phase
review step.

## Breaking Changes

None. Phase 4 already handles plan presentation and review as its first action.
Removing the redundant step in Phase 3 does not change what the user experiences
in Phase 4.

## Risks & Open Questions

None. This is a straightforward removal of a redundant step.
