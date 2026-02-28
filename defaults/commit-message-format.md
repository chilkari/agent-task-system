# Default Commit Message Format

This is the default commit message format. Projects can override this entirely
by providing their own `commit-message-format.md` in the project config
directory.

## Format

```
<type>: <short summary>

<optional body>
```

### Type

Use one of the following types:

- `feat` -- a new feature or capability
- `fix` -- a bug fix
- `refactor` -- code restructuring without behavior change
- `docs` -- documentation changes only
- `test` -- adding or updating tests only
- `chore` -- maintenance tasks (dependencies, config, tooling)
- `style` -- formatting changes only (no logic changes)

### Short Summary

- Lowercase, no period at the end.
- Imperative mood ("add feature" not "added feature").
- Under 72 characters.
- Describe what changed, not how.

### Body (Optional)

- Separate from summary with a blank line.
- Wrap at 72 characters.
- Explain why the change was made when the summary alone is not sufficient.
- Reference related issues or tasks if applicable.

## Examples

```
feat: add version comparison to container report

fix: handle missing labels in docker image inspect

refactor: extract registry token fetching into shared function

docs: update README with new configuration options

test: add unit tests for image reference parsing

chore: update typescript to 5.4
```
