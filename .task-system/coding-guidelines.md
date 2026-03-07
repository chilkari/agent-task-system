# Project Coding Guidelines

These guidelines are appended to the task system defaults.

## Unicode Override

The default coding guideline "No unicode special characters" is overridden
for this repo. Unicode characters are allowed in prompt templates and
documentation where they improve readability. Common uses:

- Arrows (→) in flow descriptions and mapping notation
- Em-dashes (—) in prose for parenthetical statements
- Section signs (§) for cross-references to document sections
- Other unicode punctuation where it aids clarity

This override applies because the files in this repo are markdown prompt
templates read by humans and AI agents, not source code parsed by compilers.

## Prompt Template Rules

- All changes to prompt templates must preserve cross-phase file references.
- Phase transition logic changes require updating the skill.md routing table.
- New default config files in `defaults/` must follow the self-documenting
  template pattern (comments explaining usage, examples where helpful).
- New file resolution entries in `skill.md` must specify the merge strategy
  (append or replace).
