# AGENTS.md — Agent Task System

## What This Repo Is

This is a **portable, spec-driven task system** for AI-assisted development. It
provides structured prompt templates and configuration that guide AI coding
agents through a 9-phase development workflow: definition → research →
planning → review → implementation → code review → review response → final
polish → retrospective.

**This repo is not a standalone application.** It is a set of markdown prompt
templates and configuration files that are consumed by AI coding agents (Claude,
Cursor, Pi, etc.) in other project repositories. It acts as a reusable "skill"
or "plugin" that any AI agent can follow.

## Repo Structure

```
skill.md                    # Entry point — the AI skill definition. Agents read
                            # this to understand commands, phase flow, file
                            # resolution, and cross-session behavior.

templates/SKILL.md          # Template for installing the skill in an agent's
                            # config directory. Users copy this and update the
                            # path to point to their clone.

prompts/                    # Phase-specific prompt templates (the core logic)
  00-setup.md               # One-time project setup instructions
  01-definition.md          # Phase 1: Task definition
  02-research.md            # Phase 2: Codebase research (medium/large tasks)
  03-plan.md                # Phase 3: Implementation planning
  04-review.md              # Phase 4: Plan review loop with user
  05-implement.md           # Phase 5: Step-by-step implementation
  06-code-review.md         # Phase 6: Code review of changes
  07-review-response.md     # Phase 7: Address review findings
  08-final-polish.md        # Phase 8: Lint, format, cleanup, summary
  09-retrospective.md       # Phase 9: Retrospective & config tuning

defaults/                   # Baseline configuration (applied to all projects)
  coding-guidelines.md      # Default coding rules (always appended)
  commit-message-format.md  # Default commit format (replaceable per-project)
  project-commands.md       # Command template with placeholders (replaceable)

review-profiles/            # Language-specific code review checklists
  typescript.md             # TypeScript review checklist
  bash.md                   # Bash review checklist

docs/                       # Detailed documentation
  phases.md                 # Phase reference with tips
  configuration.md          # Project config files and merge behavior
  git-excluded-artifacts.md # Keeping artifacts out of git
  knowledge-base.md         # Codebase knowledge base
  customization.md          # Modifying defaults, profiles, prompts
  advanced-installation.md  # Repo-specific and alternative installation
  troubleshooting.md        # FAQ and troubleshooting

task-system-design.md       # Original design brief / requirements document
task-system-design-plan.md  # Detailed design plan used to build this system
```

## How It Works

1. A consuming project references `skill.md` from its AI agent config (e.g.,
   `CLAUDE.md`, `.cursorrules`, `.pi/skills/task/SKILL.md`).
2. The agent reads `skill.md` to learn the available commands and phase flow.
3. When the user says "new task", "resume task", etc., the agent follows the
   corresponding prompt template from `prompts/`.
4. Each phase reads/writes markdown artifacts to a task directory in the
   **consuming project** (not this repo).
5. Per-project configuration (coding guidelines, commands, review profiles)
   lives in a config directory in the consuming project and merges with or
   replaces the defaults in this repo.

## Key Design Decisions

- **Prompt templates are the source of truth.** Each file in `prompts/`
  contains complete instructions for one phase. The agent follows them
  literally. If you modify a prompt, you modify agent behavior.
- **File merge strategies matter.** `coding-guidelines.md` and review profiles
  use **append** semantics (project rules add to defaults).
  `commit-message-format.md` and `project-commands.md` use **replace**
  semantics (project config fully overrides defaults).
- **Cross-session continuity via disk.** All progress is written incrementally
  to markdown files. `task-state.md` tracks current phase. Agents resume by
  reading these files — no in-memory state is assumed.
- **Phase transitions are deterministic.** See `skill.md` § "Phase Routing"
  and "Phase Transitions" for the exact flow, including skip conditions
  (small tasks skip research), loops (review ↔ review response), and optional
  phases.

## Guidelines for Modifying This Repo

### Safe changes
- Adding checks or steps within an existing phase prompt.
- Adjusting output document formatting.
- Adding new review profiles in `review-profiles/`.
- Updating default coding guidelines or commit format.
- Improving wording in prompts for clarity.

### Careful changes
- **Phase transition logic** — changing which phase follows which can break the
  flow. The routing table is in `skill.md` and each prompt references
  specific next-phase files.
- **Output file names** — phases reference each other's output by filename
  (e.g., Phase 5 reads `03-plan.md`, Phase 6 reads `05-implementation.md`).
  Renaming breaks cross-phase dependencies.
- **Removing sections from output templates** — downstream phases may depend
  on specific sections existing in upstream artifacts.

### Adding a new phase
1. Create `prompts/NN-phase-name.md` with full instructions.
2. Update `skill.md` phase routing table and transition rules.
3. Update `README.md` phase table and `docs/phases.md` reference.
4. Verify no existing phase's transition logic is broken.

### Adding a new review profile
1. Create `review-profiles/<language>.md` following the format of existing
   profiles (see `typescript.md` or `bash.md`).
2. No other changes needed — the system auto-detects languages and loads
   matching profiles.

## Codebase Knowledge Base

The system maintains a cumulative knowledge base at `.codebase-knowledge.md`
in the consuming project's tasks directory. This is created and updated during
Phase 2 (Research) and Phase 9 (Retrospective) with durable architectural
knowledge — module descriptions, conventions, patterns, dependencies.

Phase 2 reads this file at the start to skip re-exploring known areas, then
updates it at the end with new findings. This means early tasks do full
research, but later tasks benefit from accumulated context and can focus
research on task-specific areas.

The knowledge base is a head start, not an authority. The agent spot-checks
entries it plans to rely on and corrects stale information.

## Not in This Repo

- **Task output** — task artifacts (`01-definition.md`, `task-state.md`, etc.)
  are written to the consuming project, not here.
- **Codebase knowledge base** — `.codebase-knowledge.md` lives in the
  consuming project's tasks directory, not here.
- **Backlog** — `backlog.md` lives in the consuming project's tasks directory,
  not here. It captures out-of-scope items discovered during tasks.
- **Project-specific config** — per-project overrides for coding guidelines,
  commands, and review profiles live in the consuming project's config
  directory (typically `.task-system/`).
- **Runtime code** — there is no executable code. Everything is markdown
  prompt templates interpreted by AI agents.
