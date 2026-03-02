# Phase 4: Review/Revise with User

You are in the interactive review phase. The goal is to refine the plan through
conversation with the user until it is ready for implementation.

## Prerequisites

Read the following files from the task directory:
- `01-definition.md` -- the problem statement and acceptance criteria.
- `03-plan.md` -- the current plan to review.
- `04-review-notes.md` -- the review log (if it exists from a previous session).
- `task-state.md` -- confirm we are in the review phase.

If `04-review-notes.md` exists, read it to understand:
- What has already been discussed.
- What decisions have been made.
- What open items remain from the previous session.

Summarize the current state for the user before continuing the review.

## Instructions

### Present the Plan

If this is the first review round (no `04-review-notes.md` exists), present
the plan to the user and invite feedback:

"Here is the implementation plan. Please review each section and let me know
your thoughts. I am looking for feedback on:
- Are the implementation steps correct and in the right order?
- Is the testing strategy sufficient?
- Are the coding guidelines appropriate?
- Are there risks or concerns I have not addressed?
- Does anything need to be added, removed, or changed?"

### Interactive Review Loop

For each round of feedback:

1. **Listen** -- let the user raise concerns, ask questions, or request changes.
2. **Respond** -- address each point. Be opinionated: if you believe a user's
   suggestion would create problems, explain why and propose an alternative.
   But ultimately defer to the user's decision.
3. **Revise** -- update `03-plan.md` in place to reflect agreed-upon changes.
4. **Log** -- append an entry to `04-review-notes.md`:

```markdown
---

## Review Round <N> -- <date>

### Discussed
- <brief summary of each feedback point or question raised>

### Changes Made
- <concrete list of what was changed in 03-plan.md>

### Open Items
- <anything unresolved, to be addressed in the next round>
- <or "None -- all items addressed.">
```

5. **Continue** -- ask the user if they have more feedback or if they are ready
   to proceed.

### Handling Multi-Session Reviews

This phase may span multiple working sessions. The `04-review-notes.md` file
is the continuity mechanism:
- It is append-only. Never delete or rewrite previous entries.
- Each entry captures what happened in that round so the next session can pick
  up where it left off.
- If resuming a session, start by reading the review notes and summarizing the
  current state: "Last time we discussed X, Y, Z. We still have these open
  items: A, B. Where would you like to pick up?"

### Project-Specific Steps

If `phase-steps/04-review.md` exists in the project config directory, read it
and execute the additional steps it describes now. These steps run once, before
the plan is finalized — they may add additional review checks or validation.

### Finalizing the Plan

When the user explicitly approves the plan (e.g., "looks good", "ready to
proceed", "approved"), append a final entry to `04-review-notes.md`:

```markdown
---

## Plan Finalized -- <date>

### Summary of Changes from Initial Draft
- <high-level summary of what changed during the review process>

### Final State
The plan in `03-plan.md` has been approved by the user and is ready for
implementation.
```

### Update State

Update `task-state.md`:

```markdown
- **Current Phase**: 5-implement
- **Phase History**:
  - ...previous entries...
  - Phase 4 (Review): completed <timestamp>
```

## Next Steps

Proceed to Phase 5 (Implement).
