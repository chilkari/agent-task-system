# Default Coding Guidelines

These guidelines apply to every task. They are the baseline that is always
included. Project-specific additions are appended from the project config
directory. Task-specific modifications may be made during the planning phase.

## General Principles

- Prefer simple, readable code over clever code. If a solution requires a
  comment to explain what it does, consider rewriting it to be self-evident.
- No duplication. Before creating a new utility or helper function, search the
  codebase for an existing function that can be used or extended to support the
  use case.
- Handle errors explicitly. No swallowed errors, no empty catch blocks, no
  ignored return values that indicate failure.
- Use meaningful, descriptive names for variables, functions, files, and
  directories. Names should convey intent and context.
- Follow existing codebase conventions for file structure, naming patterns,
  and architectural decisions. When in doubt, match what is already there.

## Comments

- Code should be self-documenting. Do not add comments that restate what the
  code obviously does.
- Comments are only for clarifying genuinely complex or non-obvious logic.
- Non-exported/internal functions do not need doc comments unless the code is
  difficult to understand without one.
- Do not leave TODO, FIXME, HACK, or placeholder comments in shipped code
  unless they are intentional and tracked.

## Text and Formatting

- No unicode special characters in code or strings. Use ASCII equivalents:
  - Straight quotes (`'`, `"`) not curly/smart quotes
  - Hyphens (`-`) or double hyphens (`--`) not em-dashes or en-dashes
  - Three dots (`...`) not ellipsis characters
  - Standard ASCII apostrophes, not typographic ones
- Follow the project's established formatting and linting rules. Do not
  introduce new formatting conventions.

## Security

- No hardcoded secrets, credentials, API keys, or environment-specific values
  in source code. Use environment variables, config files, or secret management
  tools.
- Validate and sanitize inputs at trust boundaries.

## Commit Messages

- Follow the commit message format defined in the project's
  `commit-message-format.md` (either project-level override or the default).
- Each commit should represent a single logical change.
- Commit messages should be clear enough that someone reading the git log can
  understand what changed and why without reading the diff.
