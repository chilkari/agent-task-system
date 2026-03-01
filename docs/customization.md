# Customization

## Customizing Defaults

Fork or copy the task system and modify:

- `defaults/coding-guidelines.md` — change the baseline rules for all your
  projects.
- `defaults/commit-message-format.md` — change the default commit format.
- `defaults/project-commands.md` — change the command template.

## Adding Language Review Profiles

Create a new file in `review-profiles/`:

```markdown
# <Language> Review Profile

This checklist is used during code review (Phase 6) and final polish (Phase 8)
for tasks involving <Language>.

## <Category>

- <check 1>
- <check 2>
...
```

The task system auto-detects languages during research and applies matching
profiles. No other configuration is needed.

## Modifying Prompt Templates

The files in `prompts/` contain the full instructions for each phase. You can
modify them to adjust the workflow.

**Safe to modify:**
- Adding extra checks or steps within a phase.
- Adjusting the format of output documents.
- Adding new sections to phase output files.
- Changing the wording of user-facing messages.

**Modify with care:**
- Changing the phase transition logic (which phase follows which).
- Removing sections from output documents that other phases depend on.
- Changing the names of output files (other phases reference them by name).
