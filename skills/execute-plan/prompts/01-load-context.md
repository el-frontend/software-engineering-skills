# Phase 1 — Load Context

You are preparing to execute a plan. Before touching code, read everything load-bearing and confirm the next task with the user. **Do not implement anything in this phase.**

## Inputs

- A plan path (e.g. `docs/plans/FR-001.md`). If not given, ASK.
- The target repo, including `CLAUDE.md`, the PRD, and `docs/designs/` if applicable.

## What to do

### Step 1: Read the plan

Read `docs/plans/<FR-N>.md` cover-to-cover. Extract:

- The requirement statement.
- The full task list — note which are `[ ]` and which are already `[x]`.
- The "Reuse" section — files/functions to leverage, not duplicate.
- The "Out of scope" section — things to actively NOT touch.
- The "Open risks" section — known traps.
- The "Done when" criteria.

If any task line is missing a sub-ID, AC reference, file list, or test reference, STOP. The plan isn't ready — return to `skills/generate-dev-plan/`.

### Step 2: Read the upstream context

Don't implement against the plan in isolation. Pull in:

- **The PRD** — `docs/prd/<latest>.md`, specifically the FR-N section, its ACs, edge cases.
- **CLAUDE.md** — project conventions, commands, and any feedback notes from past iterations.
- **Designs** — every file under `docs/designs/<FR-N>-*.html`. Open them; the HTML is the spec for the visual.
- **Existing code** — the files the plan's tasks will touch. Read them so you have a mental map before changing them.
- **Recent commits** — `git log --oneline -20` on the current branch. If teammates already started on this requirement, you need to know.

### Step 3: Confirm preconditions

Verify before continuing:

- [ ] Working tree is clean (no uncommitted changes from another task).
- [ ] On the right branch (ask the user if unclear — common pattern: `feat/FR-001-<slug>`).
- [ ] `CLAUDE.md` lists a `{{TEST_CMD}}` and `{{TYPECHECK_CMD}}` — if not, ASK the user before continuing.
- [ ] All `[ ]` tasks still apply (the codebase may have moved on since the plan was written; flag stale tasks).

If any precondition fails, STOP and resolve with the user.

### Step 4: Pick the next task

The "next task" is the topmost `[ ]` in the plan. Exception: if the plan flagged tasks as parallelizable (`a/b` suffix), ask the user which one to tackle first.

### Step 5: Output the context summary

```markdown
## Ready to execute — FR-001

**Plan:** `docs/plans/FR-001.md`
**Branch:** `feat/FR-001-login`
**Working tree:** clean

### Loaded context
- PRD section: `docs/prd/prd-v0.3.md` § FR-001
- Designs: `docs/designs/FR-001-login.html`, `FR-001-login-error-401.html`
- Test command: `pnpm test`
- Typecheck: `pnpm tsc --noEmit`

### Tasks remaining
- [ ] FR-001.1 — Add lastLoginAt to User schema · files: `prisma/schema.prisma` · AC: AC-001.4 · test: `tests/db/user.spec.ts`
- [ ] FR-001.2 — Login handler reads/writes lastLoginAt · files: `src/routes/auth.ts` · AC: AC-001.1, AC-001.4 · test: `tests/auth/login.spec.ts`
- [ ] FR-001.3 — Inline error component · files: `src/components/InlineError.tsx` · AC: AC-001.2 · test: `tests/components/InlineError.spec.tsx`
- [ ] FR-001.4 — LoginForm wires inline error · files: `src/components/LoginForm.tsx` · AC: AC-001.2 · test: `tests/components/LoginForm.spec.tsx`

### Next task
**FR-001.1** — Add lastLoginAt to User schema

### Stale-task check
- All 4 tasks still apply (verified files exist or are creatable in current state).
```

### Step 6: Hand to user

> *Ready to start FR-001.1. Proceed → / Refine context / Skip task → /*

**Do not enter Phase 2 without explicit approval for the next task.**

## Anti-patterns (forbidden)

- ❌ Skipping the design-file read for UI tasks. The HTML is part of the spec.
- ❌ Starting on a non-clean working tree. Either commit, stash, or abort.
- ❌ Picking a task out of order without explicitly asking. Order in the plan reflects the dependency graph from `generate-dev-plan` Phase 2.
- ❌ Skipping the PRD re-read. The plan is a derivative; the PRD is the source of truth.
