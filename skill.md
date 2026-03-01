# Task System Skill

You have access to a spec-driven task system that guides development through
structured phases. This skill defines how you operate when the user wants to
work on a task.

## Configuration

Two paths are configured in the project's AI agent config:

- **Project config**: The directory containing project-specific overrides
  (coding guidelines, commit format, project commands, review profiles).
- **Tasks directory**: The directory where task output is stored.

If these are not configured, ask the user to run setup first.

## Commands

Respond to the following user requests:

### "set up the task system" / "initialize tasks" / "task setup"

Run the one-time project setup. Follow `prompts/00-setup.md`.

### "new task" / "start a task" / "task new"

Start a new task from Phase 1. Follow `prompts/01-definition.md`.

### "continue task" / "resume task" / "task continue"

Resume an in-progress task. Read `task-state.md` from the most recent task
directory (or ask the user which task to resume). Determine the current phase
and follow the corresponding prompt file.

### "task status"

Read `task-state.md` from the current (or specified) task and report:
- Which phase is active.
- What has been completed.
- If in Phase 4 (review), summarize open items from `04-review-notes.md`.
- If in Phase 5 (implement), summarize which steps are done/in-progress/blocked.

### "task list"

List all task directories in the tasks directory with their current phase
(read each `task-state.md`).

## Phase Flow

The phases execute in this order. Each phase has a prompt template in the
`prompts/` directory that contains full instructions.

```
Setup (one-time) --> Phase 1 --> Phase 2 --> Phase 3 --> Phase 4 --> Phase 5 --> Phase 6 --> Phase 7 --> Phase 8 --> Phase 9
                     Define      Research    Plan        Review      Implement   Code        Review      Final       Retro-
                                 (skip if                (loop)                  Review      Response    Polish      spective
                                  small)                                                    (loop to 6)             (optional)
```

### Phase Routing

| Phase | Prompt File | Trigger |
|-------|-------------|---------|
| Setup | `prompts/00-setup.md` | User requests setup |
| 1. Definition | `prompts/01-definition.md` | User requests new task |
| 2. Research | `prompts/02-research.md` | After Phase 1 (medium/large only) |
| 3. Plan | `prompts/03-plan.md` | After Phase 2 (or Phase 1 if small) |
| 4. Review | `prompts/04-review.md` | After Phase 3 (loops until approved) |
| 5. Implement | `prompts/05-implement.md` | After Phase 4 approval |
| 6. Code Review | `prompts/06-code-review.md` | After Phase 5 (or Phase 7) |
| 7. Review Response | `prompts/07-review-response.md` | After Phase 6 |
| 8. Final Polish | `prompts/08-final-polish.md` | After Phase 7 (review clean) |
| 9. Retrospective | `prompts/09-retrospective.md` | After Phase 8 (optional) |

### Phase Transitions

- After **Phase 1**: Check complexity. If small, go to Phase 3. Otherwise Phase 2.
- After **Phase 2**: Go to Phase 3. May trigger task splitting.
- After **Phase 3**: Go to Phase 4. May trigger task splitting.
- After **Phase 4**: Loops until user approves. Then go to Phase 5.
- After **Phase 5**: Go to Phase 6.
- After **Phase 6**: Go to Phase 7.
- After **Phase 7**: Loop back to Phase 6 for re-review. When review is clean
  and user is satisfied, go to Phase 8.
- After **Phase 8**: Go to Phase 9.
- After **Phase 9**: Task is complete. If user declines the retrospective,
  task is also complete.

## File Resolution

When a phase needs to read a file, resolve it in this order:

### Coding Guidelines
1. Read `defaults/coding-guidelines.md` from the task system.
2. If `coding-guidelines.md` exists in the project config directory, append
   its contents (project adds rules, does not replace defaults).

### Commit Message Format
1. If `commit-message-format.md` exists in the project config directory, use
   it (full replacement).
2. Otherwise, use `defaults/commit-message-format.md` from the task system.

### Project Commands
1. If `project-commands.md` exists in the project config directory, use it
   (full replacement).
2. Otherwise, use `defaults/project-commands.md` from the task system (which
   is just a template with placeholders).

### Language Review Profiles
1. Read `review-profiles/<language>.md` from the task system (if it exists for
   the detected language).
2. If `review-profiles/<language>.md` exists in the project config directory,
   append its contents (project adds checks, does not replace defaults).
3. If the project defines a language that the task system does not have a
   profile for, use the project's profile as-is.

## Cross-Session Behavior

Tasks may span multiple sessions. When resuming:

1. Read `task-state.md` to determine the current phase.
2. Read the relevant phase artifacts to understand what has been done.
3. Key continuity files:
   - `04-review-notes.md`: Append-only log of review discussions and decisions.
   - `05-implementation.md`: Step-by-step progress with status markers.
4. Summarize the current state for the user before continuing.
5. Never re-do work that is already recorded as complete.

## General Behavior

- Be thorough and methodical. Follow each phase's instructions completely.
- Be opinionated in reviews and planning -- share your professional judgment.
  But defer to the user's final decisions.
- Write all artifacts to disk incrementally. Do not hold state only in
  conversation -- if the session crashes, the files should reflect all progress.
- When in doubt about what to do, ask the user. Do not guess or assume.
- Keep the user informed about what you are doing and why, especially during
  longer operations (research, implementation).
