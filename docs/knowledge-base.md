# Codebase Knowledge Base

As you complete tasks, the system builds a cumulative knowledge base about
your codebase at `.codebase-knowledge.md` in the tasks directory. This
captures architecture, module descriptions, conventions, testing patterns,
and key dependencies — the expensive-to-discover context that stays relevant
across tasks.

## How It Works

- **Created during the first medium/large task** that goes through research
  (Phase 2). The agent extracts reusable architectural findings and proposes
  them for the knowledge base. You approve what goes in.
- **Updated after subsequent research phases** with new discoveries,
  corrections to stale entries, and enrichments.
- **Updated during retrospective (Phase 9)** — especially valuable for small
  tasks that skip research but where the agent learned things during planning
  or implementation.
- **Read at the start of every research phase** to skip re-exploring known
  areas and focus on what is new.

## What It Contains

- **Architecture overview** — high-level module map and data flow
- **Key modules** — location, purpose, patterns, public API, last verified
  date
- **Conventions & patterns** — naming, error handling, logging, etc.
- **Testing patterns** — frameworks, file naming, fixture/mock strategies
- **External dependencies** — key libraries with versions and doc links
- **Infrastructure** — build pipeline, deployment, config formats

## Staleness

Each module entry includes a "Last verified" date. Entries are spot-checked
when the agent plans to rely on them. If something has changed, the entry is
corrected. The knowledge base is a head start, not a substitute for looking
at the code.

## Example

After several tasks, the knowledge base might look like:

```markdown
# Codebase Knowledge

Last updated: 2026-02-28 (after task: refactor-auth-module)

## Architecture Overview
Express.js monolith with three main layers: routes, services, repositories.
All HTTP handling in routes/, business logic in services/, database access
in repositories/. Services never import from routes. Repositories use the
query builder, never raw SQL.

## Key Modules
### Auth Middleware
- **Location**: src/middleware/auth.ts
- **Purpose**: JWT validation and role-based access control
- **Key patterns**: Chain of responsibility, each middleware calls next()
- **Public API**: requireAuth(), requireRole(role)
- **Last verified**: 2026-02-28

### Event Bus
- **Location**: src/events/
- **Purpose**: In-process pub/sub for decoupling services
- **Key patterns**: Typed event definitions in events/types.ts
- **Public API**: emit(event), on(eventType, handler)
- **Last verified**: 2026-02-15
```

## File Location

```
<tasks-dir>/
  .codebase-knowledge.md    # Cumulative knowledge base
  add-verbose-flag/         # Individual task directories
  refactor-auth-module/
  ...
```
