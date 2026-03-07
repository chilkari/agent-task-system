# Review Notes

---

## Review Round 1 — 2026-03-07

### Discussed
- Branch name prefix should be configurable via `.task-system/config.md`
  (`## Branch Prefix`) rather than derived from the issue identifier's owner
  each time. This gives the user a consistent, deliberate namespace (e.g.,
  `jdw`, `feature`) across all tasks.
- `#` characters should not appear in branch names — they cause shell issues.
  Gitea identifiers like `owner/repo#1` should use only the numeric portion
  (`1`) in the branch name.
- User confirmed the `/` separator between prefix and rest of branch name
  (e.g., `jdw/1-branch-preflight-check`).

### Changes Made
- Rewrote branch name suggestion logic in Step 1 to support configurable
  `Branch Prefix` from `.task-system/config.md`.
- Added explicit examples for all combinations: with/without prefix,
  Gitea/Linear/no issue.
- Added rule: never use `#` in branch names.
- Added Step 2: document the `Branch Prefix` config option in
  `docs/configuration.md`.
- Renumbered original Step 2 → Step 3.
- Updated testing strategy with two additional verifications (config docs,
  no `#` in branch names).

### Open Items
- None — all items addressed.

---

## Plan Finalized — 2026-03-07

### Summary of Changes from Initial Draft
- Added configurable `Branch Prefix` from `.task-system/config.md` for branch
  name namespace prefixes.
- Replaced `#` with numeric-only extraction for Gitea identifiers in branch
  names.
- Added documentation step for the new config option.

### Final State
The plan in `03-plan.md` has been approved by the user and is ready for
implementation.
