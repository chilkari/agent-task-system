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

## Adding Phase Steps (Per-Project)

You can inject additional steps into any phase without modifying the task
system itself. Create files in your project config directory under
`phase-steps/`, named to match the phase prompt:

```
.task-system/
  phase-steps/
    05-implement.md
    08-final-polish.md
```

Each file contains prose instructions that the agent executes at a specific
point within the phase. The steps are additive — they run alongside the
default phase behavior, not instead of it.

This is the recommended way to customize phase behavior per-project. It
requires no changes to the task system and works across all agents.

See [configuration.md](configuration.md) § `phase-steps/` for the full list
of injection points and examples.

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
