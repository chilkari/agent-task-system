# Task: Auto-transition to Research After Definition

## Problem Statement

After Phase 1 (Definition) is finalized and the user approves the definition,
the agent pauses and waits for user input before beginning Phase 2 (Research).
This pause is unnecessary — once the definition is approved, the agent should
automatically proceed into the research phase (for medium/large tasks) or the
plan phase (for small tasks).

## Acceptance Criteria

- [ ] After the user approves the definition in Phase 1, the agent proceeds directly to Phase 2 (or Phase 3 for small tasks) without waiting for additional user input
- [ ] The `## Next Steps` section of `prompts/01-definition.md` explicitly instructs the agent to proceed immediately rather than just stating which phase is next
- [ ] No changes to the Phase 1 "Review with User" step — the user still approves the definition before transition

## Scope

### In Scope
- Modifying `prompts/01-definition.md` next-steps language to instruct auto-proceed

### Out of Scope
- Changing the user review/approval of the definition itself
- Changing other phase transitions
- Modifying `skill.md` phase routing (it already describes the correct flow)

## Complexity Assessment

**Level**: small

**Reasoning**: Single file change, clear intent, minimal risk.
