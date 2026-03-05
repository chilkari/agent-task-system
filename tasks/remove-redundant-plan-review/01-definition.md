# Task: Remove Redundant Plan Review from Phase 3

## Problem Statement

Phase 3 (Plan) ends with a "Review with User" section that asks the user to
review the plan before transitioning to Phase 4 (Review). Phase 4's entire
purpose is interactive plan review — it presents the plan, invites feedback,
and loops until the user approves. The review step at the end of Phase 3
creates a redundant pause that slows the workflow without adding value.

The desired state is that Phase 3 writes the plan, updates state, and
transitions directly into Phase 4 with no user interaction required between
the two phases.

## Acceptance Criteria

- [ ] Phase 3 writes the plan and transitions directly to Phase 4 without soliciting user review
- [ ] Phase 4 continues to present the plan and invite feedback as its first action
- [ ] No user input is required between Phase 3 completion and Phase 4 start
- [ ] The "Next Steps" section of Phase 3 triggers an automatic transition (like Phase 5 → Phase 6)

## Scope

### In Scope
- Modifying `prompts/03-plan.md` to remove the user review step and auto-transition to Phase 4

### Out of Scope
- Changes to Phase 4 (Review) prompt
- Changes to any other phase prompts
- Changes to `skill.md` phase routing or transition logic

## Complexity Assessment

**Level**: Small

**Reasoning**: This is a straightforward edit to two sections ("Review with
User" and "Next Steps") in a single file (`prompts/03-plan.md`). No new logic,
no cross-file dependencies beyond what Phase 4 already handles.
