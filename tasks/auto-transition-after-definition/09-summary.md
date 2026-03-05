# Task Summary: Auto-transition to Research After Definition

## Task
After Phase 1 (Definition), agents paused and waited for user input before
beginning the next phase. The transition should be automatic once the user
approves the definition.

## What Was Delivered
Updated `prompts/01-definition.md` to explicitly instruct agents to immediately
proceed to the next phase after definition approval.

- [x] Agent proceeds directly to Phase 2 (or Phase 3 for small tasks) without waiting for additional user input: the "Next Steps" section now says "immediately proceed... without waiting for additional user input" and uses "Load and follow ... now" language.
- [x] The `## Next Steps` section explicitly instructs auto-proceed: passive "Proceed to Phase X" replaced with active "Load and follow `prompts/XX.md` now."
- [x] No changes to the "Review with User" step: that section is untouched.

## Files Changed
- `prompts/01-definition.md` — modified `## Next Steps` section

## Review Rounds
One review round, no findings.

## Notable Decisions
- Kept the change minimal — only the "Next Steps" section was modified. The
  rest of the prompt and `skill.md` routing table were already correct.
- Used "Load and follow `prompts/XX.md` now" phrasing to be unambiguous about
  what the agent should do, referencing the actual file paths.
