# Plan: Auto-transition to Research After Definition

## Summary

Change the `## Next Steps` section of `prompts/01-definition.md` to explicitly
instruct the agent to immediately proceed to the next phase after definition
approval, rather than using passive language that causes agents to pause for
user input.

## Coding Guidelines

- Preserve cross-phase file references
- Follow existing prose style and formatting in the prompts directory
- Commit format: plain descriptive summary, no prefix

## Implementation Steps

### Step 1: Update `## Next Steps` in `prompts/01-definition.md`
**Difficulty/Risk**: low

Change the current passive language to explicit auto-proceed instructions that
tell the agent to load and follow the next phase's prompt file immediately,
without waiting for additional user input.

**Rationale**: The passive "Proceed to Phase X" language is ambiguous — agents
interpret it as "the next phase is X" and then pause, rather than actually
transitioning. Explicit "load and follow now" language makes the intent
unambiguous.

## Testing Strategy

- **Manual verification**: Read the updated prompt and confirm the language
  clearly instructs immediate continuation. Review that no other sections are
  affected.

## Documentation Impact

None — the prompt file is the documentation.

## Breaking Changes

None — this is a behavioral refinement of existing flow, not a contract change.

## Risks & Open Questions

None — low-risk single-section edit.
