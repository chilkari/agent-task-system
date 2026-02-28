# Phase 7: Respond to Code Review Suggestions

You are addressing the findings from the code review. Critical and important
issues will be fixed. Suggestions are discussed with the user.

## Prerequisites

Read the following files from the task directory:
- `06-code-review.md` (or the latest `06-code-review-rN.md`) -- the review
  findings to address.
- `03-plan.md` -- coding guidelines and commit message format.
- `task-state.md` -- confirm we are in the review response phase.

## Instructions

### Present the Review to the User

Summarize the review findings for the user:
- How many critical, important, and suggestion-level findings.
- Brief overview of the most significant issues.

### Handle Critical and Important Items

For all critical and important findings, present your fix plan to the user
confidently:

"I found N critical and M important issues. Here is my plan to fix them:

**Critical**:
- C1: <what you will do to fix it>
- C2: ...

**Important**:
- I1: <what you will do to fix it>
- I2: ...

Shall I proceed with these fixes?"

Wait for the user to approve before making changes. The user may disagree with
a classification or proposed fix -- adjust accordingly.

### Handle Suggestions

For each suggestion, present it to the user and ask for their decision:

"**S1: <title>** -- <description>. Would you like me to implement this?
(accept / reject / modify)"

For rejections, ask the user for a brief reason. This will be documented.

### Implement Fixes

After the user approves:

1. Fix all approved critical, important, and accepted suggestion items.
2. Follow the coding guidelines from `03-plan.md`.
3. Commit after each logical group of fixes using the commit message format.

### Document Dispositions

Update the code review file with the disposition of each finding:

```markdown
### C1: <title>
...original finding...
**Disposition**: Fixed -- <brief description of the fix>

### I1: <title>
...original finding...
**Disposition**: Fixed -- <brief description of the fix>

### S1: <title>
...original finding...
**Disposition**: Accepted -- <brief description of the change>

### S2: <title>
...original finding...
**Disposition**: Rejected -- <user's reason for rejecting>
```

### Re-Review Loop

After all fixes are implemented:

1. Inform the user that fixes are complete and a re-review will be performed.
2. Loop back to Phase 6 (Code Review).
3. The new review must be written to a numbered file:
   - First re-review: `06-code-review-r2.md`
   - Second re-review: `06-code-review-r3.md`
   - And so on.
4. This preserves the full history of review rounds and shows how the code
   evolved through review.

The loop continues until:
- The review comes back clean (no critical or important findings), AND
- The user is satisfied that remaining suggestions (if any) are acceptable.

When the user confirms the review cycle is complete, proceed to the next phase.

### Update State

Update `task-state.md`:

```markdown
- **Current Phase**: 8-final-polish
- **Phase History**:
  - ...previous entries...
  - Phase 7 (Review Response): completed <timestamp>
  - Review rounds: <total number of review cycles>
```

## Next Steps

Proceed to Phase 8 (Final Polish).
