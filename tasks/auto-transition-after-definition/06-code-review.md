# Code Review: Auto-transition to Research After Definition

## Review Summary

A small, focused change to the `## Next Steps` section of
`prompts/01-definition.md`. The passive "Proceed to Phase X" language was
replaced with explicit "Load and follow `prompts/XX.md` now" instructions,
prefaced by a sentence making clear the agent should not wait for user input.
The change is clean, well-scoped, and achieves its goal.

## Critical

None.

## Important

None.

## Suggestions

None.

## Language Profile Check

N/A — this is a markdown prose change, no code involved.

## Acceptance Criteria Verification

- [x] After the user approves the definition in Phase 1, the agent proceeds directly to Phase 2 (or Phase 3 for small tasks) without waiting for additional user input: **Met** — the new language explicitly says "immediately proceed... without waiting for additional user input" and instructs "Load and follow ... now."
- [x] The `## Next Steps` section of `prompts/01-definition.md` explicitly instructs the agent to proceed immediately rather than just stating which phase is next: **Met** — passive language replaced with active instructions.
- [x] No changes to the Phase 1 "Review with User" step: **Met** — that section is untouched.
