# Bash Review Profile

This checklist is used during code review (Phase 6) and final polish (Phase 8)
for tasks involving Bash scripts. Project-specific additions can be appended by
creating a `review-profiles/bash.md` in the project config directory.

## Fundamentals

- Always use `set -euo pipefail` at the top of scripts. This catches errors
  early: `-e` exits on failure, `-u` catches unset variables, `-o pipefail`
  catches failures in piped commands.
- Quote all variable expansions (`"$var"` not `$var`). Unquoted expansions
  cause word splitting and globbing bugs that are difficult to diagnose.
- Use `[[ ]]` over `[ ]` for conditionals. Double brackets prevent word
  splitting and glob expansion, support regex matching, and have clearer
  syntax for compound conditions.
- Use `$(command)` for command substitution, not backticks. Dollar-paren is
  nestable, easier to read, and less error-prone.
- Check command exit codes explicitly where needed, especially for commands
  whose failure should trigger specific recovery logic rather than script exit.

## Variables and Scope

- Use `local` for all variables inside functions. Without `local`, variables
  leak into the global scope and can cause subtle bugs in callers.
- Use `readonly` for constants and values that should not change after
  assignment.

## File and Process Handling

- Avoid parsing `ls` output. Use globs (`for f in *.txt`) or `find` with
  appropriate flags. `ls` output is not reliably parseable across platforms.
- Use `mktemp` for temporary files. Clean them up with a trap on EXIT.

## Bash 3.2 Compatibility

Scripts must be compatible with Bash 3.2 (the version shipped with macOS)
unless the script explicitly documents a newer bash requirement in a comment
near the shebang or in accompanying documentation. Bash 3.2 does not support
the following -- do not use them without an explicit compatibility declaration:

- **Associative arrays** (`declare -A`). Use paired regular arrays, case
  statements, or other workarounds instead.
- **`readarray` / `mapfile`**. Use `while IFS= read -r line` loops instead.
- **Case conversion operators** (`${var,,}` for lowercase, `${var^^}` for
  uppercase). Use `tr '[:upper:]' '[:lower:]'` or `tr '[:lower:]' '[:upper:]'`
  instead.
- **Pipe stderr shorthand** (`|&`). Use `2>&1 |` instead.
- **Append stdout+stderr shorthand** (`&>>`). Use `>> file 2>&1` instead.
- **Negative array indexing** (`${arr[-1]}`). Compute the last index explicitly:
  `${arr[${#arr[@]}-1]}`.
- **Namerefs** (`declare -n`). Restructure to avoid them, or use `eval`
  cautiously with proper quoting if absolutely necessary.
