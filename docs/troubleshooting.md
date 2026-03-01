# Troubleshooting / FAQ

### The agent is skipping phases

Check `task-state.md` in the task directory. It tracks the current phase. If
the state is wrong, edit it to the correct phase and tell the agent to resume.

### The agent cannot find the config files

The agent looks for paths in this order: agent config file, then
`.git/info/task-system`. Run "set up the task system" if neither exists.
If setup has been run, check that `.git/info/task-system` contains the
correct paths.

### Tests are failing before implementation starts

This is the pre-flight check working as intended. The codebase must have
passing tests before the agent begins. Fix the pre-existing failures first,
then resume the task.

### The agent is thrashing on a test failure

The agent should detect this and stop after a few attempts. If it does not,
tell it to stop and explain the issue. Check that `project-commands.md` has
the correct test command.

### I want to reset a task to an earlier phase

Edit `task-state.md` and set the current phase to the one you want to return
to. The agent will re-read the relevant artifacts and continue from there.
Previous phase artifacts are preserved — the agent will overwrite them as it
re-executes.

### I want to abandon a task

Delete the task directory, or leave it as-is for historical reference. No
special cleanup is needed.

### The review loop will not end

In Phase 7, after the agent fixes findings and re-reviews, you must explicitly
confirm that the review cycle is complete. Say "review is clean, proceed" or
similar.

### I want different paths in different repos

Each repo gets its own `.git/info/task-system` file during setup with its own
paths. A single global skill works across all repos. If you also specify paths
in an agent config file, those take priority for that specific repo. See
[advanced installation](advanced-installation.md).

### I don't want task artifacts in my git history

During setup, answer "no" when asked about committing artifacts. The agent
sets up a `.excluded/` directory (added to `.git/info/exclude`) and stores
everything there. See [git-excluded artifacts](git-excluded-artifacts.md).
